---
title: Amostra PowerShell - Lista de utilizadores & grupos para aplicação Proxy de aplicação
description: Exemplo powerShell que lista todos os utilizadores e grupos atribuídos a uma aplicação específica de Procuração de Aplicações Azure Ative (Azure AD).
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
ms.openlocfilehash: 184b8f4c7d1765e9a221b87f06e47d8e0d4b8057
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659170"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>Mostrar utilizadores e grupos atribuídos a uma aplicação Proxy de aplicação

Este exemplo de script PowerShell lista os utilizadores e grupos atribuídos a uma aplicação específica de procuração de aplicação Azure Ative (Azure AD).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Esta amostra requer o [AzureAD V2 PowerShell para módulo Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) ou o [AzureAD V2 PowerShell para a versão de pré-visualização do módulo Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Script de exemplo

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>Explicação do script

| Comando | Notas |
|---|---|
| [Get-AzureADUser](/powershell/module/AzureAD/get-azureaduser?view=azureadps-2.0)| Arranja um utilizador. |
| [Get-AzureADGroup](/powershell/module/AzureAD/get-azureadgroup?view=azureadps-2.0)| Arranja um grupo. |
| [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Arranja um diretor de serviço. |
| [Get-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/get-azureaduserapproleassignment?view=azureadps-2.0) | Obtenha uma atribuição de função de aplicação de utilizador. |
| [Get-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/get-azureadgroupapproleassignment?view=azureadps-2.0) | Obtenha uma atribuição de papel de candidatura em grupo. |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o módulo Azure AD PowerShell, consulte a visão geral do [módulo Azure AD PowerShell](/powershell/azure/active-directory/overview?view=azureadps-2.0).

Para outros exemplos powerShell para Aplicação Proxy, consulte [exemplos Azure AD PowerShell para Azure AD Application Proxy](../application-proxy-powershell-samples.md).