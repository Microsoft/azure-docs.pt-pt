---
title: Amostra PowerShell - Exportar aplicativos com segredos e certificados expirando no inquilino do Azure Ative Directory.
description: Exemplo da PowerShell que exporta todas as aplicações com segredos e certificados expirados para as aplicações especificadas no seu inquilino Azure Ative Directory.
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
ms.openlocfilehash: c8a37b08db5cdb35bc9328cbc29caa04a5cdf412
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010842"
---
# <a name="export-apps-with-expiring-secrets-and-certificates"></a>Aplicativos de exportação com segredos e certificados caducados

Este exemplo de script PowerShell exporta todos os registos de aplicações com segredos expirando, certificados e seus proprietários para as aplicações especificadas do seu diretório em um ficheiro CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Esta amostra requer o [AzureAD V2 PowerShell para módulo Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) ou o [AzureAD V2 PowerShell para a versão de pré-visualização do módulo Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Script de exemplo

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-expiring-secrets.ps1 "Exports all apps with expiring secrets and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>Explicação do script

O comando "Add-Member" é responsável pela criação das colunas no ficheiro CSV.
O comando "Novo Objeto" cria um objeto a ser utilizado para as colunas na exportação de ficheiros CSV.
Pode modificar a variável "$Path" diretamente no PowerShell, com um percurso de ficheiro CSV, caso prefira que a exportação não seja interativa.

| Comando | Notas |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Recupera uma aplicação do seu diretório. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Recupera os proprietários de um pedido do seu diretório. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure AD PowerShell, consulte a visão geral do [módulo Azure AD PowerShell](/powershell/azure/active-directory/overview).

Para outros exemplos de PowerShell para Gestão de Aplicações, consulte [exemplos Azure AD PowerShell para Gestão de Aplicações](../app-management-powershell-samples.md).
