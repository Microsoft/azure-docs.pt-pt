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
ms.openlocfilehash: daeea48758a9f08e7eedbfcaddcde3815f5c1e16
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729104"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>Exportar aplicativos com segredos e certificados que expiram para além da data exigida

Este exemplo de script PowerShell exporta todos os segredos e certificados de registo de aplicações que expiram para além de um período exigido para as aplicações especificadas do seu diretório num ficheiro CSV não interativamente.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Explicação do script

Este guião está a funcionar não interactivamente. O administrador que o utilizar terá de alterar os valores na secção "#PARAMETERS TO CHANGE" com o seu próprio ID de aplicação, Segredo de Aplicação, Nome do Inquilino, o período para a expiração das credenciais de apps e o caminho onde o CSV será exportado.
Este script utiliza o [Client_Credential Oauth Flow](../../develop/v2-oauth2-client-creds-grant-flow.md) A função "RefreshToken" irá construir o token de acesso com base nos valores dos parâmetros modificados pela administração.

O comando "Add-Member" é responsável pela criação das colunas no ficheiro CSV.

| Comando | Notas |
|---|---|
| [Invocar WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1&preserve-view=true) | Envia pedidos http e HTTPS para uma página web ou serviço web. Analisa a resposta e devolve coleções de links, imagens e outros elementos HTML significativos. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure AD PowerShell, consulte a visão geral do [módulo Azure AD PowerShell](/powershell/azure/active-directory/overview).

Para outros exemplos de PowerShell para Gestão de Aplicações, consulte [exemplos Azure AD PowerShell para Gestão de Aplicações](../app-management-powershell-samples.md).
