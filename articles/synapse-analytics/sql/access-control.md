---
title: Gerir o acesso a espaços de trabalho, dados e oleodutos
description: Aprenda a gerir o controlo de acesso a espaços de trabalho, dados e oleodutos num espaço de trabalho Azure Synapse Analytics (pré-visualização).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 7c6de17e4ed4f5666554908e9b13fb9e299b027a
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682065"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Gerir o acesso a espaços de trabalho, dados e oleodutos

Aprenda a gerir o controlo de acesso a espaços de trabalho, dados e oleodutos num espaço de trabalho Azure Synapse Analytics (pré-visualização).

> [!NOTE]
> Para a AG, o Azure RBAC será mais desenvolvido através da introdução de funções Azure específicas da Sinapse

## <a name="access-control-for-workspace"></a>Controlo de Acesso para Espaço de Trabalho

Para uma implantação de produção num espaço de trabalho Azure Synapse, sugerimos organizar o seu ambiente para facilitar a oferta de utilizadores e administradores.

> [!NOTE]
> A abordagem aqui adotada é criar vários grupos de segurança e, em seguida, configurar o espaço de trabalho para usá-los de forma consistente. Após a criação dos grupos, um administrador só precisa de gerir a adesão dentro dos grupos de segurança.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>Passo 1: Criar grupos de segurança com nomes seguindo este padrão

1. Criar grupo de segurança chamado `Synapse_WORKSPACENAME_Users`
2. Criar grupo de segurança chamado `Synapse_WORKSPACENAME_Admins`
3. Adicione `Synapse_WORKSPACENAME_Admins` a `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> Saiba como criar um grupo de segurança [neste artigo.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)
>
> Saiba como adicionar um grupo de segurança a outro grupo de segurança [neste artigo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-membership-azure-portal).
>
> WORKSPACENAME - deve substituir esta peça pelo nome real do espaço de trabalho.

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>Passo 2: Preparar a conta ADLS Gen2 padrão

Quando a provisionou o seu espaço de trabalho, teve de escolher uma conta [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) e um contentor para o sistema de ficheiros para o espaço de trabalho utilizar.

1. Abra o [portal do Azure](https://portal.azure.com)
2. Navegue para a conta Azure Data Lake Storage Gen2
3. Navegue para o contentor (sistema de ficheiros) que escolheu para o espaço de trabalho Azure Synapse
4. Selecione **Controlo de Acesso (IAM)**
5. Atribuir as seguintes funções:
   1. **Papel** do leitor para:  `Synapse_WORKSPACENAME_Users`
   2. **Papel do Proprietário de Dados blob de armazenamento** para:  `Synapse_WORKSPACENAME_Admins`
   3. **Papel de contribuinte de dados blob de armazenamento** para: `Synapse_WORKSPACENAME_Users`
   4. **Papel do Proprietário de Dados blob de armazenamento** para:  `WORKSPACENAME`

> [!NOTE]
> WORKSPACENAME - deve substituir esta peça pelo nome real do espaço de trabalho.

### <a name="step-3-configure-the-workspace-admin-list"></a>Passo 3: Configurar a lista de administradores do espaço de trabalho

1. Vá ao [ **Azure Synapse Web UI**](https://web.azuresynapse.net)
2. Ir para **gerir o** controlo de acesso   >  **Security**  >  **à** segurança
3. Selecione **Add Admin** e selecione `Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>Passo 4: Configurar o acesso a administradores sql para o espaço de trabalho

1. Aceda ao [Portal do Azure](https://portal.azure.com)
2. Navegue para o seu espaço de trabalho
3. Ir para **Definições**  >  **Administrador Ativo Diretório**
4. Selecione **Definir Administrador**
5. Selecione `Synapse_WORKSPACENAME_Admins`
6. Escolha **Selecionar**
7. Selecione **Guardar**

> [!NOTE]
> WORKSPACENAME - deve substituir esta peça pelo nome real do espaço de trabalho.

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>Passo 5: Adicionar e remover utilizadores e administradores a grupos de segurança

1. Adicione utilizadores que precisam de acesso administrativo a `Synapse_WORKSPACENAME_Admins`
2. Adicione todos os outros utilizadores a `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> Saiba como adicionar o utilizador como membro a um grupo de segurança [neste artigo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal)
> 
> WORKSPACENAME - deve substituir esta peça pelo nome real do espaço de trabalho.

## <a name="access-control-to-data"></a>Controlo de Acesso aos dados

O controlo de acesso aos dados subjacentes é dividido em três partes:

- Acesso de avião de dados à conta de armazenamento (já configurado acima no passo 2)
- Acesso de avião de dados às Bases de Dados SQL (tanto para piscinas SQL dedicadas como para piscinas SQL sem servidor)
- Criação de uma credencial para bases de dados de piscinas SQL sem servidor sobre a conta de armazenamento

## <a name="access-control-to-sql-databases"></a>Controlo de acesso às Bases de Dados SQL

> [!TIP]
> Os passos abaixo precisam de ser executados para **cada** base de dados SQL para conceder o acesso do utilizador a todas as bases de dados SQL, exceto na [secção Desincê-lo nível de servidor,](#server-level-permission) onde pode atribuir ao utilizador uma função sysadmin.

### <a name="serverless-sql-pool"></a>Conjunto de SQL sem servidor

Nesta secção, pode encontrar exemplos sobre como dar ao utilizador uma permissão para uma determinada base de dados ou permissões completas do servidor.

#### <a name="database-level-permission"></a>Permissão de nível de base de dados

Para conceder acesso a um utilizador a uma **única** base de dados de piscinas SQL sem servidor, siga os passos neste exemplo:

1. Criar LOGIN

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Criar UTILIZADOR

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. Adicionar UTILIZADOR aos membros da função especificada

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

> [!NOTE]
> Substitua o pseudónimo por pseudónimo do utilizador que pretende dar acesso e domínio ao domínio da empresa que está a utilizar.

#### <a name="server-level-permission"></a>Permissão de nível do servidor

Para conceder acesso total a um utilizador a **todas as** bases de dados de piscinas SQL sem servidor, siga o passo neste exemplo:

```sql
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE  sysadmin  ADD MEMBER [alias@domain.com];
```

### <a name="dedicated-sql-pool"></a>Conjunto de SQL dedicado

Para conceder acesso a um utilizador a uma **única** base de dados SQL, siga estes passos:

1. Crie o utilizador na base de dados executando o seguinte comando direcionando a base de dados desejada no seletor de contexto (dropdown para selecionar bases de dados):

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Conceda ao utilizador uma função para aceder à base de dados:

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* e *db_datawriter* podem trabalhar para permissões de leitura/escrita se a concessão *de db_owner* permissão não for desejada.
> Para que um utilizador da Spark leia e escreva diretamente da Spark dentro/a partir de uma piscina SQL dedicada, é necessária *db_owner* permissão.

Depois de criar os utilizadores, valide que pode consultar a conta de armazenamento utilizando a piscina SQL sem servidor.

## <a name="access-control-to-workspace-pipeline-runs"></a>O controlo de acesso ao gasoduto workspace funciona

### <a name="workspace-managed-identity"></a>Identidade gerida pelo espaço de trabalho

> [!IMPORTANT]
> Para executar com sucesso oleodutos que incluam conjuntos de dados ou atividades que refiram um pool de SQL dedicado, a identidade do espaço de trabalho precisa de ter acesso diretamente à piscina SQL.

Executar os seguintes comandos em cada piscina DE SQL dedicada para permitir que a identidade gerida pelo espaço de trabalho funcione para executar gasodutos na base de dados de piscinas SQL:

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

Esta permissão pode ser removida executando o seguinte script na mesma piscina SQL:

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>Próximos passos

Para uma visão geral da identidade gerida pelo espaço de trabalho da Synapse, consulte a identidade gerida pelo espaço de [trabalho Azure Synapse.](../security/synapse-workspace-managed-identity.md) Para saber mais sobre os principais da base de dados, consulte [os principais.](https://msdn.microsoft.com/library/ms181127.aspx) Informações adicionais sobre funções de base de dados podem ser encontradas no artigo [de funções de Base de Dados.](https://msdn.microsoft.com/library/ms189121.aspx)
