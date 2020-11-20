---
title: Configurar a autenticação do Azure Active Directory
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Saiba como ligar à Base de Dados SQL, sql Managed Instance e Azure Synapse Analytics utilizando a autenticação do Azure Ative Directory, depois de configurar a Azure AD.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, has-adal-ref, sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 08/17/2020
ms.openlocfilehash: c5caf48dd4e2860ec5f4815eb38629ad66391a2c
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990113"
---
# <a name="configure-and-manage-azure-ad-authentication-with-azure-sql"></a>Configure e gere a autenticação AD AD com Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Este artigo mostra-lhe como criar e povoar um exemplo de Diretório Ativo Azure (Azure AD) e, em seguida, usar Azure AD com [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md), e [Azure Synapse Analytics (anteriormente SQL Data Warehouse)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para uma visão geral, consulte [a autenticação do Azure Ative Directory](authentication-aad-overview.md).

## <a name="azure-ad-authentication-methods"></a>Métodos de autenticação AD AZure

A autenticação AZURE AD suporta os seguintes métodos de autenticação:

- Identidades só em nuvem Azure AD
- Identidades híbridas AD AZure que suportam:
  - Autenticação em nuvem com duas opções juntamente com um único sign-on sem emenda (SSO)
    - Autenticação de haxixe de senha AZure AD
    - Autenticação pass-through AD Azure
  - Autenticação federada

Para obter mais informações sobre os métodos de autenticação AZure AD, e qual escolher, consulte [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory](../../active-directory/hybrid/choose-ad-authn.md).

Para obter mais informações sobre identidades híbridas AD AD, configuração e sincronização, consulte:

- Autenticação de hash de [palavra-passe - Implementar sincronização de hash de palavra-passe com sincronização Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Autenticação pass-through - [Autenticação passativa do Diretório Ativo Azure](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Autenticação federada - [Implantação de Serviços da Federação de Diretórios Ativos em Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) e [Azure AD Connect e federação](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

## <a name="create-and-populate-an-azure-ad-instance"></a>Criar e povoar um exemplo de AD Azure

Crie uma instância AD Azure e povoe-a com utilizadores e grupos. Azure AD pode ser o domínio gerido a Azure AD inicial. A Azure AD também pode ser um serviço de domínio de diretório ativo no local que é federado com o AZure AD.

Para obter mais informações, veja [Integrating your on-premises identities with Azure Active Directory (Integrar as identidades no local ao Azure Active Directory)](../../active-directory/hybrid/whatis-hybrid-identity.md), [Add your own domain name to Azure AD (Adicionar o seu próprio nome de domínio ao Azure AD)](../../active-directory/fundamentals/add-custom-domain.md), [Microsoft Azure now supports federation with Windows Server Active Directory (O Microsoft Azure suporta agora a federação com o Windows Server Active Directory)](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administering your Azure AD directory (Administrar o seu diretório do Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md), [Manage Azure AD using Windows PowerShell (Gerir o Azure AD com o Windows PowerShell)](/powershell/azure/) e [Hybrid Identity Required Ports and Protocols (Portas e Protocolos Necessários para a Identidade Híbrida)](../../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associar ou adicionar uma subscrição do Azure ao Azure Active Directory

1. Associe a sua subscrição Azure ao Azure Ative Directory, tornando o diretório de confiança para a subscrição do Azure que hospeda a base de dados. Para mais detalhes, consulte [Associate ou adicione uma subscrição Azure ao seu inquilino Azure Ative Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Utilize o comutador de diretório no portal Azure para mudar para a subscrição associada ao domínio.

   > [!IMPORTANT]
   > Cada subscrição do Azure tem uma relação de confiança com uma instância do Azure AD. Tal significa que confia nesse diretório para autenticar utilizadores, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório, mas uma subscrição apenas pode confiar num diretório. Esta relação de confiança entre uma subscrição e um diretório é diferente da relação de uma subscrição com todos os outros recursos no Azure (sites, bases de dados, e assim sucessivamente), que são mais como recursos subordinados de uma subscrição. Em caso de expiração de uma subscrição, será interrompido o acesso a esses outros recursos associados à subscrição também. No entanto, o diretório permanece no Azure e pode associar outra subscrição a esse diretório e continuar a gerir os utilizadores do diretório. Para obter mais informações sobre recursos, consulte [o Compreensão do acesso aos recursos em Azure.](../../active-directory/external-identities/add-users-administrator.md) Para saber mais sobre esta relação de confiança veja [como associar ou adicionar uma subscrição Azure ao Azure Ative Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="azure-ad-admin-with-a-server-in-sql-database"></a>Administrador AD AD Azure com um servidor na Base de Dados SQL

Cada [servidor](logical-servers.md) em Azure (que acolhe base de dados SQL ou Azure Synapse) começa com uma única conta de administrador de servidor que é o administrador de todo o servidor. Criar uma segunda conta de administrador como conta Azure AD. Este principal é criado como um utilizador de base de dados contido na base de dados principal do servidor. As contas do administrador são membros da **função db_owner** em cada base de dados de utilizadores e introduzem cada base de dados de utilizador como utilizador do **DBO.** Para obter mais informações sobre contas de administrador, consulte [Bases de Dados e Logins](logins-create-manage.md)de Gestão .

Ao utilizar o Azure Ative Directory com geo-replicação, o administrador do Azure Ative Directory deve ser configurado tanto para os servidores primários como para os servidores secundários. Se um servidor não tiver um administrador do Azure Ative Directory, então os logins do Azure Ative Directory e dos utilizadores recebem um `Cannot connect` erro no servidor.

> [!NOTE]
> Os utilizadores que não se baseiem numa conta Azure AD (incluindo a conta de administrador do servidor) não podem criar utilizadores baseados em AD Azure, porque não têm permissão para validar utilizadores de bases de dados propostos com o AD Azure.

## <a name="provision-azure-ad-admin-sql-managed-instance"></a>Fornecimento Azure AD administrador (SQL Managed Instance)

> [!IMPORTANT]
> Só siga estes passos se estiver a atear uma Instância Gerida Azure SQL. Esta operação só pode ser executada por administrador da Global/Empresa ou por um Administrador de Função Privilegiada em Azure AD.
>
> Na **pré-visualização pública,** pode atribuir o papel **de Leitores** de Diretório a um grupo em Azure AD. Os proprietários do grupo podem então adicionar a identidade de instância gerida como membro deste grupo, o que lhe permitiria providenciar um administrador AD Azure para a SQL Managed Instance. Para obter mais informações sobre esta funcionalidade, consulte o [papel dos Leitores de Diretório no Diretório Ativo Azure para a Azure SQL](authentication-aad-directory-readers-role.md).

O seu SQL Managed Instance precisa de permissões para ler AZure AD para realizar tarefas como a autenticação dos utilizadores através da adesão ao grupo de segurança ou criação de novos utilizadores. Para que isto funcione, você precisa conceder a permissão de SqL Managed Instance para ler Azure AD. Pode fazê-lo utilizando o portal Azure ou o PowerShell.

### <a name="azure-portal"></a>Portal do Azure

Para conceder a sua SQL Managed Instance AD ler permissão de leitura usando o portal Azure, faça login como administrador global/empresa em Azure AD e siga estes passos:

1. No [portal Azure,](https://portal.azure.com)no canto superior direito, selecione a sua ligação a partir de uma lista de possíveis Diretórios Ativos.

2. Escolha o Diretório Ativo correto como O AD AZure predefinido.

   Este passo liga a subscrição associada ao Ative Directory à SQL Managed Instance, certificando-se de que a mesma subscrição é usada tanto para a instância AD AZure como para a SQL Managed Instance.

3. Navegue para a SQL Managed Instance que pretende utilizar para integração AZure AD.

   ![Screenshot do portal Azure mostrando a página de administração do Ative Directory aberta para a instância gerida pelo SQL selecionado.](./media/authentication-aad-configure/aad.png)

4. Selecione o banner em cima da página de administração do Ative Directory e conceda permissão ao utilizador atual.

    ![Screenshot do diálogo para conceder permissões a uma placa gerida pelo SQL para aceder ao Ative Directory. O botão de permissões Grant está selecionado.](./media/authentication-aad-configure/grant-permissions.png)

5. Após o sucesso da operação, a seguinte notificação aparecerá no canto superior direito:

    ![Screenshot de uma notificação confirmando que as permissões de leitura de diretório ativo foram atualizadas com sucesso para a instância gerida.](./media/authentication-aad-configure/success.png)

6. Agora pode escolher o seu administrador AD Azure para a sua SQL Managed Instance. Para isso, na página de administração do Ative Directory, selecione Definir o comando **de administração.**

    ![Screenshot mostrando o comando de administração set realçado na página de administração do Ative Directory para a instância gerida pelo SQL selecionado.](./media/authentication-aad-configure/set-admin.png)

7. Na página de administração AD AZure, procure um utilizador, selecione o utilizador ou grupo para ser administrador e, em seguida, **selecione Select**.

   A página de administração do Ative Directory mostra todos os membros e grupos do seu Diretório Ativo. Os utilizadores ou grupos que estão acinzentados não podem ser selecionados porque não são suportados como administradores da AD Azure. Consulte a lista de administradores suportados em [Funcionalidades e Limitações AD Azure](authentication-aad-overview.md#azure-ad-features-and-limitations). O controlo de acesso baseado em funções (Azure RBAC) aplica-se apenas ao portal Azure e não é propagado à Base de Dados SQL, SQL Managed Instance ou Azure Synapse.

    ![Adicionar administrador do Diretório Ativo Azure](./media/authentication-aad-configure/add-azure-active-directory-admin.png)

8. No topo da página de administração do Ative Directory, **selecione Save**.

    ![Screenshot da página de administração do Ative Directory com o botão Guardar na primeira linha ao lado dos botões de administração set e Remover.](./media/authentication-aad-configure/save.png)

    O processo de alteração do administrador pode demorar vários minutos. Em seguida, o novo administrador aparece na caixa de administração do Ative Directory.

Depois de providenciar um administrador AD Azure para a sua SQL Managed Instance, pode começar a criar os principais servidores AD do Azure (logins) com a sintaxe <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">DE LOGIN CREATE.</a> Para obter mais informações, consulte [a visão geral do SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration).

> [!TIP]
> Para posteriormente remover um Administrador, no topo da página de administração do Ative Directory, selecione **Remover a administração** e, em seguida, selecione **Save**.

### <a name="powershell"></a>PowerShell

Para conceder a sua sql Managed Instance AD ler permissão de leitura usando o PowerShell, execute este script:

```powershell
# Gives Azure Active Directory read permission to a Service Principal representing the SQL Managed Instance.
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

# Get service principal for your SQL Managed Instance
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

### <a name="powershell-for-sql-managed-instance"></a>PowerShell para sql caso gerido

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar os cmdlets PowerShell, é necessário ter o Azure PowerShell instalado e a funcionar. Para obter informações detalhadas, veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/).

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager (RM) ainda é suportado pela Azure SQL Managed Instance, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de erros até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre a sua compatibilidade, consulte [a introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Para obter um administrador AD Azure, execute os seguintes comandos Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Os cmdlets utilizados para a prestação e gestão do administrador AdD Ad para a sua SQL Managed Instance estão listados na tabela seguinte:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Disposições um administrador AD Azure para a SQL Managed Instance na subscrição atual. (Deve ser da subscrição atual)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Remove um administrador AD Azure para a SqL Managed Instance na subscrição atual. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Devolve informações sobre um administrador AD Azure para a SQL Managed Instance na subscrição atual.|

O seguinte comando obtém informações sobre um administrador AD Azure para uma SqL Managed Instance chamada ManagedInstance01 que está associada a um grupo de recursos chamado ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

As seguintes disposições de comando um grupo de administradores AD Azure chamado DBAs para a SQL Managed ManagedInstance01. Este servidor está associado ao grupo de recursos ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

O seguinte comando remove o administrador AD Azure para a SqL Managed Managed Instance chamada ManagedInstanceName01 associado ao grupo de recursos ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Também pode providenciar um administrador AD AD Azure para a SqL Managed Instance, chamando os seguintes comandos CLI:

| Comando | Descrição |
| --- | --- |
|[az sql mi ad-admin criar](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Disposições um administrador do Azure Ative Directory para a SqL Managed Instance (deve ser da subscrição atual). |
|[az sql mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Remove um administrador do Azure Ative Directory para a SQL Managed Instance. |
|[az sql mi ad-admin lista](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Devolve informações sobre um administrador do Azure Ative Directory atualmente configurado para a SQL Managed Instance. |
|[az sql mi ad-admin atualização](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Atualiza o administrador do Ative Directory para a SQL Managed Instance. |

Para obter mais informações sobre os comandos CLI, consulte [az sql mi](/cli/azure/sql/mi).

* * *

## <a name="provision-azure-ad-admin-sql-database"></a>Fornecimento Azure AD administrador (SQL Database)

> [!IMPORTANT]
> Só siga estes passos se estiver a auser um [servidor](logical-servers.md) para a SQL Database ou para a Azure Synapse.

Os dois procedimentos seguintes mostram-lhe como providenciar um administrador do Azure Ative Directory para o seu servidor no portal Azure e utilizando o PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. No [portal Azure,](https://portal.azure.com/)no canto superior direito, selecione a sua ligação para deixar cair uma lista de possíveis Diretórios Ativos. Escolha o Diretório Ativo correto como O AD AZure predefinido. Este passo liga o Ative Directory associado a subscrição com o servidor certificando-se de que a mesma subscrição é usada tanto para o AD AZure como para o servidor.

2. Procure e selecione **o servidor SQL**.

    ![Procure e selecione servidores SQL](./media/authentication-aad-configure/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Nesta página, antes de selecionar **servidores SQL,** pode selecionar a **estrela** ao lado do nome para *favorito* da categoria e adicionar **servidores SQL** à barra de navegação esquerda.

3. Na página **do Servidor SQL,** selecione **Ative Directory admin**.

4. Na página de administração do **Diretório Ativo,** selecione **Definir administração**.

    ![Servidores SQL definem administrador de Diretório Ativo](./media/authentication-aad-configure/sql-servers-set-active-directory-admin.png)  

5. Na página **de administração Adicionar,** procure um utilizador, selecione o utilizador ou grupo para ser administrador e, em seguida, selecione **Select**. (A página de administração do Ative Directory mostra todos os membros e grupos do seu Diretório Ativo. Os utilizadores ou grupos que estão acinzentados não podem ser selecionados porque não são suportados como administradores da AD Azure. (Consulte a lista de administradores suportados na secção **Azure AD Features and Limitations** of [Use Azure Ative Directory Authentication for authentication with SQL Database ou Azure Synapse](authentication-aad-overview.md).) O controlo de acesso baseado em funções (Azure RBAC) aplica-se apenas ao portal e não é propagado ao SQL Server.

    ![Selecione administrador do Diretório Ativo Azure](./media/authentication-aad-configure/select-azure-active-directory-admin.png)  

6. No topo da página de administração do **Ative Directory,** selecione **SAVE**.

    ![salvar administrador](./media/authentication-aad-configure/save-admin.png)

O processo de alteração do administrador pode demorar vários minutos. Em seguida, o novo administrador aparece na caixa de administração do **Ative Directory.**

   > [!NOTE]
   > Ao configurar o administrador Azure AD, o novo nome de administração (utilizador ou grupo) já não pode estar presente na base de dados principal virtual como utilizador de autenticação do servidor. Se estiver presente, a configuração do administrador AD Azure falhará; reverter a sua criação e indicando que tal administrador (nome) já existe. Uma vez que um utilizador de autenticação de servidor não faz parte do AD Azure, qualquer esforço para ligar ao servidor utilizando a autenticação AD Azure falha.

Para posteriormente remover um Administrador, no topo da página de administração do **Ative Directory,** selecione **Remover a administração** e, em seguida, selecione **Save**.

### <a name="powershell-for-sql-database-and-azure-synapse"></a>PowerShell para base de dados SQL e Sinapse Azure

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar os cmdlets PowerShell, é necessário ter o Azure PowerShell instalado e a funcionar. Para obter informações detalhadas, veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/). Para obter um administrador AD Azure, execute os seguintes comandos Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets utilizados para a prestação e gestão do administrador AZure AD para a Base de Dados SQL e Azure Synapse:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Disposições um administrador do Azure Ative Directory para o servidor que hospeda a Base de Dados SQL ou Azure Synapse. (Deve ser da subscrição atual) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Remove um administrador do Azure Ative Directory para o servidor que hospeda a Base de Dados SQL ou Azure Synapse.|
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Devolve informações sobre um administrador do Azure Ative Directory atualmente configurado para o servidor que hospeda a Base de Dados SQL ou Azure Synapse. |

Utilize o comando PowerShell obter ajuda para ver mais informações para cada um destes comandos. Por exemplo, `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

As seguintes disposições de script um grupo de administrador AD Azure nomeado **DBA_Group** (ID do `40b79501-b343-44ed-9ce7-da4c8cc7353f` objeto) para o servidor **demo_server** num grupo de recursos chamado **Grupo-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

O parâmetro de entrada **DisplayName** aceita o nome de exibição Azure AD ou o Nome Principal do Utilizador. Por exemplo, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Para grupos AZure AD apenas o nome de exibição Azure AD é suportado.

> [!NOTE]
> O comando Azure PowerShell ```Set-AzSqlServerActiveDirectoryAdministrator``` não o impede de aducionar administradores AZure AD para utilizadores não apoiados. Um utilizador não suportado pode ser a provisionado, mas não pode ligar-se a uma base de dados.

O exemplo a seguir utiliza o **ObjectID** opcional:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> O Azure AD **ObjectID** é necessário quando o **DisplayName** não é único. Para recuperar os valores **objectID** e **DisplayName,** utilize a secção Ative Directory do Portal Clássico Azure e veja as propriedades de um utilizador ou grupo.

O exemplo a seguir devolve informações sobre o atual administrador AD Ad para o servidor:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

O exemplo a seguir remove um administrador AD Azure:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode providenciar um administrador AD Azure, chamando os seguintes comandos CLI:

| Comando | Descrição |
| --- | --- |
|[az sql servidor ad-admin criar](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Disposições um administrador do Azure Ative Directory para o servidor que hospeda a Base de Dados SQL ou Azure Synapse. (Deve ser da subscrição atual) |
|[ad-ad-admin do servidor az sql](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Remove um administrador do Azure Ative Directory para o servidor que hospeda a Base de Dados SQL ou Azure Synapse. |
|[lista de ad-admin do servidor az sql](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Devolve informações sobre um administrador do Azure Ative Directory atualmente configurado para o servidor que hospeda a Base de Dados SQL ou Azure Synapse. |
|[atualização de ad-admin do servidor az sql](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Atualiza o administrador ative directory para o servidor que hospeda a Base de Dados SQL ou Azure Synapse. |

Para obter mais informações sobre os comandos CLI, consulte [o servidor Az Sql](/cli/azure/sql/server).

* * *

> [!NOTE]
> Também pode providenciar um Administrador de Diretório Ativo Azure utilizando as APIs REST. Para mais informações, consulte [Gestão de Serviços REST Referência e Operações de Base de Dados Azure SQL para Base de Dados Azure SQL](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Configure os computadores do seu cliente

Em todas as máquinas clientes, a partir das quais as suas aplicações ou utilizadores se ligam à Base de Dados SQL ou à Azure Synapse utilizando identidades AD Azure, deve instalar o seguinte software:

- .NET Quadro 4.6 ou posteriormente de [https://msdn.microsoft.com/library/5a4x27ek.aspx](/dotnet/framework/install/guide-for-developers) .
- Biblioteca de autenticação do Diretório Ativo Azure para o Servidor SQL *(ADAL.DLL*). Abaixo estão os links de descarregamento para instalar os mais recentes SSMS, ODBC e OLE DB que contém a biblioteca *ADAL.DLL.*
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
  - [Controlador ODBC 17 para sql server](https://www.microsoft.com/download/details.aspx?id=56567)
  - [OLE DB Driver 18 para SQL Server](https://www.microsoft.com/download/details.aspx?id=56730)

Pode cumprir estes requisitos através de:

- A instalação da versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou das Ferramentas de [Dados do Servidor SQL](/sql/ssdt/download-sql-server-data-tools-ssdt) satisfaz o requisito .NET Framework 4.6.
  - O SSMS instala a versão x86 do *ADAL.DLL*.
  - A SSDT instala a versão amd64 do *ADAL.DLL*.
  - O mais recente Visual Studio de [Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) cumpre o requisito .NET Framework 4.6, mas não instala a versão amd64 necessária de *ADAL.DLL*.

## <a name="create-contained-users-mapped-to-azure-ad-identities"></a>Criar utilizadores contidos mapeados para identidades AD Azure

Como a SQL Managed Instance suporta os principais dos servidores Azure AD (logins), não é necessário utilizar utilizadores de bases de dados contidos. Os principais servidores AD do Azure (logins) permitem-lhe criar logins a partir de utilizadores, grupos ou aplicações Azure AD. Isto significa que pode autenticar com o seu SQL Managed Instance utilizando o login do servidor Azure AD em vez de um utilizador de base de dados contido. Para obter mais informações, consulte [a visão geral do SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration). Para sintaxe sobre a criação de principais servidores AD do Azure (logins), consulte <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

No entanto, a utilização da autenticação do Azure Ative Directory com base de dados SQL e Azure Synapse requer a utilização de utilizadores de bases de dados contidos com base numa identidade AD Azure. Um utilizador de base de dados contido não tem um login na base de dados principal, e mapeia para uma identidade em Azure AD que está associada à base de dados. A identidade AZure AD pode ser uma conta de utilizador individual ou um grupo. Para obter mais informações sobre os utilizadores de bases de dados contidos, consulte [os Utilizadores da Base de Dados Contidos, tornando a sua base de dados portátil](/sql/relational-databases/security/contained-database-users-making-your-database-portable).

> [!NOTE]
> Os utilizadores de bases de dados (com exceção dos administradores) não podem ser criados através do portal Azure. As funções Azure não são propagadas à base de dados na Base de Dados SQL, na SQL Managed Instance ou na Azure Synapse. As funções Azure são usadas para gerir recursos Azure, e não se aplicam a permissões de base de dados. Por exemplo, a função **de Contribuinte do Servidor SQL** não permite o acesso à ligação à base de dados na Base de Dados SQL, na SQL Managed Instance ou na Azure Synapse. A permissão de acesso deve ser concedida diretamente na base de dados utilizando declarações Transact-SQL.

> [!WARNING]
> Caracteres especiais como cólon `:` ou ampersand `&` quando incluídos como nomes de utilizador no T-SQL `CREATE LOGIN` e `CREATE USER` declarações não são suportados.

Para criar um utilizador de base de dados com base de dados com base de dados Azure AD (com base de dados que não seja o administrador do servidor que detém a base de dados), conecte-se à base de dados com uma identidade AD Azure, como utilizador com pelo menos a permissão **ALTER ANY USER.** Em seguida, utilize a seguinte sintaxe Transact-SQL:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* pode ser o nome principal de um utilizador Azure AD ou o nome de exibição de um grupo AD Azure.

**Exemplos:** Para criar um utilizador de base de dados contido que represente um utilizador de domínio federado ou gerido da Azure:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Para criar um utilizador de base de dados contido que represente um grupo de domínio Azure AD ou federado, forneça o nome de exibição de um grupo de segurança:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Para criar um utilizador de base de dados contido que represente uma aplicação que se conecta utilizando um token AD Azure:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Este comando requer que o SQL aceda a Azure AD (o "fornecedor externo") em nome do utilizador com início. Por vezes, surgirão circunstâncias que fazem com que a Azure AD devolva uma exceção ao SQL. Nestes casos, o utilizador verá o erro SQL 33134, que deve conter a mensagem de erro específica da AD Azure. Na maior parte das vezes, o erro dirá que o acesso é negado, ou que o utilizador deve inscrever-se no MFA para aceder ao recurso, ou que o acesso entre aplicações de primeira parte deve ser tratado através da pré-autorização. Nos dois primeiros casos, o problema é geralmente causado por políticas de acesso condicional que são definidas no inquilino Azure AD do utilizador: impedem o utilizador de aceder ao fornecedor externo. Atualizar as políticas da AC para permitir o acesso à aplicação '0000002-0000-0000-c0000-000000000000000000000) (o ID de aplicação da AZure AD Graph API) deve resolver a questão. No caso de o erro dizer que o acesso entre aplicações de primeira parte deve ser tratado através da pré-autorização, a questão é porque o utilizador é inscrito como diretor de serviço. O comando deve ter sucesso se for executado por um utilizador.

> [!TIP]
> Não é possível criar diretamente um utilizador a partir de um Diretório Ativo Azure que não seja o Diretório Ativo Azure que esteja associado à sua subscrição Azure. No entanto, os membros de outros Diretórios Ativos que são utilizadores importados no Diretório Ativo associado (conhecido como utilizadores externos) podem ser adicionados a um grupo de Diretório Ativo no diretório ativo do inquilino. Ao criar um utilizador de base de dados contido para esse grupo de AD, os utilizadores do Diretório Ativo externo podem ter acesso à Base de Dados SQL.

Para obter mais informações sobre a criação de utilizadores de bases de dados contidos com base nas identidades do Azure Ative Directory, consulte [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql).

> [!NOTE]
> A remoção do administrador do Azure Ative Directory para o servidor impede que qualquer utilizador de autenticação AD Azure se conecte ao servidor. Se necessário, os utilizadores Azure AD inutilizáveis podem ser largados manualmente por um administrador da Base de Dados SQL.

> [!NOTE]
> Se receber um **tempo limite de ligação expirado,** poderá ter de definir `TransparentNetworkIPResolution` o parâmetro do fio de ligação em falso. Para obter mais informações, consulte [a emissão de tempo limite de ligação com .NET Framework 4.6.1 - TransparentNetworkIPResolution](/archive/blogs/dataaccesstechnologies/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution).

Quando cria uma base de dados, esse utilizador recebe a permissão **CONNECT** e pode ligar-se a essa base de dados como membro do **PÚBLICO.** Inicialmente, as únicas permissões disponíveis para o utilizador são quaisquer permissões concedidas ao **PÚBLICO,** ou quaisquer permissões concedidas a quaisquer grupos AD do Azure de que sejam membros. Uma vez que fornece um utilizador de base de dados com base de dados Azure AD, pode conceder ao utilizador permissões adicionais, da mesma forma que concede permissão a qualquer outro tipo de utilizador. Normalmente, concede permissões a funções de base de dados e adiciona os utilizadores a funções. Para obter mais informações, consulte [bases de dados de permissão do motor.](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx) Para obter mais informações sobre funções especiais de base de dados SQL, consulte [Bases de Dados de Gestão e Logins na Base de Dados Azure SQL](logins-create-manage.md).
Uma conta de utilizador de domínio federada que seja importada para um domínio gerido como utilizador externo, deve utilizar a identidade de domínio gerido.

> [!NOTE]
> Os utilizadores de AD Azure estão marcados nos metadados da base de dados com o tipo E (EXTERNAL_USER) e para grupos com tipo X (EXTERNAL_GROUPS). Para mais informações, consulte [sys.database_principals.](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql)

## <a name="connect-to-the-database-using-ssms-or-ssdt"></a>Ligue à base de dados utilizando SSMS ou SSDT  

Para confirmar que o administrador AD da Azure está devidamente configurado, ligue-se à base de dados **principal** utilizando a conta de administrador AD do Azure.
Para obter um utilizador de base de dados com base de dados com base de dados Azure AD (com base de dados que não seja o administrador do servidor que detém a base de dados), conecte-se à base de dados com uma identidade AD AZure que tenha acesso à base de dados.

> [!IMPORTANT]
> O suporte para a autenticação do Azure Ative Directory está disponível com [o SQL Server 2016 Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e as [Ferramentas de Dados do SQL Server](/sql/ssdt/download-sql-server-data-tools-ssdt) no Visual Studio 2015. O lançamento de agosto de 2016 do SSMS inclui também suporte para autenticação universal do Diretório Ativo, que permite aos administradores exigir a Autenticação Multi-Factor usando uma chamada telefónica, mensagem de texto, cartões inteligentes com pin ou notificação de aplicações móveis.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Usando uma identidade AD Azure para ligar usando SSMS ou SSDT

Os seguintes procedimentos mostram-lhe como ligar à Base de Dados SQL com uma identidade AD AZure utilizando o SQL Server Management Studio ou as Ferramentas de Base de Dados do Servidor SQL.

### <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Diretório Ativo

Utilize este método se estiver registado no Windows utilizando as credenciais do Azure Ative Directory a partir de um domínio federado ou de um domínio gerido que esteja configurado para um único registo sem costura para a autenticação de hash de passagem e de palavra-passe. Para obter mais informações, consulte [o Azure Ative Directory Seamless Single Sign-On](../../active-directory/hybrid/how-to-connect-sso.md).

1. Iniciar a caixa de diálogo 'Iniciar gestão' ou ferramentas de dados e na caixa de diálogo **Connect to Server** (ou Connect to Database **Engine),** na caixa **de autenticação,** selecione **Azure Ative Directory - Integrado**. Não é necessária nenhuma palavra-passe ou pode ser inserida porque as suas credenciais existentes serão apresentadas para a ligação.

   ![Selecione autenticação integrada de anúncios][11]

2. Selecione o botão **Opções** e, na página **'Propriedades de Ligação,',** na caixa **de base de dados 'Ligar para base de dados',** digite o nome da base de dados do utilizador a que pretende ligar. Para obter mais informações, consulte o artigo [Multi-factor Azure AD auth](authentication-mfa-ssms-overview.md#azure-ad-domain-name-or-tenant-id-parameter) sobre as diferenças entre as Propriedades de Conexão para SSMS 17.x e 18.x.

   ![Selecione o nome da base de dados][13]

### <a name="active-directory-password-authentication"></a>Autenticação de senha de diretório ativo

Utilize este método ao ligar-se a um nome principal Azure AD utilizando o domínio gerido Azure AD. Também pode usá-lo para contas federadas sem acesso ao domínio, por exemplo, quando funciona remotamente.

Utilize este método para autenticar na base de dados na Base de Dados SQL ou na SQL Managed Instance com utilizadores de identidade azure AD apenas na nuvem, ou aqueles que utilizam identidades híbridas AD AZure. Este método suporta utilizadores que pretendam utilizar a sua credencial Windows, mas a sua máquina local não está associada ao domínio (por exemplo, utilizando acesso remoto). Neste caso, um utilizador do Windows pode indicar a sua conta de domínio e palavra-passe, e pode autenticar na base de dados na Base de Dados SQL, na SqL Managed Instance ou no Azure Synapse.

1. Iniciar a caixa de diálogo 'Iniciar gestão' ou ferramentas de dados e na caixa de diálogo **Connect to Server** (ou Connect to Database **Engine),** na caixa **de autenticação,** selecione **Azure Ative Directory - Password**.

2. Na caixa de **nome do utilizador,** digite o nome de utilizador do Azure Ative Directory no nome de utilizador do formato **\@ domain.com**. Os nomes de utilizador devem ser uma conta do Azure Ative Directory ou uma conta de um domínio gerido ou federado com o Azure Ative Directory.

3. Na caixa **password,** digite a sua palavra-passe de utilizador para a conta Azure Ative Directory ou conta de domínio gerida/federada.

    ![Selecione autenticação de senha de anúncio][12]

4. Selecione o botão **Opções** e, na página **'Propriedades de Ligação,',** na caixa **de base de dados 'Ligar para base de dados',** digite o nome da base de dados do utilizador a que pretende ligar. (Ver o gráfico na opção anterior.)

### <a name="active-directory-interactive-authentication"></a>Autenticação interativa do Diretório Ativo

Utilize este método para autenticação interativa com ou sem Autenticação Multi-Factor (MFA), com a palavra-passe a ser solicitada interativamente. Este método pode ser usado para autenticar na base de dados na Base de Dados SQL, na SQL Managed Instance e no Azure Synapse para utilizadores de identidade apenas em nuvem AZure AD, ou aqueles que utilizam identidades híbridas AZURE AD.

Para obter mais informações, consulte [a utilização da autenticação Azure AD multi-factor com base de dados SQL e Sinapse Azure (suporte SSMS para MFA)](authentication-mfa-ssms-overview.md).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Usando uma identidade AD AZure para ligar a partir de uma aplicação de cliente

Os seguintes procedimentos mostram-lhe como ligar a uma Base de Dados SQL com uma identidade AD AZure a partir de uma aplicação do cliente.

### <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Diretório Ativo

Para utilizar a autenticação integrada do Windows, o Diretório Ativo do seu domínio deve ser federado com o Azure Ative Directory, ou deve ser um domínio gerido que está configurado para um único sinal de inscrição sem emenda para autenticação de hash de passagem ou palavra-passe. Para obter mais informações, consulte [o Azure Ative Directory Seamless Single Sign-On](../../active-directory/hybrid/how-to-connect-sso.md).

> [!NOTE]
> [MSAL.NET (Microsoft.Identity.Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) para autenticação integrada do Windows não é suportado para uma única sação sem emenda para a autenticação de hash de passagem e palavra-passe.

A sua aplicação do cliente (ou um serviço) que se conecta à base de dados deve estar a funcionar numa máquina ligada a domínios sob as credenciais de domínio de um utilizador.

Para se ligar a uma base de dados utilizando a autenticação integrada e uma identidade AD Azure, a palavra-chave de autenticação no fio de ligação da base de dados deve ser definida para `Active Directory Integrated` . A amostra de código C# a seguir utiliza ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

A palavra-chave do fio de ligação `Integrated Security=True` não é suportada para ligar à Base de Dados Azure SQL. Ao es fazer uma ligação ODBC, terá de remover espaços e definir a autenticação para 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Autenticação de senha de diretório ativo

Para se ligar a uma base de dados utilizando contas de utilizador de identidade apenas em nuvem Azure AD, ou aqueles que utilizam identidades híbridas AD AZure, a palavra-chave de autenticação deve ser definida para `Active Directory Password` . O fio de ligação deve conter palavras-chave ID/UID do utilizador e password/PWD. A amostra de código C# a seguir utiliza ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Saiba mais sobre os métodos de autenticação AD AZure utilizando as amostras de código de demonstração disponíveis na [Azure AD Authentication GitHub Demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Ficha AD de Azure

Este método de autenticação permite que os serviços de nível médio obtenham [tokens Web JSON (JWT)](../../active-directory/develop/id-tokens.md) para ligar à base de dados na Base de Dados SQL, na SQL Managed Instance, ou na Azure Synapse, obtendo um token da Azure AD. Este método permite vários cenários de aplicação, incluindo identidades de serviço, princípios de serviço e aplicações usando a autenticação baseada em certificados. Deve completar quatro etapas básicas para utilizar a autenticação simbólica Azure:

1. Registe a sua candidatura no Azure Ative Directory e obtenha a identificação do cliente para o seu código.
2. Criar um utilizador de base de dados que represente a aplicação. (Concluído anteriormente no passo 6.)
3. Criar um certificado no computador cliente executa a aplicação.
4. Adicione o certificado como chave para a sua candidatura.

Cadeia de ligação da amostra:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Para mais informações, consulte [o SQL Server Security Blog](/archive/blogs/sqlsecurity/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth). Para obter informações sobre a adição de um certificado, consulte [Começar com autenticação baseada em certificados no Diretório Ativo Azure](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

As seguintes declarações, conecte-se usando a versão 13.1 de sqlcmd, que está disponível no [Centro de Descarregamentos.](https://www.microsoft.com/download/details.aspx?id=53591)

> [!NOTE]
> `sqlcmd` com o `-G` comando não funciona com identidades do sistema, e requer um login principal do utilizador.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshoot-azure-ad-authentication"></a>Ad resolução de problemas Ad

As orientações sobre problemas de resolução de problemas com a autenticação AZure AD podem ser encontradas no seguinte blog: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Próximos passos

- Para uma visão geral de logins, utilizadores, funções de base de dados e permissões na Base de Dados SQL, consulte [Logins, utilizadores, funções de base de dados e contas de utilizador.](logins-create-manage.md)
- Para obter mais informações sobre os principais de bases de dados, veja [Principals (Principais)](/sql/relational-databases/security/authentication-access/principals-database-engine).
- Para obter mais informações sobre as funções de base de dados, veja [Database roles (Funções de base de dados)](/sql/relational-databases/security/authentication-access/database-level-roles).
- Para obter mais informações sobre as regras de firewall na Base de Dados SQL, veja [Regras de firewall da Base de Dados SQL](firewall-configure.md).
- Para obter informações sobre como definir um utilizador convidado AZure AD como administrador AD Azure, consulte [os utilizadores convidados create AD da Azure e definido como um administrador AD Azure](authentication-aad-guest-users.md).
- Para obter informações sobre como servir os principais com o Azure SQL, consulte [utilizadores da Create AZure AD utilizando aplicações AD Azure](authentication-aad-service-principal-tutorial.md)

<!--Image references-->

[11]: ./media/authentication-aad-configure/active-directory-integrated.png
[12]: ./media/authentication-aad-configure/12connect-using-pw-auth2.png
[13]: ./media/authentication-aad-configure/13connect-to-db2.png
