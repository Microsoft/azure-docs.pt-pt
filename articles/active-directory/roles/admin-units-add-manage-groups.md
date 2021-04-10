---
title: Adicione, remova e liste grupos numa unidade administrativa - Azure Ative Directory | Microsoft Docs
description: Gerir grupos e as suas permissões de função numa unidade administrativa no Azure Ative Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 03/10/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: f88a0818d93c33b6265cc8c695479d2a42678ada
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103011039"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Adicionar e gerir grupos numa unidade administrativa no Azure Ative Directory

No Azure Ative Directory (Azure AD), pode adicionar grupos a uma unidade administrativa para um âmbito administrativo de controlo mais granular.

Para preparar a utilização do PowerShell e do Microsoft Graph para a gestão da unidade administrativa, consulte [Começar](admin-units-manage.md#get-started).

## <a name="add-groups-to-an-administrative-unit"></a>Adicionar grupos a uma unidade administrativa

Pode adicionar grupos a uma unidade administrativa utilizando o portal Azure, PowerShell ou Microsoft Graph.

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Só pode atribuir grupos individuais a uma unidade administrativa. Não há opção de atribuir grupos como uma operação a granel. No portal Azure, pode atribuir um grupo a uma unidade administrativa de duas formas:

* Do **painel grupo:**

  1. No portal Azure, vá ao **Azure AD.**
  1. Selecione **Grupos** e, em seguida, selecione o grupo que pretende atribuir à unidade administrativa. 
  1. No painel esquerdo, selecione **unidades administrativas** para apresentar uma lista das unidades administrativas a que o grupo está designado. 

     ![Screenshot da ligação "Atribuir à unidade administrativa" no painel "Unidades Administrativas".](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. Selecione **Atribuir à unidade administrativa**.
  1. No painel direito, selecione a unidade administrativa.

* Das **unidades**  >  **administrativas todos os grupos** panorâmicas:

  1. No portal Azure, vá ao **Azure AD.**
  
  1. No painel esquerdo, selecione **unidades administrativas** e, em seguida, selecione **Todos os Grupos**. 
     Os grupos já designados para a unidade administrativa são apresentados no painel direito. 

  1. No painel **grupo,** **selecione Adicionar**.
    O painel direito lista todos os grupos disponíveis na sua organização Azure AD. 

     ![Screenshot do botão "Adicionar" para adicionar um grupo a uma unidade administrativa.](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. Selecione um ou mais grupos a atribuir à unidade administrativa e, em seguida, selecione o botão **Selecione.**

### <a name="use-powershell"></a>Utilizar o PowerShell

No exemplo seguinte, utilize o `Add-AzureADMSAdministrativeUnitMember` cmdlet para adicionar o grupo à unidade administrativa. A identificação do objeto da unidade administrativa e a identificação do objeto do grupo a adicionar são tomadas como argumentos. Altere a secção realçada conforme necessário para o seu ambiente específico.


```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>Use o Gráfico microsoft

Execute os seguintes comandos:

Pedir

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Corpo

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{group-id}"
}
```

Exemplo

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>Ver uma lista de grupos numa unidade administrativa

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. No portal Azure, vá ao **Azure AD.**

1. No painel esquerdo, selecione **unidades administrativas** e, em seguida, selecione a unidade administrativa cujos grupos pretende ver. Por predefinição, **todos os utilizadores são** selecionados no painel esquerdo. 

1. No painel esquerdo, selecione **Grupos**. O painel direito apresenta uma lista de grupos que são membros da unidade administrativa selecionada.

   ![Screenshot do painel "Grupos" mostrando uma lista de grupos numa unidade administrativa.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>Utilizar o PowerShell

Para apresentar uma lista de todos os membros da unidade administrativa, executar o seguinte comando: 

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId
```

Para exibir todos os grupos que são membros da unidade administrativa, utilize o seguinte corte de código:

```powershell
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>Use o Gráfico microsoft

Execute o seguinte comando:

Pedir

```http
GET /directory/administrativeUnits/{admin-unit-id}/members/$/microsoft.graph.group
```

Corpo

```http
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>Ver uma lista de unidades administrativas para um grupo

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. No portal Azure, vá ao **Azure AD.**

1. No painel esquerdo, selecione **Grupos** para apresentar uma lista de grupos.

1. Selecione um grupo para abrir o perfil do grupo. 

1. No painel esquerdo, selecione **unidades administrativas** para listar todas as unidades administrativas onde o grupo é membro.

   ![Screenshot do painel "Unidades Administrativas", exibindo uma lista de unidades administrativas a que um grupo está designado.](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>Utilizar o PowerShell

Execute o seguinte comando:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>Use o Gráfico microsoft

Execute o seguinte comando:

```http
https://graph.microsoft.com/v1.0/groups/{group-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>Remova um grupo de uma unidade administrativa

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Pode remover um grupo de uma unidade administrativa do portal Azure de duas formas:

- Remova-o de uma visão geral do grupo:

  1. No portal Azure, vá ao **Azure AD.**
  1. No painel esquerdo, selecione **Grupos** e, em seguida, abra o perfil para o grupo que pretende remover de uma unidade administrativa.
  1. No painel esquerdo, selecione **unidades administrativas** para listar todas as unidades administrativas a que o grupo está designado. 
  1. Selecione a unidade administrativa de onde pretende remover o grupo e, em seguida, **selecione Remover da unidade administrativa**.

     ![Screenshot do painel "Unidades Administrativas", mostrando uma lista de grupos atribuídos à unidade administrativa selecionada.](./media/admin-units-add-manage-groups/group-au-remove.png)

- Remova-o de uma unidade administrativa:

  1. No portal Azure, vá ao **Azure AD.**
  1. No painel esquerdo, selecione **unidades administrativas** e, em seguida, selecione a unidade administrativa a que o grupo está designado.
  1. No painel esquerdo, selecione **Grupos** para listar todos os grupos que são atribuídos à unidade administrativa.
  1. Selecione o grupo que pretende remover e, em seguida, **selecione Remover grupos**.

    ![Screenshot do painel "Grupos", mostrando uma lista dos grupos numa unidade administrativa.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>Utilizar o PowerShell

Execute o seguinte comando:

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>Use o Gráfico microsoft

Execute o seguinte comando:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{group-id}/$ref
```

## <a name="next-steps"></a>Passos seguintes

- [Atribuir um papel a uma unidade administrativa](admin-units-assign-roles.md)
- [Gerir utilizadores numa unidade administrativa](admin-units-add-manage-users.md)
