---
title: Criar um dashboard em tempo real com a base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização) tutorial
description: Este tutorial mostra como criar, preencher e consultar tabelas distribuídas na base de dados do Azure para PostgreSQL Hiperescala (Citus) (pré-visualização).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: a5e4b2073a29785ee851b2733c12d6331afe59d8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791314"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Tutorial: Criar um dashboard de análise em tempo real através da utilização da base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização)

Neste tutorial, vai utilizar base de dados do Azure para PostgreSQL - Hiperescala (Citus) (pré-visualização) para saber como:

> [!div class="checklist"]
> * Criar um grupo de servidores de grande escala (Citus)
> * Utilizar o utilitário psql para criar um esquema
> * Tabelas de partição horizontal em todos os nós
> * Gerar dados de exemplo
> * Executar rollups
> * Consultar dados não processados e agregados
> * Expirar dados

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Utilizar o utilitário psql para criar um esquema

Assim que estiver ligado à base de dados do Azure para PostgreSQL - Hiperescala (Citus) (pré-visualização) com psql, pode concluir algumas tarefas básicas. Este tutorial explica-lhe ingerir dados de tráfego de análise da web, em seguida, acumular os dados para fornecer dashboards em tempo real com base nesses dados.

Vamos criar uma tabela que irá consumir todos os nossos dados de tráfego web não processados. Execute os seguintes comandos no psql terminal:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

Também vamos criar uma tabela que irá conter o nosso agregados por minuto e uma tabela que mantém a posição do nosso último rollup. Execute os seguintes comandos no também psql:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

Pode ver as tabelas recém-criado na lista de tabelas agora com o comando psql:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Tabelas de partição horizontal em todos os nós

Uma implementação de hiperescala armazena linhas de tabela em nós diferentes com base no valor de uma coluna designado pelo usuário. Este "coluna de distribuição" marca como os dados são em partição horizontal em todos os nós.

Vamos definir a coluna de distribuição para que seja o site\_id, a chave de partição horizontal. No psql, execute estas funções:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Gerar dados de exemplo

Agora, nosso grupo de servidor deve estar pronto para a ingestão de alguns dados. Podemos executar o seguinte localmente a partir de nossa `psql` ligação continuamente inserir dados.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    COMMIT;
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

A consulta insere cada segundo de aproximadamente oito linhas. As linhas são armazenadas em nós de trabalho diferentes, conforme indicado pela coluna de distribuição, `site_id`.

   > [!NOTE]
   > Deixe a consulta de geração de dados em execução e abrir uma segunda ligação psql para os comandos restantes neste tutorial.
   >

## <a name="query"></a>Consulta

A opção de alojamento de hiperescala permite que vários nós processar consultas em paralelo para a velocidade. Por exemplo, a base de dados calcula as agregações, como soma e CONTAGEM em nós de trabalho e combina os resultados numa resposta final.

Eis uma consulta para a contagem de solicitações da web por minuto, juntamente com algumas estatísticas.
Tente executá-lo no psql e observe os resultados.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="rolling-up-data"></a>Sem interrupção dos dados

A consulta anterior funciona bem nos estágios iniciais, mas o desempenho degrada consoante o dimensionamento de dados. Mesmo com o processamento distribuído, é mais rápido computar previamente os dados que ao recalcular isso repetidamente.

Podemos garantir que o nosso dashboard permanece rápida, acumular regularmente os dados não processados para uma tabela de agregação. Pode experimentar com a duração de agregação. Utilizamos uma tabela de agregação de minuto, mas poderia dividir dados em 60, 5 ou 15 minutos em vez disso.

Para executar este rollup mais facilmente, vamos colocá-la numa função de plpgsql. Executar estes comandos psql para criar o `rollup_http_request` função.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

Com a nossa função in-loco, executá-lo para os dados de agregação:

```sql
SELECT rollup_http_request();
```

E com nossos dados num formulário pré-agregados pode consultar a tabela de agregação para obter o mesmo relatório como anteriormente. Execute a seguinte consulta:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Expirar dados antigos

Os rollups efetuam consultas mais rápido, mas ainda temos de expirar dados antigos para evitar os custos de armazenamento não vinculado. Decida quanto pretende manter os dados para cada granularidade e utilize consultas padrão para eliminar dados expirados. No exemplo a seguir, decidimos de manter os dados não processados de um dia e por minuto agregações durante um mês:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

Na produção, pode encapsular essas consultas numa função e chamá-lo a cada minuto numa tarefa cron.

## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, criou os recursos do Azure num grupo de servidor. Se não espera precisa destes recursos no futuro, elimine o grupo de servidor. Prima a *eliminar* botão no *descrição geral* página para o seu grupo de servidor. Quando lhe for pedido numa página de pop-up, confirme o nome do grupo de servidor e clique no último *eliminar* botão.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu como aprovisionar um grupo de servidores de grande escala (Citus). Conectados a ele com psql, criou um esquema e dados distribuídos. Aprendeu a consultar dados de ambos no formato não processado, regularmente agregar dados, consultar as tabelas agregadas e expirar os dados antigos.

Em seguida, saiba mais sobre os conceitos de hiperescala.
> [!div class="nextstepaction"]
> [Tipos de nós de Hiperescala](https://aka.ms/hyperscale-concepts)