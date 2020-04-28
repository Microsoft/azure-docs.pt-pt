---
title: Amostra PowerShell - Substitua o certificado nas aplicações proxy de aplicação
description: O exemplo da PowerShell que substitui um certificado através das aplicações de proxy de aplicação do Azure Ative Directory (Azure AD).
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
ms.openlocfilehash: 91aafe00856f37e9d7e6b3babafecd2cbdf5f856
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75482000"
---
# <a name="get-all-application-proxy-applications-published-with-the-identical-certificate-and-replace-it"></a>Obtenha todos os pedidos de procuração de aplicação publicados com o certificado idêntico e substitua-o

Este exemplo de script PowerShell permite-lhe substituir o certificado a granel por todas as aplicações de Proxy de Aplicação do Diretório Ativo Azure (Azure AD) que são publicadas com o certificado idêntico.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Esta amostra requer o [AzureAD V2 PowerShell para módulo gráfico](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) ou o [AzureAD V2 PowerShell para a versão de pré-visualização](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) do módulo Graph (AzureADPreview).

## <a name="sample-script"></a>Script de exemplo

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-replace-cert.ps1 "Get all Application Proxy applications published with the identical certificate and replace it")]

## <a name="script-explanation"></a>Explicação do script

| Comando | Notas |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Arranja um diretor de serviço. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Obtém uma aplicação Azure AD. |
|[Get-AzureADApplicationProxyApplicationApplicationApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Recupera uma aplicação configurada para procuração de aplicação em Anúncio Azure. |
|[Set-AzureADApplicationProxyApplicationCustomDomainCertificate](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplicationproxyapplicationcustomdomaincertificate?view=azureadps-2.0) | Atribui um certificado a uma aplicação configurada para procuração de aplicação em AD Azure. Este comando envia o certificado e permite que a aplicação utilize Domínios Personalizados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo PowerShell Azure AD, consulte a visão geral do [módulo PowerShell Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Para outros exemplos da PowerShell para proxy de aplicação, consulte [os exemplos de PowerShell da Azure AD para procuração de aplicação ad .](../application-proxy-powershell-samples.md)