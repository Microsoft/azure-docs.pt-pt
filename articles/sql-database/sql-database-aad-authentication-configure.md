---
title: Configurar a autenticação do Azure Active Directory
description: Saiba como ligar-se à Base de Dados SQL, à instância gerida e à Azure Synapse Analytics utilizando a Autenticação de Diretório Ativo Azure - depois de configurar o Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/27/2020
ms.openlocfilehash: 0e244ea185011bbb7d9f0facad399bb9b577bbc2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419851"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Configure e gerea autenticação de Diretório Ativo Azure com SQL

Este artigo mostra-lhe como criar e povoar a AD Azure, e depois utilizar o Azure AD com a Base de Dados Azure [SQL (SQL DB),](sql-database-technical-overview.md) [instância gerida (MI)](sql-database-managed-instance.md)e [Azure Synapse Analytics (ex-Azure SQL Data Warehouse)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para uma visão geral, consulte [a autenticação do diretório ativo azure](sql-database-aad-authentication.md).

> [!NOTE]
> Este artigo aplica-se ao servidor Azure SQL, e tanto à Base de Dados SQL como ao Azure Synapse. Para a simplicidade, a Base de Dados SQL é utilizada quando se refere tanto à Base de Dados SQL como ao Synapse Azure.

> [!IMPORTANT]  
> A ligação ao SQL Server em execução num VM Azure não é suportada utilizando uma conta de Diretório Ativo Azure. Utilize uma conta de diretório ativo de domínio.

## <a name="azure-ad-authentication-methods"></a>Métodos de autenticação Azure AD

A autenticação azure AD suporta os seguintes métodos de autenticação:

- Identidades apenas em nuvem azure
- Identidades híbridas Azure AD que suportam:
  - Autenticação em nuvem com duas opções aliadas a um único sinal insígnia (SSO)
    - Autenticação de hash de senha azure AD
    - Autenticação pass-through da AD Azure
  - Autenticação federada

Para mais informações sobre os métodos de autenticação da AD Azure, e qual escolher ver, o artigo:
- [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory](../active-directory/hybrid/choose-ad-authn.md)

Para obter mais informações sobre as identidades híbridas Azure AD, a configuração e a sincronização, consulte os seguintes artigos:

- Autenticação de hash password - [Implemente a sincronização de hash de senha com o sincronde Azure AD Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Autenticação pass-through - [Autenticação de Passagem de Diretório Ativo Azure](../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Autenticação federada - Implantação de Serviços da Federação de [Diretório Ativo em Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) e [Azure AD Connect e federação](../active-directory/hybrid/how-to-connect-fed-whatis.md)

Todos os métodos de autenticação acima são suportados para SQL DB (bases de dados únicas e bases de dados), instância gerida, e Azure Synapse.

## <a name="create-and-populate-an-azure-ad"></a>Criar e povoar um Anúncio Azure

Crie um Anúncio Azure e povoe-o com utilizadores e grupos. O Azure AD pode ser o domínio gerido pela Azure AD inicial. A Azure AD também pode ser um Serviço de Domínio ativo de diretório ativo no local que é federado com o Azure AD.

Para obter mais informações, veja [Integrating your on-premises identities with Azure Active Directory (Integrar as identidades no local ao Azure Active Directory)](../active-directory/hybrid/whatis-hybrid-identity.md), [Add your own domain name to Azure AD (Adicionar o seu próprio nome de domínio ao Azure AD)](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure now supports federation with Windows Server Active Directory (O Microsoft Azure suporta agora a federação com o Windows Server Active Directory)](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administering your Azure AD directory (Administrar o seu diretório do Azure AD)](../active-directory/fundamentals/active-directory-administer.md), [Manage Azure AD using Windows PowerShell (Gerir o Azure AD com o Windows PowerShell)](/powershell/azure/overview) e [Hybrid Identity Required Ports and Protocols (Portas e Protocolos Necessários para a Identidade Híbrida)](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associar ou adicionar uma subscrição do Azure ao Azure Active Directory

1. Associe a sua subscrição Azure ao Azure Ative Directory, tornando o diretório um diretório de confiança para a subscrição azure que acolhe a base de dados. Para mais detalhes, consulte como as [subscrições do Azure estão associadas ao Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Utilize o comutador de diretório no portal Azure para mudar para a subscrição associada ao domínio.

   > [!IMPORTANT]
   > Cada subscrição do Azure tem uma relação de confiança com uma instância do Azure AD. Tal significa que confia nesse diretório para autenticar utilizadores, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório, mas uma subscrição apenas pode confiar num diretório. Esta relação de confiança entre uma subscrição e um diretório é diferente da relação de uma subscrição com todos os outros recursos no Azure (sites, bases de dados, e assim sucessivamente), que são mais como recursos subordinados de uma subscrição. Em caso de expiração de uma subscrição, será interrompido o acesso a esses outros recursos associados à subscrição também. No entanto, o diretório permanece no Azure e pode associar outra subscrição a esse diretório e continuar a gerir os utilizadores do diretório. Para obter mais informações sobre recursos, consulte [Understanding access in Azure](../active-directory/active-directory-b2b-admin-add-users.md). Para saber mais sobre esta relação de confiança veja [como associar ou adicionar uma subscrição Azure ao Azure Ative Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Criar um administrador de AD Azure para o servidor Azure SQL

Cada servidor Azure SQL (que acolhe uma Base de Dados SQL ou Azure Synapse) começa com uma única conta de administrador de servidor que é o administrador de todo o servidor Azure SQL. Deve ser criado um segundo administrador do SQL Server, ou seja, uma conta Azure AD. Este diretor é criado como um utilizador de base de dados contido na base de dados principal. Como administradores, as contas do administrador do servidor são membros do **db_owner** papel em cada base de dados de utilizadores e introduzem cada base de dados do utilizador como utilizador **dbo.** Para obter mais informações sobre as contas do administrador do servidor, consulte A Gestão de Bases de [Dados e Logins na Base de Dados SQL do Azure](sql-database-manage-logins.md).

Ao utilizar o Diretório Ativo Azure com geo-replicação, o administrador do Diretório Ativo Azure deve ser configurado tanto para os servidores primários como secundários. Se um servidor não tiver um administrador de Diretório Ativo Azure, então os logins do Diretório Ativo do Azure e os utilizadores recebem um erro "Não pode ligar" ao erro do servidor.

> [!NOTE]
> Os utilizadores que não se baseiam numa conta Azure AD (incluindo a conta de administrador de servidores Azure SQL), não podem criar utilizadores baseados em AD Azure, uma vez que não têm autorização para validar os utilizadores de bases de dados propostos com o Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Provisão de um administrador de Diretório Ativo Azure para a sua instância gerida

> [!IMPORTANT]
> Siga estas etapas apenas se estiver a fornecer uma instância gerida. Esta operação só pode ser executada pelo administrador global/empresa ou por um Administrador de Funções Privilegiadas em Azure AD. Seguindo os passos, descrevem o processo de concessão de permissões para utilizadores com diferentes privilégios no diretório.

> [!NOTE]
> Para os administradores da Azure AD para o MI criados antes da GA, mas continuem a operar o post GA, não há nenhuma alteração funcional no comportamento existente. Para mais informações, consulte a funcionalidade de [administração da New Azure AD para](#new-azure-ad-admin-functionality-for-mi) obter mais detalhes.

A sua instância gerida necessita de permissões para ler a AD Azure para realizar com sucesso tarefas como a autenticação dos utilizadores através da adesão ao grupo de segurança ou a criação de novos utilizadores. Para que isto funcione, é necessário conceder permissões para a leitura de Azure AD. Há duas maneiras de fazê-lo: do Portal e da PowerShell. Os seguintes passos ambos os métodos.

1. No portal Azure, no canto superior direito, selecione a sua ligação para deixar cair uma lista de possíveis Diretórios Ativos.

2. Escolha o Diretório Ativo correto como o AD Azure predefinido.

   Este passo liga a subscrição associada ao Ative Directy com a Managed Instance certificando-se de que a mesma subscrição é usada tanto para o Azure AD como para o Managed Instance.

3. Navegue para A Instância Gerida e selecione um que deseja utilizar para integração de Anúncios Azure.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Selecione o banner em cima da página de administração do Diretório Ativo e conceda permissão ao utilizador atual. Se estiver registado como administrador global/empresa em Azure AD, pode fazê-lo a partir do portal Azure ou utilizar o PowerShell com o script abaixo.

    ![conceder permissões-portal](./media/sql-database-aad-authentication/grant-permissions.png)

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

5. Após a conclusão da operação com sucesso, a seguinte notificação aparecerá no canto superior direito:

    ![exito](./media/sql-database-aad-authentication/success.png)

6. Agora pode escolher o seu administrador Azure AD para a sua instância gerida. Para isso, na página de administração do Diretório Ativo, selecione o comando **de administração set.**

    ![set-administrador](./media/sql-database-aad-authentication/set-admin.png)

7. Na página de administração aAD, procure um utilizador, selecione o utilizador ou grupo para ser administrador e, em seguida, selecione **Select**.

   A página de administração do Diretório Ativo mostra todos os membros e grupos do seu Diretório Ativo. Os utilizadores ou grupos que estão acinzentados não podem ser selecionados porque não são suportados como administradores da AD Azure. Consulte a lista de administradores suportados em [Funcionalidades e Limitações ad-a..](sql-database-aad-authentication.md#azure-ad-features-and-limitations) O controlo de acesso baseado em funções (RBAC) aplica-se apenas ao portal Azure e não é propagado ao Servidor SQL.

    ![Adicionar administração de Diretório Ativo Azure](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. No topo da página de administração do Diretório Ativo, selecione **Guardar**.

    ![save](./media/sql-database-aad-authentication/save.png)

    O processo de alteração do administrador pode demorar vários minutos. Em seguida, o novo administrador aparece na caixa de administração do Diretório Ativo.

Depois de fornecer um administrador Azure AD para a sua instância gerida, pode começar a criar diretores de servidores Azure AD (logins) com a sintaxe <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN.</a> Para mais informações, consulte a [visão geral da instância gerida](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Para mais tarde remover um Administrador, no topo da página de administração do Diretório Ativo, **selecione Remover a administração**, e, em seguida, selecione **Guardar**.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Nova funcionalidade de administração da AD Azure para O MI

A tabela abaixo resume a funcionalidade para a pré-visualização pública de login Azure AD para MI, versus uma nova funcionalidade entregue com ACE para logins Azure AD.

| Administração de login azure ad para MI durante pré-visualização pública | Funcionalidade GA para administração da AD Azure para MI |
| --- | ---|
| Comporta-se da mesma forma que a administração da Azure AD para a Base de Dados SQL, que permite a autenticação da AD Azure, mas o administrador da AD Azure não pode criar logins Azure AD ou SQL no master db para MI. | A administração da Azure AD tem permissão de sysadmina e pode criar logins AAD e SQL em master db para MI. |
| Não está presente na vista sys.server_principals | Está presente na vista sys.server_principals |
| Permite que os utilizadores individuais de Anúncios Azure sejam criados como administrador ad-ad azure para MI. Para mais informações, consulte os utilizadores de colaboração Add [Azure Ative Directory B2B no portal Azure](../active-directory/b2b/add-users-administrator.md). | Requer a criação de um grupo Azure AD com utilizadores convidados como membros para criar este grupo como administrador ad-ad azure para MI. Para mais informações, consulte o [negócio da Azure AD para o apoio às empresas.](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support) |

Como uma melhor prática para os administradores atuais da AD Azure para o MI criados antes da GA, e ainda operando o post GA, redefinir o administrador da AD Azure utilizando a opção "Remover administrador" e "Administração definida" para o mesmo utilizador ou grupo Azure AD.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Questões conhecidas com o login da AD Azure para MI

- Se existe um login Azure AD na base de dados principal `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`para O MI, criada com o comando T-SQL, não pode ser configurada como administrador ad-ad azure para MI. Sentirá um erro definindo o login como um administrador Azure AD utilizando o portal Azure, PowerShell ou comandos CLI para criar o login Azure AD.
  - O login deve ser introduzido na `DROP LOGIN [myaadaccount]`base de dados principal utilizando o comando, antes de a conta poder ser criada como administrador ad.ida e statal.
  - Criar a conta de administração da Azure AD no portal Azure após o `DROP LOGIN` sucesso. 
  - Se não conseguir configurar a conta de administração da AD Azure, verifique na base de dados principal da instância gerida o login. Utilize o seguinte comando:`SELECT * FROM sys.server_principals`
  - A criação de um administrador Azure AD para O MI criará automaticamente um login na base de dados principal para esta conta. A remoção do administrador Azure AD retirará automaticamente o login da base de dados principal.

- Os utilizadores individuais de anúncios da AD Azure não são suportados como administradores da Azure AD para o MI. Os utilizadores convidados devem fazer parte de um grupo Azure AD para ser criado como administrador da Azure AD. Atualmente, a lâmina do portal Azure não gray os utilizadores convidados para outro Azure AD, permitindo que os utilizadores continuem com a configuração da administração. Salvar os utilizadores como administrador da AD Azure fará com que a configuração falhe.
  - Se desejar fazer de um utilizador convidado um administrador Azure AD para MI, inclua o utilizador convidado num grupo Azure AD, e coloque este grupo como um administrador Azure AD.

### <a name="powershell-for-sql-managed-instance"></a>PowerShell para instância gerida pela SQL

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar os cmdlets PowerShell, é necessário instalar e executar o Azure PowerShell. Para obter informações detalhadas, veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager (RM) ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para mais informações sobre a sua compatibilidade, consulte [A introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Para fornecer um administrador Azure AD, execute os seguintes comandos Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Os Cmdlets utilizados para fornecer e gerir a administração da Azure AD para a instância gerida pela SQL:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Provisões um administrador da AD Azure para a SQL gerido na subscrição atual. (Deve ser da subscrição atual)|
| [Remover-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Remove um administrador de Anúncio SQL para a SQL gerida na subscrição atual. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Devolve informações sobre um administrador da AD Azure para a SQL gerida na subscrição atual.|

O seguinte comando obtém informações sobre um administrador da AD Azure para uma instância gerida chamada ManagedInstance01 que está associada a um grupo de recursos chamado ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

As seguintes disposições de comando um grupo de administrador da AD Azure chamado DBAs para a instância gerida chamada ManagedInstance01. Este servidor está associado ao grupo de recursos ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

O comando seguinte remove o administrador azure AD para a instância gerida chamada ManagedInstanceName01 associado ao grupo de recursos ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Também pode fornecer um administrador Azure AD para a instância gerida pela SQL, ligando para os seguintes comandos CLI:

| Comando | Descrição |
| --- | --- |
|[az sql mi ad-admin criar](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Provisões um administrador de Diretório Ativo Azure para a instância gerida pela SQL. (Deve ser da subscrição atual) |
|[az sql mi ad-admin excluir](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Remove um administrador de Diretório Ativo Azure para a instância gerida pela SQL. |
|[az sql mi lista de anúncios](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Devolve informações sobre um administrador de Diretório Ativo Azure atualmente configurado para a instância gerida pela SQL. |
|[az sql mi ad-admin atualização](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Atualiza o administrador de Diretório Ativo para uma instância gerida pela SQL. |

Para obter mais informações sobre comandos CLI, consulte [az sql mi](/cli/azure/sql/mi).

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Fornecer um administrador de Diretório Ativo Azure para o seu servidor de base de dados Azure SQL

> [!IMPORTANT]
> Siga estes passos apenas se estiver a fornecer um servidor de base de dados Azure SQL ou Azure Synapse Analytics.

Os dois procedimentos seguintes mostram-lhe como fornecer um administrador de Diretório Ativo Azure para o seu servidor Azure SQL no portal Azure e utilizando o PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. No [portal Azure](https://portal.azure.com/), no canto superior direito, selecione a sua ligação para deixar cair uma lista de possíveis Diretórios Ativos. Escolha o Diretório Ativo correto como o AD Azure predefinido. Este passo liga o Diretório Ativo associado à subscrição com o servidor Azure SQL certificando-se de que a mesma subscrição é usada tanto para o Azure AD como para o SQL Server. (O servidor Azure SQL pode ser hospedado quer a Azure SQL Database quer a Azure Synapse.)

    ![escolher-anúncio][8]

2. Procure e selecione **o servidor SQL**.

    ![Pesquisar e selecionar servidores SQL](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Nesta página, antes de selecionar **servidores SQL,** pode selecionar a **estrela** ao lado do nome para *favorito* da categoria e adicionar **servidores SQL** à barra de navegação esquerda.

3. Na página **do Servidor SQL,** selecione **administração de Diretório Ativo**.

4. Na página de administração do **Diretório Ativo,** selecione **Administração set**.

    ![Servidores SQL definir administração de Diretório Ativo](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. Na página **'Adicionar',** procure um utilizador, selecione o utilizador ou grupo para ser administrador e, em seguida, selecione **Select**. (A página de administração do Diretório Ativo mostra todos os membros e grupos do seu Diretório Ativo. Os utilizadores ou grupos que estão acinzentados não podem ser selecionados porque não são suportados como administradores da AD Azure. (Consulte a lista de administradores suportados na secção de **Funcionalidades e Limitações da AD Azure** da [Utilização Azure Ative Directory Autenticação para autenticação com Base de Dados SQL ou Synapse Azure](sql-database-aad-authentication.md).) O controlo de acesso baseado em funções (RBAC) aplica-se apenas ao portal e não é propagado ao Servidor SQL.

    ![Selecione administração de Diretório Ativo Azure](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. No topo da página de administração do **Diretório Ativo,** selecione **SAVE**.

    ![salvar administrador](./media/sql-database-aad-authentication/save-admin.png)

O processo de alteração do administrador pode demorar vários minutos. Em seguida, o novo administrador aparece na caixa de administração do **Diretório Ativo.**

   > [!NOTE]
   > Ao configurar o administrador DaD Azure, o novo nome de administrador (utilizador ou grupo) já não pode estar presente na base de dados principal virtual como utilizador de autenticação do Servidor SQL. Se presente, a configuração de administração da AD Azure falhará; relançar a sua criação e indicar que tal administrador (nome) já existe. Uma vez que este utilizador de autenticação SQL Server não faz parte do AD Azure, qualquer esforço de ligação ao servidor utilizando a autenticação AD Azure falha.

Para mais tarde remover um Administrador, no topo da página de administração do **Diretório Ativo,** selecione **Remover a administração**, e, em seguida, selecione **Guardar**.

### <a name="powershell-for-azure-sql-database-and-azure-synapse"></a>PowerShell para Base de Dados Azure SQL e Azure Synapse

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar os cmdlets PowerShell, é necessário instalar e executar o Azure PowerShell. Para obter informações detalhadas, veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Para fornecer um administrador Azure AD, execute os seguintes comandos Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Os Cmdlets utilizados para fornecer e gerir a administração da Azure AD para a Base de Dados Azure SQL e azure Synapse:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Provisões um administrador de Diretório Ativo Azure para o servidor Azure SQL ou Azure Synapse. (Deve ser da subscrição atual) |
| [Remover-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Remove um administrador de Diretório Ativo Azure para o servidor Azure SQL ou Azure Synapse. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Devolve informações sobre um administrador de Diretório Ativo Azure atualmente configurado para o servidor Azure SQL ou Azure Synapse. |

Utilize o comando PowerShell get-help para ver mais informações para cada um destes comandos. Por exemplo, `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

As seguintes disposições de script um grupo de `40b79501-b343-44ed-9ce7-da4c8cc7353f`administrador da AD Azure chamado **DBA_Group** (ID de objeto ) para o servidor **demo_server** num grupo de recursos chamado **Grupo-23:**

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

O parâmetro de entrada **DisplayName** aceita o nome de exibição Azure AD ou o Nome Principal do Utilizador. Por ``DisplayName="John Smith"`` exemplo, ``DisplayName="johns@contoso.com"``e . Para os grupos Azure AD apenas o nome de exibição Azure AD é suportado.

> [!NOTE]
> O comando ```Set-AzSqlServerActiveDirectoryAdministrator``` Azure PowerShell não o impede de fornecer administradores da Azure AD para utilizadores não suportados. Um utilizador não suportado pode ser provisionado, mas não pode ligar-se a uma base de dados.

O exemplo que se segue utiliza o **ObjectID**opcional:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> O **ObjectID** Azure AD é necessário quando o **DisplayName** não é único. Para recuperar os valores **ObjectID** e **DisplayName,** utilize a secção de Diretório Ativo do Portal Clássico Do Azure e veja as propriedades de um utilizador ou grupo.

O exemplo seguinte devolve informações sobre o atual administrador da Azure AD para o servidor Azure SQL:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

O exemplo seguinte remove um administrador da AD Azure:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode fornecer um administrador Azure AD, ligando para os seguintes comandos CLI:

| Comando | Descrição |
| --- | --- |
|[az sql servidor ad-admin criar](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Provisões um administrador de Diretório Ativo Azure para o servidor Azure SQL ou Azure Synapse. (Deve ser da subscrição atual) |
|[az sql servidor ad-admin excluir](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Remove um administrador de Diretório Ativo Azure para o servidor Azure SQL ou Azure Synapse. |
|[az sql servidor lista de anúncios](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Devolve informações sobre um administrador de Diretório Ativo Azure atualmente configurado para o servidor Azure SQL ou Azure Synapse. |
|[az sql server ad-admin atualização](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Atualiza o administrador de Diretório Ativo para um servidor Azure SQL ou Azure Synapse. |

Para obter mais informações sobre comandos CLI, consulte [o servidor az sql](/cli/azure/sql/server).

* * *

> [!NOTE]
> Também pode fornecer um Administrador de Diretório Ativo Azure utilizando as APIs REST. Para mais informações, consulte referência e operações de referência e operações de base de [dados Azure SQL para](/rest/api/sql/) base de dados Azure SQL

## <a name="configure-your-client-computers"></a>Configure os computadores dos seus clientes

Em todas as máquinas de clientes, a partir das quais as suas aplicações ou utilizadores se ligam à Base de Dados Azure SQL ou ao Azure Synapse utilizando identidades Azure AD, deve instalar o seguinte software:

- .NET Quadro 4.6 [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx)ou posterior de .
- Biblioteca de Autenticação de Diretório Ativo Azure para Servidor SQL *(ADAL. DLL*). Abaixo estão os links de descarregamento para instalar o mais recente ssms, ODBC e o controlador OLE DB que contém o *ADAL. Biblioteca DLL.*
    1. [Estúdio de Gestão de Servidores SQL](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [Condutor ODBC 17 para Servidor SQL](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [OLE DB Driver 18 para SQL Server](https://www.microsoft.com/download/details.aspx?id=56730)

Pode cumprir estes requisitos através de:

- A instalação da versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou das Ferramentas de Dados do [Servidor SQL](/sql/ssdt/download-sql-server-data-tools-ssdt) cumpre o requisito .NET Framework 4.6.
    - O SSMS instala a versão x86 do *ADAL. DLL.*
    - O SSDT instala a versão amd64 do *ADAL. DLL.*
    - O mais recente Estúdio Visual da [Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) cumpre o requisito .NET Framework 4.6, mas não instala a versão amd64 necessária da *ADAL. DLL.*

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Crie utilizadores de bases de dados contidos na sua base de dados mapeada para identidades da AD Azure

> [!IMPORTANT]
> A instância gerida agora suporta os principais servidores DaD Azure (logins), o que lhe permite criar logins a partir de utilizadores, grupos ou aplicações da AD Azure. Os diretores de servidores da Azure AD (logins) fornecem a capacidade de autenticar a sua instância gerida sem exigir que os utilizadores de bases de dados sejam criados como um utilizador de base de dados contido. Para mais informações, consulte a [visão geral da instância gerida](sql-database-managed-instance.md#azure-active-directory-integration). Para sintaxe na criação de diretores de servidores Azure AD (logins), consulte <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

A autenticação do Diretório Ativo do Azure exige que os utilizadores de bases de dados sejam criados como utilizadores de bases de dados contidos. Um utilizador de base de dados contido com base numa identidade Azure AD, é um utilizador de base de dados que não tem um login na base de dados principal, e que mapeia uma identidade no diretório da AD Azure que está associada à base de dados. A identidade Azure AD pode ser uma conta de utilizador individual ou um grupo. Para mais informações sobre utilizadores de bases de dados contidos, consulte [utilizadores de bases de dados contidos- Tornando](https://msdn.microsoft.com/library/ff929188.aspx)a sua base de dados portátil .

> [!NOTE]
> Os utilizadores de bases de dados (com exceção dos administradores) não podem ser criados através do portal Azure. As funções RBAC não são propagadas ao SQL Server, SQL Database ou Azure Synapse. As funções Azure RBAC são utilizadas para a gestão dos Recursos Azure e não se aplicam às permissões de base de dados. Por exemplo, a função De Contribuinte do **Servidor SQL** não concede acesso à ligação à Base de Dados SQL ou ao Synapse Azure. A autorização de acesso deve ser concedida diretamente na base de dados utilizando declarações transact-SQL.

> [!WARNING]
> Personagens especiais como `:` cólon `&` ou ampersand quando incluídos como nomes de utilizador nas declarações de LOGIN e CREATE USER do T-SQL não são suportados.

Para criar um utilizador de base de dados baseado em AD Azure (com a lém do administrador do servidor que detém a base de dados), ligue-se à base de dados com uma identidade Azure AD, como utilizador com pelo menos a permissão **ALTER ANY USER.** Em seguida, utilize a seguinte sintaxe Transact-SQL:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* pode ser o nome principal do utilizador de um utilizador da AD Azure ou o nome de exibição de um grupo Azure AD.

**Exemplos:** Para criar um utilizador de base de dados contido que represente um utilizador de domínio ad's ad- ad:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Para criar um utilizador de base de dados contido que represente um grupo de domínio Azure AD ou federado, forneça o nome de exibição de um grupo de segurança:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Para criar um utilizador de base de dados contido que represente uma aplicação que se conecta utilizando um token Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Este comando requer que a SQL aceda à AD Azure (o "fornecedor externo") em nome do utilizador registado. Por vezes, surgem circunstâncias que fazem com que a AD Azure devolva uma exceção à SQL. Nestes casos, o utilizador verá o erro SQL 33134, que deve conter a mensagem de erro específica da AAD. Na maior parte das vezes, o erro dirá que o acesso é negado, ou que o utilizador deve inscrever-se no MFA para aceder ao recurso, ou que o acesso entre aplicações de primeira parte deve ser tratado por pré-autorização. Nos dois primeiros casos, o problema é geralmente causado por políticas de Acesso Condicional que são definidas no inquilino AAD do utilizador: impedem o utilizador de aceder ao fornecedor externo. A atualização das políticas da AC para permitir o acesso à aplicação '00000002-0000-0000-c000-000000000000000000000000000000' (o ID de aplicação da AAD Graph API) deve resolver a questão. No caso de o erro dizer que o acesso entre aplicações de primeira parte deve ser tratado por pré-autorização, o problema é porque o utilizador é inscrito como diretor de serviço. O comando deve ter sucesso se for executado por um utilizador.

> [!TIP]
> Não é possível criar diretamente um utilizador a partir de um Diretório Ativo Azure que não seja o Diretório Ativo Azure que esteja associado à sua subscrição Azure. No entanto, os membros de outros Diretórios Ativos que sejam utilizadores importados no Diretório Ativo associado (conhecido como utilizadores externos) podem ser adicionados a um grupo de Diretório Ativo no Diretório Ativo do inquilino. Ao criar um utilizador de base de dados contido para esse grupo AD, os utilizadores do Diretório Ativo externo podem ter acesso à Base de Dados SQL.

Para obter mais informações sobre a criação de utilizadores de bases de dados contidos com base em identidades do Diretório Ativo do Azure, consulte [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> A remoção do administrador de Diretório Ativo Azure para o servidor Azure SQL impede que qualquer utilizador de autenticação Azure AD se ligue ao servidor. Se necessário, os utilizadores de Anúncios Azure inutilizáveis podem ser abandonados manualmente por um administrador de base de dados SQL.

> [!NOTE]
> Se receber um tempo de **ligação expirado,** poderá ter de definir o `TransparentNetworkIPResolution` parâmetro da cadeia de ligação a falso. Para mais informações, consulte a questão do [tempo limite de ligação com .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Quando cria um utilizador de base de dados, esse utilizador recebe a permissão **CONNECT** e pode ligar-se a essa base de dados como membro da função **PÚBLICA.** Inicialmente, as únicas permissões disponíveis ao utilizador são quaisquer permissões concedidas ao **PÚBLICO,** ou quaisquer permissões concedidas a quaisquer grupos de AD Azure de que sejam membros. Uma vez que for prestada a um utilizador de base de dados baseado em AD Azure, pode conceder ao utilizador permissões adicionais, da mesma forma que concede permissão a qualquer outro tipo de utilizador. Normalmente concedepers permissões a funções de base de dados e adiciona os utilizadores às funções. Para mais informações, consulte base de dados de [permissões de motores básicos](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obter mais informações sobre as funções especiais da Base de Dados SQL, consulte A Gestão de Bases de [Dados e Logins na Base de Dados Azure SQL](sql-database-manage-logins.md).
Uma conta de utilizador de domínio federado que seja importada para um domínio gerido como utilizador externo, deve utilizar a identidade de domínio gerida.

> [!NOTE]
> Os utilizadores de AD Azure estão marcados na base de dados metadados com o tipo E (EXTERNAL_USER) e para grupos com o tipo X (EXTERNAL_GROUPS). Para mais informações, consulte [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

## <a name="connect-to-the-user-database-or-azure-synapse-by-using-ssms-or-ssdt"></a>Ligue-se à base de dados do utilizador ou ao Azure Synapse utilizando SSMS ou SSDT  

Para confirmar que o administrador da AD Azure está corretamente configurado, ligue-se à base de dados **principal** utilizando a conta de administrador da AD Azure.
Para fornecer um utilizador de base de dados baseado em AD Azure (com além do administrador do servidor que detém a base de dados), ligue-se à base de dados com uma identidade Azure AD que tenha acesso à base de dados.

> [!IMPORTANT]
> O suporte para autenticação do Diretório Ativo Azure está disponível com [o SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [sQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) no Visual Studio 2015. O lançamento de agosto de 2016 do SSMS inclui também suporte para autenticação universal de diretório ativo, que permite aos administradores exigirem a Autenticação Multi-Factor usando uma chamada telefónica, mensagem de texto, cartões inteligentes com pin ou notificação de aplicação móvel.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Utilização de uma identidade Azure AD para se conectar usando SSMS ou SSDT

Os seguintes procedimentos mostram como se conectar a uma base de dados SQL com uma identidade Azure AD utilizando o SQL Server Management Studio ou sQL Server Database Tools. 

### <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Diretório Ativo

Utilize este método se estiver registado no Windows utilizando as suas credenciais de Diretório Ativo Azure a partir de um domínio federado, ou um domínio gerido que esteja configurado para uma única inscrição perfeita para a autenticação de hash pass-through e password. Para mais informações, consulte [Azure Ative Directory Seamless Single Sign-On](../active-directory/hybrid/how-to-connect-sso.md).

1. Inicie o Estúdio de Gestão ou ferramentas de dados e na caixa de diálogo **Connect to Server** (ou Connect to Database **Engine),** na caixa **de autenticação,** selecione **Azure Ative Directory - Integrado**. Não é necessária nenhuma palavra-passe ou pode ser inserida porque as suas credenciais existentes serão apresentadas para a ligação.

    ![Selecione Autenticação Integrada aD][11]

2. Selecione o botão **Opções** e na página Propriedades de **Ligação,** na caixa de **dados Connect to database,** escreva o nome da base de dados do utilizador a que pretende ligar. Para mais informações, consulte o artigo [Multi-factor AAD auth](sql-database-ssms-mfa-authentication.md#azure-ad-domain-name-or-tenant-id-parameter) sobre as diferenças entre as Propriedades de Ligação para SSMS 17.x e 18.x. 

    ![Selecione o nome da base de dados][13]

### <a name="active-directory-password-authentication"></a>Autenticação de senha de diretório ativo

Utilize este método quando estiver ligado a um nome principal da AD Azure utilizando o domínio gerido pelo Azure AD. Também pode usá-lo para contas federadas sem acesso ao domínio, por exemplo, quando trabalha remotamente.

Utilize este método para autenticar o SQL DB ou MI com utilizadores de identidade apenas azure AD, ou aqueles que usam identidades híbridas Azure AD. Este método suporta utilizadores que pretendam utilizar a sua credencial Windows, mas a sua máquina local não está unida com o domínio (por exemplo, utilizando acesso remoto). Neste caso, um utilizador do Windows pode indicar a sua conta de domínio e senha, podendo autenticar a SQL DB, MI ou Azure Synapse.

1. Inicie o Estúdio de Gestão ou ferramentas de dados e na caixa de diálogo **Connect to Server** (ou Connect to Database **Engine),** na caixa **de autenticação,** selecione **Azure Ative Directory - Password**.

2. Na caixa de nome sinuoso, digite o nome de utilizador do Diretório Ativo Azure no nome **User name** **\@** de utilizador do formato domain.com . Os nomes dos utilizadores devem ser uma conta do Azure Ative Directory ou uma conta de um domínio gerido ou federado com o Diretório Ativo Azure.

3. Na caixa **Password,** digite a sua palavra-passe de utilizador para a conta De Diretório Ativo Azure ou para a conta de domínio gerida/federada.

    ![Selecione Autenticação de senha aD][12]

4. Selecione o botão **Opções** e na página Propriedades de **Ligação,** na caixa de **dados Connect to database,** escreva o nome da base de dados do utilizador a que pretende ligar. (Ver o gráfico na opção anterior.)

### <a name="active-directory-interactive-authentication"></a>Autenticação interativa do Diretório Ativo

Utilize este método para autenticação interativa com ou sem autenticação multi-factor (MFA), sendo a palavra-passe solicitada interativamente. Este método pode ser usado para autenticar a SQL DB, MI e Azure Synapse para utilizadores de identidade azure AD apenas para utilizadores de identidade apenas em nuvem, ou aqueles que usam identidades híbridas Azure AD.

Para mais informações, consulte [a autenticação AAD multifactor com base de dados Azure SQL e Azure Synapse Analytics (suporte SSMS para MFA)](sql-database-ssms-mfa-authentication.md).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Usando uma identidade Azure AD para se conectar a partir de uma aplicação de cliente

Os seguintes procedimentos mostram-lhe como se conectar a uma base de dados SQL com uma identidade Azure AD a partir de uma aplicação de cliente.

### <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Diretório Ativo

Para utilizar a autenticação integrada do Windows, o Diretório Ativo do seu domínio deve ser federado com o Diretório Ativo do Azure, ou deve ser um domínio gerido que esteja configurado para uma única inscrição perfeita para a autenticação de hash pass-through ou password. Para mais informações, consulte [Azure Ative Directory Seamless Single Sign-On](../active-directory/hybrid/how-to-connect-sso.md).

> [!NOTE]
> [MSAL.NET (Microsoft.Identity.Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) para autenticação integrada do Windows não é suportado para uma única inscrição perfeita para a autenticação de hash pass-through e password.

A sua aplicação ao cliente (ou um serviço) que ligue à base de dados deve estar a funcionar numa máquina unida ao domínio sob as credenciais de domínio de um utilizador.

Para se ligar a uma base de dados utilizando a autenticação integrada e uma identidade Azure AD, a palavra-chave de autenticação na cadeia de ligação à base de dados deve ser definida para `Active Directory Integrated`. A seguinte amostra de código C# utiliza ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

A palavra-chave `Integrated Security=True` de corda de ligação não é suportada para a ligação à Base de Dados Azure SQL. Ao efetuar uma ligação ODBC, terá de remover espaços e definir a Autenticação para 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Autenticação de senha de diretório ativo

Para se ligar a uma base de dados utilizando contas de utilizadores de identidade apenas com a Nuvem Azure, ou a quem utiliza identidades híbridas Azure AD, a palavra-chave de autenticação deve ser definida para `Active Directory Password`. A cadeia de ligação deve conter palavras e valores id/uID do utilizador e palavras-chave/palavras-chave/PWD. A seguinte amostra de código C# utiliza ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Saiba mais sobre os métodos de autenticação da AD Azure utilizando as amostras de código de demonstração disponíveis na [Demo Demo Demo de Autenticação AD Azure](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Ficha da AD Azure

Este método de autenticação permite que os serviços de nível médio obtenham [tokens Web JSON (JWT)](../active-directory/develop/id-tokens.md) para ligar à Base de Dados Azure SQL ou azure Synapse, obtendo um símbolo do Azure Ative Directory (AAD). Este método permite vários cenários de aplicação, incluindo identidades de serviço, diretores de serviço e aplicações usando a autenticação baseada em certificados. Deve completar quatro passos básicos para utilizar a autenticação token Azure AD:

1. Registe a sua aplicação no Azure Ative Directory e obtenha o ID do cliente para o seu código.
2. Crie um utilizador de base de dados que represente a aplicação. (Concluído mais cedo no passo 6.)
3. Criar um certificado no computador cliente executa a aplicação.
4. Adicione o certificado como chave para a sua aplicação.

Cadeia de ligação da amostra:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Para mais informações, consulte [o SQL Server Security Blog](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Para obter informações sobre a adição de um certificado, consulte [Iniciar com autenticação baseada em certificados no Diretório Ativo Azure](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

As seguintes declarações, liguem-se utilizando a versão 13.1 de sqlcmd, que está disponível no [Centro de Descarregamento](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd`com `-G` o comando não funciona com identidades do sistema, e requer um login principal do utilizador.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>Resolução de problemas da autenticação da AD Azure

A orientação sobre problemas de resolução de problemas com a Autenticação AD Azure pode ser encontrada no seguinte blog:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral dos logins, utilizadores, funções de base de dados e permissões na Base de Dados SQL, consulte [Logins, utilizadores, funções](sql-database-manage-logins.md)de base de dados e contas de utilizador .
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
