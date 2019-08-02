---
title: Configurar autenticação de Azure Active Directory-SQL | Microsoft Docs
description: Saiba como se conectar ao banco de dados SQL, Instância Gerenciada e SQL Data Warehouse usando a autenticação Azure Active Directory-depois de configurar o Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: b6414ac41b1bb43e3fe1470a7ae2b1358126003a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569681"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Configurar e gerenciar a autenticação de Azure Active Directory com o SQL

Este artigo mostra como criar e popular o Azure AD e, em seguida, usar o Azure AD com o [banco de dados SQL](sql-database-technical-overview.md)do azure, [instância gerenciada](sql-database-managed-instance.md)e [SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para obter uma visão geral, consulte [Azure Active Directory autenticação](sql-database-aad-authentication.md).

> [!NOTE]
> Este artigo aplica-se ao SQL Server do Azure e ao banco de dados SQL e SQL Data Warehouse bancos que são criados no SQL Server do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.
> [!IMPORTANT]  
> Não há suporte para a conexão com SQL Server em execução em uma VM do Azure usando uma conta de Azure Active Directory. Em vez disso, use uma conta de Active Directory de domínio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

## <a name="create-and-populate-an-azure-ad"></a>Criar e popular um Azure AD

Crie um Azure AD e popule-o com usuários e grupos. O Azure AD pode ser o domínio gerenciado inicial do Azure AD. O Azure AD também pode ser um Active Directory Domain Services local federado com o Azure AD.

Para obter mais informações, veja [Integrating your on-premises identities with Azure Active Directory (Integrar as identidades no local ao Azure Active Directory)](../active-directory/hybrid/whatis-hybrid-identity.md), [Add your own domain name to Azure AD (Adicionar o seu próprio nome de domínio ao Azure AD)](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure now supports federation with Windows Server Active Directory (O Microsoft Azure suporta agora a federação com o Windows Server Active Directory)](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administering your Azure AD directory (Administrar o seu diretório do Azure AD)](../active-directory/fundamentals/active-directory-administer.md), [Manage Azure AD using Windows PowerShell (Gerir o Azure AD com o Windows PowerShell)](/powershell/azure/overview) e [Hybrid Identity Required Ports and Protocols (Portas e Protocolos Necessários para a Identidade Híbrida)](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associar ou adicionar uma assinatura do Azure ao Azure Active Directory

1. Associe sua assinatura do Azure ao Azure Active Directory tornando o diretório um diretório confiável para a assinatura do Azure que hospeda o banco de dados. Para obter detalhes, consulte [como as assinaturas do Azure estão associadas ao Azure ad](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
2. Use o alternador de diretório no portal do Azure para alternar para a assinatura associada ao domínio.

   **Informações adicionais:** Cada subscrição do Azure tem uma relação de confiança com uma instância do Azure AD. Tal significa que confia nesse diretório para autenticar utilizadores, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório, mas uma subscrição apenas pode confiar num diretório. Esta relação de confiança entre uma subscrição e um diretório é diferente da relação de uma subscrição com todos os outros recursos no Azure (sites, bases de dados, e assim sucessivamente), que são mais como recursos subordinados de uma subscrição. Em caso de expiração de uma subscrição, será interrompido o acesso a esses outros recursos associados à subscrição também. No entanto, o diretório permanece no Azure e pode associar outra subscrição a esse diretório e continuar a gerir os utilizadores do diretório. Para obter mais informações sobre recursos, consulte [noções básicas sobre o acesso aos recursos no Azure](../active-directory/active-directory-b2b-admin-add-users.md). Para saber mais sobre essa relação confiável, consulte [como associar ou adicionar uma assinatura do Azure ao Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Criar um administrador do Azure AD para o SQL Server do Azure

Cada servidor do SQL Azure (que hospeda um banco de dados SQL ou SQL Data Warehouse) começa com uma única conta de administrador de servidor que é o administrador de todo o SQL Server do Azure. Um segundo administrador de SQL Server deve ser criado, ou seja, uma conta do Azure AD. Essa entidade de segurança é criada como um usuário de banco de dados independente no banco de dados mestre. Como administradores, as contas de administrador de servidor são membros da função **db_owner** em cada banco de dados de usuário e inserem cada banco de dados de usuário como o usuário **dbo** . Para obter mais informações sobre as contas de administrador do servidor, consulte Gerenciando bancos de dados [e logons no Azure SQL Database](sql-database-manage-logins.md).

Ao usar Azure Active Directory com a replicação geográfica, o administrador de Azure Active Directory deve ser configurado tanto para os servidores primários quanto para os secundários. Se um servidor não tiver um administrador de Azure Active Directory, Azure Active Directory logons e usuários receberão um erro "não é possível conectar-se" ao servidor.

> [!NOTE]
> Os usuários que não são baseados em uma conta do Azure AD (incluindo a conta de administrador do Azure SQL Server) não podem criar usuários baseados no Azure AD, pois não têm permissão para validar os usuários de banco de dados proposto com o Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Provisionar um administrador de Azure Active Directory para seu Instância Gerenciada

> [!IMPORTANT]
> Somente siga estas etapas se estiver provisionando um Instância Gerenciada. Esta operação só pode ser executada pelo administrador global/da empresa no Azure AD. As etapas a seguir descrevem o processo de conceder permissões para usuários com privilégios diferentes no diretório.

Seu Instância Gerenciada precisa de permissões para ler o Azure AD para realizar com êxito tarefas como a autenticação de usuários por meio da Associação de grupo de segurança ou da criação de novos usuários. Para que isso funcione, você precisa conceder permissões para Instância Gerenciada para ler o Azure AD. Há duas maneiras de fazer isso: do portal e do PowerShell. As etapas a seguir são os dois métodos.

1. No portal do Azure, no canto superior direito, selecione sua conexão para listar uma listagem de diretórios ativos possíveis.
2. Escolha o Active Directory correto como o Azure AD padrão.

   Esta etapa vincula a assinatura associada ao Active Directory com Instância Gerenciada certificando-se de que a mesma assinatura seja usada tanto para o Azure AD quanto para o Instância Gerenciada.
3. Navegue até Instância Gerenciada e selecione um que você deseja usar para a integração do Azure AD.

   ![AAD](./media/sql-database-aad-authentication/aad.png)

4. Selecione a faixa na parte superior da página Active Directory administrador e conceda permissão ao usuário atual. Se você estiver conectado como administrador global/da empresa no Azure AD, poderá fazer isso no portal do Azure ou usando o PowerShell com o script abaixo.

    ![conceder permissões-Portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the Managed Instance.
    # Can be executed only by a "Company Administrator" or "Global Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

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
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null)
    {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1))
    {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null)
    {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else
    {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. Depois que a operação for concluída com êxito, a notificação a seguir será exibida no canto superior direito:

    ![success](./media/sql-database-aad-authentication/success.png)

6. Agora você pode escolher o administrador do Azure AD para sua Instância Gerenciada. Para isso, na página Administrador de Active Directory, selecione **definir** comando de administrador.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Na página Administrador do AAD, procure um usuário, selecione o usuário ou grupo que será um administrador e selecione **selecionar**.

   A página de administração Active Directory mostra todos os membros e grupos de sua Active Directory. Os usuários ou grupos que estão esmaecidos não podem ser selecionados porque não têm suporte como administradores do Azure AD. Consulte a lista de administradores com suporte em [limitações e recursos do Azure ad](sql-database-aad-authentication.md#azure-ad-features-and-limitations). O RBAC (controle de acesso baseado em função) aplica-se somente ao portal do Azure e não é propagado para SQL Server.

    ![add-admin](./media/sql-database-aad-authentication/add-admin.png)

8. Na parte superior da página do administrador do Active Directory, selecione **salvar**.

    ![guardar](./media/sql-database-aad-authentication/save.png)

    O processo de alteração do administrador pode levar vários minutos. Em seguida, o novo administrador aparece na caixa Active Directory admin.

Depois de provisionar um administrador do Azure AD para seu Instância Gerenciada, você pode começar a criar entidades de segurança de servidor do Azure AD (logons) (**Visualização pública**) com a sintaxe <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Create login</a> . Para obter mais informações, consulte [instância gerenciada visão geral](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Para remover um administrador mais tarde, na parte superior da página Administrador do Active Directory, selecione **remover administrador**e, em seguida, selecione **salvar**.

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Provisionar um administrador de Azure Active Directory para seu servidor de banco de dados SQL do Azure

> [!IMPORTANT]
> Somente siga estas etapas se estiver provisionando um servidor de banco de dados SQL do Azure ou data warehouse.

Os dois procedimentos a seguir mostram como provisionar um administrador de Azure Active Directory para o SQL Server do Azure no portal do Azure e usando o PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. No [portal do Azure](https://portal.azure.com/), no canto superior direito, selecione sua conexão para listar uma listagem de diretórios ativos possíveis. Escolha o Active Directory correto como o Azure AD padrão. Esta etapa vincula o Active Directory associado à assinatura ao SQL Server do Azure, certificando-se de que a mesma assinatura seja usada tanto para o Azure AD quanto para o SQL Server. (O SQL Server do Azure pode hospedar o banco de dados SQL do Azure ou o Azure SQL Data Warehouse.) ![escolher-AD][8]

2. Na faixa à esquerda, selecione **todos os serviços**e, no tipo de filtro no **SQL Server**. Selecione **SQL Servers**.

    ![sqlservers.png](media/sql-database-aad-authentication/sqlservers.png)

    >[!NOTE]
    > Nesta página, antes de selecionar **SQL Servers**, você pode selecionar a **estrela** ao lado do nome para a categoria *favorita* e adicionar **servidores SQL** à barra de navegação à esquerda.

3. Na página **SQL Server** , selecione **Active Directory admin**.
4. Na página **administrador do Active Directory** , selecione **definir administrador**.  ![selecionar Active Directory](./media/sql-database-aad-authentication/select-active-directory.png)  

5. Na página **Adicionar Administrador** , procure um usuário, selecione o usuário ou grupo que será um administrador e selecione **selecionar**. (A página de administração Active Directory mostra todos os membros e grupos de sua Active Directory. Os usuários ou grupos que estão esmaecidos não podem ser selecionados porque não têm suporte como administradores do Azure AD. (Consulte a lista de administradores com suporte na seção **recursos e limitações do Azure ad** de [usar Azure Active Directory autenticação para autenticação com o banco de dados SQL ou SQL data warehouse](sql-database-aad-authentication.md).) O RBAC (controle de acesso baseado em função) aplica-se somente ao portal e não é propagado para SQL Server.
    ![selecionar administrador](./media/sql-database-aad-authentication/select-admin.png)  

6. Na parte superior da página do **administrador do Active Directory** , selecione **salvar**.
    ![salvar administrador](./media/sql-database-aad-authentication/save-admin.png)

O processo de alteração do administrador pode levar vários minutos. Em seguida, o novo administrador aparece na caixa **Active Directory admin** .

   > [!NOTE]
   > Ao configurar o administrador do Azure AD, o novo nome de administrador (usuário ou grupo) não pode já estar presente no banco de dados mestre virtual como um usuário de autenticação SQL Server. Se estiver presente, a instalação do administrador do Azure AD falhará; reverter sua criação e indicar que esse administrador (nome) já existe. Como esse usuário de autenticação SQL Server não faz parte do Azure AD, qualquer esforço para se conectar ao servidor usando a autenticação do Azure AD falhará.

Para remover um administrador mais tarde, na parte superior da página **administrador do Active Directory** , selecione **remover administrador**e, em seguida, selecione **salvar**.

### <a name="powershell"></a>PowerShell

Para executar os cmdlets do PowerShell, você precisa ter Azure PowerShell instalado e em execução. Para informações detalhadas, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Para provisionar um administrador do Azure AD, execute os seguintes comandos de Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets usados para provisionar e gerenciar o administrador do Azure AD:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Provisiona um administrador de Azure Active Directory para o Azure SQL Server ou o Azure SQL Data Warehouse. (Deve ser da assinatura atual.) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Remove um administrador de Azure Active Directory para o Azure SQL Server ou o SQL Data Warehouse do Azure. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Retorna informações sobre um administrador de Azure Active Directory configurado atualmente para o Azure SQL Server ou o Azure SQL Data Warehouse. |

Use o comando Get-Help do PowerShell para ver mais informações para cada um desses comandos, ``get-help Set-AzSqlServerActiveDirectoryAdministrator``por exemplo.

O script a seguir provisiona um grupo de administradores do Azure AD chamado DBA_Group `40b79501-b343-44ed-9ce7-da4c8cc7353f`(ID de objeto) para o servidor **demo_server** em um grupo de recursos denominado **Group-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

O parâmetro de entrada **DisplayName** aceita o nome de exibição do Azure ad ou o nome principal do usuário. Por exemplo, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Para grupos do Azure AD, há suporte apenas para o nome de exibição do Azure AD.

> [!NOTE]
> O comando ```Set-AzSqlServerActiveDirectoryAdministrator``` Azure PowerShell não impede que você provisione administradores do Azure ad para usuários sem suporte. Um usuário sem suporte pode ser provisionado, mas não pode se conectar a um banco de dados.

O exemplo a seguir usa o **ObjectID**opcional:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> O **ObjectID** do Azure AD é necessário quando **DisplayName** não é exclusivo. Para recuperar os valores **ObjectID** e **DisplayName** , use a seção Active Directory de portal clássico do Azure e exiba as propriedades de um usuário ou grupo.

O exemplo a seguir retorna informações sobre o administrador atual do Azure AD para o SQL Server do Azure:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

O exemplo a seguir remove um administrador do Azure AD:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Você também pode provisionar um administrador de Azure Active Directory usando as APIs REST. Para obter mais informações, consulte [referência da API REST do gerenciamento de serviços e operações para operações de banco de dados SQL do Azure para banco de dados SQL do Azure](https://docs.microsoft.com/rest/api/sql/)

### <a name="cli"></a>CLI  

Você também pode provisionar um administrador do Azure AD chamando os seguintes comandos da CLI:

| Comando | Descrição |
| --- | --- |
|[az sql server ad-admin create](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) |Provisiona um administrador de Azure Active Directory para o Azure SQL Server ou o Azure SQL Data Warehouse. (Deve ser da assinatura atual.) |
|[az sql server ad-admin delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) |Remove um administrador de Azure Active Directory para o Azure SQL Server ou o SQL Data Warehouse do Azure. |
|[az sql server ad-admin list](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) |Retorna informações sobre um administrador de Azure Active Directory configurado atualmente para o Azure SQL Server ou o Azure SQL Data Warehouse. |
|[az sql server ad-admin update](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) |Atualiza o administrador de Active Directory para um Azure SQL Server ou Azure SQL Data Warehouse. |

Para obter mais informações sobre comandos da CLI, consulte [SQL-AZ SQL](https://docs.microsoft.com/cli/azure/sql/server).  

## <a name="configure-your-client-computers"></a>Configurar seus computadores cliente

Em todos os computadores cliente, dos quais seus aplicativos ou usuários se conectam ao banco de dados SQL do Azure ou ao Azure SQL Data Warehouse usando identidades do Azure AD, você deve instalar o seguinte software:

- .NET Framework 4,6 ou posterior de [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Azure Active Directory biblioteca de autenticação para SQL Server (**ADALSQL. DLL**) está disponível em vários idiomas (x86 e AMD64) do centro de download no [Microsoft Biblioteca de Autenticação do Active Directory para Microsoft SQL Server](https://www.microsoft.com/download/details.aspx?id=48742).

Você pode atender a esses requisitos:

- A instalação do [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) ou [SQL Server Data Tools para o Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) atende aos requisitos do .NET Framework 4,6.
- O SSMS instala a versão x86 do **ADALSQL. DLL**.
- SSDT instala a versão amd64 do **ADALSQL. DLL**.
- O Visual Studio mais recente dos [downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) atende aos requisitos .NET Framework 4,6, mas não instala a versão de AMD64 necessária do **ADALSQL. DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Criar usuários de banco de dados independente em seu banco de dados mapeado para identidades do Azure AD

>[!IMPORTANT]
>O Instância Gerenciada agora dá suporte às entidades de segurança do Azure AD Server (logons) (**Visualização pública**), que permite criar logons de usuários, grupos ou aplicativos do Azure AD. As entidades de segurança do servidor do Azure AD (logons) fornecem a capacidade de autenticar para seu Instância Gerenciada sem exigir que os usuários do banco de dados sejam criados como um usuário de banco de dados independente. Para obter mais informações, consulte [instância gerenciada visão geral](sql-database-managed-instance.md#azure-active-directory-integration). Para obter a sintaxe sobre a criação de entidades de segurança do servidor do Azure AD (logons), consulte <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">criar logon</a>.

Azure Active Directory autenticação requer que os usuários do banco de dados sejam criados como usuários de banco de dados independente. Um usuário de banco de dados independente baseado em uma identidade do Azure AD é um usuário de banco de dados que não tem um logon no banco de dados mestre e que mapeia para uma identidade no diretório do AD do Azure que está associado ao banco de dados. A identidade do Azure AD pode ser uma conta de usuário individual ou um grupo. Para obter mais informações sobre usuários de banco de dados independente, consulte [usuários de banco de dados independente-tornando seu banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Os usuários do banco de dados (com exceção dos administradores) não podem ser criados usando o portal do Azure. As funções RBAC não são propagadas para SQL Server, banco de dados SQL ou SQL Data Warehouse. As funções RBAC do Azure são usadas para gerenciar recursos do Azure e não se aplicam às permissões de banco de dados. Por exemplo, a função **colaborador de SQL Server** não concede acesso para se conectar ao banco de dados SQL ou SQL data warehouse. A permissão de acesso deve ser concedida diretamente no banco de dados usando instruções Transact-SQL.
> [!WARNING]
> Caracteres especiais como dois `:` -pontos `&` ou e comercial quando incluídos como nomes de usuário nas instruções CREATE login e Create User do T-SQL não têm suporte.

Para criar um usuário de banco de dados independente baseado no Azure AD (diferente do administrador do servidor que possui o banco de dados), conecte-se ao banco de dados com uma identidade do Azure AD, como um usuário com pelo menos a permissão **ALTER ANY User** . Em seguida, use a seguinte sintaxe Transact-SQL:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* pode ser o nome principal de usuário de um usuário do Azure ad ou o nome de exibição de um grupo do Azure AD.

**Exemplos:** Para criar um usuário de banco de dados independente representando um usuário de domínio gerenciado ou federado do Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Para criar um usuário de banco de dados independente representando um grupo de domínio federado ou do Azure AD, forneça o nome de exibição de um grupo de segurança:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Para criar um usuário de banco de dados independente representando um aplicativo que se conecta usando um token do Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!TIP]
> Você não pode criar um usuário diretamente de um Azure Active Directory que não seja o Azure Active Directory associado à sua assinatura do Azure. No entanto, os membros de outros diretórios ativos que são importados por usuários no Active Directory associado (conhecido como usuários externos) podem ser adicionados a um grupo de Active Directory no Active Directory de locatário. Ao criar um usuário de banco de dados independente para esse grupo do AD, os usuários do Active Directory externo podem obter acesso ao banco de dados SQL.

Para obter mais informações sobre como criar usuários de banco de dados independente com base em identidades Azure Active Directory, consulte [Create User (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Remover o administrador de Azure Active Directory do SQL Server do Azure impede que qualquer usuário de autenticação do Azure AD se conecte ao servidor. Se necessário, os usuários inutilizáveis do Azure AD podem ser removidos manualmente por um administrador do banco de dados SQL.
> [!NOTE]
> Se você receber um **tempo limite de conexão expirado**, talvez seja necessário `TransparentNetworkIPResolution` definir o parâmetro da cadeia de conexão como false. Para obter mais informações, consulte [problema de tempo limite de conexão com .NET Framework 4.6.1-TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Quando você cria um usuário de banco de dados, esse usuário recebe a permissão **Connect** e pode se conectar a esse banco de dados como um membro da função **Public** . Inicialmente, as únicas permissões disponíveis para o usuário são permissões concedidas à função **pública** ou quaisquer permissões concedidas a quaisquer grupos do Azure ad dos quais eles são membros. Depois de provisionar um usuário de banco de dados independente baseado no Azure AD, você pode conceder ao usuário permissões adicionais, da mesma maneira que concede permissão a qualquer outro tipo de usuário. Normalmente, conceda permissões a funções de banco de dados e adicione usuários a funções. Para obter mais informações, consulte [noções básicas sobre permissões de mecanismo de banco de dados](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obter mais informações sobre funções de banco de dados SQL especiais, consulte [Managing Database e logons in Azure SQL Database](sql-database-manage-logins.md).
Uma conta de usuário de domínio federado que é importada para um domínio gerenciado como um usuário externo, deve usar a identidade de domínio gerenciada.

> [!NOTE]
> Os usuários do Azure AD são marcados nos metadados do banco de dados com o tipo E (EXTERNAL_USER) e para grupos com o tipo X (EXTERNAL_GROUPS). Para obter mais informações, consulte [Sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Conectar-se ao banco de dados do usuário ou data warehouse usando o SSMS ou SSDT  

Para confirmar se o administrador do Azure AD está configurado corretamente, conecte-se ao banco de dados **mestre** usando a conta de administrador do Azure AD.
Para provisionar um usuário de banco de dados independente baseado no Azure AD (diferente do administrador do servidor que possui o banco de dados), conecte-se ao banco de dados com uma identidade do Azure AD que tenha acesso ao banco de dados.

> [!IMPORTANT]
> O suporte para autenticação Azure Active Directory está disponível com [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) no Visual Studio 2015. A versão de agosto de 2016 do SSMS também inclui suporte para Active Directory autenticação universal, que permite que os administradores exijam a autenticação multifator usando uma chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação de aplicativo móvel.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Usando uma identidade do Azure AD para se conectar usando o SSMS ou o SSDT

Os procedimentos a seguir mostram como se conectar a um banco de dados SQL com uma identidade do Azure AD usando as ferramentas de banco de dados SQL Server Management Studio ou SQL Server.

### <a name="active-directory-integrated-authentication"></a>Active Directory autenticação integrada

Use esse método se você estiver conectado ao Windows usando suas credenciais de Azure Active Directory de um domínio federado.

1. Inicie o Management Studio ou as ferramentas de dados e, na caixa de diálogo **conectar ao servidor** (ou **conectar ao mecanismo de banco de dados**), na caixa **autenticação** , selecione **integrado ao Active Directory**. Nenhuma senha é necessária ou pode ser inserida porque suas credenciais existentes serão apresentadas para a conexão.

    ![Selecionar Autenticação integrada do AD][11]
2. Selecione o botão **Opções** e, na página **Propriedades da conexão** , na caixa **conectar ao banco de dados** , digite o nome do banco de dados de usuário ao qual você deseja se conectar. (A opção **nome de domínio do AD ou ID do locatário**"só tem suporte para opções de **conexão universal com MFA** ; caso contrário, ela fica esmaecida.)  

    ![Selecione o nome do banco de dados][13]

## <a name="active-directory-password-authentication"></a>Autenticação por palavra-passe do Active Directory

Use esse método ao se conectar com um nome de entidade de segurança do Azure AD usando o domínio gerenciado do Azure AD. Você também pode usá-lo para contas federadas sem acesso ao domínio, por exemplo, ao trabalhar remotamente.

Use este método para autenticar o banco de BD SQL/DW com o Azure AD para usuários nativos ou federados do Azure AD. Um usuário nativo é explicitamente criado no Azure AD e sendo autenticado usando o nome de usuário e a senha, enquanto um usuário federado é um usuário do Windows cujo domínio é federado com o Azure AD. O último método (usando o usuário & senha) pode ser usado quando um usuário deseja usar sua credencial do Windows, mas seu computador local não está ingressado no domínio (por exemplo, usando um acesso remoto). Nesse caso, um usuário do Windows pode indicar sua conta de domínio e senha e pode autenticar no BD/DW do SQL usando credenciais federadas.

1. Inicie o Management Studio ou o Data Tools e, na caixa de diálogo **conectar ao servidor** (ou **conectar ao mecanismo de banco de dados**), na caixa **autenticação** , selecione **Active Directory-senha**.
2. Na caixa **nome de usuário** , digite o nome de usuário do Azure Active Directory no **formato\@nome**de usuário Domain.com. Os nomes de usuário devem ser uma conta do Azure Active Directory ou uma conta de um domínio federado com o Azure Active Directory.
3. Na caixa **senha** , digite sua senha de usuário para a conta de Azure Active Directory ou conta de domínio federado.

    ![Selecionar autenticação de senha do AD][12]
4. Selecione o botão **Opções** e, na página **Propriedades da conexão** , na caixa **conectar ao banco de dados** , digite o nome do banco de dados de usuário ao qual você deseja se conectar. (Veja o gráfico na opção anterior.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Usando uma identidade do Azure AD para se conectar de um aplicativo cliente

Os procedimentos a seguir mostram como se conectar a um banco de dados SQL com uma identidade do Azure AD de um aplicativo cliente.

### <a name="active-directory-integrated-authentication"></a>Active Directory autenticação integrada

Para usar a autenticação integrada do Windows, o Active Directory do seu domínio deve ser federado com Azure Active Directory. Seu aplicativo cliente (ou um serviço) conectando-se ao banco de dados deve estar em execução em um computador ingressado no domínio sob as credenciais de domínio de um usuário.

Para se conectar a um banco de dados usando a autenticação integrada e uma identidade do Azure AD, a palavra-chave de autenticação na cadeia de conexão do banco de dados deve ser definida como Active Directory integrada. O exemplo C# de código a seguir usa ADO .net.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

A palavra-chave ``Integrated Security=True`` da cadeia de conexão não tem suporte para conexão com o banco de dados SQL do Azure. Ao fazer uma conexão ODBC, você precisará remover os espaços e definir a autenticação como ' ActiveDirectoryIntegrated '.

### <a name="active-directory-password-authentication"></a>Autenticação por palavra-passe do Active Directory

Para se conectar a um banco de dados usando a autenticação integrada e uma identidade do Azure AD, a palavra-chave de autenticação deve ser definida como Active Directory senha. A cadeia de conexão deve conter os valores e as palavras-chave ID do usuário/UID e password/PWD. O exemplo C# de código a seguir usa ADO .net.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Saiba mais sobre os métodos de autenticação do Azure AD usando os exemplos de código de demonstração disponíveis na [demonstração do GitHub de autenticação do Azure ad](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Token do Azure AD

Esse método de autenticação permite que os serviços de camada intermediária se conectem ao banco de dados SQL do Azure ou ao Azure SQL Data Warehouse obtendo um token de Azure Active Directory (AAD). Ele permite cenários sofisticados, incluindo a autenticação baseada em certificado. Você deve concluir quatro etapas básicas para usar a autenticação de token do Azure AD:

1. Registre seu aplicativo com Azure Active Directory e obtenha a ID do cliente para seu código.
2. Crie um usuário de banco de dados que representa o aplicativo. (Concluído anteriormente na etapa 6.)
3. Criar um certificado no computador cliente executa o aplicativo.
4. Adicione o certificado como uma chave para seu aplicativo.

Exemplo de cadeia de conexão:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Para obter mais informações, consulte [SQL Server blog de segurança](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Para obter informações sobre como adicionar um certificado, consulte Introdução [à autenticação baseada em certificado no Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

As instruções a seguir, conectem-se usando a versão 13,1 do sqlcmd, que está disponível no [centro de download](https://go.microsoft.com/fwlink/?LinkID=825643).

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Passos Seguintes

- Para obter uma descrição geral do acesso e controlo na Base de Dados SQL, veja [Acesso e controlo da Base de Dados SQL](sql-database-control-access.md).
- Para obter uma descrição geral de inícios de sessão, utilizadores e funções de base de dados da Base de Dados SQL, veja [Inícios de sessão, utilizadores e funções de base de dados](sql-database-manage-logins.md).
- Para obter mais informações sobre os principais de bases de dados, veja [Principals (Principais)](https://msdn.microsoft.com/library/ms181127.aspx).
- Para obter mais informações sobre as funções de base de dados, veja [Database roles (Funções de base de dados)](https://msdn.microsoft.com/library/ms189121.aspx).
- Para obter mais informações sobre as regras de firewall na Base de Dados SQL, veja [Regras de firewall da Base de Dados SQL](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
