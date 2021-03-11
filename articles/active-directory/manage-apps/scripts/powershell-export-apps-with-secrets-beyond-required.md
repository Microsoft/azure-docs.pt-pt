---
title: Amostra PowerShell - Exportar aplicativos com segredos e certificados que expiram para além da data exigida no inquilino do Azure Ative Directory.
description: Exemplo da PowerShell que exporta todas as aplicações com segredos e certificados que expiram para além da data exigida para as aplicações especificadas no seu inquilino Azure Ative Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: 3572f481cc2cbcb1df73b33eb2543e32256ad9fb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584396"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>Exportar aplicativos com segredos e certificados que expiram para além da data exigida

Este exemplo de script PowerShell exporta todos os segredos e certificados de aplicações que expiram para além da data exigida para as aplicações especificadas do seu diretório num ficheiro CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Esta amostra requer o [AzureAD V2 PowerShell para módulo Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) ou o [AzureAD V2 PowerShell para a versão de pré-visualização do módulo Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Script de exemplo

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Explicação do script

O comando "Add-Member" é responsável pela criação das colunas no ficheiro CSV.
Pode modificar a variável "$Path" diretamente no PowerShell, com um percurso de ficheiro CSV, caso prefira que a exportação não seja interativa.

| Comando | Notas |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Recupera uma aplicação do seu diretório. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Recupera os proprietários de um pedido do seu diretório. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure AD PowerShell, consulte a visão geral do [módulo Azure AD PowerShell](/powershell/azure/active-directory/overview).

Para outros exemplos de PowerShell para Gestão de Aplicações, consulte [exemplos Azure AD PowerShell para Gestão de Aplicações](../app-management-powershell-samples.md).
