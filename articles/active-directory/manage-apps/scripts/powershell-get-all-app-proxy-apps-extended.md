---
title: Amostra PowerShell - Lista de informações estendidas para aplicações proxy de aplicação
description: Exemplo powerShell que lista todas as aplicações de proxy de aplicação do Diretório Ativo Azure (Azure AD) juntamente com o ID da aplicação (AppId), nome (DisplayName), URL externo (ExternalUrl), URL interno (InternalUrl) e tipo de autenticação ( Tipo de Autenticação Externa).
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
ms.openlocfilehash: f58de8c081dbb0323a2d9638777e29d879172072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482143"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Obtenha todas as aplicações de Procuração de Aplicações e lista informações estendidas

Este exemplo de script PowerShell lista informações sobre todas as aplicações de proxy de aplicação do Diretório Ativo Azure (Azure AD), incluindo o ID da aplicação (AppId), nome (DisplayName), URL externo (ExternalUrl), URL interno (InternalUrl) e tipo de autenticação (ExternalAuthenticationType).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Esta amostra requer o [AzureAD V2 PowerShell para módulo gráfico](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) ou o [AzureAD V2 PowerShell para a versão de pré-visualização](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) do módulo Graph (AzureADPreview).

## <a name="sample-script"></a>Script de exemplo

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Explicação do script

| Comando | Notas |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Arranja um diretor de serviço. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Obtém uma aplicação Azure AD. |
|[Get-AzureADApplicationProxyApplicationApplicationApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Recupera uma aplicação configurada para procuração de aplicação em Anúncio Azure. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo PowerShell Azure AD, consulte a visão geral do [módulo PowerShell Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Para outros exemplos da PowerShell para proxy de aplicação, consulte [os exemplos de PowerShell da Azure AD para procuração de aplicação ad .](../application-proxy-powershell-samples.md)