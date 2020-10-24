---
title: Criar utilizadores de AD Azure usando os principais serviços
description: Este tutorial acompanha-o através da criação de um utilizador AZure AD com aplicações AD Azure (principais serviços) na Base de Dados Azure SQL e Azure Synapse Analytics
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/21/2020
ms.openlocfilehash: a666acbcd2aed168bd1d871c0ef0fb8c3205fd05
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92479153"
---
# <a name="tutorial-create-azure-ad-users-using-azure-ad-applications"></a>Tutorial: Criar utilizadores de AD Azure usando aplicações AD Azure

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

> [!NOTE]
> Este artigo está em **pré-visualização pública.** Para mais informações, consulte [o diretor de serviço do Azure Ative Directory com a Azure SQL.](authentication-aad-service-principal.md) Este artigo utilizará a Base de Dados Azure SQL para demonstrar os passos tutoriais necessários, mas pode ser igualmente aplicado ao [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Este artigo leva-o através do processo de criação de utilizadores Azure AD na Base de Dados Azure SQL, utilizando os principais serviços Azure (aplicações Azure AD). Esta funcionalidade já existe em Azure SQL Managed Instance, mas está agora a ser introduzida na Azure SQL Database e na Azure Synapse Analytics. Para suportar este cenário, uma Identidade AD Azure deve ser gerada e atribuída ao servidor lógico Azure SQL.

Para obter mais informações sobre a autenticação Azure AD para Azure SQL, consulte o artigo Autenticação do [Diretório Ativo Azure](authentication-aad-overview.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Atribua uma identidade ao servidor lógico Azure SQL
> - Atribuir permissão aos Leitores de Diretório para a identidade lógica do servidor SQL
> - Criar um principal serviço (uma aplicação AD Azure) em Azure AD
> - Criar um utilizador principal de serviço na Base de Dados Azure SQL
> - Criar um utilizador AD Azure diferente na Base de Dados SQL utilizando um utilizador principal de serviço Azure

## <a name="prerequisites"></a>Pré-requisitos

- Uma base de [dados Azure SQL](single-database-create-quickstart.md) existente ou uma implementação [Azure Synapse Analytics.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Assumimos que tem uma base de dados SQL a funcionar para este tutorial.
- Acesso a um Diretório Ativo Azure já existente.
- O módulo [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) ou superior é necessário ao utilizar o PowerShell para configurar uma aplicação AD AD Azure individual como administrador Azure AD para Azure SQL. Certifique-se de que está atualizado para o módulo mais recente.

## <a name="assign-an-identity-to-the-azure-sql-logical-server"></a>Atribua uma identidade ao servidor lógico Azure SQL

1. Ligue-se ao seu Diretório Ativo Azure. Terá de encontrar a sua identificação do inquilino. Isto pode ser encontrado indo para o [portal Azure,](https://portal.azure.com)e indo para o seu recurso **Azure Ative Directory.** No **painel de visão** geral, você deve ver o seu **ID do inquilino.** Execute o seguinte comando do PowerShell:

    - `<TenantId>`Substitua-o pelo seu **ID do inquilino.**

    ```powershell
    Connect-AzAccount -Tenant <TenantId>
    ```

    Grave o `TenantId` uso futuro neste tutorial.

1. Gere uma identidade AD Azure para o servidor lógico Azure SQL. Execute o seguinte comando PowerShell:

    - Substitua `<resource group>` e `<server name>` os seus recursos. Se o nome do seu servidor for `myserver.database.windows.net` , `<server name>` substitua-o por `myserver` .

    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <server name> -AssignIdentity
    ```

    Para mais informações, consulte o comando [Set-AzSqlServer.](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)

    > [!IMPORTANT]
    > Se for criada uma Identidade AD Azure para o servidor lógico Azure SQL, a permissão [**dos Leitores**](../../active-directory/roles/permissions-reference.md#directory-readers) de Diretório deve ser concedida à identidade. Vamos passar por este passo na secção seguinte. **Não** salte este passo pois a autenticação AZure AD deixará de funcionar.

    - Se usou o comando [New-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) com o parâmetro `AssignIdentity` para uma nova criação de servidor SQL no passado, terá de executar o comando [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) posteriormente como um comando separado para ativar esta propriedade no tecido Azure.

1. Verifique se a identidade do servidor foi atribuída com sucesso. Execute o seguinte comando PowerShell:

    - Substitua `<resource group>` e `<server name>` os seus recursos. Se o nome do seu servidor for `myserver.database.windows.net` , `<server name>` substitua-o por `myserver` .
    
    ```powershell
    $xyz = Get-AzSqlServer  -ResourceGroupName <resource group> -ServerName <server name>
    $xyz.identity
    ```

    A sua saída deve mostrar-lhe, `PrincipalId` `Type` e . `TenantId` . A identidade atribuída é `PrincipalId` a.

1. Também pode verificar a identidade indo ao [portal Azure](https://portal.azure.com).

    - Sob o recurso **Azure Ative Directory,** aceda às **aplicações da Enterprise**. Digite o nome do seu servidor lógico SQL. Verá que tem um **ID de objeto** ligado ao recurso.
    
    :::image type="content" source="media/authentication-aad-service-principals-tutorial/enterprise-applications-object-id.png" alt-text="objeto-id":::

## <a name="assign-directory-readers-permission-to-the-sql-logical-server-identity"></a>Atribuir permissão aos Leitores de Diretório para a identidade lógica do servidor SQL

Para permitir que a identidade atribuída à Azure AD funcione corretamente para o Azure SQL, a permissão AD AD `Directory Readers` Azure deve ser concedida à identidade do servidor.

Para conceder esta permissão necessária, execute o seguinte script.

> [!NOTE] 
> Este script deve ser executado por um AD Azure `Global Administrator` ou um `Privileged Roles Administrator` .
>
> Na **pré-visualização pública,** pode atribuir o `Directory Readers` papel a um grupo em Azure AD. Os proprietários do grupo podem então adicionar a identidade gerida como membro deste grupo, o que contornaria a necessidade de um `Global Administrator` ou para conceder o `Privileged Roles Administrator` `Directory Readers` papel. Para obter mais informações sobre esta funcionalidade, consulte o [papel dos Leitores de Diretório no Diretório Ativo Azure para a Azure SQL](authentication-aad-directory-readers-role.md).

- `<TenantId>`Substitua-a `TenantId` mais cedo.
- Substitua `<server name>` pelo nome do servidor lógico SQL. Se o nome do seu servidor for `myserver.database.windows.net` , `<server name>` substitua-o por `myserver` .

```powershell
# This script grants Azure "Directory Readers" permission to a Service Principal representing the Azure SQL logical server
# It can be executed only by a "Global Administrator" or "Privileged Roles Administrator" type of user.
# To check if the "Directory Readers" permission was granted, execute this script again

Import-Module AzureAD
Connect-AzureAD -TenantId "<TenantId>"    #Enter your actual TenantId
$AssignIdentityName = "<server name>"     #Enter Azure SQL logical server name
 
# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}
 
# Get service principal for managed instance
$roleMember = Get-AzureADServicePrincipal -SearchString $AssignIdentityName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$($AssignIdentityName)', make sure that AssignIdentityName parameter was entered correctly."
    exit
}

if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$($AssignIdentityName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}
 
# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
 
if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($AssignIdentityName)' to 'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
    Write-Output "'$($AssignIdentityName)' service principal added to 'Directory Readers' role'..."
 
    #Write-Output "Dumping service principal '$($AssignIdentityName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
} else {
    Write-Output "Service principal '$($AssignIdentityName)' is already member of 'Directory Readers' role'."
}
```

> [!NOTE]
> A saída deste script acima indicará se a permissão dos Leitores do Diretório foi concedida à identidade. Pode re-executar o script se não tiver a certeza se a permissão foi concedida.

Para obter uma abordagem semelhante sobre como definir a permissão dos Leitores de **Diretório** para a SQL Managed Instance, consulte [o administrador da Provision Ad (SQL Managed Instance)](authentication-aad-configure.md#powershell).

## <a name="create-a-service-principal-an-azure-ad-application-in-azure-ad"></a>Criar um principal serviço (uma aplicação AD Azure) em Azure AD

1. Siga o guia aqui para registar a [sua aplicação e definir permissões.](active-directory-interactive-connect-azure-sql-db.md#register-your-app-and-set-permissions)

    Certifique-se de adicionar as **permissões de Aplicação,** bem como as **permissões delegadas.**

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-apps.png" alt-text="objeto-id":::

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-app-registration-api-permissions.png" alt-text="objeto-id":::

2. Também terá de criar um segredo de cliente para se inscrever. Siga o guia aqui para [fazer o upload de um certificado ou criar um segredo para iniciar sessão.](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)

3. Grave o seguinte a partir do seu registo de inscrição. Deve estar disponível no painel de **visão** geral:
    - **ID da Aplicação**
    - **ID do inquilino** - Este deve ser o mesmo de antes

Neste tutorial, vamos usar *o AppSP* como nosso principal serviço principal, e o *myapp* como o segundo utilizador principal de serviço que será criado em Azure SQL pela *AppSP.* Terá de criar duas aplicações, *AppSP* e *myapp.*

Para obter mais informações sobre como criar uma aplicação AD Azure, consulte o artigo [Como: Usar o portal para criar uma aplicação AD AD Azure que possa aceder a recursos.](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="permissions-required-to-set-or-unset-the-azure-ad-admin"></a>Permissões necessárias para definir ou desaparasitar o administrador AD Azure

Para que o diretor de serviço desconfiem ou desinteça um administrador AD Azure para o Azure SQL, é necessária uma Autorização API adicional. A permissão da API [de aplicação.Read.All](https://docs.microsoft.com/graph/permissions-reference#application-permissions-18) Application terá de ser adicionada à sua aplicação em Azure AD.

:::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-directory-reader-all-permissions.png" alt-text="objeto-id":::

O diretor de serviço também necessitará da função [**de contribuinte do SQL Server**](../../role-based-access-control/built-in-roles.md#sql-server-contributor) para a Base de Dados SQL, ou da [**função de contribuinte de instância gerida sql**](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) para a sql Managed Instance.

> [!NOTE]
> Embora a Azure AD Graph API esteja a ser depreciada, o **Diretório.Reader.Toda a** permissão ainda se aplica a este tutorial. A API do Microsoft Graph não se aplica a este tutorial.

## <a name="create-the-service-principal-user-in-azure-sql-database"></a>Criar o utilizador principal do serviço na Base de Dados Azure SQL

Uma vez criado um principal de serviço em Azure AD, crie o utilizador na Base de Dados SQL. Terá de se ligar à sua Base de Dados SQL com um login válido com permissões para criar utilizadores na base de dados.

1. Crie o *AppSP* do utilizador na Base de Dados SQL utilizando o seguinte comando T-SQL:

    ```sql
    CREATE USER [AppSP] FROM EXTERNAL PROVIDER
    GO
    ```

2. Conceder `db_owner` permissão ao *AppSP,* que permite ao utilizador criar outros utilizadores AZure AD na base de dados.

    ```sql
    EXEC sp_addrolemember 'db_owner', [AppSP]
    GO
    ```

    Para mais informações, consulte [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

    Em alternativa, `ALTER ANY USER` a permissão pode ser concedida em vez de dar o `db_owner` papel. Isto permitirá ao diretor de serviço adicionar outros utilizadores AZure AD.

    ```sql
    GRANT ALTER ANY USER TO [AppSp]
    GO
    ```

    > [!NOTE]
    > A definição acima não é necessária quando *o AppSP* é definido como um administrador AD Azure para o servidor. Para definir o principal de serviço como um administrador AD para o servidor lógico SQL, pode utilizar os comandos Azure portal, PowerShell ou Azure CLI. Para obter mais informações, consulte [o administrador AD Azure (SQL Database)](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse).

## <a name="create-an-azure-ad-user-in-sql-database-using-an-azure-ad-service-principal"></a>Criar um utilizador AZURE AD na Base de Dados SQL utilizando um responsável de serviço AZure AD

> [!IMPORTANT]
> O diretor de serviço utilizado para iniciar sessão na SQL Database deve ter um segredo de cliente. Se não tiver um, siga o passo 2 da [Create a principal de serviço (uma aplicação AD Azure) em Azure AD](#create-a-service-principal-an-azure-ad-application-in-azure-ad). Este segredo do cliente precisa de ser adicionado como um parâmetro de entrada no script abaixo.

1. Utilize o seguinte script para criar um *myapp* principal de serviço AZure AD utilizando o serviço principal *AppSP*.

    - `<TenantId>`Substitua-a `TenantId` mais cedo.
    - `<ClientId>`Substitua-a `ClientId` mais cedo.
    - `<ClientSecret>`Substitua-o pelo segredo do seu cliente criado anteriormente.
    - Substitua `<server name>` pelo nome do servidor lógico SQL. Se o nome do seu servidor for `myserver.database.windows.net` , `<server name>` substitua-o por `myserver` .
    - Substitua `<database name>` o nome da base de dados SQL.

    ```powershell
    # PowerShell script for creating a new SQL user called myapp using application AppSP with secret

    $tenantId = "<TenantId>"   #  tenantID (Azure Directory ID) were AppSP resides
    $clientId = "<ClientId>"   #  AppID also ClientID for AppSP     
    $clientSecret = "<ClientSecret>"   #  client secret for AppSP 
    $Resource = "https://database.windows.net/"
    
    $adalPath  = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\5.8.3"
    # To install the latest AzureRM.profile version execute  -Install-Module -Name AzureRM.profile
    $adal      = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    [System.Reflection.Assembly]::LoadFrom($adal) | Out-Null
      $resourceAppIdURI = 'https://database.windows.net/'

      # Set Authority to Azure AD Tenant
      $authority = 'https://login.windows.net/' + $tenantId

      $ClientCred = [Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential]::new($clientId, $clientSecret)
      $authContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]::new($authority)
      $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI,$ClientCred)
      $Tok = $authResult.Result.CreateAuthorizationHeader()
      $Tok=$Tok.Replace("Bearer ","")
      Write-host "token"
      $Tok
      Write-host  " "

    $SQLServerName = "<server name>"    # Azure SQL logical server name 
    Write-Host "Create SQL connectionstring"
    $conn = New-Object System.Data.SqlClient.SQLConnection 
    $DatabaseName = "<database name>"     # Azure SQL database name
    $conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30"
    $conn.AccessToken = $Tok
    
    Write-host "Connect to database and execute SQL script"
    $conn.Open() 
    $ddlstmt = 'CREATE USER [myapp] FROM EXTERNAL PROVIDER;'
    Write-host " "
    Write-host "SQL DDL command"
    $ddlstmt
    $command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn)       
    
    Write-host "results"
    $command.ExecuteNonQuery()
    $conn.Close()  
    ``` 

    Em alternativa, pode utilizar a amostra de código no blog, autenticação principal do [Serviço AD Azure para a SQL DB - Code Sample](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467). Modifique o script para executar uma declaração DDL `CREATE USER [myapp] FROM EXTERNAL PROVIDER` . O mesmo script pode ser usado para criar um utilizador Azure AD regular um grupo na Base de Dados SQL.

    > [!NOTE]
    > Se precisar de instalar o módulo AzureRM.profile, terá de abrir o PowerShell como administrador. Pode utilizar os seguintes comandos para instalar automaticamente a versão mais recente do perfil AzureRM.profile e definir `$adalpath` para o script acima:
    > 
    > ```powershell
    > Install-Module AzureRM.profile -force
    > Import-Module AzureRM.profile
    > $version = (Get-Module -Name AzureRM.profile).Version.toString()
    > $adalPath = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\${version}"
    > ```
    
2. Verifique se o *myapp* do utilizador existe na base de dados executando o seguinte comando:

    ```sql
    SELECT name, type, type_desc, CAST(CAST(sid as varbinary(16)) as uniqueidentifier) as appId from sys.database_principals WHERE name = 'myapp'
    GO
    ```

    Deverá ver um resultado semelhante a:

    ```output
    name    type    type_desc   appId
    myapp   E   EXTERNAL_USER   6d228f48-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

## <a name="next-steps"></a>Passos seguintes

- [Azure Ative Directory service principal com Azure SQL](authentication-aad-service-principal.md)
- [Quais são as identidades geridas para os recursos do Azure?](../../active-directory/managed-identities-azure-resources/overview.md)
- [Como utilizar identidades geridas para o Serviço de Aplicações e Funções Azure](../../app-service/overview-managed-identity.md)
- [Autenticação principal do Serviço AZure para a SQL DB - Amostra de Código](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467)
- [Objetos do principal de serviço e aplicação no Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md)
- [Criar um principal de serviço do Azure com o Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
- [Papel de Leitores de Diretório no Azure Ative Directory para Azure SQL](authentication-aad-directory-readers-role.md)
