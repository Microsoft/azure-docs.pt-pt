---
title: Adicione, remova e liste grupos numa unidade administrativa - Azure Ative Directory / Microsoft Docs
description: Gerir grupos e as suas permissões de funções numa unidade administrativa no Azure Ative Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 10/07/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec566fecb0dd14a4e56a2a3b9a59745bfe549faf
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377992"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Adicionar e gerir grupos em unidades administrativas no Azure Ative Directory

No Azure Ative Directory (Azure AD), pode adicionar grupos a uma unidade administrativa (AU) para um âmbito administrativo de controlo mais granular.

Para obter os passos que se preparem para utilizar o PowerShell e o Microsoft Graph para a gestão da unidade administrativa, consulte [Get start](admin-units-manage.md#get-started).

## <a name="add-groups-to-an-au"></a>Adicionar grupos a uma AU

### <a name="azure-portal"></a>Portal do Azure

Pode atribuir grupos individualmente a uma unidade administrativa. Não há opção de atribuir grupos a granel a uma unidade administrativa. Pode atribuir um grupo a uma unidade administrativa de uma das duas formas no portal:

1. Da página **Azure AD > Grupos**

    Abra a página geral dos Grupos em Azure AD e selecione o grupo que precisa de ser atribuído à unidade administrativa. Do lado esquerdo, selecione **unidades administrativas** para listar as unidades administrativas a que o grupo está designado. No topo encontrará a opção Atribua à unidade administrativa e clica-lhe no lado direito para escolher a unidade administrativa.

    ![atribuir um grupo individualmente a uma unidade administrativa](./media/admin-units-add-manage-groups/assign-to-group-1.png)

1. A partir da **Azure AD > Unidades Administrativas > página de Todos os Grupos**

    Abra a lâmina de todos os grupos em Azure AD > Unidades Administrativas. Se houver grupos já atribuídos à unidade administrativa, serão exibidos no lado direito. **Selecione Adicionar** na parte superior e um painel direito irá deslizar na listagem dos grupos disponíveis na sua organização AZure AD. Selecione um ou mais grupos a atribuir às unidades administrativas.

    ![selecione uma unidade administrativa e, em seguida, selecione Adicionar membro](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

Neste exemplo, o Add-AzureADMSAdministrativeUnitMember cmdlet é utilizado para adicionar o grupo à unidade administrativa. A identificação do objeto da unidade administrativa e a identificação do objeto do grupo a adicionar são tomadas como argumento. A secção realçada pode ser alterada conforme necessário para o ambiente específico.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref

Request body
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{id}"
}
```

Exemplo:

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="list-groups-in-an-au"></a>Grupos de listas numa AU

### <a name="azure-portal"></a>Portal do Azure

Vá ao **Azure AD > Unidades Administrativas** no portal. Selecione a unidade administrativa para a qual pretende listar os utilizadores. Por predefinição, **todos os utilizadores** já estão selecionados no painel esquerdo. Selecione **Todos os grupos** e à direita encontrará a lista de grupos que são membros da unidade administrativa selecionada.

![Grupos de lista numa unidade administrativa](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId
```

Isto vai ajudá-lo a obter todos os membros da unidade administrativa. Se pretender exibir todos os grupos que são membros da unidade administrativa, pode utilizar o corte de código abaixo:

```http
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
HTTP request
GET /directory/administrativeUnits/{Admin id}/members/$/microsoft.graph.group
Request body
{}
```

## <a name="list-aus-for-a-group"></a>Lista de AUs para um grupo

### <a name="azure-portal"></a>Portal do Azure

No portal AD AZure, pode abrir os detalhes de um grupo abrindo **Grupos**. Selecione um grupo para abrir o perfil do grupo. Selecione **unidades administrativas** para listar todas as unidades administrativas onde o grupo é membro.

![Listar unidades administrativas para um grupo](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/v1.0/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-au"></a>Remova um grupo de uma AU

### <a name="azure-portal"></a>Portal do Azure

Há duas formas de remover um grupo de uma unidade administrativa no portal Azure.

- Remover de uma visão geral do grupo

  1. Abra os grupos **AD Azure**  >  **Groups** e abra o perfil para o grupo que pretende remover de uma unidade administrativa.
  1. Selecione **unidades administrativas** no painel esquerdo para listar todas as unidades administrativas onde o grupo é membro. Selecione a unidade administrativa de onde pretende remover o grupo e, em seguida, **selecione Remover da unidade administrativa**.

    ![Remova um grupo de uma unidade administrativa](./media/admin-units-add-manage-groups/group-au-remove.png)

- Remover de uma unidade administrativa

  1. Abra as unidades administrativas **Azure AD**  >  **Administrative units** e selecione a unidade administrativa onde o grupo é membro.
  1. Selecione **grupos** no painel esquerdo para listar os grupos membros.
  1. Selecione o grupo a retirar da unidade administrativa e, em seguida, selecione **Remover grupos**.

    ![Grupos de lista numa unidade administrativa](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/v1.0/directory/AdministrativeUnits/<adminunit-id>/members/<group-id>/$ref
```

## <a name="next-steps"></a>Passos seguintes

- [Atribuir um papel a uma unidade administrativa](admin-units-assign-roles.md)
- [Gerir utilizadores numa unidade administrativa](admin-units-add-manage-users.md)
