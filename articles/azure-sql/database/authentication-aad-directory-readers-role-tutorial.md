---
title: Atribuir papel de Leitores de Diretório a um grupo AD Azure e gerir atribuições de funções
description: Este artigo guia-o através da capacitação do papel dos Leitores de Diretório utilizando grupos AD Azure para gerir atribuições de funções Azure AD com Azure SQL Database, Azure SQL Managed Instance e Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: d6c447deedbdcc4f2439fc069f368db88b3560b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91278044"
---
# <a name="tutorial-assign-directory-readers-role-to-an-azure-ad-group-and-manage-role-assignments"></a>Tutorial: Atribuir papel de Leitores de Diretório a um grupo AD Azure e gerir atribuições de funções

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> A atribuição do papel **de Diretoria de Leitores** a um grupo neste artigo está em **pré-visualização pública.** 

Este artigo guia-o através da criação de um grupo no Azure Ative Directory (Azure AD), e atribuindo esse grupo o papel [**de Leitores de Diretório.**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) As permissões do Diretório leitores permitem aos proprietários do grupo adicionar membros adicionais ao grupo, tais como uma [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) da Base de [Dados Azure SQL,](sql-database-paas-overview.md) [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md), e [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Isto ignora a necessidade de um [Administrador Global](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) ou Administrador [de Função Privilegiado](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) atribuir diretamente o papel de Leitores de Diretório para cada identidade lógica do servidor Azure SQL no arrendatário.

Este tutorial utiliza a funcionalidade introduzida nos [grupos de nuvem Use para gerir atribuições de funções no Azure Ative Directory (pré-visualização)](../../active-directory/users-groups-roles/roles-groups-concept.md). 

Para obter mais informações sobre os benefícios de atribuir o papel de Leitores de Diretório a um grupo AZure AD para a Azure SQL, consulte o papel de Leitores de [Diretório no Azure Ative Directory para a Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="prerequisites"></a>Pré-requisitos

- Um caso de AD Azure. Para mais informações, consulte [Configure e gerencie a autenticação AZURE AD com a Azure SQL.](authentication-aad-configure.md)
- Uma Base de Dados SQL, SQL Managed Instance, ou Azure Synapse.

## <a name="directory-readers-role-assignment-using-the-azure-portal"></a>Atribuição de função de diretoria de leitores usando o portal Azure

### <a name="create-a-new-group-and-assign-owners-and-role"></a>Criar um novo grupo e atribuir proprietários e papel

1. É necessário um utilizador com permissões [de Administrador Global](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) ou Administrador de [Função Privilegiada](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) para esta configuração inicial.
1. Tenha o utilizador privilegiado a entrar no [portal Azure](https://portal.azure.com).
1. Aceda ao recurso **Azure Ative Directory.** Under **Managed**, vá a **Grupos.** Selecione **Novo grupo** para criar um novo grupo.
1. Selecione **a Segurança** como o tipo de grupo e preencha o resto dos campos. Certifique-se de que as funções AD de definição **podem ser atribuídas ao grupo (Preview)** é comutada para **Sim**. Em seguida, atribua o papel **de leitores do Ad Directory** Azure para o grupo.
1. Atribua os utilizadores Azure AD como proprietários(s) ao grupo que foi criado. Um proprietário do grupo pode ser um utilizador regular de AD sem qualquer papel administrativo Azure AD atribuído. O proprietário deve ser um utilizador que está a gerir a sua Base de Dados SQL, SQL Managed Instance ou Azure Synapse.

   :::image type="content" source="media/authentication-aad-directory-readers-role/new-group.png" alt-text="aad-novo grupo":::

1. Selecione **Criar**

### <a name="checking-the-group-that-was-created"></a>Verificar o grupo que foi criado

> [!NOTE]
> Certifique-se de que o **Tipo de Grupo** é de **Segurança**. Os grupos *Microsoft 365* não são suportados pelo Azure SQL.

Para verificar e gerir o grupo que foi criado, volte ao painel **de grupos** no portal Azure e procure o nome do seu grupo. Proprietários e membros adicionais podem ser adicionados no menu **Proprietários** e **Membros** da definição de **Manage** após a seleção do seu grupo. Também pode rever as **funções atribuídas** para o grupo.

:::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-group-created.png" alt-text="aad-novo grupo":::

### <a name="add-azure-sql-managed-identity-to-the-group"></a>Adicione Azure SQL identidade gerida ao grupo

> [!NOTE]
> Estamos a usar a SQL Managed Instance para este exemplo, mas passos semelhantes podem ser aplicados para a Base de Dados SQL ou Azure Synapse para obter os mesmos resultados.

Para os passos seguintes, o administrador global ou utilizador de administrador de função privilegiada já não é necessário.

1. Inicie sessão no portal Azure como o utilizador que gere a SQL Managed Instance, e é proprietário do grupo criado anteriormente.

1. Encontre o nome do seu recurso **de instância gerido SQL** no portal Azure.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance.png" alt-text="aad-novo grupo":::

   Durante a criação do seu SQL Managed Instance, foi criada uma identidade Azure para o seu exemplo. A identidade criada tem o mesmo nome que o prefixo do seu nome SQL Managed Instance. Pode encontrar o principal de serviço para a sua identidade SQL Managed Instance que criou como uma Aplicação AD Azure seguindo estes passos:

    - Aceda ao recurso **Azure Ative Directory.** Na definição **'Gerir',** selecione **aplicações Enterprise**. O **ID do objeto** é a identidade do caso.
    
    :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance-service-principal.png" alt-text="aad-novo grupo":::

1. Aceda ao recurso **Azure Ative Directory.** Under **Managed**, vá a **Grupos.** Selecione o grupo que criou. Sob a definição **gerida** do seu grupo, selecione **Membros**. **Selecione Adicionar os membros** e adicione o seu principal de serviço SQL Managed Instance como membro do grupo, procurando o nome acima encontrado.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-add-managed-instance-service-principal.png" alt-text="aad-novo grupo":::

> [!NOTE]
> Pode levar alguns minutos para propagar as permissões principais do serviço através do sistema Azure, e permitir o acesso à Azure AD Graph API. Poderá ter de esperar alguns minutos antes de providenciar um administrador AD Azure para a SQL Managed Instance.

### <a name="remarks"></a>Observações

Para a Base de Dados SQL e Azure Synapse, a identidade do servidor pode ser criada durante a criação do servidor lógico Azure SQL ou após a criação do servidor. Para obter mais informações sobre como criar ou definir a identidade do servidor na Base de Dados SQL ou na Azure Synapse, consulte [Ativar os principais serviços para criar utilizadores AD do Azure](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

Para a SQL Managed Instance, o papel **de Leitores** de Diretório deve ser atribuído à identidade de exemplo gerida antes de poder [configurar um administrador AD Azure para o caso gerido](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

A atribuição do papel **de Leitores** de Diretório à identidade do servidor não é necessária para a Base de Dados SQL ou Azure Synapse ao configurar um administrador AD Azure para o servidor lógico. No entanto, para permitir a criação de um objeto AZure AD na Base de Dados SQL ou Azure Synapse em nome de uma aplicação AD Azure, é necessário o papel de Leitores de **Diretório.** Se a função não for atribuída à identidade lógica do servidor SQL, a criação de utilizadores AD Azure em Azure SQL falhará. Para mais informações, consulte [o diretor de serviço do Azure Ative Directory com a Azure SQL.](authentication-aad-service-principal.md)

## <a name="directory-readers-role-assignment-using-powershell"></a>Atribuição de função de diretoria de leitores usando PowerShell

> [!IMPORTANT]
> Um [Administrador Global](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) ou Administrador [privilegiado](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) terá de executar estes passos iniciais. Além do PowerShell, o AD AZure oferece a Microsoft Graph API para [criar um grupo atribuível a papéis em Ad AZure](../../active-directory/users-groups-roles/roles-groups-create-eligible.md#using-microsoft-graph-api).

1. Descarregue o módulo PowerShell de pré-visualização Azure AD utilizando os seguintes comandos. Pode ter de executar o PowerShell como administrador.

    ```powershell
    Install-Module azureadpreview
    Import-Module azureadpreview
    #To verify that the module is ready to use, use the following command:
    Get-Module azureadpreview
    ```

1. Ligue-se ao seu inquilino AZure AD.

    ```powershell
    Connect-AzureAD
    ```

1. Crie um grupo de segurança para atribuir o papel **de Leitores de Diretório.**

    - `DirectoryReaderGroup`, `Directory Reader Group` e pode ser alterado de acordo com a sua `DirRead` preferência.

    ```powershell
    $group = New-AzureADMSGroup -DisplayName "DirectoryReaderGroup" -Description "Directory Reader Group" -MailEnabled $False -SecurityEnabled $true -MailNickName "DirRead" -IsAssignableToRole $true
    $group
    ```

1. Atribua o papel **de Leitores de Diretório** ao grupo.

    ```powershell
    # Displays the Directory Readers role information
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Directory Readers'" 
    $roleDefinition
    ```

    ```powershell
    # Assigns the Directory Readers role to the group
    $roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id
    $roleAssignment
    ```

1. Atribua os proprietários ao grupo.

    - `<username>`Substitua-o pelo utilizador que pretende possuir este grupo. Vários proprietários podem ser adicionados repetindo estes passos.

    ```powershell
    $RefObjectID = Get-AzureADUser -ObjectId "<username>"
    $RefObjectID
    ```

    ```powershell
    $GrOwner = Add-AzureADGroupOwner -ObjectId $group.ID -RefObjectId $RefObjectID.ObjectID
    ```

    Verifique os proprietários do grupo utilizando o seguinte comando:

    ```powershell
    Get-AzureADGroupOwner -ObjectId $group.ID
    ```

    Pode ainda verificar os proprietários do grupo no [portal Azure](https://portal.azure.com). Siga os passos no [Checking do grupo que foi criado.](#checking-the-group-that-was-created)

### <a name="assigning-the-service-principal-as-a-member-of-the-group"></a>Atribuindo o diretor de serviço como membro do grupo

Para os passos seguintes, o administrador global ou utilizador de administrador de função privilegiada já não é necessário.

1. Utilizando um proprietário do grupo, que também gere o recurso Azure SQL, executar o seguinte comando para ligar ao seu AD Azure.

    ```powershell
    Connect-AzureAD
    ```

1. Atribua o diretor de serviço como membro do grupo que foi criado.

    - Substitua `<ServerName>` pelo nome do servidor lógico Azure SQL ou pelo nome da Instância Gerida. Para mais informações, consulte a secção, [Adicione a identidade de serviço Azure SQL ao grupo](#add-azure-sql-managed-identity-to-the-group)

    ```powershell
    # Returns the service principal of your Azure SQL resource
    $miIdentity = Get-AzureADServicePrincipal -SearchString "<ServerName>"
    $miIdentity
    ```

    ```powershell
    # Adds the service principal to the group as a member
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId 
    ```

    O seguinte comando devolverá o ID principal do serviço indicando que foi adicionado ao grupo:

    ```powershell
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId
    ```

## <a name="next-steps"></a>Passos seguintes

- [Papel de Leitores de Diretório no Azure Ative Directory para Azure SQL](authentication-aad-directory-readers-role.md)
- [Tutorial: Criar utilizadores de AD Azure usando aplicações AD Azure](authentication-aad-service-principal-tutorial.md)
- [Configure e gere a autenticação AD AD com Azure SQL](authentication-aad-configure.md)