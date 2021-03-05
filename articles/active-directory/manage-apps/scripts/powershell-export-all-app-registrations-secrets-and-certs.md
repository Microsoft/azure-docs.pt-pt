---
title: Amostra PowerShell - Exportar registos de aplicações, segredos e certificados no inquilino do Azure Ative Directory.
description: Exemplo da PowerShell que exporta todos os registos de aplicações, segredos e certificados para as aplicações especificadas no seu inquilino Azure Ative Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: 768f2f3241144085acb7a218b60034cdfa9e45b9
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102185392"
---
# <a name="export-app-registrations-secrets-and-certificates"></a>Exportar registos, segredos e certificados de aplicativos

Este exemplo de script PowerShell exporta todos os registos de aplicações, segredos e certificados para as aplicações especificadas no seu diretório.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Esta amostra requer o [AzureAD V2 PowerShell para módulo Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) ou o [AzureAD V2 PowerShell para a versão de pré-visualização do módulo Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Script de exemplo

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all app registrations, secrets, and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>Explicação do script

| Comando | Notas |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Exporta todos os registos de aplicações, segredos e certificados para as aplicações especificadas no seu diretório. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure AD PowerShell, consulte a visão geral do [módulo Azure AD PowerShell](/powershell/azure/active-directory/overview).

Para outros exemplos de PowerShell para Gestão de Aplicações, consulte [exemplos Azure AD PowerShell para Gestão de Aplicações](../app-management-powershell-samples.md).
