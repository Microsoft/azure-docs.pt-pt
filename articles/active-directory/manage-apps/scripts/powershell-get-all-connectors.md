---
title: Amostra PowerShell - Lista todos os grupos de conector de procuração de aplicação
description: Exemplo powerShell que lista todos os grupos de conector de aplicação Proxy da aplicação Azure Ative (Azure AD) no seu diretório.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: f689cfd2cb2ba95bae2a2727f5b32279745eafd5
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99253328"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>Obtenha todos os grupos de conector e conectores no diretório

Este exemplo de script PowerShell lista todos os grupos de conector de aplicação proxy de aplicação Azure Ative (Azure AD) no seu diretório.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Esta amostra requer o [AzureAD V2 PowerShell para módulo Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) ou o [AzureAD V2 PowerShell para a versão de pré-visualização do módulo Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Script de exemplo

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>Explicação do script

| Comando | Notas |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Recupera uma lista de todos os grupos de conector, ou se especificado, detalhes do grupo de conector especificado. |
| [Get-AzureADApplicationProxyConnectorGroupMembers](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers) | Obtém todos os conectores Proxy de aplicação associados a cada grupo de conector.|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure AD PowerShell, consulte a visão geral do [módulo Azure AD PowerShell](/powershell/azure/active-directory/overview).

Para outros exemplos powerShell para Aplicação Proxy, consulte [exemplos Azure AD PowerShell para Azure AD Application Proxy](../application-proxy-powershell-samples.md).
