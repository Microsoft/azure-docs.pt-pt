---
title: 'Quickstart: criar um grupo de servidores - Hyperscale (Citus) - Base de Dados Azure para PostgreSQL'
description: Quickstart para criar e consultar tabelas distribuídas na Base de Dados Azure para a Hiperescala Pós-SQL (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/17/2020
ms.openlocfilehash: d4925b8ecb7768f1f49b2c971f7234d58339c056
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92481113"
---
# <a name="quickstart-create-a-hyperscale-citus-server-group-in-the-azure-portal"></a>Quickstart: criar um grupo de servidores Hyperscale (Citus) no portal Azure

A Base de Dados do Azure para o PostgreSQL é um serviço gerido com o qual pode executar, gerir e dimensionar as bases de dados de elevada disponibilidade do PostgreSQL na cloud. Este Quickstart mostra-lhe como criar um grupo de servidores Azure Database for PostgreSQL - Hyperscale (Citus) utilizando o portal Azure. Você explorará dados distribuídos: tabelas de fragmentos através de nós, ingerir dados de amostras e consultas de execução que executam em múltiplos nós.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Criar e distribuir tabelas

Uma vez ligado ao nó coordenador de hiperescala utilizando o psql, pode completar algumas tarefas básicas.

Dentro dos servidores Hyperscale (Citus) existem três tipos de tabelas:

- Tabelas distribuídas ou estantes de fragmentos (espalhadas para ajudar a escalar para desempenho e paralelização)
- Tabelas de referência (várias cópias mantidas)
- Tabelas locais (frequentemente utilizadas para tabelas de administração interna)

Neste arranque rápido, vamos focar-nos principalmente em mesas distribuídas e familiarizarmo-nos com elas.

O modelo de dados com que vamos trabalhar é simples: dados de utilizador e eventos do GitHub. Os eventos incluem criação de garfos, git compromete-se relacionado com uma organização, e muito mais.

Depois de ligares via PSQL, vamos criar as nossas mesas. Na corrida da consola psql:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

O `payload` campo de tem um tipo de `github_events` dados JSONB. JSONB é o tipo de dados JSON na forma binária em Postgres. O tipo de dados facilita a armazenação de um esquema flexível numa única coluna.

Postgres podem criar um `GIN` índice neste tipo, que irá indexar cada chave e valor dentro dele. Com um índice, torna-se rápido e fácil de consultar a carga útil com várias condições. Vamos em frente e criar um par de índices antes de carregar os nossos dados. Em psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Em seguida, pegaremos as mesas postgres no nó coordenador e diremos à Hyperscale (Citus) para os apanhar pelos trabalhadores. Para tal, vamos fazer uma consulta para cada mesa especificando a chave para o enromer. No exemplo atual, vamos resolver tanto os eventos como a mesa dos utilizadores `user_id` em:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

Estamos prontos para carregar dados. No psql ainda, descasca para descarregar os ficheiros:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Em seguida, carregue os dados dos ficheiros nas tabelas distribuídas:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Executar consultas

Agora é hora da parte divertida, na verdade, fazer algumas consultas. Vamos começar com um simples `count (*)` para ver quantos dados carregamos:

```sql
SELECT count(*) from github_events;
```

Funcionou bem. Voltaremos a esse tipo de agregação daqui a pouco, mas por agora vamos ver mais algumas consultas. Dentro da coluna JSONB `payload` há um bom pedaço de dados, mas varia com base no tipo de evento. `PushEvent` os eventos contêm um tamanho que inclui o número de compromissos distintos para o impulso. Podemos usá-lo para encontrar o número total de compromissos por hora:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Até agora, as consultas envolveram exclusivamente os \_ eventos github, mas podemos combinar esta informação com os utilizadores do \_ Github. Uma vez que nós encaderramos tanto os utilizadores como os eventos no mesmo identificador ( `user_id` ), as filas de ambas as tabelas com iDs de utilizador correspondentes serão [colocamos](concepts-hyperscale-colocation.md) nos mesmos nós da base de dados e podem ser facilmente unidas.

Se nos `user_id` juntarmos, a Hyperscale (Citus) pode empurrar a execução da junta para baixo em fragmentos para execução em paralelo nos nós dos trabalhadores. Por exemplo, vamos encontrar os utilizadores que criaram o maior número de repositórios:

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Limpar os recursos

Nos passos anteriores, criou recursos Azure num grupo de servidores. Se não espera precisar destes recursos no futuro, elimine o grupo de servidores. Prima o botão **Eliminar** na página **'Vista Geral'** para o seu grupo de servidor. Quando solicitado numa página pop-up, confirme o nome do grupo de servidor e clique no botão final **eliminar.**

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a providenciar um grupo de servidores Hyperscale (Citus). Ligou-se a ele com o PSQL, criou um esquema e distribuiu dados.

- Siga um tutorial para [construir aplicações escaláveis de multi-inquilinos](./tutorial-design-database-hyperscale-multi-tenant.md)
- Determine o melhor [tamanho inicial](howto-hyperscale-scaling.md#picking-initial-size) para o seu grupo de servidor