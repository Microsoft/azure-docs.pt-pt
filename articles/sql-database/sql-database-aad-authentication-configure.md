---
title: Configurar a autenticação do Azure Active Directory - SQL | Documentos da Microsoft
description: Saiba como ligar à base de dados SQL, instância gerida e SQL Data Warehouse com a autenticação do Active Directory do Azure - depois de configurar o Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/04/2019
ms.openlocfilehash: e4ccb9be5d13ea72086fbaae2ffb2ec63ad55786
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57340325"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Configurar e gerir a autenticação do Azure Active Directory com o SQL

Este artigo mostra-lhe como criar e preencher do Azure AD e, em seguida, utilizar o Azure AD com o Azure [base de dados SQL](sql-database-technical-overview.md), [instância gerida](sql-database-managed-instance.md), e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para uma descrição geral, consulte [Azure Active Directory Authentication](sql-database-aad-authentication.md).

> [!NOTE]
> Este artigo aplica-se ao servidor SQL do Azure e a base de dados SQL e SQL Data Warehouse bases de dados que são criadas no servidor SQL do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.
> [!IMPORTANT]  
> Ligar ao SQL Server em execução numa VM do Azure não é suportada através de uma conta do Azure Active Directory. Utilize um conta do Active Directory do domínio em vez disso.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-and-populate-an-azure-ad"></a>Criar e preencher um Azure AD

Criar um Azure AD e preenchê-lo com os utilizadores e grupos. O Azure AD pode ser o Azure AD inicial domínio gerido. O Azure AD também pode ser no local Active Directory Domain Services que está Federado com o Azure AD.

Para obter mais informações, veja [Integrating your on-premises identities with Azure Active Directory (Integrar as identidades no local ao Azure Active Directory)](../active-directory/hybrid/whatis-hybrid-identity.md), [Add your own domain name to Azure AD (Adicionar o seu próprio nome de domínio ao Azure AD)](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure now supports federation with Windows Server Active Directory (O Microsoft Azure suporta agora a federação com o Windows Server Active Directory)](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administering your Azure AD directory (Administrar o seu diretório do Azure AD)](../active-directory/fundamentals/active-directory-administer.md), [Manage Azure AD using Windows PowerShell (Gerir o Azure AD com o Windows PowerShell)](/powershell/azure/overview?view=azureadps-2.0) e [Hybrid Identity Required Ports and Protocols (Portas e Protocolos Necessários para a Identidade Híbrida)](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associar ou adicionar uma subscrição do Azure ao Azure Active Directory

1. Associe a sua subscrição do Azure ao Azure Active Directory, tornando o diretório de um diretório de confiança para a subscrição do Azure que aloja a base de dados. Para obter detalhes, consulte [subscrições do Azure como estão associadas com o Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
2. Utilize o alternador de diretório no portal do Azure para mudar para a subscrição associada a domínio.

   **Informações adicionais:** Cada subscrição do Azure tem uma relação de confiança com uma instância do Azure AD. Tal significa que confia nesse diretório para autenticar utilizadores, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório, mas uma subscrição apenas pode confiar num diretório. Esta relação de confiança entre uma subscrição e um diretório é diferente da relação de uma subscrição com todos os outros recursos no Azure (sites, bases de dados, e assim sucessivamente), que são mais como recursos subordinados de uma subscrição. Em caso de expiração de uma subscrição, será interrompido o acesso a esses outros recursos associados à subscrição também. No entanto, o diretório permanece no Azure e pode associar outra subscrição a esse diretório e continuar a gerir os utilizadores do diretório. Para obter mais informações sobre os recursos, consulte [compreender o acesso a recursos no Azure](../active-directory/active-directory-b2b-admin-add-users.md). Para obter mais informações sobre esta fidedigno Consulte de relação [como associar ou adicionar uma subscrição do Azure ao Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Criar um administrador do Azure AD para o servidor SQL do Azure

Cada servidor de SQL do Azure (que aloja uma base de dados SQL ou SQL Data Warehouse) começa com uma conta de administrador de servidor único que é o administrador de todo o servidor SQL do Azure. Um administrador do SQL Server segundo tem de ser criado, que é uma conta do Azure AD. Este principal é criado como um utilizador de base de dados contidos na base de dados mestra. Como administradores, as contas de administrador de servidor são membros do **db_owner** função em todos os utilizadores de base de dados e introduza cada base de dados de utilizador como o **dbo** utilizador. Para obter mais informações sobre as contas de administrador do servidor, consulte [gerir bases de dados e inícios de sessão na base de dados do Azure SQL](sql-database-manage-logins.md).

Ao utilizar o Azure Active Directory com a georreplicação, o Azure Active Directory tem de ser configurado de principal e servidores secundários. Se um servidor não tem um administrador do Azure Active Directory, em seguida, inícios de sessão do Azure Active Directory e os utilizadores recebem um "não é possível ligar" para erro de servidor.

> [!NOTE]
> Os utilizadores que não se baseiam numa conta do Azure AD (incluindo a conta de administrador do servidor SQL do Azure), não é possível criar utilizadores do Azure baseada no AD, porque não têm permissão para validar os utilizadores de base de dados proposto com o Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Aprovisionar um administrador do Azure Active Directory da sua instância gerida

> [!IMPORTANT]
> Apenas siga estes passos se estiver a aprovisionar uma instância gerida. Esta operação só pode ser executada pelo Administrador Global/da empresa no Azure AD. Passos seguintes descrevem o processo de conceder permissões a utilizadores com privilégios diferentes no diretório.

Sua instância gerida necessita de permissões para ler do Azure AD com êxito a realizar tarefas como autenticação de utilizadores através da associação de grupo de segurança ou criação de novos utilizadores. Para isso funcionar, terá de conceder permissões para a instância gerida do Azure AD de ler. Existem duas formas de fazê-lo: partir do Portal e PowerShell. Os seguintes passos ambos os métodos.

1. No portal do Azure, no canto superior direito, selecione a ligação a uma lista de diretórios Active Directory possível pendente.
2. Escolha o Active Directory correto como padrão do Azure AD.

   Este passo liga a subscrição associada do Active Directory com a instância gerida, certificar-se de que a mesma subscrição é utilizada para o Azure AD e a instância gerida.
3. Navegue para a instância gerida e selecione aquele que pretende utilizar para a integração do Azure AD.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Selecione a faixa na parte superior da página de administração do Active Directory e conceder permissão ao utilizador atual. Se estiver conectado como Administrador Global/da empresa no Azure AD, pode fazê-lo a partir do portal do Azure ou utilizar o PowerShell com o script abaixo.

    ![conceder permissões-portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```PowerShell
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

5. Após a operação é concluída com êxito, a seguinte notificação será apresentada no canto superior direito:

    ![exito](./media/sql-database-aad-authentication/success.png)

6. Agora pode escolher o seu administrador do Azure AD da sua instância gerida. Para isso, na página de administração do Active Directory, selecione **definir administrador** comando.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Na página de administrador do AAD, procure um utilizador, selecione o utilizador ou grupo para ser um administrador e, em seguida, selecione **selecione**.

   Página de administração do Active Directory mostra todos os membros e grupos do Active Directory. Não não possível selecionar utilizadores ou grupos que são bloqueados porque eles não são suportados como administradores do Azure AD. Ver a lista de administradores suportados no [funcionalidades do Azure AD e limitações](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Controlo de acesso baseado em funções (RBAC) aplica-se apenas ao portal do Azure e não é propagado para o SQL Server.

    ![add-admin](./media/sql-database-aad-authentication/add-admin.png)

8. Na parte superior da página de administração do Active Directory, selecione **guardar**.

    ![guardar](./media/sql-database-aad-authentication/save.png)

    O processo de alterar o administrador pode demorar vários minutos. Em seguida, o administrador do novo é apresentado na caixa de administração do Active Directory.

Depois de aprovisionar um administrador do Azure AD da sua instância gerida, pode começar a criar o Azure AD principais de servidor (inícios de sessão) (**pré-visualização pública**) com o <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> sintaxe. Para obter mais informações, consulte [descrição geral de instância geridas](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Para mais tarde remover um administrador, na parte superior da página de administração do Active Directory, selecione **remover administrador**e, em seguida, selecione **guardar**.

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Aprovisionar um administrador do Azure Active Directory para o seu servidor de base de dados do Azure SQL

> [!IMPORTANT]
> Apenas siga estes passos se estiver a aprovisionar um servidor de base de dados do Azure SQL ou armazém de dados.

Os dois procedimentos seguintes mostram-lhe como aprovisionar um administrador do Azure Active Directory para o seu servidor SQL do Azure no portal do Azure e com o PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. Na [portal do Azure](https://portal.azure.com/), no canto superior direito, selecione a ligação a uma lista de diretórios Active Directory possível pendente. Escolha o Active Directory correto como padrão do Azure AD. Este passo liga o Active Directory associado à subscrição com o servidor SQL do Azure, tornando-se de que a mesma subscrição é utilizada para o Azure AD e o SQL Server. (O servidor SQL do Azure pode ser a alojar a base de dados do Azure SQL ou armazém de dados SQL do Azure.) ![ad escolha][8]

2. Na faixa da esquerda, selecione **todos os serviços**e no tipo de filtro na **do SQL server**. Selecione **servidores Sql**.

    ![sqlservers.png](media/sql-database-aad-authentication/sqlservers.png)

    >[!NOTE]
    > Nesta página, antes de selecionar **servidores SQL**, pode selecionar o **estrela** junto ao nome para *favorito* a categoria e adicione **servidores SQL**na barra de navegação à esquerda.

3. No **SQL Server** página, selecione **administrador do Active Directory**.
4. Na **administrador do Active Directory** página, selecione **definir administrador**.  ![selecione active directory](./media/sql-database-aad-authentication/select-active-directory.png)  

5. Na **Adicionar administrador** página, procure por um utilizador, selecione o utilizador ou grupo para ser um administrador e, em seguida, selecione **selecione**. (A página de administração do Active Directory mostra todos os membros e grupos do Active Directory. Não não possível selecionar utilizadores ou grupos que são bloqueados porque não são suportadas como administradores do Azure AD. (Ver a lista de administradores suportados no **funcionalidades do Azure AD e limitações** secção [utilizar do Azure Active Directory a autenticação para a autenticação com base de dados SQL ou SQL Data Warehouse](sql-database-aad-authentication.md).) Controlo de acesso baseado em funções (RBAC) aplica-se apenas ao portal e não é propagado para o SQL Server.
    ![Selecione o administrador](./media/sql-database-aad-authentication/select-admin.png)  

6. Na parte superior a **administrador do Active Directory** página, selecione **guardar**.
    ![Guardar admin](./media/sql-database-aad-authentication/save-admin.png)

O processo de alterar o administrador pode demorar vários minutos. Em seguida, o administrador do novo é apresentado na **administrador do Active Directory** caixa.

   > [!NOTE]
   > Ao definir o administrador do Azure AD, o novo nome de administrador (utilizador ou grupo) não pode já estar presente na base de dados mestra virtual como um utilizador de autenticação do SQL Server. Se estiver presente, a configuração de administração do Azure AD irá falhar; a reverter a sua criação e indicando que tal um administrador (nome) já existe. Uma vez que tal um utilizador de autenticação do SQL Server não faz parte do Azure AD, falha qualquer esforço para ligar ao servidor com a autenticação do Azure AD.

Para mais tarde remover um administrador, na parte superior a **administrador do Active Directory** página, selecione **remover administrador**e, em seguida, selecione **guardar**.

### <a name="powershell"></a>PowerShell

Para executar os cmdlets do PowerShell, terá de ter o Azure PowerShell instalada e em execução. Para informações detalhadas, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Para aprovisionar um administrador do Azure AD, execute os seguintes comandos do Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets utilizados para aprovisionar e gerir administradores do Azure AD:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Aprovisiona um administrador do Azure Active Directory para o servidor SQL do Azure ou Azure SQL Data Warehouse. (Tem de ser da subscrição atual.) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Remove um administrador do Azure Active Directory para o servidor SQL do Azure ou Azure SQL Data Warehouse. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Devolve informações sobre um administrador do Azure Active Directory configurado atualmente para o servidor SQL do Azure ou o Azure SQL Data Warehouse. |

Utilize o comando de PowerShell get-help para obter mais informações para cada um desses comandos, por exemplo ``get-help Set-AzSqlServerActiveDirectoryAdministrator``.

O seguinte script aprovisiona um grupo de administrador do Azure AD com o nome **DBA_Group** (ID de objeto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) para o **demo_server** servidor num grupo de recursos com o nome **grupo-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

O **DisplayName** parâmetro de entrada aceita o nome a apresentar do Azure AD ou o nome do Principal de utilizador. Por exemplo, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Para grupos do Azure AD apenas o Azure AD é suportado o nome a apresentar.

> [!NOTE]
> O comando do PowerShell do Azure ```Set-AzSqlServerActiveDirectoryAdministrator``` não o impede de aprovisionamento de administradores do Azure AD para utilizadores não suportados. Um utilizador não suportado pode ser aprovisionado, mas não é possível ligar à base de dados.

O exemplo seguinte utiliza o opcional **ObjectID**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> O Azure AD **ObjectID** é necessária quando a **DisplayName** não é exclusivo. Para obter o **ObjectID** e **DisplayName** valores, utilize a secção de Active Directory do Portal clássico do Azure e ver as propriedades de um utilizador ou grupo.

O exemplo seguinte devolve informações sobre o atual administradores do Azure AD para o Azure SQL server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

O exemplo seguinte remove o administrador do Azure AD:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Também pode aprovisionar um administrador do Azure Active Directory com as APIs REST. Para obter mais informações, consulte [referência de API do REST de gestão do serviço e operações para operações de banco de dados SQL do Azure para a base de dados do Azure SQL](https://docs.microsoft.com/rest/api/sql/)

### <a name="cli"></a>CLI  

Também pode aprovisionar um administrador do Azure AD ao chamar os seguintes comandos da CLI:
| Comando | Descrição |
| --- | --- |
|[az sql server ad-admin create](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) |Aprovisiona um administrador do Azure Active Directory para o servidor SQL do Azure ou Azure SQL Data Warehouse. (Tem de ser da subscrição atual.) |
|[az sql server ad-admin delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) |Remove um administrador do Azure Active Directory para o servidor SQL do Azure ou Azure SQL Data Warehouse. |
|[az sql server ad-admin list](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) |Devolve informações sobre um administrador do Azure Active Directory configurado atualmente para o servidor SQL do Azure ou o Azure SQL Data Warehouse. |
|[az sql server ad-admin update](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) |Atualiza o administrador do Active Directory para um servidor SQL do Azure ou o Azure SQL Data Warehouse. |

Para obter mais informações sobre os comandos da CLI, veja [SQL - az sql](https://docs.microsoft.com/cli/azure/sql/server).  

## <a name="configure-your-client-computers"></a>Configurar os seus computadores de cliente

Em todos os computadores de cliente, a partir do qual os seus aplicativos ou os utilizadores ligam à base de dados do Azure SQL ou o Azure SQL Data Warehouse com identidades do Azure AD, tem de instalar o seguinte software:

- .NET framework 4.6 ou posterior a partir [ https://msdn.microsoft.com/library/5a4x27ek.aspx ](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- O Azure Active Directory Authentication Library para SQL Server (**ADALSQL. DLL**) está disponível em vários idiomas (x86 e amd64) do Centro de download na [Microsoft Active Directory Authentication Library para Microsoft SQL Server](https://www.microsoft.com/download/details.aspx?id=48742).

Pode satisfazer estes requisitos por:

- Instalar qualquer um [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) ou [SQL Server Data Tools para Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) satisfaz os requisitos do .NET Framework 4.6.
- Versão do SSMS instalações x86 de **ADALSQL. DLL**.
- SSDT instala a versão de amd64 do **ADALSQL. DLL**.
- O Visual Studio mais recente a partir [transferências do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) satisfaz os requisitos do .NET Framework 4.6, mas não instala a versão necessária amd64 do **ADALSQL. DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Criar utilizadores de base de dados contidos na base de dados mapeado para identidades do Azure AD

>[!IMPORTANT]
>Principais de servidor do Azure AD (inícios de sessão) suporta agora a instância gerida (**pré-visualização pública**), que permite-lhe criar inícios de sessão de utilizadores, grupos ou aplicações do Azure AD. Principais de servidor do Azure AD (inícios de sessão) fornece a capacidade para autenticar a sua instância gerida, sem a necessidade dos utilizadores de base de dados a ser criado como um utilizador de base de dados contida. Para obter mais informações, consulte [descrição geral de instância geridas](sql-database-managed-instance.md#azure-active-directory-integration). Para a sintaxe sobre a criação do Azure AD de entidades de segurança do servidor (inícios de sessão), consulte <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

Autenticação do Azure Active Directory requer que os utilizadores da base de dados ser criadas como usuários do banco de dados independente. Um utilizador de base de dados contida, com base numa identidade do Azure AD, é um utilizador de base de dados que não tem um início de sessão na base de dados mestra, e que é mapeado para uma identidade no diretório do Azure AD que estão associado com a base de dados. A identidade do Azure AD pode ser uma conta de utilizador individual ou um grupo. Para obter mais informações sobre os utilizadores de base de dados contida, consulte [tomada de utilizadores de base de dados contidas sua base de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Os utilizadores de base de dados (com exceção dos administradores) não não possível criar com o portal do Azure. Funções RBAC não são propagadas para o SQL Server, base de dados SQL ou SQL Data Warehouse. Funções RBAC do Azure são utilizadas para gerir recursos do Azure e não se aplicam às permissões de base de dados. Por exemplo, o **contribuinte do SQL Server** função não concede acesso para ligar à base de dados SQL ou SQL Data Warehouse. A permissão de acesso deve ser concedida diretamente na base de dados com instruções Transact-SQL.
> [!WARNING]
> Carateres especiais, como dois pontos `:` ou e comercial `&` quando incluídas como nomes de utilizador nas instruções T-SQL CREATE LOGIN e criar o utilizador não são suportados.

Criar utilizador do Azure baseada no AD contidos da base de dados (que não seja o administrador do servidor que é proprietário da base de dados), ligar à base de dados com uma identidade do Azure AD, como um utilizador com, pelo menos, o **ALTER ANY utilizador** permissão. Em seguida, utilize a seguinte sintaxe de Transact-SQL:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* pode ser o nome do principal de utilizador de um utilizador do Azure AD ou o nome a apresentar para um grupo do Azure AD.

**Exemplos:** Para criar uma base de dados contida o utilizador que representa um Azure AD federados ou geridos de utilizador de domínio:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Para criar um utilizador de base de dados contida que representa um Azure AD ou federadas o grupo de domínio, forneça o nome a apresentar de um grupo de segurança:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Para criar um utilizador de base de dados contida que representa uma aplicação que estabelece ligação utilizar um token do Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!TIP]
> Não é possível criar diretamente um utilizador de um Azure Active Directory que não seja o Azure Active Directory que está associado a sua subscrição do Azure. No entanto, os membros de outros diretórios do Active Directory que são importados os utilizadores no Active Directory associados (conhecido como utilizadores externos) podem ser adicionados a um grupo do Active Directory no inquilino do Active Directory. Ao criar um utilizador de base de dados contida para esse grupo do AD, os utilizadores do Active Directory externo conseguirem aceder à base de dados SQL.

Para obter mais informações sobre a criação de contidos bases de dados de utilizadores com base em identidades do Azure Active Directory, consulte [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Remover o administrador do Azure Active Directory para o servidor SQL do Azure impede que qualquer utilizador de autenticação do Azure AD ligar ao servidor. Se necessário, inutilizável utilizadores do Azure AD podem ser removidos manualmente por um administrador de base de dados SQL.
> [!NOTE]
> Se receber um **o tempo limite da ligação excedido**, poderá ter de definir o `TransparentNetworkIPResolution` parâmetro da cadeia de ligação para false. Para obter mais informações, consulte [problema de tempo limite de ligação com o .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Quando cria um base de dados do utilizador, o que o utilizador recebe a **CONNECT** permissão e pode ligar a esse banco de dados como um membro dos **público** função. Inicialmente as permissões apenas disponíveis para o utilizador são todas as permissões concedidas para o **público** de função ou todas as permissões concedidas a todos os grupos do Azure AD, que são membros de. Depois de aprovisionar um utilizador de base de dados contidas com base no AD do Azure, pode conceder ao utilizador permissões adicionais, da mesma forma como pode conceder permissão a qualquer outro tipo de utilizador. Normalmente, conceder permissões a funções de base de dados e adicionar utilizadores às funções. Para obter mais informações, consulte [Noções básicas de permissão do motor de base de dados](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obter mais informações sobre as funções de base de dados SQL especiais, consulte [gerir bases de dados e inícios de sessão na base de dados do Azure SQL](sql-database-manage-logins.md).
Uma conta de utilizador de domínio federado que é importada para um domínio gerido como um utilizador externo, tem de utilizar a identidade de domínio gerido.

> [!NOTE]
> Utilizadores do Azure AD são marcados nos metadados da base de dados com o tipo E (EXTERNAL_USER) e para grupos com o tipo de X (EXTERNAL_GROUPS). Para obter mais informações, consulte [database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Ligar ao armazém de dados ou base de dados de utilizador com o SSMS ou o SSDT  

Para confirmar que o administrador do Azure AD está devidamente configurado, ligue para o **mestre** da base de dados com a conta de administrador do Azure AD.
Para aprovisionar o utilizador do Azure baseada no AD contidos da base de dados (que não seja o administrador do servidor que é proprietário da base de dados), ligar à base de dados com uma identidade do Azure AD que tenha acesso à base de dados.

> [!IMPORTANT]
> Suporte para a autenticação do Azure Active Directory está disponível com [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) no Visual Studio 2015. O lançamento de Agosto de 2016 do SSMS também inclui suporte para autenticação do Active Directory Universal, que permite aos administradores exigir autenticação Multifator com uma chamada telefónica, mensagem de texto, smart cards com pin ou notificação de aplicação móvel.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Utilizar uma identidade do Azure AD para ligar com o SSMS ou o SSDT

Os procedimentos seguintes mostram como ligar a uma base de dados SQL com uma identidade do Azure AD com o SQL Server Management Studio ou as ferramentas de base de dados do SQL Server.

### <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Active Directory

Utilize este método se tiver sessão iniciada Windows com as suas credenciais do Azure Active Directory de um domínio federado.

1. Iniciar o Management Studio ou as ferramentas de dados e, no **ligar ao servidor** (ou **ligar ao motor de base de dados**) caixa de diálogo a **autenticação** caixa, selecione  **Active Directory - integrado**. Nenhuma palavra-passe é necessária ou pode ser introduzida porque suas credenciais existentes serão apresentadas para a ligação.

    ![Selecionar a autenticação integrada do AD][11]
2. Selecione o **opções** botão e, no **propriedades de ligação** página o **ligar à base de dados** , escreva o nome da base de dados de utilizador que pretende ligar a. (A **ID de inquilino ou nome de domínio do AD**"opção só é suportada para **Universal com ligação de MFA** opções, caso contrário, ele está esbatido.)  

    ![Selecione o nome de base de dados][13]

## <a name="active-directory-password-authentication"></a>Autenticação por palavra-passe do Active Directory

Utilize este método quando estabelecer ligação com um nome do principal do Azure AD com o Azure AD o domínio gerido. Também pode utilizar para contas federadas sem acesso ao domínio, por exemplo, ao trabalhar remotamente.

Utilize este método para autenticar para o SQL DB/DW, com o Azure AD nativos ou federadas utilizadores do Azure AD. Um utilizador nativo é uma explicitamente criado no Azure AD e a ser autenticado utilizando o nome de utilizador e palavra-passe, embora um utilizador federado é um utilizador do Windows com um domínio está Federado com o Azure AD. Este segundo método (usando o utilizador e palavra-passe) pode ser utilizado quando um usuário deseja usar as suas credenciais do windows, mas sua máquina local não está associada com o domínio (por exemplo, através de um acesso remoto). Neste caso, um utilizador do Windows pode indicar a respetiva conta de domínio e palavra-passe e pode autenticar-se para o SQL DB/DW utilizando credenciais federadas.

1. Iniciar o Management Studio ou as ferramentas de dados e, no **ligar ao servidor** (ou **ligar ao motor de base de dados**) caixa de diálogo a **autenticação** caixa, selecione  **O Active Directory - palavra-passe**.
2. Na **nome de utilizador** , escreva o nome de utilizador do Azure Active Directory no formato **username@domain.com**. Os nomes de utilizador tem de ser uma conta do Azure Active Directory ou uma conta de um domínio a federar com o Azure Active Directory.
3. Na **palavra-passe** caixa, escreva a palavra-passe de utilizador para a conta do Azure Active Directory ou federado a conta de domínio.

    ![Selecionar a autenticação de palavra-passe do AD][12]
4. Selecione o **opções** botão e, no **propriedades de ligação** página o **ligar à base de dados** , escreva o nome da base de dados de utilizador que pretende ligar a. (Veja o gráfico na opção anterior).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Utilizar uma identidade do Azure AD para ligar a partir de uma aplicação cliente

Os procedimentos seguintes mostram como ligar a uma base de dados SQL com uma identidade do Azure AD de um aplicativo cliente.

### <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Active Directory

Para utilizar a autenticação integrada do Windows, Active Directory do seu domínio tem de ser federado no Azure Active Directory. A aplicação de cliente (ou um serviço) ligar à base de dados deve estar em execução num computador associado a um domínio com credenciais de domínio de um utilizador.

Para ligar a uma base de dados através da autenticação integrada e uma identidade do Azure AD, a palavra-chave de autenticação na cadeia de ligação de base de dados tem de ser definida para o Active Directory integrado. O seguinte exemplo de código do c# usa ADO .NET.

```C#
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

A palavra-chave da cadeia de ligação ``Integrated Security=True`` não é suportada para ligar à base de dados do Azure SQL. Ao fazer uma conexão ODBC, terá de remover espaços e defina a autenticação para 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Autenticação por palavra-passe do Active Directory

Para ligar a uma base de dados através da autenticação integrada e uma identidade do Azure AD, a palavra-chave de autenticação tem de ser definida para a palavra-passe de diretório Active Directory. A cadeia de ligação tem de conter ID/UID de utilizador e palavras-chave de palavra-passe/PWD e valores. O seguinte exemplo de código do c# usa ADO .NET.

```C#
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Saiba mais sobre os métodos de autenticação do Azure AD com os exemplos de código de demonstração disponíveis em [demonstração de GitHub de autenticação do Azure AD](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Tokens do Azure AD

Este método de autenticação permite que os serviços de camada intermediária ligar à base de dados do Azure SQL ou o Azure SQL Data Warehouse, obtendo um token do Azure Active Directory (AAD). Permite cenários sofisticados, incluindo autenticação baseada em certificados. Tem de concluir quatro etapas básicas para utilizar a autenticação de token do Azure AD:

1. Registar a sua aplicação no Azure Active Directory e obter o id de cliente para o seu código.
2. Crie um utilizador de base de dados que representa o aplicativo. (Concluído anteriormente no passo 6).
3. Crie um certificado nas execuções de computador cliente a aplicação.
4. Adicione o certificado como uma chave para a sua aplicação.

Cadeia de ligação de exemplo:

```c#
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Para obter mais informações, consulte [Blog de segurança do SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Para obter informações sobre como adicionar um certificado, consulte [introdução à autenticação baseada em certificados no Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

As seguintes instruções, ligar através da versão 13.1 de sqlcmd, que está disponível a partir da [Centro de transferências](https://go.microsoft.com/fwlink/?LinkID=825643).

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
