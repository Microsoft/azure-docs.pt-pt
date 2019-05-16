---
title: Base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização) início rápido
description: Início rápido para criar e consultar distribuído tabelas na base de dados do Azure para PostgreSQL Hiperescala (Citus) (pré-visualização).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: efc3801ab03f739761a41bec754f975fe43dcd8e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65757519"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Início rápido: Criar uma base de dados do Azure para PostgreSQL - Hiperescala (Citus) (pré-visualização) no portal do Azure

A Base de Dados do Azure para o PostgreSQL é um serviço gerido com o qual pode executar, gerir e dimensionar as bases de dados de elevada disponibilidade do PostgreSQL na cloud. Este guia de introdução mostra-lhe como criar uma base de dados do Azure para PostgreSQL - Hiperescala (Citus) (pré-visualização) grupo de servidores com o portal do Azure. Vou explorar dados distribuídos: tabelas de fragmentação em todos os nós, ao ingerir dados de exemplo e execução de consultas que são executadas em vários nós.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Criar e distribuir tabelas

Depois de ligado para o nó coordenador de hiperescala com psql, pode concluir algumas tarefas básicas.

Numa Hiperescala lá os servidores são três tipos de tabelas:

- Tabelas distribuídas ou em partição horizontal (espalhadas para ajudar a dimensionar para o desempenho e paralelização)
- Tabelas de referência (várias cópias mantidas)
- Tabelas locais (muitas vezes, utilizadas para tabelas de administrador interno)

Neste início rápido, focaremos principalmente em tabelas distribuídas e familiarizar-se com eles.

O modelo de dados, vamos trabalhar com é simples: dados de eventos e de utilizador do GitHub. Eventos incluem a criação do fork, confirmações do git relacionados com uma organização e muito mais.

Depois de se ligar através do psql, vamos criar nossa tabelas. Na consola do psql executar:

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

O `payload` campo `github_events` tem um tipo de dados JSONB. JSONB é o tipo de dados JSON no formato binário em Postgres. O tipo de dados torna mais fácil de armazenar um esquema flexível numa única coluna.

Pode criar Postgres um `GIN` índice neste tipo, que será indexar cada chave e valor dentro do mesmo. Com um índice, torna rápido e fácil de consultar o payload com várias condições. Vamos continuar e criar alguns dos índices antes de que carregarmos nossos dados. No psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Em seguida vamos tomar essas tabelas Postgres no nó coordenador e dizer de Hiperescala para a partição horizontal-los entre as funções de trabalho. Para fazer isso, vamos executar uma consulta para cada tabela especificar a chave de partição horizontal-la nas. No exemplo atual, veremos os eventos de usuários e tabela de partições horizontais `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Estamos prontos para carregar dados. No psql ainda assim, shell para transferir os ficheiros:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Em seguida, carregue os dados dos arquivos para as tabelas distribuídas:

```sql
\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Executar consultas

Agora chegou a hora para a diversão parte, na verdade, execução de algumas consultas. Vamos começar com um simples `count (*)` para ver a quantidade de dados foi carregado:

```sql
SELECT count(*) from github_events;
```

Funcionou muito bem. Vamos voltar para esse tipo de agregação daqui a pouco, mas por agora vamos examinar algumas outras consultas. Dentro do JSONB `payload` coluna aqui é um pouco de dados, mas ele varia com base no tipo de evento. `PushEvent` eventos contêm um tamanho que inclui o número de consolidações distintas para o envio. Podemos utilizar para localizar o número total de consolidações por hora:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Até agora, as consultas envolvesse o github\_eventos exclusivamente, mas podemos combinar essas informações com o github\_utilizadores. Desde em partição horizontal que os utilizadores e os eventos do mesmo identificador (`user_id`), as linhas de ambas as tabelas com o ID de utilizador correspondentes serão [colocalizados](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) no mesmo nós de base de dados e podem facilmente ser associadas.

Se, Junte-se no `user_id`, Hiperescala pode empurrar a execução de associação em partições horizontais para execução em paralelo em nós de trabalho. Por exemplo, vamos descobrir quando os utilizadores que criou ao maior número de repositórios:

```sql
SELECT login, count(*)
FROM github_events ge
JOIN github_users gu
ON ge.user_id = gu.user_id
WHERE event_type = 'CreateEvent' AND
      payload @> '{"ref_type": "repository"}'
GROUP BY login
ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, criou os recursos do Azure num grupo de servidor. Se não espera precisa destes recursos no futuro, elimine o grupo de servidor. Prima a **eliminar** botão no **descrição geral** página para o seu grupo de servidor. Quando lhe for pedido numa página de pop-up, confirme o nome do grupo de servidor e clique no último **eliminar** botão.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu como aprovisionar um grupo de servidores de grande escala (Citus). Conectados a ele com psql, criou um esquema e dados distribuídos.

Em seguida, siga um tutorial para criar aplicações escaláveis de multi-inquilinos.
> [!div class="nextstepaction"]
> [Criar uma base de dados do multi-inquilino](https://aka.ms/hyperscale-tutorial-multi-tenant)
