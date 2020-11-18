---
title: Lista Azure AD Application Proxy grupos de conector para apps
description: Exemplo powerShell que lista todos os grupos de conector de aplicação Azure Ative (Azure AD) com as aplicações atribuídas.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 5f2905a88f7c769ed9bdcfadcdc91ec23107e3bb
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659357"
---
# <a name="get-all-application-proxy-apps-and-list-by-connector-group"></a>Obtenha todas as aplicações Proxy de aplicação e lista por grupo de conector

Este exemplo de script PowerShell lista informações sobre todos os grupos de conector de aplicação Proxy Proxy (Azure AD) do Azure Ative Com as aplicações atribuídas.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Esta amostra requer o [AzureAD V2 PowerShell para módulo Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) ou o [AzureAD V2 PowerShell para a versão de pré-visualização do módulo Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Script de exemplo

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-by-connectorgroup.ps1 "Get all Application Proxy Connector groups with the assigned applications")]

## <a name="script-explanation"></a>Explicação do script

| Comando | Notas |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Arranja um diretor de serviço. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Obtém uma aplicação AD Azure. |
|[Get-AzureADApplicationProxyApplicationApplicationApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Recupera uma aplicação configurada para Procuração de Aplicação em Azure AD. |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Recupera uma lista de todos os grupos de conector, ou se especificado, detalhes do grupo de conector especificado. |


## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o módulo Azure AD PowerShell, consulte a visão geral do [módulo Azure AD PowerShell](/powershell/azure/active-directory/overview?view=azureadps-2.0).

Para outros exemplos powerShell para Aplicação Proxy, consulte [exemplos Azure AD PowerShell para Azure AD Application Proxy](../application-proxy-powershell-samples.md).