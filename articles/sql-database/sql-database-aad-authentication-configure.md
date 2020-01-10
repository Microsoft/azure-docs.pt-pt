---
title: Configurar a autenticação do Azure Active Directory
description: Saiba como se conectar ao banco de dados SQL, instância gerenciada e SQL Data Warehouse usando a autenticação Azure Active Directory-depois de configurar o Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 01/07/2020
ms.openlocfilehash: dc2661bbc443201d6a2da4b5efb7ecdc2caad444
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732574"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Configurar e gerenciar a autenticação de Azure Active Directory com o SQL

Este artigo mostra como criar e popular o Azure AD e, em seguida, usar o Azure AD com o [banco de dados SQL](sql-database-technical-overview.md)do Azure, [instância gerenciada](sql-database-managed-instance.md)e [SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para obter uma visão geral, consulte [Azure Active Directory autenticação](sql-database-aad-authentication.md).

> [!NOTE]
> Este artigo aplica-se ao SQL Server do Azure e ao banco de dados SQL e SQL Data Warehouse bancos que são criados no SQL Server do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.

> [!IMPORTANT]  
> Não há suporte para a conexão com SQL Server em execução em uma VM do Azure usando uma conta de Azure Active Directory. Em vez disso, use uma conta de Active Directory de domínio.

## <a name="create-and-populate-an-azure-ad"></a>Criar e popular um Azure AD

Crie um Azure AD e popule-o com usuários e grupos. O Azure AD pode ser o domínio gerenciado inicial do Azure AD. O Azure AD também pode ser um Active Directory Domain Services local federado com o Azure AD.

Para obter mais informações, veja [Integrating your on-premises identities with Azure Active Directory (Integrar as identidades no local ao Azure Active Directory)](../active-directory/hybrid/whatis-hybrid-identity.md), [Add your own domain name to Azure AD (Adicionar o seu próprio nome de domínio ao Azure AD)](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure now supports federation with Windows Server Active Directory (O Microsoft Azure suporta agora a federação com o Windows Server Active Directory)](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administering your Azure AD directory (Administrar o seu diretório do Azure AD)](../active-directory/fundamentals/active-directory-administer.md), [Manage Azure AD using Windows PowerShell (Gerir o Azure AD com o Windows PowerShell)](/powershell/azure/overview) e [Hybrid Identity Required Ports and Protocols (Portas e Protocolos Necessários para a Identidade Híbrida)](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associar ou adicionar uma assinatura do Azure ao Azure Active Directory

1. Associe sua assinatura do Azure ao Azure Active Directory tornando o diretório um diretório confiável para a assinatura do Azure que hospeda o banco de dados. Para obter detalhes, consulte [como as assinaturas do Azure estão associadas ao Azure ad](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Use o alternador de diretório no portal do Azure para alternar para a assinatura associada ao domínio.

   > [!IMPORTANT]
   > Cada subscrição do Azure tem uma relação de confiança com uma instância do Azure AD. Tal significa que confia nesse diretório para autenticar utilizadores, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório, mas uma subscrição apenas pode confiar num diretório. Esta relação de confiança entre uma subscrição e um diretório é diferente da relação de uma subscrição com todos os outros recursos no Azure (sites, bases de dados, e assim sucessivamente), que são mais como recursos subordinados de uma subscrição. Em caso de expiração de uma subscrição, será interrompido o acesso a esses outros recursos associados à subscrição também. No entanto, o diretório permanece no Azure e pode associar outra subscrição a esse diretório e continuar a gerir os utilizadores do diretório. Para obter mais informações sobre recursos, consulte [noções básicas sobre o acesso aos recursos no Azure](../active-directory/active-directory-b2b-admin-add-users.md). Para saber mais sobre essa relação confiável, consulte [como associar ou adicionar uma assinatura do Azure ao Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Criar um administrador do Azure AD para o SQL Server do Azure

Cada servidor do SQL Azure (que hospeda um banco de dados SQL ou SQL Data Warehouse) começa com uma única conta de administrador de servidor que é o administrador de todo o SQL Server do Azure. Um segundo administrador de SQL Server deve ser criado, ou seja, uma conta do Azure AD. Essa entidade de segurança é criada como um usuário de banco de dados independente no banco de dados mestre. Como administradores, as contas de administrador do servidor são membros da função **db_owner** em cada banco de dados de usuário e inserem cada banco de dados de usuário como o usuário **dbo** . Para obter mais informações sobre as contas de administrador do servidor, consulte [Gerenciando bancos de dados e logons no Azure SQL Database](sql-database-manage-logins.md).

Ao usar Azure Active Directory com a replicação geográfica, o administrador de Azure Active Directory deve ser configurado tanto para os servidores primários quanto para os secundários. Se um servidor não tiver um administrador de Azure Active Directory, Azure Active Directory logons e usuários receberão um erro "não é possível conectar-se" ao servidor.

> [!NOTE]
> Os usuários que não são baseados em uma conta do Azure AD (incluindo a conta de administrador do Azure SQL Server) não podem criar usuários baseados no Azure AD, pois não têm permissão para validar os usuários de banco de dados proposto com o Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Provisionar um administrador de Azure Active Directory para sua instância gerenciada

> [!IMPORTANT]
> Somente siga estas etapas se você estiver Provisionando uma instância gerenciada. Esta operação só pode ser executada pelo administrador global/da empresa ou por um administrador de função com privilégios no Azure AD. As etapas a seguir descrevem o processo de conceder permissões para usuários com privilégios diferentes no diretório.

> [!NOTE]
> Para administradores do Azure AD para MI criado antes do GA, mas continue operando após a GA, não há nenhuma alteração funcional no comportamento existente. Para obter mais informações, consulte a seção [nova funcionalidade de administrador do Azure ad para mi](#new-azure-ad-admin-functionality-for-mi) para obter mais detalhes.

Sua instância gerenciada precisa de permissões para ler o Azure AD para realizar tarefas com êxito, como a autenticação de usuários por meio da Associação de grupo de segurança ou da criação de novos usuários. Para que isso funcione, você precisa conceder permissões à instância gerenciada para ler o Azure AD. Há duas maneiras de fazer isso: do portal e do PowerShell. As etapas a seguir são os dois métodos.

1. No portal do Azure, no canto superior direito, selecione sua conexão para listar uma listagem de diretórios ativos possíveis.

2. Escolha o Active Directory correto como o Azure AD padrão.

   Esta etapa vincula a assinatura associada ao Active Directory com Instância Gerenciada certificando-se de que a mesma assinatura seja usada tanto para o Azure AD quanto para o Instância Gerenciada.

3. Navegue até Instância Gerenciada e selecione um que você deseja usar para a integração do Azure AD.

   ![AAD](./media/sql-database-aad-authentication/aad.png)

4. Selecione a faixa na parte superior da página Active Directory administrador e conceda permissão ao usuário atual. Se você estiver conectado como administrador global/da empresa no Azure AD, poderá fazer isso no portal do Azure ou usando o PowerShell com o script abaixo.

    ![conceder permissões-Portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

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
    if ($roleMember -eq $null) {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1)) {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null) {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. Depois que a operação for concluída com êxito, a notificação a seguir será exibida no canto superior direito:

    ![exito](./media/sql-database-aad-authentication/success.png)

6. Agora você pode escolher o administrador do Azure AD para sua instância gerenciada. Para isso, na página Administrador de Active Directory, selecione **definir** comando de administrador.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Na página Administrador do AAD, procure um usuário, selecione o usuário ou grupo que será um administrador e selecione **selecionar**.

   A página de administração Active Directory mostra todos os membros e grupos de sua Active Directory. Os usuários ou grupos que estão esmaecidos não podem ser selecionados porque não têm suporte como administradores do Azure AD. Consulte a lista de administradores com suporte em [limitações e recursos do Azure ad](sql-database-aad-authentication.md#azure-ad-features-and-limitations). O RBAC (controle de acesso baseado em função) aplica-se somente ao portal do Azure e não é propagado para SQL Server.

    ![Adicionar Azure Active Directory administrador](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. Na parte superior da página do administrador do Active Directory, selecione **salvar**.

    ![guardar](./media/sql-database-aad-authentication/save.png)

    O processo de alteração do administrador pode levar vários minutos. Em seguida, o novo administrador aparece na caixa Active Directory admin.

Depois de provisionar um administrador do Azure AD para sua instância gerenciada, você pode começar a criar entidades de segurança de servidor do Azure AD (logons) com a sintaxe <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Create login</a> . Para obter mais informações, consulte [visão geral da instância gerenciada](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Para remover um administrador mais tarde, na parte superior da página Administrador do Active Directory, selecione **remover administrador**e, em seguida, selecione **salvar**.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Nova funcionalidade de administrador do Azure AD para MI

A tabela a seguir resume a funcionalidade do administrador de logon do Azure AD de visualização pública para MI, em comparação com uma nova funcionalidade fornecida com o GA para logons do Azure AD.

| Administrador de logon do Azure AD para MI durante a visualização pública | Funcionalidade de GA para administrador do Azure AD para MI |
| --- | ---|
| Comporta-se de forma semelhante ao administrador do Azure AD para o banco de dados SQL, que habilita a autenticação do Azure AD, mas o administrador do Azure AD não pode criar logons do Azure AD ou SQL no BD mestre para MI. | O administrador do Azure AD tem a permissão sysadmin e pode criar logons do AAD e do SQL no BD mestre para MI. |
| Não está presente na exibição sys. server_principals | Está presente na exibição sys. server_principals |
| Permite que usuários convidados individuais do Azure AD sejam configurados como administrador do Azure AD para MI. Para obter mais informações, consulte [adicionar Azure Active Directory usuários de colaboração B2B no portal do Azure](../active-directory/b2b/add-users-administrator.md). | Requer a criação de um grupo do Azure AD com usuários convidados como membros para configurar esse grupo como um administrador do Azure AD para MI. Para obter mais informações, consulte [suporte do Azure ad Business to Business](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support). |

Como prática recomendada para os administradores existentes do Azure AD para MI criado antes do GA, e ainda operando após a GA, redefina o administrador do Azure AD usando a opção portal do Azure "remover administrador" e "definir administrador" para o mesmo usuário ou grupo do Azure AD.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Problemas conhecidos com o logon do Azure AD GA para MI

- Se um logon do Azure AD existir no banco de dados mestre para MI, criado usando o comando T-SQL `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`, ele não poderá ser configurado como um administrador do Azure AD para MI. Você terá um erro ao definir o logon como um administrador do Azure AD usando os comandos portal do Azure, PowerShell ou CLI para criar o logon do Azure AD.
  - O logon deve ser descartado no banco de dados mestre usando o comando `DROP LOGIN [myaadaccount]`, antes que a conta possa ser criada como um administrador do Azure AD.
  - Configure a conta de administrador do Azure AD no portal do Azure depois que a `DROP LOGIN` for realizada com sucesso. 
  - Se você não puder configurar a conta de administrador do Azure AD, faça check-in do banco de dados mestre da instância gerenciada para o logon. Use o seguinte comando: `SELECT * FROM sys.server_principals`
  - Configurar um administrador do Azure AD para MI criará automaticamente um logon no banco de dados mestre para essa conta. Remover o administrador do Azure AD removerá automaticamente o logon do banco de dados mestre.

- Não há suporte para usuários convidados individuais do Azure AD como administradores do Azure AD para MI. Os usuários convidados devem fazer parte de um grupo do Azure AD para ser configurado como administrador do Azure AD. Atualmente, a folha portal do Azure não esmaece os usuários convidados para outro Azure AD, permitindo que os usuários continuem com a configuração do administrador. Salvar usuários convidados como um administrador do Azure AD fará com que a instalação falhe.
  - Se você quiser tornar um usuário convidado um administrador do Azure AD para MI, inclua o usuário convidado em um grupo do Azure AD e defina esse grupo como um administrador do Azure AD.

### <a name="powershell-for-sql-managed-instance"></a>PowerShell para instância gerenciada do SQL

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar os cmdlets do PowerShell, você precisa ter Azure PowerShell instalado e em execução. Para obter informações detalhadas, veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell (RM) ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [apresentando o novo módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

Para provisionar um administrador do Azure AD, execute os seguintes comandos de Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets usados para provisionar e gerenciar o administrador do Azure AD para instância gerenciada do SQL:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Provisiona um administrador do Azure AD para instância gerenciada do SQL na assinatura atual. (Deve ser da assinatura atual)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Remove um administrador do Azure AD para instância gerenciada do SQL na assinatura atual. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Retorna informações sobre um administrador do Azure AD para instância gerenciada do SQL na assinatura atual.|

O comando a seguir obtém informações sobre um administrador do Azure AD para uma instância gerenciada denominada ManagedInstance01 que está associada a um grupo de recursos chamado ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

O comando a seguir provisiona um grupo de administradores do Azure AD chamado DBAs para a instância gerenciada chamada ManagedInstance01. Este servidor está associado ao grupo de recursos ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

O comando a seguir remove o administrador do Azure AD da instância gerenciada denominada ManagedInstanceName01 associada ao grupo de recursos ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você também pode provisionar um administrador do Azure AD para instância gerenciada do SQL chamando os seguintes comandos da CLI:

| Comando | Descrição |
| --- | --- |
|[AZ SQL mi ad-admin Create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Provisiona um administrador de Azure Active Directory para instância gerenciada do SQL. (Deve ser da assinatura atual) |
|[AZ SQL mi ad-admin Delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Remove um administrador de Azure Active Directory para a instância gerenciada do SQL. |
|[AZ SQL mi ad-lista de administradores](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Retorna informações sobre um administrador de Azure Active Directory configurado atualmente para instância gerenciada do SQL. |
|[AZ SQL mi-atualização de administração](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Atualiza o administrador de Active Directory para uma instância gerenciada do SQL. |

Para obter mais informações sobre comandos da CLI, consulte [AZ SQL mi](/cli/azure/sql/mi).

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Provisionar um administrador de Azure Active Directory para seu servidor de banco de dados SQL do Azure

> [!IMPORTANT]
> Somente siga estas etapas se estiver provisionando um servidor de banco de dados SQL do Azure ou data warehouse.

Os dois procedimentos a seguir mostram como provisionar um administrador de Azure Active Directory para o SQL Server do Azure no portal do Azure e usando o PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. No [portal do Azure](https://portal.azure.com/), no canto superior direito, selecione sua conexão para listar uma listagem de diretórios ativos possíveis. Escolha o Active Directory correto como o Azure AD padrão. Esta etapa vincula o Active Directory associado à assinatura ao SQL Server do Azure, certificando-se de que a mesma assinatura seja usada tanto para o Azure AD quanto para o SQL Server. (O SQL Server do Azure pode hospedar o banco de dados SQL do Azure ou o Azure SQL Data Warehouse.)

    ![escolher-AD][8]

2. Pesquise e selecione **SQL Server**.

    ![Pesquisar e selecionar servidores SQL](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Nesta página, antes de selecionar **SQL Servers**, você pode selecionar a **estrela** ao lado do nome para a categoria *favorita* e adicionar **servidores SQL** à barra de navegação à esquerda.

3. Na página **SQL Server** , selecione **Active Directory admin**.

4. Na página **administrador do Active Directory** , selecione **definir administrador**.

    ![SQL Servers Set Active Directory admin](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. Na página **Adicionar Administrador** , procure um usuário, selecione o usuário ou grupo que será um administrador e selecione **selecionar**. (A página de administração Active Directory mostra todos os membros e grupos de sua Active Directory. Os usuários ou grupos que estão esmaecidos não podem ser selecionados porque não têm suporte como administradores do Azure AD. (Consulte a lista de administradores com suporte na seção **recursos e limitações do Azure ad** de [usar Azure Active Directory autenticação para autenticação com o banco de dados SQL ou SQL data warehouse](sql-database-aad-authentication.md).) O RBAC (controle de acesso baseado em função) aplica-se somente ao portal e não é propagado para SQL Server.

    ![Selecionar administrador de Azure Active Directory](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. Na parte superior da página do **administrador do Active Directory** , selecione **salvar**.

    ![salvar administrador](./media/sql-database-aad-authentication/save-admin.png)

O processo de alteração do administrador pode levar vários minutos. Em seguida, o novo administrador aparece na caixa **Active Directory admin** .

   > [!NOTE]
   > Ao configurar o administrador do Azure AD, o novo nome de administrador (usuário ou grupo) não pode já estar presente no banco de dados mestre virtual como um usuário de autenticação SQL Server. Se estiver presente, a instalação do administrador do Azure AD falhará; reverter sua criação e indicar que esse administrador (nome) já existe. Como esse usuário de autenticação SQL Server não faz parte do Azure AD, qualquer esforço para se conectar ao servidor usando a autenticação do Azure AD falhará.

Para remover um administrador mais tarde, na parte superior da página **administrador do Active Directory** , selecione **remover administrador**e, em seguida, selecione **salvar**.

### <a name="powershell-for-azure-sql-database-and-azure-sql-data-warehouse"></a>PowerShell para banco de dados SQL do Azure e Azure SQL Data Warehouse

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar os cmdlets do PowerShell, você precisa ter Azure PowerShell instalado e em execução. Para obter informações detalhadas, veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Para provisionar um administrador do Azure AD, execute os seguintes comandos de Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets usados para provisionar e gerenciar o administrador do Azure AD para o banco de dados SQL do Azure e o Azure SQL Data Warehouse:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Provisiona um administrador de Azure Active Directory para o Azure SQL Server ou o Azure SQL Data Warehouse. (Deve ser da assinatura atual) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Remove um administrador de Azure Active Directory para o Azure SQL Server ou o SQL Data Warehouse do Azure. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Retorna informações sobre um administrador de Azure Active Directory configurado atualmente para o Azure SQL Server ou o Azure SQL Data Warehouse. |

Use o comando Get-Help do PowerShell para ver mais informações para cada um desses comandos. Por exemplo, `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

O script a seguir provisiona um grupo de administradores do Azure AD chamado **DBA_Group** (ID de objeto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) para o servidor de **demo_server** em um grupo de recursos denominado **Group-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

O parâmetro de entrada **DisplayName** aceita o nome de exibição do Azure ad ou o nome principal do usuário. Por exemplo, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Para grupos do Azure AD, há suporte apenas para o nome de exibição do Azure AD.

> [!NOTE]
> O ```Set-AzSqlServerActiveDirectoryAdministrator``` de comando Azure PowerShell não impede que você provisione administradores do Azure AD para usuários sem suporte. Um usuário sem suporte pode ser provisionado, mas não pode se conectar a um banco de dados.

O exemplo a seguir usa o **ObjectID**opcional:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
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

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você pode provisionar um administrador do Azure AD chamando os seguintes comandos da CLI:

| Comando | Descrição |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Provisiona um administrador de Azure Active Directory para o Azure SQL Server ou o Azure SQL Data Warehouse. (Deve ser da assinatura atual) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Remove um administrador de Azure Active Directory para o Azure SQL Server ou o SQL Data Warehouse do Azure. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Retorna informações sobre um administrador de Azure Active Directory configurado atualmente para o Azure SQL Server ou o Azure SQL Data Warehouse. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Atualiza o administrador de Active Directory para um Azure SQL Server ou Azure SQL Data Warehouse. |

Para obter mais informações sobre comandos da CLI, consulte [AZ SQL Server](/cli/azure/sql/server).

* * *

> [!NOTE]
> Você também pode provisionar um administrador de Azure Active Directory usando as APIs REST. Para obter mais informações, consulte [referência da API REST do gerenciamento de serviços e operações para operações de banco de dados SQL do Azure para banco de dados SQL do Azure](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Configurar seus computadores cliente

Em todos os computadores cliente, dos quais seus aplicativos ou usuários se conectam ao banco de dados SQL do Azure ou ao Azure SQL Data Warehouse usando identidades do Azure AD, você deve instalar o seguinte software:

- .NET Framework 4,6 ou posterior de [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Azure Active Directory biblioteca de autenticação para SQL Server (*Adal. DLL*). Abaixo estão os links de download para instalar o driver SSMS, ODBC e OLE DB mais recente que contém a *Adal.* Biblioteca de dll.
    1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [ODBC Driver 17 para SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [Driver OLE DB 18 para SQL Server](https://www.microsoft.com/download/details.aspx?id=56730)

Você pode atender a esses requisitos:

- A instalação da versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) atende aos requisitos .NET Framework 4,6.
    - O SSMS instala a versão x86 do *Adal. DLL*.
    - SSDT instala a versão amd64 do *Adal. DLL*.
    - O Visual Studio mais recente dos [downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) atende ao requisito de .NET Framework 4,6, mas não instala a versão de AMD64 necessária do *Adal. DLL*.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Criar usuários de banco de dados independente em seu banco de dados mapeado para identidades do Azure AD

> [!IMPORTANT]
> A instância gerenciada agora dá suporte a entidades de segurança de servidor do Azure AD (logons), que permite que você crie logons de usuários, grupos ou aplicativos do Azure AD. As entidades de segurança do servidor do Azure AD (logons) fornecem a capacidade de autenticar para sua instância gerenciada sem exigir que os usuários do banco de dados sejam criados como um usuário de banco de dados independente. Para obter mais informações, consulte [visão geral da instância gerenciada](sql-database-managed-instance.md#azure-active-directory-integration). Para obter a sintaxe sobre a criação de entidades de segurança do servidor do Azure AD (logons), consulte <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">criar logon</a>.

Azure Active Directory autenticação requer que os usuários do banco de dados sejam criados como usuários de banco de dados independente. Um usuário de banco de dados independente baseado em uma identidade do Azure AD é um usuário de banco de dados que não tem um logon no banco de dados mestre e que mapeia para uma identidade no diretório do AD do Azure que está associado ao banco de dados. A identidade do Azure AD pode ser uma conta de usuário individual ou um grupo. Para obter mais informações sobre usuários de banco de dados independente, consulte [usuários de banco de dados independente-tornando seu banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Os usuários do banco de dados (com exceção dos administradores) não podem ser criados usando o portal do Azure. As funções RBAC não são propagadas para SQL Server, banco de dados SQL ou SQL Data Warehouse. As funções RBAC do Azure são usadas para gerenciar recursos do Azure e não se aplicam às permissões de banco de dados. Por exemplo, a função **colaborador de SQL Server** não concede acesso para se conectar ao banco de dados SQL ou SQL data warehouse. A permissão de acesso deve ser concedida diretamente no banco de dados usando instruções Transact-SQL.

> [!WARNING]
> Caracteres especiais como dois pontos `:` ou e comercial `&` quando incluídos como nomes de usuário nas instruções CREATE LOGIN e CREATE USER do T-SQL não têm suporte.

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

> [!NOTE]
> Esse comando requer que o SQL Access do Azure AD (o "provedor externo") em nome do usuário conectado. Às vezes, ocorrerão circunstâncias que fazem com que o Azure AD retorne uma exceção para o SQL. Nesses casos, o usuário verá o erro SQL 33134, que deve conter a mensagem de erro específica do AAD. Na maioria das vezes, o erro dirá que o acesso foi negado ou que o usuário deve se registrar no MFA para acessar o recurso ou que o acesso entre aplicativos primários deve ser tratado por meio de autorização. Nos dois primeiros casos, o problema geralmente é causado pelas políticas de acesso condicional que são definidas no locatário do AAD do usuário: elas impedem que o usuário acesse o provedor externo. Atualizar as políticas de autoridade de certificação para permitir o acesso ao aplicativo ' 00000002-0000-0000-C000-000000000000 ' (a ID do aplicativo do API do Graph do AAD) deve resolver o problema. No caso de o erro dizer que o acesso entre os aplicativos primários deve ser manipulado por meio de autorização, o problema ocorre porque o usuário está conectado como uma entidade de serviço. O comando deverá ter sucesso se for executado por um usuário.

> [!TIP]
> Você não pode criar um usuário diretamente de um Azure Active Directory que não seja o Azure Active Directory associado à sua assinatura do Azure. No entanto, os membros de outros diretórios ativos que são importados por usuários no Active Directory associado (conhecido como usuários externos) podem ser adicionados a um grupo de Active Directory no Active Directory de locatário. Ao criar um usuário de banco de dados independente para esse grupo do AD, os usuários do Active Directory externo podem obter acesso ao banco de dados SQL.

Para obter mais informações sobre como criar usuários de banco de dados independente com base em identidades Azure Active Directory, consulte [Create User (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Remover o administrador de Azure Active Directory do SQL Server do Azure impede que qualquer usuário de autenticação do Azure AD se conecte ao servidor. Se necessário, os usuários inutilizáveis do Azure AD podem ser removidos manualmente por um administrador do banco de dados SQL.

> [!NOTE]
> Se você receber um **tempo limite de conexão expirado**, talvez seja necessário definir o parâmetro `TransparentNetworkIPResolution` da cadeia de conexão como false. Para obter mais informações, consulte [problema de tempo limite de conexão com .NET Framework 4.6.1-TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Quando você cria um usuário de banco de dados, esse usuário recebe a permissão **Connect** e pode se conectar a esse banco de dados como um membro da função **Public** . Inicialmente, as únicas permissões disponíveis para o usuário são permissões concedidas à função **pública** ou quaisquer permissões concedidas a quaisquer grupos do Azure ad dos quais eles são membros. Depois de provisionar um usuário de banco de dados independente baseado no Azure AD, você pode conceder ao usuário permissões adicionais, da mesma maneira que concede permissão a qualquer outro tipo de usuário. Normalmente, conceda permissões a funções de banco de dados e adicione usuários a funções. Para obter mais informações, consulte [noções básicas sobre permissões de mecanismo de banco de dados](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obter mais informações sobre funções de banco de dados SQL especiais, consulte [Managing Database e logons in Azure SQL Database](sql-database-manage-logins.md).
Uma conta de usuário de domínio federado que é importada para um domínio gerenciado como um usuário externo, deve usar a identidade de domínio gerenciada.

> [!NOTE]
> Os usuários do Azure AD são marcados nos metadados do banco de dados com o tipo E (EXTERNAL_USER) e para grupos com o tipo X (EXTERNAL_GROUPS). Para obter mais informações, consulte [Sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

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

## <a name="active-directory-password-authentication"></a>Autenticação por palavra-passe do Azure Active Directory

Use esse método ao se conectar com um nome de entidade de segurança do Azure AD usando o domínio gerenciado do Azure AD. Você também pode usá-lo para contas federadas sem acesso ao domínio, por exemplo, ao trabalhar remotamente.

Use este método para autenticar o banco de BD SQL/DW com o Azure AD para usuários nativos ou federados do Azure AD. Um usuário nativo é explicitamente criado no Azure AD e sendo autenticado usando o nome de usuário e a senha, enquanto um usuário federado é um usuário do Windows cujo domínio é federado com o Azure AD. O último método (usando o usuário & senha) pode ser usado quando um usuário deseja usar sua credencial do Windows, mas seu computador local não está ingressado no domínio (por exemplo, usando um acesso remoto). Nesse caso, um usuário do Windows pode indicar sua conta de domínio e senha e pode autenticar no BD/DW do SQL usando credenciais federadas.

1. Inicie o Management Studio ou o Data Tools e, na caixa de diálogo **conectar ao servidor** (ou **conectar ao mecanismo de banco de dados**), na caixa **autenticação** , selecione **Active Directory-senha**.

2. Na caixa **nome de usuário** , digite o nome de usuário do Azure Active Directory no formato nome de usuário **\@Domain.com**. Os nomes de usuário devem ser uma conta do Azure Active Directory ou uma conta de um domínio federado com o Azure Active Directory.

3. Na caixa **senha** , digite sua senha de usuário para a conta de Azure Active Directory ou conta de domínio federado.

    ![Selecionar autenticação de senha do AD][12]

4. Selecione o botão **Opções** e, na página **Propriedades da conexão** , na caixa **conectar ao banco de dados** , digite o nome do banco de dados de usuário ao qual você deseja se conectar. (Veja o gráfico na opção anterior.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Usando uma identidade do Azure AD para se conectar de um aplicativo cliente

Os procedimentos a seguir mostram como se conectar a um banco de dados SQL com uma identidade do Azure AD de um aplicativo cliente.

### <a name="active-directory-integrated-authentication"></a>Active Directory autenticação integrada

Para usar a autenticação integrada do Windows, o Active Directory do seu domínio deve ser federado com Azure Active Directory. Seu aplicativo cliente (ou um serviço) conectando-se ao banco de dados deve estar em execução em um computador ingressado no domínio sob as credenciais de domínio de um usuário.

Para se conectar a um banco de dados usando a autenticação integrada e uma identidade do Azure AD, a palavra-chave de autenticação na cadeia de conexão do banco de dados deve ser definida como Active Directory integrada. O exemplo C# de código a seguir usa ADO .net.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

A palavra-chave da cadeia de conexão `Integrated Security=True` não tem suporte para conexão com o banco de dados SQL do Azure. Ao fazer uma conexão ODBC, você precisará remover os espaços e definir a autenticação como ' ActiveDirectoryIntegrated '.

### <a name="active-directory-password-authentication"></a>Autenticação por palavra-passe do Azure Active Directory

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

As instruções a seguir, conectem-se usando a versão 13,1 do sqlcmd, que está disponível no [centro de download](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd` com o comando `-G` não funciona com as identidades do sistema e requer um logon principal do usuário.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>Solucionando problemas de autenticação do Azure AD

A orientação sobre solução de problemas com a autenticação do Azure AD pode ser encontrada no seguinte blog: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Passos seguintes

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
