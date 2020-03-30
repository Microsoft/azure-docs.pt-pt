---
title: Amostra PowerShell - Listar todos os grupos de conector proxy de aplicação
description: Exemplo powerShell que lista todos os grupos de conectores proxy de aplicação do Azure Ative Directory (Azure AD) no seu diretório.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53af7f3f9049edc23ed4226f43e7fe67398c29f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482117"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>Obtenha todos os grupos e conectores de conectores no diretório

Este exemplo de script PowerShell lista todos os grupos de conectores proxy de aplicação do Azure Ative Directory (Azure AD) no seu diretório.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Esta amostra requer o [AzureAD V2 PowerShell para módulo gráfico](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) ou o [AzureAD V2 PowerShell para a versão de pré-visualização](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) do módulo Graph (AzureADPreview).

## <a name="sample-script"></a>Script de exemplo

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>Explicação do script

| Comando | Notas |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Recupera uma lista de todos os grupos de conector, ou se especificado, detalhes do grupo de conector especificado. |
| [Get-AzureADApplicationProxyConnectorGroupMembers](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers?view=azureadps-2.0) | Obtém todos os conectores proxy de aplicação associados a cada grupo de conectores.|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo PowerShell Azure AD, consulte a visão geral do [módulo PowerShell Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Para outros exemplos da PowerShell para proxy de aplicação, consulte [os exemplos de PowerShell da Azure AD para procuração de aplicação ad .](../application-proxy-powershell-samples.md)