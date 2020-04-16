---
title: Adicionar e remover unidades administrativas (pré-visualização) - Diretório Ativo Azure / Microsoft Docs
description: Utilizar unidades administrativas para restringir o âmbito das permissões de funções no Diretório Ativo do Azure
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
ms.openlocfilehash: 351cbee9cbc7cc02c7f20553b6fc8c3f861541df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428163"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Gerir unidades administrativas no Diretório Ativo do Azure

Para um controlo administrativo mais granular no Azure Ative Directory (Azure AD), pode atribuir aos utilizadores uma função De AD Azure com um âmbito limitado a uma ou mais unidades administrativas (UA).

## <a name="getting-started"></a>Introdução

1. Para executar as seguintes instruções através do [Graph Explorer,](https://aka.ms/ge)certifique-se das seguintes instruções:

    1. Vá ao Azure AD no portal e, em seguida, nas aplicações selecione Graph Explorer e forneça consentimento administrativo ao Graph Explorer.

        ![selecione Graph Explorer e forneça consentimento administrativo nesta página](./media/roles-admin-units-manage/select-graph-explorer.png)

    1. No Graph Explorer, certifique-se de que seleciona a versão beta.

        ![selecione a versão beta antes da operação POST](./media/roles-admin-units-manage/select-beta-version.png)

1. Por favor, utilize a versão de pré-visualização do Azure AD PowerShell. Instruções detalhadas estão aqui.

## <a name="add-an-administrative-unit"></a>Adicione uma unidade administrativa

### <a name="azure-portal"></a>Portal do Azure

1. Vá ao Diretório Ativo no portal e selecione Unidades Administrativas no painel esquerdo.

    ![navegar para unidades administrativas em Diretório Ativo Azure](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Selecione **Adicionar*** e fornecer o nome da unidade administrativa e opcionalmente pode adicionar uma descrição para a unidade administrativa.

    ![selecione Adicionar e, em seguida, introduzir um nome para a unidade administrativa](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Selecione **Adicionar** para finalizar a unidade administrativa.

### <a name="powershell"></a>PowerShell

Instale o Azure AD PowerShell (versão de pré-visualização) antes de tentar executar as ações abaixo:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

Os valores acima referidos podem ser modificados conforme necessário.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Remover uma unidade administrativa

No Azure Ative Directory (Azure AD), pode remover uma unidade de administração de que já não precisa como unidade de âmbito para funções administrativas.

### <a name="azure-portal"></a>Portal do Azure

Vá a **Azure AD > Unidades Administrativas** no portal. Selecione a unidade administrativa a eliminar e, em seguida, **selecione Apagar**. Depois de confirmar **Sim,** a unidade administrativa será eliminada.

![Selecione uma unidade administrativa para eliminar](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="powershell"></a>PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

A secção realçada pode ser alterada conforme necessário para o ambiente específico.

### <a name="graph-api"></a>Graph API

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Passos seguintes

[Gestão de utilizadores em unidade](roles-admin-units-add-manage-users.md)
administrativa[Gerir grupos em unidade administrativa](roles-admin-units-add-manage-groups.md)
