---
title: Adicionar e remover unidades administrativas - Azure Ative Directory / Microsoft Docs
description: Utilize unidades administrativas para restringir o âmbito das permissões de funções no Diretório Ativo Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b47ea3cc55be26521dfa6e2b3230b477f82f442
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377908"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Gerir unidades administrativas em Azure Ative Directory

Para um controlo administrativo mais granular em Azure Ative Directory (Azure AD), pode atribuir os utilizadores a uma função AD Azure com um âmbito limitado a uma ou mais unidades administrativas (UA).

## <a name="get-started"></a>Introdução

1. Para executar consultas a partir das seguintes instruções através [do Graph Explorer,](https://aka.ms/ge)faça o seguinte:

    a. No portal Azure, vá ao Azure AD. Na lista de aplicações, selecione **Graph Explorer**e, em seguida, selecione **Grant admin consent to Graph Explorer**.

    ![Screenshot mostrando link para "Grant consentimento administrativo"](./media/admin-units-manage/select-graph-explorer.png)


1. Utilize a versão de pré-visualização do Azure AD PowerShell.

## <a name="add-an-administrative-unit"></a>Adicionar uma unidade administrativa

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. No portal Azure, vá ao Azure AD e, em seguida, no painel esquerdo, selecione **Unidades Administrativas**.

    ![Screenshot da ligação administrativa em Azure AD](./media/admin-units-manage/nav-to-admin-units.png)

1. **Selecione Adicionar** e, em seguida, insira o nome da unidade administrativa. Opcionalmente, adicione uma descrição da unidade administrativa.

    ![Screenshot do botão Adicionar e da caixa de texto para introduzir o nome da unidade administrativa](./media/admin-units-manage/add-new-admin-unit.png)

1. **Selecione Adicionar** para finalizar a unidade administrativa.

### <a name="use-powershell"></a>Utilizar o PowerShell

Instale a Azure AD PowerShell (pré-visualização) antes de tentar executar os seguintes comandos:

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

Pode modificar os valores que estão incluídos nas aspas, conforme necessário.

### <a name="use-microsoft-graph"></a>Use o Gráfico microsoft

```http
Http Request
POST /administrativeUnits
Request body
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Remover uma unidade administrativa

Em Azure AD, pode remover uma unidade administrativa de que já não necessita como unidade de âmbito para funções administrativas.

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. No portal Azure, **Azure AD**vá às  >  **unidades administrativas**Azure AD . 
1. Selecione a unidade administrativa a eliminar e, em seguida, **selecione Delete**. 
1. Para confirmar que pretende eliminar a unidade administrativa, selecione **Sim**. A unidade administrativa é eliminada.

![Screenshot da unidade administrativa Eliminar botão e janela de confirmação](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Utilizar o PowerShell

```powershell
$delau = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $delau.ObjectId
```

Pode modificar os valores que estão incluídos em aspas, conforme necessário para o ambiente específico.

### <a name="use-the-graph-api"></a>Utilize a API do Gráfico

```http
HTTP request
DELETE /administrativeUnits/{Admin id}
Request body
{}
```

## <a name="next-steps"></a>Passos seguintes

* [Gerir utilizadores numa unidade administrativa](admin-units-add-manage-users.md)
* [Gerir grupos numa unidade administrativa](admin-units-add-manage-groups.md)
