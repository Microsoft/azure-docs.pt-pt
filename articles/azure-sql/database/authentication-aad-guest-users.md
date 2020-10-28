---
title: Criar utilizadores convidados Azure AD
description: Como criar utilizadores convidados Azure AD e defini-los como administrador AZure AD sem usar grupos AD Azure em Azure SQL Database, Azure SQL Managed Instance e Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: 7a4d9fb9f803a497e84fa189d9a89c2d9097bb70
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675054"
---
# <a name="create-azure-ad-guest-users-and-set-as-an-azure-ad-admin"></a>Criar utilizadores convidados do Azure AD e definir como administrador do Azure AD

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Este artigo está em **pré-visualização pública.**

Os utilizadores convidados em Azure Ative Directory (Azure AD) são utilizadores que foram importados para o atual AD Azure de outros Diretórios Azure Ative, ou fora dele. Por exemplo, os utilizadores convidados podem incluir utilizadores de outros Diretórios Azure Ative, ou de contas como *\@ outlook.com* , *\@ hotmail.com* , *\@ live.com* , ou *\@ gmail.com* . Este artigo irá demonstrar como criar um utilizador convidado AZure AD, e definir esse utilizador como um administrador AD Azure para o servidor lógico Azure SQL, sem precisar de ter esse utilizador convidado a fazer parte de um grupo dentro do Azure AD.

## <a name="feature-description"></a>Descrição da funcionalidade

Esta funcionalidade levanta a limitação atual que apenas permite aos utilizadores convidados ligarem-se à Base de Dados Azure SQL, SQL Managed Instance ou Azure Synapse Analytics quando são membros de um grupo criado em Azure AD. O grupo precisava de ser mapeado manualmente para um utilizador utilizando a declaração [DO UTILIZADOR CREATE (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) numa determinada base de dados. Uma vez criado um utilizador de base de dados para o grupo Azure AD que contém o utilizador convidado, o utilizador convidado pode entrar na base de dados utilizando o Azure Ative Directy com autenticação MFA. Como parte desta **pré-visualização pública,** os utilizadores convidados podem ser criados e conectar-se diretamente à Base de Dados SQL, SQL Managed Instance ou Azure Synapse sem a exigência de adicioná-los a um grupo AD Azure primeiro, e depois criar um utilizador de base de dados para esse grupo AZure AD.

Como parte desta funcionalidade, você também tem a capacidade de definir o utilizador azure AD convidado diretamente como um administrador AD para o servidor lógico Azure SQL. A funcionalidade existente onde o utilizador convidado pode fazer parte de um grupo AZure AD, e esse grupo pode então ser definido como o administrador AD AD Azure para o servidor lógico Azure SQL não é impactado. Os utilizadores convidados na base de dados que fazem parte de um grupo AZure AD também não são afetados por esta mudança.

Para obter mais informações sobre o suporte existente para utilizadores convidados que utilizem grupos AD Azure, consulte [a autenticação do Diretório Ativo Azure multi-factor](authentication-mfa-ssms-overview.md).

## <a name="prerequisite"></a>Pré-requisito

- O módulo [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) ou superior é necessário quando se utiliza o PowerShell para definir um utilizador convidado como um administrador AD Azure para o servidor lógico Azure SQL.

## <a name="create-database-user-for-azure-ad-guest-user"></a>Criar utilizador de base de dados para o utilizador convidado Azure AD 

Siga estes passos para criar um utilizador de base de dados utilizando um utilizador convidado Azure AD.

### <a name="create-guest-user-in-sql-database-and-azure-synapse"></a>Criar o utilizador convidado na Base de Dados SQL e Azure Synapse

1. Certifique-se de que o utilizador convidado (por exemplo, `user1@gmail.com` ) já está adicionado ao seu AD Azure e foi definido um administrador AD AD Azure para o servidor de base de dados. É necessário ter um administrador AdD Azure para a autenticação do Azure Ative Directory.

1. Ligue-se à base de dados SQL como administrador AD AZure ou utilizador Azure AD com permissões SQL suficientes para criar utilizadores, e executar o comando abaixo na base de dados onde o utilizador convidado precisa de ser adicionado:

    ```sql
    CREATE USER [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Deve agora existir um utilizador de base de dados criado para o utilizador convidado `user1@gmail.com` .

1. Executar o comando abaixo para verificar se o utilizador da base de dados foi criado com sucesso:

    ```sql
    SELECT * FROM sys.database_principals
    ```

1. Desconecte e assine na base de dados como utilizador convidado `user1@gmail.com` utilizando o [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) utilizando o método de autenticação **Azure Ative Directory - Universal com MFA** . Para obter mais informações, consulte [a autenticação do Diretório Ativo Azure multi-factor](authentication-mfa-ssms-overview.md).

### <a name="create-guest-user-in-sql-managed-instance"></a>Criar o utilizador convidado em SQL Managed Instance

> [!NOTE]
> SQL Managed Instance suporta logins para utilizadores de Azure AD, bem como utilizadores de bases de dados Azure AD contidos. Os passos abaixo mostram como criar um login e um utilizador para um utilizador convidado AZure AD em SQL Managed Instance. Também pode optar por criar um [utilizador de base de dados contido](/sql/relational-databases/security/contained-database-users-making-your-database-portable) em SQL Managed Instance utilizando o método no utilizador de [configuração do utilizador convidado na secção SQL Database e Azure Synapse.](#create-guest-user-in-sql-database-and-azure-synapse)

1. Certifique-se de que o utilizador convidado (por exemplo, `user1@gmail.com` ) já está adicionado ao seu AD Azure e foi definido um administrador AD AD Azure para o servidor SQL Managed Instance. É necessário ter um administrador AdD Azure para a autenticação do Azure Ative Directory.

1. Ligue-se ao servidor SQL Managed Instance como o administrador AD AZure ou um utilizador AZure AD com permissões SQL suficientes para criar utilizadores, e executar o seguinte comando na `master` base de dados para criar um login para o utilizador convidado:

    ```sql
    CREATE LOGIN [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Deverá agora ser criado um login para o utilizador convidado `user1@gmail.com` na `master` base de dados.

1. Executar o comando abaixo para verificar se o login foi criado com sucesso:

    ```sql
    SELECT * FROM sys.server_principals
    ```

1. Execute o comando abaixo na base de dados onde o utilizador convidado precisa de ser adicionado: 

    ```sql
    CREATE USER [user1@gmail.com] FROM LOGIN [user1@gmail.com]
    ```

1. Deve agora existir um utilizador de base de dados criado para o utilizador convidado `user1@gmail.com` .

1. Desconecte e assine na base de dados como utilizador convidado `user1@gmail.com` utilizando o [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) utilizando o método de autenticação **Azure Ative Directory - Universal com MFA** . Para obter mais informações, consulte [a autenticação do Diretório Ativo Azure multi-factor](authentication-mfa-ssms-overview.md).

## <a name="setting-a-guest-user-as-an-azure-ad-admin"></a>Definição de um utilizador convidado como administrador AD Azure

Siga estes passos para definir um utilizador convidado Azure AD como administrador AD Azure para o servidor lógico SQL.

### <a name="set-azure-ad-admin-for-sql-database-and-azure-synapse"></a>Definir administrador AD AD para base de dados SQL e Azure Synapse

1. Certifique-se de que o utilizador convidado (por exemplo, `user1@gmail.com` ) já está adicionado ao seu AD Azure.

1. Executar o seguinte comando PowerShell para adicionar o utilizador convidado como administrador AD AZure para o seu servidor lógico Azure SQL:

    - Substitua `<ResourceGroupName>` o nome do Grupo de Recursos Azure que contém o servidor lógico Azure SQL.
    - Substitua `<ServerName>` o nome do servidor lógico Azure SQL. Se o nome do seu servidor for `myserver.database.windows.net` , `<Server Name>` substitua-o por `myserver` .
    - `<DisplayNameOfGuestUser>`Substitua-o pelo nome de utilizador do seu convidado.

    ```powershell
    Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -ServerName <ServerName> -DisplayName <DisplayNameOfGuestUser>
    ```

    Também pode utilizar o [ad-administrador do servidor Azure CLI](/cli/azure/sql/server/ad-admin) para definir o utilizador convidado como um administrador AD AD Azure para o seu servidor lógico Azure SQL.

### <a name="set-azure-ad-admin-for-sql-managed-instance"></a>Definir administrador AD Ad para SQL Caso Gerido

1. Certifique-se de que o utilizador convidado (por exemplo, `user1@gmail.com` ) já está adicionado ao seu AD Azure.

1. Vá ao [portal Azure](https://portal.azure.com)e vá ao seu recurso **Azure Ative Directory.** Under **Manage** , vá ao painel **de Utilizadores.** Selecione o seu utilizador convidado e grave o `Object ID` . 

1. Executar o seguinte comando PowerShell para adicionar o utilizador convidado como administrador AD Azure para a sua SQL Managed Instance:

    - Substitua `<ResourceGroupName>` o nome do Grupo de Recursos Azure que contém a SqL Managed Instance.
    - Substitua `<ManagedInstanceName>` o nome SQL Managed Instance.
    - `<DisplayNameOfGuestUser>`Substitua-o pelo nome de utilizador do seu convidado.
    - `<AADObjectIDOfGuestUser>`Substitua-a `Object ID` mais cedo.

    ```powershell
    Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -InstanceName "<ManagedInstanceName>" -DisplayName <DisplayNameOfGuestUser> -ObjectId <AADObjectIDOfGuestUser>
    ```

    Também pode utilizar o comando Azure CLI [az sql mi ad-admin](/cli/azure/sql/mi/ad-admin) para definir o utilizador convidado como um administrador AD AD Azure para a sua SQL Managed Instance.

## <a name="limitations"></a>Limitações

Existe uma limitação no portal Azure que impede a seleção de um utilizador convidado Azure AD como administrador AD Azure para Azure Managed Instance. Para contas de hóspedes fora do seu AD Azure como *\@ outlook.com* , *\@ hotmail.com* , *\@ live.com* , ou *\@ gmail.com* , o seletor de administração AD mostra estas contas, mas estão acinzentadas e não podem ser selecionadas. Utilize os [comandos PowerShell ou CLI](#setting-a-guest-user-as-an-azure-ad-admin) acima listados para definir a administração AD Azure. Em alternativa, um grupo AD Azure contendo o utilizador convidado pode ser definido como o administrador AD AZure para a Sql Managed Instance.

Esta funcionalidade será ativada para SQL Managed Instance antes da Disponibilidade Geral desta funcionalidade.

## <a name="next-steps"></a>Passos seguintes

- [Configure e gere a autenticação AD AD com Azure SQL](authentication-aad-configure.md)
- [Utilização de autenticação de diretório ativo Azure multi-factor](authentication-mfa-ssms-overview.md)
- [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql)