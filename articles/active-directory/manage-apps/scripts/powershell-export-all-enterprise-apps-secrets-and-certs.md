---
title: Amostra powerShell - Segredos de exportação e certificados para aplicações empresariais no inquilino do Azure Ative Directory.
description: Exemplo da PowerShell que exporta todos os segredos e certificados para as aplicações empresariais especificadas no seu inquilino Azure Ative Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 536197ebc5df94447f3937773e0447e47961bd92
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378606"
---
# <a name="export-secrets-and-certificates-for-enterprise-apps"></a>Segredos de exportação e certificados para aplicações empresariais
Este exemplo de script PowerShell exporta todos os segredos, certificados e proprietários para as aplicações empresariais especificadas do seu diretório para um ficheiro CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Esta amostra requer o [AzureAD V2 PowerShell para módulo Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) ou o [AzureAD V2 PowerShell para a versão de pré-visualização do módulo Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Script de exemplo

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-enterprise-apps-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified enterprise apps in your directory.")]

## <a name="script-explanation"></a>Explicação do script

O comando "Add-Member" é responsável pela criação das colunas no ficheiro CSV.
Pode modificar a variável "$Path" diretamente no PowerShell, com um percurso de ficheiro CSV, caso prefira que a exportação não seja interativa.

| Comando | Notas |
|---|---|
| [Get-AzureADServicePrincipal](/powershell/module/azuread/Get-azureADServicePrincipal?view=azureadps-2.0&preserve-view=true) | Recupera um pedido de empresa do seu diretório. |
| [Get-AzureADServicePrincipalOwner](/powershell/module/azuread/Get-AzureADServicePrincipalOwner?view=azureadps-2.0&preserve-view=true) | Recupera os proprietários de um pedido de empresa do seu diretório. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure AD PowerShell, consulte a visão geral do [módulo Azure AD PowerShell](/powershell/azure/active-directory/overview).

Para outros exemplos de PowerShell para Gestão de Aplicações, consulte [exemplos Azure AD PowerShell para Gestão de Aplicações](../app-management-powershell-samples.md).
