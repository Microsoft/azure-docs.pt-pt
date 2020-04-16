---
title: Gerir o acesso a espaços de trabalho, dados e oleodutos
description: Saiba como gerir o controlo de acesso a espaços de trabalho, dados e oleodutos num espaço de trabalho Azure Synapse Analytics (pré-visualização).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 89d2105ab080309639c4341072c3f5f36608dfce
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424770"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Gerir o acesso a espaços de trabalho, dados e oleodutos

Saiba como gerir o controlo de acesso a espaços de trabalho, dados e oleodutos num espaço de trabalho Azure Synapse Analytics (pré-visualização).

> [!NOTE]
> Para a GA, o RBAC será mais desenvolvido através da introdução de funções Azure RBAC específicas do Synapse

## <a name="access-control-for-workspace"></a>Controlo de Acesso para Espaço de Trabalho

Para uma implantação de produção num espaço de trabalho Azure Synapse, sugerimos organizar o seu ambiente para facilitar a disponibilização de utilizadores e administradores.

> [!NOTE]
> A abordagem aqui adotada é criar vários grupos de segurança e, em seguida, configurar o espaço de trabalho para usá-los de forma consistente. Após a criação dos grupos, um administrador só precisa de gerir a adesão dentro dos grupos de segurança.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>Passo 1: Configurar grupos de segurança com nomes que seguem este padrão

1. Criar grupo de segurança chamado`Synapse_WORKSPACENAME_Users`
2. Criar grupo de segurança chamado`Synapse_WORKSPACENAME_Admins`
3. Adicione `Synapse_WORKSPACENAME_Admins` a `ProjectSynapse_WORKSPACENAME_Users`

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>Passo 2: Preparar a conta Predefinida ADLS Gen2

Quando disponibilizou o seu espaço de trabalho, teve de escolher uma conta ADLSGEN2 e um recipiente para o sistema de ficheiros para o espaço de trabalho utilizar.

1. Abra o [portal do Azure](https://portal.azure.com)
2. Navegue para a conta ADLSGEN2
3. Navegue para contentor (sistema de ficheiros) escolhido para o espaço de trabalho Azure Synapse
4. Clique no **Controlo de Acesso (IAM)**
5. Atribuir as seguintes funções:
   1. **Papel do leitor:**`Synapse_WORKSPACENAME_Users`
   2. Papel de proprietário de **dados blob de armazenamento:**`Synapse_WORKSPACENAME_Admins`
   3. Papel de **Colaborador de Dados blob** de armazenamento:`Synapse_WORKSPACENAME_Users`
   4. Papel de proprietário de **dados blob de armazenamento:**`WORKSPACENAME`
  
### <a name="step-3-configure-the-workspace-admin-list"></a>Passo 3: Configure a lista de administradores do espaço de trabalho

1. Ir à [ **Azure Synapse Web UI**](https://web.azuresynapse.net)
2. Ir para gerir**o controlo** **de**  > acesso à**segurança** > 
3. Clique **em Adicionar Administrador,** e selecione`Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>Passo 4: Configure Acesso a Administrador sQL para o espaço de trabalho

1. Aceda ao [Portal do Azure](https://portal.azure.com)
2. Navegue para o seu espaço de trabalho
3. Ir ao Administrador ativo de**Diretórios De** **Definições** > 
4. Clique **em Administração de Conjunto**
5. Selecione `Synapse_WORKSPACENAME_Admins`
6. clique em **Selecionar**
7. clique em **Guardar**

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>Passo 5: Adicionar e remover utilizadores e administradores a grupos de segurança

1. Adicione utilizadores que precisam de acesso administrativo a`Synapse_WORKSPACENAME_Admins`
2. Adicione todos os outros utilizadores a`Synapse_WORKSPACENAME_Users`

## <a name="access-control-to-data"></a>Controlo de Acesso a dados

O controlo de acesso aos dados subjacentes é dividido em três partes:

- Acesso data-plane à conta de armazenamento (já configurado acima no passo 2)
- Acesso de data-plane às bases de dados SQL (tanto para piscinas SQL como sQL a pedido)
- Criação de uma credencial para bases de dados on-demand SQL sobre a conta de armazenamento

## <a name="access-control-to-sql-databases"></a>Controlo de acesso às Bases de Dados SQL

> [!TIP]
> Os passos abaixo devem ser executados para **cada** base de dados SQL para dar acesso ao utilizador a todas as bases de dados SQL.

### <a name="sql-on-demand"></a>SQL a pedido

Para conceder acesso a um utilizador a uma **única** base de dados on-demand SQL, siga os passos neste exemplo:

1. Criar login

    ```sql
    use master
    go
    CREATE LOGIN [John.Thomas@microsoft.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Criar USER

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER john FROM LOGIN [John.Thomas@microsoft.com];
    ```

3. Adicionar USER aos membros da função especificada

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member john -- Type USER name from step 2
    ```

### <a name="sql-pools"></a>Piscinas SQL

Para conceder acesso a um utilizador a uma **única** Base de Dados SQL, siga estes passos:

1. Crie o utilizador na base de dados executando o seguinte comando direcionando a base de dados desejada no seletor de contexto (dropdown para selecionar bases de dados):

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Conceda ao utilizador um papel para aceder à base de dados:

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* e *db_datawriter* podem trabalhar para permissões de leitura/escrita se a concessão *de db_owner* permissão não for desejada.
> Para que um utilizador da Spark leia e escreva diretamente a partir de Spark para/a partir de uma piscina SQL, é necessária *db_owner* permissão.

Depois de criar os utilizadores, valide que a SQL a pedido pode consultar a conta de armazenamento:

- Executar o seguinte comando direcionado para a base de dados **principal** da SQL a pedido:

    ```sql
    CREATE CREDENTIAL [https://<storageaccountname>.dfs.core.windows.net]
    WITH IDENTITY='User Identity';
    ```

## <a name="access-control-to-workspace-pipeline-runs"></a>Controlo de acesso a gasodutos de espaço de trabalho funciona

### <a name="workspace-managed-identity"></a>Identidade gerida pelo espaço de trabalho

> [!IMPORTANT]
> Para executar com sucesso gasodutos que incluam conjuntos de dados ou atividades que referenciam um pool SQL, a identidade do espaço de trabalho precisa de ter acesso diretamente ao pool SQL.

Executar os seguintes comandos em cada piscina SQL para permitir que a identidade gerida pelo espaço de trabalho execute os gasodutos na base de dados de piscinas SQL:

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

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral do acesso e controlo em Synapse SQL, consulte o controlo de [acesso Synapse SQL](../sql/access-control.md). Para saber mais sobre os principais da base de dados, consulte [os diretores.](https://msdn.microsoft.com/library/ms181127.aspx) Informações adicionais sobre funções de base de dados podem ser encontradas no artigo de funções da [Base de Dados.](https://msdn.microsoft.com/library/ms189121.aspx)
