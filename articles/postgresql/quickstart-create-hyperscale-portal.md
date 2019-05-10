---
title: Base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização) início rápido
description: Início rápido para criar e consultar distribuído tabelas na base de dados do Azure para PostgreSQL Hiperescala (Citus) (pré-visualização).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: 4271d94f07125a870cc4aa859b01db819d583f40
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406438"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Início rápido: Criar uma base de dados do Azure para PostgreSQL - Hiperescala (Citus) (pré-visualização) no portal do Azure

A Base de Dados do Azure para o PostgreSQL é um serviço gerido com o qual pode executar, gerir e dimensionar as bases de dados de elevada disponibilidade do PostgreSQL na cloud. Este guia de introdução mostra-lhe como criar uma base de dados do Azure para PostgreSQL - Hiperescala (Citus) (pré-visualização) grupo de servidores com o portal do Azure. Irá explorar os dados distribuídos: tabelas de fragmentação em todos os nós, ao ingerir dados de exemplo e execução de consultas que são executadas em vários nós.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Criar uma Base de Dados do Azure para o PostgreSQL

Siga estes passos para criar uma Base de Dados do Azure para o servidor PostgreSQL:
1. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.
2. Selecione **Bases de Dados**, na página **Nova**, e selecione **Base de Dados do Azure para o PostgreSQL**, na página **Bases de Dados**.
3. Para a opção de implementação, clique nas **Create** botão em **grupo de servidores de grande escala (Citus) - pré-visualização.**
4. Preencha o formulário com os detalhes do novo servidor com as seguintes informações:
   - Grupo de recursos: clique nas **criar novo** link abaixo da caixa de texto para este campo. Introduza um nome, tal como **myresourcegroup**.
   - Nome do grupo de servidor: introduza um nome exclusivo para o novo grupo de servidor, que também irá ser utilizado para um subdomínio de servidor.
   - Nome de utilizador administrador: introduza um nome de utilizador exclusivo, será utilizado mais tarde para ligar à base de dados.
   - Palavra-passe: tem de ter, pelo menos, oito carateres de comprimento e conter carateres de três das seguintes categorias – em inglês letras maiúsculas, em minúscula inglesas, números (0-9) e carateres não alfanuméricos (!, $, #, %, etc.)
   - Localização: Utilize a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.

   > [!IMPORTANT]
   > O início de sessão e a palavra-passe de administrador de servidor que especificar aqui serão necessários para iniciar sessão no servidor e nas respetivas bases de dados mais tarde neste guia de introdução. Lembre-se ou grave estas informações para utilização posterior.

5. Clique em **configurar grupo de servidor**. Deixe as definições, em que secção inalterada e clique em **guardar**.
6. Clique em **rever + criar** e, em seguida **criar** para aprovisionar o servidor. O aprovisionamento demora alguns minutos.
7. A página irá redirecionar para monitorizar a implementação. Quando o estado em direto é alterado de **sua implementação está em curso** para **sua implementação está completa**, clique nas **saídas** item de menu no lado esquerdo da página.
8. A página de saídas irá conter um nome de anfitrião do coordenador com um botão junto ao mesmo para copiar o valor para a área de transferência. Registe estas informações para uso posterior.

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

O Azure de base de dados para o PostgreSQL – Hiperescala (Citus) (pré-visualização) serviço utiliza uma firewall ao nível do servidor. Por predefinição, o firewall impede que todas as aplicações e ferramentas externas estabeleçam uma ligação para o nó coordenador e quaisquer bases de dados dentro. Podemos tem de adicionar uma regra para abrir a firewall para um intervalo de endereços IP específico.

1. Do **saídas** secção onde copiou anteriormente o nome de anfitrião do nó de coordenador, clique em volta para o **descrição geral** item de menu.

2. O nome do grupo de dimensionamento da sua implementação terá o prefixo "sg-". Está disponível na lista de recursos e clique no mesmo.

3. Clique em **Firewall** sob **segurança** no menu da esquerda.

4. Clique no link **+ Adicionar regra de firewall para o endereço IP de cliente atual**. Por último, clique a **guardar** botão.

5. Clique em **Guardar**.

   > [!NOTE]
   > O servidor PostgreSQL do Azure comunica através da porta 5432. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao servidor da Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 5432.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Ligar à base de dados com o psql no Cloud Shell

Agora, vamos utilizar o utilitário da linha de comandos [psql](https://www.postgresql.org/docs/current/app-psql.html) para ligar ao servidor da Base de Dados do Azure para PostgreSQL.
1. Inicie o Azure Cloud Shell através do ícone de terminal no painel de navegação superior.

   ![Base de Dados de Azure para o PostgreSQL – ícone do terminal do Azure Cloud Shell](./media/quickstart-create-hyperscale-portal/psql-cloud-shell.png)

2. O Azure Cloud Shell é aberto no browser, onde poderá escrever os comandos bash.

   ![Base de Dados do Azure para o PostgreSQL – Linha de Comandos Bash do Azure Shell](./media/quickstart-create-hyperscale-portal/psql-bash.png)

3. Na linha de comandos do Cloud Shell, estabeleça ligação à Base de Dados do Azure para o servidor PostgreSQL com os comandos psql. O formato seguinte é utilizado para estabelecer ligação a uma Base de Dados do Azure para o servidor PostgreSQL com o utilitário [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Por exemplo, o comando seguinte liga à base de dados predefinido chamado **citus** no servidor PostgreSQL **mydemoserver.postgres.database.azure.com** com as credenciais de acesso. Quando lhe for pedido, introduza a palavra-passe de administrador do servidor.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="create-and-distribute-tables"></a>Criar e distribuir tabelas

Depois de ligado para o nó coordenador de hiperescala com psql, pode concluir algumas tarefas básicas.

Numa Hiperescala lá os servidores são três tipos de tabelas:

- Tabelas distribuídas ou em partição horizontal (espalhadas para ajudar a dimensionar para o desempenho e paralelização)
- Tabelas de referência (várias cópias mantidas)
- Tabelas locais (muitas vezes, utilizadas para tabelas de administrador interno)

Neste início rápido concentraremos principalmente em tabelas distribuídas e familiarizar-se com eles.

O modelo de dados, vamos trabalhar com é simples: dados de eventos e de utilizador do GitHub. Eventos incluem a criação do fork, confirmações do git relacionados com uma organização e muito mais.

Assim que tiver ligado através do psql vamos crie nossa tabelas. Na consola do psql executar:

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

O `payload` campo `github_events` tem um tipo de dados JSONB. JSONB é o tipo de dados JSON no formato binário em Postgres. Isto torna mais fácil de armazenar um esquema mais flexível numa única coluna.

Pode criar Postgres um `GIN` índice neste tipo de indexação cada chave e valor dentro do mesmo. Com um índice, torna rápido e fácil de consultar o payload com várias condições. Vamos continuar e criar alguns dos índices antes de que carregarmos nossos dados. No psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Em seguida vamos tomar essas tabelas Postgres no nó coordenador e dizer de Hiperescala para a partição horizontal-los entre as funções de trabalho. Para fazer isso, vamos executar uma consulta para cada tabela especificar a chave de partição horizontal-la nas. No exemplo atual, veremos os eventos de usuários e tabela de partições horizontais `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Estamos prontos para carregar dados. Transferir os ficheiros de duas exemplo [Users. csv da](https://examples.citusdata.com/users.csv) e [events](https://examples.citusdata.com/events.csv). Depois de transferir os ficheiros, ligar à base de dados com psql, tendo cuidado para executar o psql do diretório que contém os ficheiros que transferiu. Carregar os dados com o `\copy` comando:

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
