---
title: Projete um painel em tempo real com o banco de dados do Azure para PostgreSQL – tutorial de Citus (hiperescala)
description: Este tutorial mostra como criar, popular e consultar tabelas distribuídas no banco de dados do Azure para PostgreSQL Citus (hiperescala).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 32487d65397a96d9e96ae3bf3476eed23ddb8adc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73482881"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Tutorial: criar um painel de análise em tempo real usando o banco de dados do Azure para PostgreSQL – Citus (hiperescala)

Neste tutorial, você usa o banco de dados do Azure para PostgreSQL-Citus (hiperescala) para aprender a:

> [!div class="checklist"]
> * Criar um grupo do servidor Hyperscale (Citus)
> * Usar o utilitário psql para criar um esquema
> * Tabelas de fragmento entre nós
> * Gerar dados de exemplo
> * Executar rollups
> * Consultar dados brutos e agregados
> * Expirar dados

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Usar o utilitário psql para criar um esquema

Uma vez conectado ao banco de dados do Azure para PostgreSQL-Citus (hiperescala) usando psql, você pode concluir algumas tarefas básicas. Este tutorial orienta a ingestão de dados de tráfego do Web Analytics e, em seguida, a distribuição dos dados para fornecer painéis em tempo real com base nesses dados.

Vamos criar uma tabela que consumirá todos os nossos dados brutos de tráfego da Web. Execute os seguintes comandos no terminal psql:

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

Também vamos criar uma tabela que conterá as agregações por minuto e uma tabela que mantém a posição de nosso último ROLLUP. Execute os seguintes comandos no psql também:

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

Você pode ver as tabelas recém-criadas na lista de tabelas agora com este comando psql:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Tabelas de fragmento entre nós

Uma implantação de hiperescala armazena linhas de tabela em nós diferentes com base no valor de uma coluna designada pelo usuário. Essa "coluna de distribuição" marca como os dados são fragmentados entre os nós.

Vamos definir a coluna de distribuição como ID do site\_, a chave de fragmentação. No psql, execute estas funções:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Gerar dados de exemplo

Agora, nosso grupo de servidores deve estar pronto para ingerir alguns dados. Podemos executar o seguinte localmente de nossa conexão de `psql` para inserir dados continuamente.

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

A consulta insere aproximadamente oito linhas a cada segundo. As linhas são armazenadas em nós de trabalho diferentes, conforme direcionado pela coluna de distribuição, `site_id`.

   > [!NOTE]
   > Deixe a consulta de geração de dados em execução e abra uma segunda conexão psql para os comandos restantes neste tutorial.
   >

## <a name="query"></a>Consulta

A opção de Hospedagem de hiperescala permite que vários nós processem consultas em paralelo para velocidade. Por exemplo, o banco de dados calcula agregações como SUM e COUNT em nós de trabalho e combina os resultados em uma resposta final.

Aqui está uma consulta para contar solicitações da Web por minuto juntamente com algumas estatísticas.
Tente executá-lo em psql e observe os resultados.

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

## <a name="rolling-up-data"></a>Acumulando dados

A consulta anterior funciona bem nos estágios iniciais, mas seu desempenho diminui à medida que seus dados são dimensionados. Mesmo com o processamento distribuído, é mais rápido calcular previamente os dados do que recalculá-los repetidamente.

Podemos garantir que nosso painel permaneça rápido ao acumular regularmente os dados brutos em uma tabela de agregação. Você pode experimentar a duração da agregação. Usamos uma tabela de agregação por minuto, mas você poderia dividir os dados em 5, 15 ou 60 minutos em vez disso.

Para executar esse acúmulo mais facilmente, vamos colocá-lo em uma função Plpgsql. Execute estes comandos em psql para criar a função `rollup_http_request`.

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

Com nossa função em vigor, execute-a para acumular os dados:

```sql
SELECT rollup_http_request();
```

E, com nossos dados em uma forma previamente agregada, podemos consultar a tabela de rollup para obter o mesmo relatório anterior. Execute a seguinte consulta:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Dados antigos expirados

Os rollups tornam as consultas mais rápidas, mas ainda precisamos expirar dados antigos para evitar custos de armazenamento não associados. Decida por quanto tempo deseja manter os dados para cada granularidade e use as consultas padrão para excluir os dados expirados. No exemplo a seguir, decidimos manter dados brutos por um dia e agregações por minuto para um mês:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

Em produção, você pode encapsular essas consultas em uma função e chamá-las a cada minuto em um trabalho cron.

## <a name="clean-up-resources"></a>Limpar recursos

Nas etapas anteriores, você criou recursos do Azure em um grupo de servidores. Se você não espera precisar desses recursos no futuro, exclua o grupo de servidores. Pressione o botão *excluir* na página *visão geral* do seu grupo de servidores. Quando solicitado em uma página pop-up, confirme o nome do grupo de servidores e clique no botão *excluir* final.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a provisionar um grupo de servidores de hiperescala (Citus). Você se conectou a ele com psql, criou um esquema e distribuiu dados. Você aprendeu a consultar dados na forma bruta, agregar regularmente esses dados, consultar as tabelas agregadas e expirar dados antigos.

Em seguida, saiba mais sobre os conceitos de hiperescala.
> [!div class="nextstepaction"]
> [Tipos de nó de hiperescala](https://aka.ms/hyperscale-concepts)