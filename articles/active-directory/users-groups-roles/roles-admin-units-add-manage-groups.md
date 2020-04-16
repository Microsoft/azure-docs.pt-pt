---
title: Adicionar, remover e listar grupos numa unidade administrativa (pré-visualização) - Diretório Ativo Azure [ Diretório Ativo Azure ] Microsoft Docs
description: Gerir grupos e as suas permissões de funções numa unidade administrativa em Azure Ative Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78f45d9e1ca5b8237779fe096154c83749aa08fe
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428436"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Adicionar e gerir grupos em unidades administrativas no Diretório Ativo do Azure

No Azure Ative Directory (Azure AD), pode adicionar grupos a uma unidade administrativa (UA) para um âmbito de controlo administrativo mais granular.

Para obter medidas para se preparar para utilizar powerShell e Microsoft Graph para gestão de unidades administrativas, consulte [Iniciar -](roles-admin-units-manage.md#getting-started)

## <a name="add-groups-to-an-au"></a>Adicionar grupos a uma UA

### <a name="azure-portal"></a>Portal do Azure

Na pré-visualização, pode atribuir grupos apenas individualmente a uma unidade administrativa. Não existe opção de atribuição a granel de grupos a uma unidade administrativa. Pode atribuir um grupo a uma unidade administrativa de uma das duas vias do portal:

1. A partir da página **de Grupos de > Da AD Azure**

    Abra a página geral dos Grupos em Azure AD e selecione o grupo que precisa de ser atribuído à unidade administrativa. Do lado esquerdo, selecione **unidades administrativas** para listar as unidades administrativas a que o grupo está atribuído. No topo encontrará a opção Atribuir à unidade administrativa e clicar nela dará um painel do lado direito para escolher a unidade administrativa.

    ![atribuir um grupo individualmente a uma unidade administrativa](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. A partir das **unidades administrativas > Da AD Azure > página de Todos os Grupos**

    Abra a lâmina de Todos os Grupos em Unidades Administrativas > AD Azure. Se houver grupos já atribuídos à unidade administrativa, serão exibidos no lado direito. Selecione **Adicionar** na parte superior e um painel direito irá deslizar na listagem dos grupos disponíveis na sua organização Azure AD. Selecione um ou mais grupos a atribuir às unidades administrativas.

    ![selecione uma unidade administrativa e, em seguida, selecione Adicionar membro](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

Neste exemplo, o cmdlet Add-AzureADAdministrativeUnitMember é utilizado para adicionar o grupo à unidade administrativa. A identificação do objeto da unidade administrativa e a identificação do objeto do grupo a adicionar são tomadas como argumento. A secção realçada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref

    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
    }

Exemplo:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
    }

## <a name="list-groups-in-an-au"></a>Grupos de listas numa UA

### <a name="azure-portal"></a>Portal do Azure

Vá a **Azure AD > Unidades Administrativas** no portal. Selecione a unidade administrativa para a qual pretende listar os utilizadores. Por predefinição, **todos os utilizadores** já estão selecionados no painel esquerdo. Selecione **Todos os grupos** e à direita encontrará a lista de grupos que são membros da unidade administrativa selecionada.

![Selecione uma unidade administrativa para eliminar](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

Isto vai ajudá-lo a obter todos os membros da unidade administrativa. Se quiser exibir todos os grupos que são membros da unidade administrativa, pode utilizar o código abaixo:

    foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
    {
    if($member.ObjectType -eq "Group")
    {
    Get-AzureADGroup -ObjectId $member.ObjectId
    }
    }

### <a name="microsoft-graph"></a>Microsoft Graph

    HTTP request
    GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
    Request body
    {}

## <a name="list-aus-for-a-group"></a>Lista AUs para um grupo

### <a name="azure-portal"></a>Portal do Azure

No portal Azure AD, pode abrir os detalhes de um grupo abrindo **Grupos.** Selecione um grupo para abrir o perfil do grupo. Selecione **unidades administrativas** para listar todas as unidades administrativas onde o grupo é membro.

![Lista de unidades administrativas para um grupo](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>Remova um grupo de uma UA

### <a name="azure-portal"></a>Portal do Azure

Há duas formas de remover um grupo de uma unidade administrativa no portal Azure.

Abra > **os Grupos** **AD Azure**e abra o perfil para o grupo que pretende remover da unidade administrativa. Selecione **unidades administrativas** no painel esquerdo para listar todas as unidades administrativas onde o grupo é membro. Selecione a unidade administrativa de que pretende retirar o grupo e, em seguida, **selecione Remover da unidade administrativa**.

![Retire um grupo de uma unidade administrativa](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

Em alternativa, pode ir às > **unidades administrativas** **da AD Azure**e selecionar a unidade administrativa onde o grupo é membro. Selecione **Grupos** no painel esquerdo para listar os grupos membros. Selecione o grupo a retirar da unidade administrativa e, em seguida, **selecione Remover grupos**.

![Grupos de lista sinos numa unidade administrativa](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>Passos seguintes

- [Atribuir um papel a uma unidade administrativa](roles-admin-units-assign-roles.md)
- [Gerir utilizadores numa unidade administrativa](roles-admin-units-add-manage-users.md)
