---
title: Escale a base de dados Azure para o grupo de servidores de hiperescala PostgreSQL
description: Escale a base de dados Azure para o grupo de servidores de hiperescala PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 17bdae658c7095c44a7ae9f30fd85a6c45bf1546
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96779979"
---
# <a name="scale-out-your-azure-arc-enabled-postgresql-hyperscale-server-group-by-adding-more-worker-nodes"></a>Dimensione o seu Azure Arc ativado pelo grupo de servidores pós-escala PostgreSQL adicionando mais nóns de trabalhadores
Este documento explica como escalar um grupo de servidores de hiperescala PósgreSQL ativado por Azure Arc. Fá-lo levando-te a um cenário. **Se não quiser correr o cenário e quiser apenas ler sobre como escalar, salte para o parágrafo [Escala para fora](#scale-out)**.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-started"></a>Introdução
Se já estiver familiarizado com o modelo de escala de Azure Arc ativado pela Hyperscale PostgreSQL ou base de dados Azure para a hiperescala pós-alta (Citus), pode saltar este parágrafo. Caso contrário, recomenda-se que comece por ler sobre este modelo de escala na página de documentação da Base de Dados Azure para a Hiperescala Pós-SQL (Citus). A Azure Database for PostgreSQL Hyperscale (Citus) é a mesma tecnologia que é hospedada como um serviço em Azure (Platform As A Service também conhecido como PAAS) em vez de ser oferecido como parte de Azure Arc habilitado serviços de dados:
- [Nós e tabelas](../../postgresql/concepts-hyperscale-nodes.md)
- [Determinar o tipo de aplicação](../../postgresql/concepts-hyperscale-app-type.md)
- [Escolher uma coluna de distribuição](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
- [Colocalização de tabela](../../postgresql/concepts-hyperscale-colocation.md)
- [Distribuir e modificar tabelas](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
- [Desenhe uma base de dados multi-inquilinos](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
- [Desenhe um painel de análise em tempo real](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> \* Nos documentos acima, salte as secções **Iniciar sessão No portal Azure,**& **Criar uma Base de Dados Azure para PostgreSQL - Hiperescala (Citus)**. Implemente os passos restantes na sua implantação do Arco Azure. Estas secções são específicas da Base de Dados Azure para a Hiperescala Pós-SQL (Citus) oferecida como um serviço PaaS na nuvem Azure, mas as outras partes dos documentos são diretamente aplicáveis à sua Hiperescala Pós-Altura pós-SQL ativada pelo Arco Azure.

## <a name="scenario"></a>Scenario
Este cenário refere-se ao grupo de servidores de hiperescala PostgreSQL que foi criado como um exemplo na documentação do [grupo de servidores de hiperescala PostgreSQL ativada por Um Arco Azure.](create-postgresql-hyperscale-server-group.md)

### <a name="load-test-data"></a>Carregar os dados de teste
O cenário utiliza uma amostra de dados do GitHub publicamente disponíveis, disponíveis no site do [Citus Data](https://www.citusdata.com/) (Os Dados do Citus fazem parte da Microsoft).

#### <a name="connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Ligue-se ao seu grupo de servidores de hiperescala pós-escala Azure Arc

##### <a name="list-the-connection-information"></a>Listar as informações de ligação
Ligue-se ao seu grupo de servidores de hiperescala PostgreSQL ativado, obtendo primeiro a informação de ligação: O formato geral deste comando é
```console
azdata arc postgres endpoint list -n <server name>
```
Por exemplo:
```console
azdata arc postgres endpoint list -n postgres01
```

Exemplo de saída:

```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

##### <a name="connect-with-the-client-tool-of-your-choice"></a>Conecte-se com a ferramenta cliente à sua escolha.

Faça a seguinte consulta para verificar se tem atualmente dois (ou mais nós de trabalhadores de hiperescala), cada um correspondente a uma cápsula Kubernetes:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(2 rows)
```

#### <a name="create-a-sample-schema"></a>Criar um esquema de amostra
Criar duas tabelas executando a seguinte consulta:

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

JSONB é o tipo de dados JSON na forma binária em PostgreSQL. Armazena um esquema flexível numa única coluna e com o PostgreSQL. O esquema terá um índice de GIN para indexar todas as chaves e valor dentro dele. Com um índice de GIN, torna-se rápido e fácil de consultar com várias condições diretamente nessa carga útil. Então vamos em frente e criar um par de índices antes de carregar os nossos dados:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Para as tabelas padrão de cace, execute uma consulta para cada mesa. Especifique a mesa que queremos escassear, e a chave que queremos escasseá-la. Vamos resolver os eventos e a mesa dos utilizadores no user_id:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

#### <a name="load-sample-data"></a>Carregar os dados de exemplo
Carregue os dados com COPY ... DO PROGRAMA:

```sql
COPY github_users FROM PROGRAM 'curl "https://examples.citusdata.com/users.csv"' WITH ( FORMAT CSV );
COPY github_events FROM PROGRAM 'curl "https://examples.citusdata.com/events.csv"' WITH ( FORMAT CSV );
```

#### <a name="query-the-data"></a>Consultar os dados
E agora meça quanto tempo uma simples consulta leva com dois nóns:

```sql
SELECT COUNT(*) FROM github_events;
```
Tome nota do tempo de execução da consulta.


## <a name="scale-out"></a>Aumentar horizontalmente
O formato geral do comando de escala é:
```console
azdata arc postgres server edit -n <server group name> -w <target number of worker nodes>
```

> [!CAUTION]
> A versão de pré-visualização não permite a redução horizontal. Por exemplo, ainda não pode reduzir o número de nós de trabalho. Se precisar de o fazer, tem de extrair/fazer cópia de segurança dos dados, remover o grupo de servidores, criar um novo grupo de servidores com menos nós de trabalho e, em seguida, importar os dados.

Neste exemplo, aumentamos o número de nós de trabalhador de 2 para 4, executando o seguinte comando:

```console
azdata arc postgres server edit -n postgres01 -w 4
```

Ao adicionar nós, verá um estado pendente para o grupo de servidores. Por exemplo:
```console
azdata arc postgres server list
```

```console
Name        State          Workers
----------  -------------  ---------
postgres01  Pending 4/5    4
```

Uma vez que os nós estão disponíveis, o Reequilíbrio de Fragmento de Hiperescala funciona automaticamente e redistribui os dados para os novos nós. A operação de escala é uma operação online. Enquanto os nós são adicionados e os dados são redistribuídos através dos nós, os dados permanecem disponíveis para consultas.

### <a name="verify-the-new-shape-of-the-server-group-optional"></a>Verifique a nova forma do grupo de servidores (opcional)
Utilize qualquer um dos métodos abaixo para verificar se o grupo de servidor está agora a usar os nós de trabalho adicionais que adicionou.

#### <a name="with-azdata"></a>Com azdata:
Execute o comando:
```console
azdata arc postgres server list
```

Devolve a lista de grupos de servidores criados no seu espaço de nome e indica o seu número de nós de trabalhadores. Por exemplo:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

#### <a name="with-kubectl"></a>Com kubectl:
Execute o comando:
```console
kubectl get postgresql-12
```

Devolve a lista de grupos de servidores criados no seu espaço de nome e indica o seu número de nós de trabalhadores. Por exemplo:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   4/4          10.0.0.4:31066      4d20h
```
> [!NOTE]
> Se criou um grupo de servidores da versão 11 PostgreSQL em vez de 12, executar o seguinte comando: _kubectl obter postgresql-11_

#### <a name="with-a-sql-query"></a>Com uma consulta SQL:
Conecte-se ao seu grupo de servidor com a ferramenta cliente à sua escolha e execute a seguinte consulta:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      3 |       3 | pg1-3.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      4 |       4 | pg1-4.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(4 rows)
```

## <a name="return-to-the-scenario"></a>Voltar ao cenário

Se quiser comparar o tempo de execução da consulta de contagem selecionada com o conjunto de dados da amostra, utilize a mesma consulta de contagem. Pode ser usado em todos os quatro nós operários, sem alterações na declaração SQL.

```sql
SELECT COUNT(*) FROM github_events;
```
Reparem no tempo de execução.


> [!NOTE]
> Dependendo do seu ambiente - por exemplo, se implementou o seu grupo de servidor de teste com `kubeadm` um único VM de nó - poderá ver uma melhoria modesta no tempo de execução. Para obter uma melhor ideia do tipo de melhoria de desempenho que pode alcançar com a Azure Arc ativada em Escala pós-SQL, assista aos seguintes vídeos curtos:
>* [HTAP de alto desempenho com hiperescala Azure PostgreSQL (Citus)](https://www.youtube.com/watch?v=W_3e07nGFxY)
>* [Aplicações HTAP de construção com & hiperescala pós-alta de Azure (Citus)](https://www.youtube.com/watch?v=YDT8_riLLs0)


## <a name="next-steps"></a>Passos seguintes

- Leia sobre como escalar para [cima e para baixo (memória, vCores) o seu Azure Arc ativou o grupo de servidores pós-escala PostgreSQL](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- Leia sobre como definir parâmetros de servidor no seu Azure Arc ativado grupo de servidores de hiperescala postgreSQL
- Leia os conceitos e guias de como fazer a Base de Dados Azure para a Hiperescala Pós-SQL para distribuir os seus dados através de vários nós de hiperescala postgreSQL e para beneficiar de toda a potência da Base de Dados Azure para a Escala de Hiperescala PostgresQL. :
    * [Nós e tabelas](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinar o tipo de aplicação](../../postgresql/concepts-hyperscale-app-type.md)
    * [Escolher uma coluna de distribuição](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocalização de tabela](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuir e modificar tabelas](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Desenhe uma base de dados multi-inquilinos](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Desenhe um painel de análise em tempo real](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

 > \* Nos documentos acima, salte as secções **Iniciar sessão No portal Azure,**& **Criar uma Base de Dados Azure para PostgreSQL - Hiperescala (Citus)**. Implemente os passos restantes na sua implantação do Arco Azure. Estas secções são específicas da Base de Dados Azure para a Hiperescala Pós-SQL (Citus) oferecida como um serviço PaaS na nuvem Azure, mas as outras partes dos documentos são diretamente aplicáveis à sua Hiperescala Pós-Altura pós-SQL ativada pelo Arco Azure.

- [Configuração de armazenamento e conceitos de armazenamento de Kubernetes](storage-configuration.md)
- [Modelo de recurso Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
