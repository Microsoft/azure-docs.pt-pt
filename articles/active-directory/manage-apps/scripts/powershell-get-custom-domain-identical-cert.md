---
title: Amostra PowerShell - Aplicações proxy com certs idênticos
description: Exemplo powerShell que lista todas as aplicações de Proxy de Aplicação do Diretório Ativo Azure (Azure AD) que são publicadas com o certificado idêntico.
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
ms.openlocfilehash: ef654226d041a7602c03e1ce4670d2f8eea0c2e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75482013"
---
# <a name="get-all-azure-ad-proxy-application-apps-that-are-published-with-the-identical-certificate"></a>Obtenha todas as aplicações de aplicação Da AD Proxy azure que são publicadas com o certificado idêntico

Este exemplo de script PowerShell lista todas as aplicações de Proxy de Aplicação do Diretório Ativo Azure (Azure AD) que são publicadas com o certificado idêntico.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Esta amostra requer o [AzureAD V2 PowerShell para módulo gráfico](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) ou o [AzureAD V2 PowerShell para a versão de pré-visualização](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) do módulo Graph (AzureADPreview).

## <a name="sample-script"></a>Script de exemplo

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-identical-cert.ps1 "Get all Azure AD Proxy application apps published with the identical certificate")]

## <a name="script-explanation"></a>Explicação do script

| Comando | Notas |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Arranja um diretor de serviço. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Obtém uma aplicação Azure AD. |
|[Get-AzureADApplicationProxyApplicationApplicationApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Recupera uma aplicação configurada para procuração de aplicação em Anúncio Azure. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo PowerShell Azure AD, consulte a visão geral do [módulo PowerShell Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Para outros exemplos da PowerShell para proxy de aplicação, consulte [os exemplos de PowerShell da Azure AD para procuração de aplicação ad .](../application-proxy-powershell-samples.md)