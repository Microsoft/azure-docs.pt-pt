---
title: Amostra PowerShell - Aplicações Proxy de aplicação com certificados idênticos
description: Exemplo powerShell que lista todas as aplicações de procuração de aplicação Azure Ative (Azure AD) que são publicadas com o certificado idêntico.
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 18200b5571c84c8bd442d5dcb2714c6d52d4bcf5
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377058"
---
# <a name="get-all-application-proxy-apps-that-are-published-with-the-identical-certificate"></a>Obtenha todas as aplicações Proxy de aplicação que são publicadas com o certificado idêntico

Este exemplo de script PowerShell lista todas as aplicações de procuração de aplicação Azure Ative (Azure AD) que são publicadas com o certificado idêntico.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Esta amostra requer o [AzureAD V2 PowerShell para módulo Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) ou o [AzureAD V2 PowerShell para a versão de pré-visualização do módulo Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Script de exemplo

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-identical-cert.ps1 "Get all Azure AD Proxy application apps published with the identical certificate")]

## <a name="script-explanation"></a>Explicação do script

| Comando | Notas |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Arranja um diretor de serviço. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Obtém uma aplicação AD Azure. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Recupera uma aplicação configurada para Procuração de Aplicação em Azure AD. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure AD PowerShell, consulte a visão geral do [módulo Azure AD PowerShell](/powershell/azure/active-directory/overview).

Para outros exemplos powerShell para Aplicação Proxy, consulte [exemplos Azure AD PowerShell para Azure AD Application Proxy](../application-proxy-powershell-samples.md).
