---
title: Adicione e remova unidades administrativas - Azure Ative Directory | Microsoft Docs
description: Utilize unidades administrativas para restringir o âmbito das permissões de funções no Diretório Ativo Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0706fad1e5340625c32eab691ac3e4d58eeafc9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012126"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Gerir unidades administrativas em Azure Ative Directory

Para um controlo administrativo mais granular no Azure Ative Directory (Azure AD), pode atribuir os utilizadores a uma função AD Azure com um âmbito limitado a uma ou mais unidades administrativas.

## <a name="get-started"></a>Introdução

1. Para executar consultas a partir das seguintes instruções através [do Graph Explorer,](https://aka.ms/ge)faça o seguinte:

    a. No portal Azure, vá ao Azure AD. 
    
    b. Na lista de aplicações, selecione **Graph explorer**.
    
    c. No painel **permissões,** selecione **Grant admin consent for Graph explorer**.

    ![Screenshot mostrando o link "Grant admin consent for Graph explorer".](./media/admin-units-manage/select-graph-explorer.png)


1. Use [a ad powershell Azure](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="add-an-administrative-unit"></a>Adicionar uma unidade administrativa

Pode adicionar uma unidade administrativa utilizando o portal Azure ou o PowerShell.

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. No portal Azure, vá ao Azure AD. Em seguida, no painel esquerdo, selecione **Unidades Administrativas.**

    ![Screenshot do link "Unidades Administrativas" em Azure AD.](./media/admin-units-manage/nav-to-admin-units.png)

1. Selecione o botão **Adicionar** na parte superior do painel e, em seguida, na caixa **Nome,** introduza o nome da unidade administrativa. Opcionalmente, adicione uma descrição da unidade administrativa.

    ![Screenshot mostrando o botão Adicionar e a caixa Nome para introduzir o nome da unidade administrativa.](./media/admin-units-manage/add-new-admin-unit.png)

1. Selecione o botão **azul Adicionar** para finalizar a unidade administrativa.

### <a name="use-powershell"></a>Utilizar o PowerShell

Instale [a Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/) antes de tentar executar os seguintes comandos:

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

Pode modificar os valores que estão incluídos nas aspas, conforme necessário.

### <a name="use-microsoft-graph"></a>Use o Gráfico microsoft

Pedir

```http
POST /administrativeUnits
```

Corpo

```http
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Remover uma unidade administrativa

Em Azure AD, pode remover uma unidade administrativa de que já não necessita como unidade de âmbito para funções administrativas.

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. No portal Azure, vá ao **Azure AD,** e depois selecione **unidades administrativas**. 
1. Selecione a unidade administrativa a eliminar e, em seguida, **selecione Delete**. 
1. Para confirmar que pretende eliminar a unidade administrativa, selecione **Sim**. A unidade administrativa é eliminada.

![Screenshot da unidade administrativa Apagar botão e janela de confirmação.](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Utilizar o PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $adminUnitObj.ObjectId
```

Pode modificar os valores que estão incluídos em aspas, conforme necessário para o ambiente específico.

### <a name="use-the-graph-api"></a>Utilizar o Graph API

Pedir

```http
DELETE /administrativeUnits/{admin-unit-id}
```

Corpo

```http
{}
```

## <a name="next-steps"></a>Passos seguintes

* [Gerir utilizadores numa unidade administrativa](admin-units-add-manage-users.md)
* [Gerir grupos numa unidade administrativa](admin-units-add-manage-groups.md)
