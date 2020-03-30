---
title: Amostra PowerShell - Atribuir grupo a uma aplicação proxy de aplicação
description: Exemplo powerShell que atribui um grupo a uma aplicação de proxy de aplicação azure Ative Directory (Azure AD).
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
ms.openlocfilehash: 540453e5de3eed97b1207ca16443b46dffd5f8e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483352"
---
# <a name="assign-a-group-to-a-specific-azure-ad-application-proxy-application"></a>Atribuir um grupo a uma aplicação específica de procuração de aplicação ad ad

Este exemplo de script PowerShell permite-lhe atribuir um grupo específico a uma aplicação de proxy de aplicação do Azure Ative Directory (Azure AD).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Esta amostra requer o [AzureAD V2 PowerShell para módulo gráfico](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) ou o [AzureAD V2 PowerShell para a versão de pré-visualização](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) do módulo Graph (AzureADPreview).

## <a name="sample-script"></a>Script de exemplo

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-group-to-app.ps1 "Assign a group to a specific Azure AD Application Proxy application")]

## <a name="script-explanation"></a>Explicação do script

| Comando | Notas |
|---|---|
| [New-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/New-azureadgroupapproleassignment?view=azureadps-2.0) | Atribui um grupo a uma função de candidatura. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo PowerShell Azure AD, consulte a visão geral do [módulo PowerShell Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Para outros exemplos da PowerShell para proxy de aplicação, consulte [os exemplos de PowerShell da Azure AD para procuração de aplicação ad .](../application-proxy-powershell-samples.md)