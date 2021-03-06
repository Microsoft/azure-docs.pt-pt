---
title: Amostra PowerShell - Lista de informações estendidas para aplicações Proxy de aplicações
description: Exemplo powerShell que lista todas as aplicações de procuração de aplicação Azure Ative (Azure AD) juntamente com o ID da aplicação (AppId), nome (DisplayName), URL externo (ExternalUrl), URL interno (InternalUrl) e tipo de autenticação (ExternalAuthenticationType).
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
ms.openlocfilehash: 1b3234a13a0e3fac760a899ce66e7faa7e7b642c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377366"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Obtenha todas as aplicações Proxy de aplicação e liste informações estendidas

Este exemplo de script PowerShell lista informações sobre todas as aplicações de procuração de aplicações Azure Ative (Azure AD), incluindo o ID de aplicação (AppId), nome (DisplayName), URL externo (ExternalUrl), URL interno (InternalUrl), tipo de autenticação (ExternalAuthenticationType), modo SSO e outras definições.

Alterar o valor da variável $ssoMode permite uma saída filtrada pelo modo SSO. Mais detalhes estão documentados no script.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Esta amostra requer o [AzureAD V2 PowerShell para módulo Graph](/powershell/azure/active-directory/install-adv2) (AzureAD).

## <a name="sample-script"></a>Script de exemplo

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Explicação do script

| Comando | Notas |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Arranja um diretor de serviço. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Obtém uma aplicação AD Azure. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Recupera uma aplicação configurada para Procuração de Aplicação em Azure AD. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure AD PowerShell, consulte a visão geral do [módulo Azure AD PowerShell](/powershell/azure/active-directory/overview).

Para outros exemplos powerShell para Aplicação Proxy, consulte [exemplos Azure AD PowerShell para Azure AD Application Proxy](../application-proxy-powershell-samples.md).
