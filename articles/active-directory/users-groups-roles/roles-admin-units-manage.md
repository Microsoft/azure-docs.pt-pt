---
title: Adicionar e remover unidades administrativas (pré-visualização) - Diretório Ativo Azure / Microsoft Docs
description: Utilize unidades administrativas para restringir o âmbito das permissões de funções no Diretório Ativo do Azure.
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
ms.openlocfilehash: 7a4a0dfaeda18b3f68ddc3c7cc7333b8c994d174
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684918"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Gerir unidades administrativas no Diretório Ativo do Azure

Para um controlo administrativo mais granular no Azure Ative Directory (Azure AD), pode atribuir aos utilizadores uma função De AD Azure com um âmbito limitado a uma ou mais unidades administrativas (UA).

## <a name="get-started"></a>Introdução

1. Para executar consultas a partir das seguintes instruções através [do Graph Explorer,](https://aka.ms/ge)faça o seguinte:

    a. No portal Azure, vá ao Azure AD. Na lista de aplicações, selecione **Graph Explorer**, e, em seguida, selecione **Grant admin consent e graph Explorer**.

    ![Screenshot mostrando link para "Grant admin consent"](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. No Graph Explorer, selecione a versão **beta.**

    ![Screenshot mostrando a versão beta selecionada](./media/roles-admin-units-manage/select-beta-version.png)

1. Utilize a versão de pré-visualização do Azure AD PowerShell.

## <a name="add-an-administrative-unit"></a>Adicione uma unidade administrativa

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. No portal Azure, vá ao Azure AD e, em seguida, no painel esquerdo, selecione **unidades administrativas.**

    ![Screenshot do link Unidades Administrativas (Pré-visualização) em Azure AD](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Selecione **Adicionar** e, em seguida, introduza o nome da unidade administrativa. Opcionalmente, adicione uma descrição da unidade administrativa.

    ![Screenshot do botão Adicionar e da caixa de texto para introduzir o nome da unidade administrativa](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Selecione **Adicionar** para finalizar a unidade administrativa.

### <a name="use-powershell"></a>Utilizar o PowerShell

Instale o Azure AD PowerShell (pré-visualização) antes de tentar executar os seguintes comandos:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

Pode modificar os valores que estão fechados em aspas, conforme necessário.

### <a name="use-microsoft-graph"></a>Use Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Remover uma unidade administrativa

Em Azure AD, pode remover uma unidade administrativa de que já não precisa como unidade de âmbito para funções administrativas.

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. No portal Azure, vá para**unidades administrativas** **da Azure AD.** >  
1. Selecione a unidade administrativa a eliminar e, em seguida, **selecione Apagar**. 
1. Para confirmar que pretende eliminar a unidade administrativa, selecione **Sim**. A unidade administrativa é suprimida.

![Screenshot da unidade administrativa Eliminar botão e janela de confirmação](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Utilizar o PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

Pode modificar os valores que estão fechados em aspas, conforme necessário para o ambiente específico.

### <a name="use-the-graph-api"></a>Use a API de gráfico

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Passos seguintes

* [Gerir utilizadores numa unidade administrativa](roles-admin-units-add-manage-users.md)
* [Gerir grupos numa unidade administrativa](roles-admin-units-add-manage-groups.md)
