---
title: Criar tabelas distribuídas-Citus (hiperescala)-banco de dados do Azure para PostgreSQL
description: Início rápido para criar e consultar tabelas distribuídas no banco de dados do Azure para PostgreSQL Citus (hiperescala).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: 02e009e6fff2e717693d1579d409199ab179d941
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973425"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-in-the-azure-portal"></a>Início rápido: criar um banco de dados do Azure para PostgreSQL-Citus (hiperescala) no portal do Azure

A Base de Dados do Azure para o PostgreSQL é um serviço gerido com o qual pode executar, gerir e dimensionar as bases de dados de elevada disponibilidade do PostgreSQL na cloud. Este guia de início rápido mostra como criar um grupo de servidores do banco de dados do Azure para PostgreSQL-Citus (hiperescala) usando o portal do Azure. Você explorará dados distribuídos: fragmentando tabelas entre nós, ingerindo dados de exemplo e executando consultas que são executadas em vários nós.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Criar e distribuir tabelas

Uma vez conectado ao nó de coordenador de hiperescala usando psql, você pode concluir algumas tarefas básicas.

Em servidores de hiperescala, há três tipos de tabelas:

- Tabelas distribuídas ou fragmentadas (distribuídas para ajudar a dimensionar o desempenho e a paralelização)
- Tabelas de referência (várias cópias mantidas)
- Tabelas locais (geralmente usadas para tabelas de administração internas)

Neste guia de início rápido, nos concentraremos principalmente nas tabelas distribuídas e nos familiarizaremos com elas.

O modelo de dados com o qual vamos trabalhar é simples: dados de usuário e evento do GitHub. Os eventos incluem criação de bifurcação, confirmações git relacionadas a uma organização e muito mais.

Depois de se conectar via psql, vamos criar nossas tabelas. Na execução do console do psql:

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

O campo `payload` de `github_events` tem um tipo de dados JSONB. JSONB é o tipo de dados JSON em formato binário em Postgres. O tipo de dados torna mais fácil armazenar um esquema flexível em uma única coluna.

Postgres pode criar um índice de `GIN` nesse tipo, que indexará cada chave e valor dentro dele. Com um índice, torna-se rápido e fácil consultar a carga com várias condições. Vamos continuar e criar alguns índices antes de carregarmos nossos dados. Em psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Em seguida, pegaremos essas tabelas postgres no nó de coordenador e dizem para a subescala para fragmentá-las entre os trabalhadores. Para fazer isso, executaremos uma consulta para cada tabela especificando a chave para fragmentá-la. No exemplo atual, nós fragmentaremos a tabela de eventos e de usuários em `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Estamos prontos para carregar dados. No psql ainda, saia do Shell para baixar os arquivos:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Em seguida, carregue os dados dos arquivos nas tabelas distribuídas:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Executar consultas

Agora é hora da parte divertida, realmente executando algumas consultas. Vamos começar com um `count (*)` simples para ver a quantidade de dados que carregamos:

```sql
SELECT count(*) from github_events;
```

Isso funcionou bem. Voltaremos a esse tipo de agregação em um pouco, mas, por enquanto, vamos dar uma olhada em algumas outras consultas. Na coluna JSONB `payload`, há uma boa quantidade de dados, mas varia de acordo com o tipo de evento. `PushEvent` eventos contêm um tamanho que inclui o número de confirmações distintas para o envio por push. Podemos usá-lo para encontrar o número total de confirmações por hora:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Até agora, as consultas envolveram o GitHub\_eventos exclusivamente, mas podemos combinar essas informações com os usuários do GitHub\_. Como nós Fragmentamos os usuários e os eventos no mesmo identificador (`user_id`), as linhas de ambas as tabelas com IDs de usuário correspondentes serão [colocalizadas](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) nos mesmos nós de banco de dados e poderão ser facilmente Unidas.

Se entrarmos em `user_id`, a hiperescala poderá enviar por push a execução de junção para dentro de fragmentos para execução em paralelo em nós de trabalho. Por exemplo, vamos encontrar os usuários que criaram o maior número de repositórios:

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

Nas etapas anteriores, você criou recursos do Azure em um grupo de servidores. Se você não espera precisar desses recursos no futuro, exclua o grupo de servidores. Pressione o botão **excluir** na página **visão geral** do seu grupo de servidores. Quando solicitado em uma página pop-up, confirme o nome do grupo de servidores e clique no botão **excluir** final.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a provisionar um grupo de servidores de hiperescala (Citus). Você se conectou a ele com psql, criou um esquema e distribuiu dados.

Em seguida, siga um tutorial para criar aplicativos multilocatário escalonáveis.
> [!div class="nextstepaction"]
> [Criar um banco de dados de vários locatários](https://aka.ms/hyperscale-tutorial-multi-tenant)
