---
title: Criar tabelas distribuídas - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Quickstart para criar e consultar tabelas distribuídas na Base de Dados Azure para Hiperescala PostgreSQL (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: 02e009e6fff2e717693d1579d409199ab179d941
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363369"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-in-the-azure-portal"></a>Quickstart: Criar uma Base de Dados Azure para PostgreSQL - Hiperescala (Citus) no portal Azure

A Base de Dados do Azure para o PostgreSQL é um serviço gerido com o qual pode executar, gerir e dimensionar as bases de dados de elevada disponibilidade do PostgreSQL na cloud. Este Quickstart mostra-lhe como criar uma Base de Dados Azure para o grupo de servidores PostgreSQL - Hyperscale (Citus) utilizando o portal Azure. Você vai explorar dados distribuídos: sharding tabelas através de nós, ingerir dados de amostra, e executar consultas que executam em vários nós.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Criar e distribuir tabelas

Uma vez ligado ao nó coordenador de hiperescala usando psql, você pode completar algumas tarefas básicas.

Dentro dos servidores de Hiperescala existem três tipos de tabelas:

- Mesas distribuídas ou esfartos (espalhadas para ajudar a escalonar para desempenho e paraparação)
- Tabelas de referência (várias cópias mantidas)
- Tabelas locais (frequentemente utilizadas para tabelas internas de administração)

Neste arranque rápido, vamos focar-nos principalmente em mesas distribuídas e familiarizarmo-nos com elas.

O modelo de dados com o qual vamos trabalhar é simples: dados de utilizador e evento do GitHub. Os eventos incluem criação de garfos, git compromete-se relacionado com uma organização, e muito mais.

Depois de ligar através do PSQL, vamos criar as nossas mesas. Na corrida da consola psql:

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

O campo `payload` de `github_events` tem um tipo de dados JSONB. JSONB é o tipo de dados JSON em forma binária em Postgres. O tipo de dados facilita a armazenação de um esquema flexível numa única coluna.

Os postgres podem criar um índice `GIN` neste tipo, que indexará todas as teclas e valores dentro dele. Com um índice, torna-se rápido e fácil consultar a carga útil com várias condições. Vamos em frente e criar alguns índices antes de carregarmos os nossos dados. No psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Em seguida, pegaremos as mesas postgres no nó coordenador e diremos hyperscale para estoirá-las através dos trabalhadores. Para tal, faremos uma consulta para cada mesa especificando a chave para escasseá-la. No exemplo atual, vamos estoirá-los tanto os eventos como os utilizadores na tabela sobre `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Estamos prontos para carregar dados. Ainda em psql, shell out para descarregar os ficheiros:

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

Agora está na hora da parte divertida, na verdade, fazer algumas perguntas. Vamos começar com uma simples `count (*)` para ver quantos dados carregamos:

```sql
SELECT count(*) from github_events;
```

Funcionou bem. Voltaremos a este tipo de agregação daqui a pouco, mas por agora vamos ver outras perguntas. Dentro da coluna de `payload` JSONB há uma boa parte dos dados, mas varia com base no tipo de evento. `PushEvent` eventos contêm um tamanho que inclui o número de compromissos distintos para o impulso. Podemos usá-lo para encontrar o número total de compromissos por hora:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Até agora as consultas envolveram o github\_eventos exclusivamente, mas podemos combinar esta informação com os utilizadores\_github. Uma vez que esfarrapamos tanto os utilizadores como os eventos no mesmo identificador (`user_id`), as filas de ambas as tabelas com IDs de utilizador correspondentes serão [coalojadas](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) nos mesmos nós de base de dados e podem facilmente ser unidas.

Se nos juntarmos a `user_id`, a Hyperscale pode empurrar a execução de união para baixo em fragmentos para execução em paralelo nos nós dos trabalhadores. Por exemplo, vamos encontrar os utilizadores que criaram o maior número de repositórios:

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

## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, criou os recursos do Azure num grupo de servidores. Se não espera precisar destes recursos no futuro, elimine o grupo de servidores. Prima o botão **Eliminar** na página **'Visão Geral'** para o seu grupo de servidores. Quando solicitado numa página pop-up, confirme o nome do grupo do servidor e clique no **botão** Eliminar final.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a fornecer um grupo de servidores de Hiperescala (Citus). Ligaste-lhe com o PSQL, criaste um esquema e distribuíste dados.

Em seguida, siga um tutorial para construir aplicações escaláveis de vários inquilinos.
> [!div class="nextstepaction"]
> [Conceber uma base de dados multi-inquilino](https://aka.ms/hyperscale-tutorial-multi-tenant)
