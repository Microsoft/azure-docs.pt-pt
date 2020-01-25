---
title: 'Tutorial: Designar um dashboard em tempo real - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL'
description: Este tutorial mostra como criar, povoar e consultar mesas distribuídas na Base de Dados Azure para hiperescala PostgreSQL (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: f4eeb646de8b68c2c8d30586d0c75cece5317e40
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716323"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Tutorial: Desenhe um dashboard de análise em tempo real utilizando a Base de Dados Azure para PostgreSQL – Hyperscale (Citus)

Neste tutorial, utiliza a Base de Dados Azure para PostgreSQL - Hyperscale (Citus) para aprender a:

> [!div class="checklist"]
> * Criar um grupo do servidor Hyperscale (Citus)
> * Use a utilidade psql para criar um esquema
> * Mesas de caco em todos os nódosos
> * Gerar dados de exemplo
> * Realizar rollups
> * Consulta de dados crus e agregados
> * Dados expirados

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Use a utilidade psql para criar um esquema

Uma vez ligado à Base de Dados Azure para PostgreSQL - Hiperescala (Citus) utilizando o psql, pode completar algumas tarefas básicas. Este tutorial acompanha-o através da ingestão de dados de tráfego a partir da análise web, em seguida, rolando os dados para fornecer dashboards em tempo real com base em esses dados.

Vamos criar uma tabela que consumirá todos os nossos dados de tráfego web crus. Executar os seguintes comandos no terminal psql:

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

Também vamos criar uma mesa que vai manter os nossos agregados por minuto, e uma mesa que mantém a posição da nossa última rollup. Executar os seguintes comandos no PSQL também:

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

Pode ver as mesas recém-criadas na lista de tabelas agora com este comando psql:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Mesas de caco em todos os nódosos

Uma implantação de hiperescala armazena linhas de mesa em diferentes nódosos com base no valor de uma coluna designada pelo utilizador. Esta "coluna de distribuição" marca como os dados são espalhados por nós.

Vamos definir a coluna de distribuição para ser o local\_id, a chave do fragmento. No psql, executar estas funções:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Gerar dados de exemplo

Agora o nosso grupo de servidores deve estar pronto para ingerir alguns dados. Podemos executar o seguinte localmente a partir da nossa ligação `psql` para inserir continuamente dados.

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

A consulta insere aproximadamente oito linhas a cada segundo. As filas são armazenadas em diferentes nós de trabalhador, tal como dirigido pela coluna de distribuição, `site_id`.

   > [!NOTE]
   > Deixe a consulta de geração de dados em execução, e abra uma segunda ligação psql para os restantes comandos neste tutorial.
   >

## <a name="query"></a>Consulta

A opção de hospedagem de hiperescala permite que vários nódosos processem consultas paralelas à velocidade. Por exemplo, a base de dados calcula agregados como SUM e COUNT em nós de trabalhadores, e combina os resultados numa resposta final.

Aqui está uma consulta para contar pedidos web por minuto, juntamente com algumas estatísticas.
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

## <a name="rolling-up-data"></a>Elaboração de dados

A consulta anterior funciona bem nas fases iniciais, mas o seu desempenho degrada-se à medida que os seus dados se degradam. Mesmo com o processamento distribuído, é mais rápido pré-calcular os dados do que recalculá-lo repetidamente.

Podemos garantir que o nosso dashboard se mantenha rápido, rolando regularmente os dados brutos numa tabela agregada. Pode experimentar a duração da agregação. Usamos uma tabela de agregação por minuto, mas pode quebrar os dados em 5, 15 ou 60 minutos.

Para executar este roll-up mais facilmente, vamos colocá-lo em uma função plpgsql. Executar estes comandos em psql para criar a função `rollup_http_request`.

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

Com a nossa função no lugar, execute-os para arregaçar os dados:

```sql
SELECT rollup_http_request();
```

E com os nossos dados de forma pré-agregada podemos consultar a mesa de rollup para obter o mesmo relatório de antes. Executar a seguinte consulta:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Expirando dados antigos

Os rollups fazem consultas mais rápidas, mas ainda precisamos de expirar dados antigos para evitar custos de armazenamento ilimitados. Decida quanto tempo pretende manter os dados para cada granularidade e use consultas padrão para eliminar dados expirados. No exemplo seguinte, decidimos manter dados brutos por um dia, e agregações por minuto por um mês:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

Na produção, pode embrulhar estas consultas numa função e chamá-las a cada minuto num trabalho de compadrio.

## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, criou os recursos do Azure num grupo de servidores. Se não espera precisar destes recursos no futuro, elimine o grupo de servidores. Prima o botão *Eliminar* na página *'Visão Geral'* para o seu grupo de servidores. Quando solicitado numa página pop-up, confirme o nome do grupo do servidor e clique no *botão* Eliminar final.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a fornecer um grupo de servidores de Hiperescala (Citus). Ligaste-lhe com o PSQL, criaste um esquema e distribuíste dados. Aprendeu a consultar dados na forma bruta, agregar regularmente esses dados, consultar as tabelas agregadas e expirar dados antigos.

Em seguida, aprenda sobre os conceitos de hiperescala.
> [!div class="nextstepaction"]
> [Tipos de nó de hiperescala](https://aka.ms/hyperscale-concepts)
