---
title: Exemplos do PowerShell para o Azure Proxy de Aplicativo do AD
description: Use estes exemplos do PowerShell para o Azure Proxy de Aplicativo do AD para obter informações sobre os aplicativos e conectores de proxy de aplicativo em seu diretório, atribuir usuários e grupos a aplicativos e obter informações de certificado.
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
ms.openlocfilehash: 25097f8b72c7c8dde742701ce576c1b0e2fbabd2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480947"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Exemplos do PowerShell do Azure AD para o Azure Proxy de Aplicativo do AD

A tabela a seguir inclui links para exemplos de script do PowerShell para Proxy de Aplicativo do AD do Azure. Esses exemplos exigem o [módulo AzureAD v2 PowerShell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) ou a [versão de visualização do PowerShell do AzureAD v2 para o módulo do Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview), salvo indicação em contrário.


Para obter mais informações sobre os cmdlets usados nesses exemplos, consulte [Gerenciamento de aplicativos de proxy](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) de aplicativo e [Gerenciamento de conector de proxy de aplicativo](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management).

| | |
|---|---|
|**Aplicativos de proxy de aplicativo**||
| [Listar informações básicas para todos os aplicativos de proxy de aplicativo](scripts/powershell-get-all-app-proxy-apps-basic.md) | Lista informações básicas (AppId, DisplayName, ObjId) sobre todos os aplicativos de proxy de aplicativo em seu diretório. |
| [Listar informações estendidas para todos os aplicativos de proxy de aplicativo](scripts/powershell-get-all-app-proxy-apps-extended.md) | Lista informações estendidas (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) sobre todos os aplicativos de proxy de aplicativo em seu diretório.  |
| [Listar todos os aplicativos de proxy de aplicativo por grupo de conectores](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Lista informações sobre todos os aplicativos de proxy de aplicativo em seu diretório e a quais grupos os aplicativos são atribuídos. |
| [Obter todos os aplicativos de proxy de aplicativo com uma política de tempo de vida de token](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Lista todos os aplicativos de proxy de aplicativo em seu diretório com uma política de tempo de vida de token e seus detalhes. Este exemplo requer o [PowerShell do AzureAD v2 para versão de visualização do módulo do Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview). |
|**Grupos de conectores**||
| [Obter todos os grupos de conectores e conectores no diretório](scripts/powershell-get-all-connectors.md) | Lista todos os grupos de conectores e conectores em seu diretório. |
| [Mover todos os aplicativos atribuídos a um grupo de conectores para outro grupo de conectores](scripts/powershell-move-all-apps-to-connector-group.md) | Move todos os aplicativos atualmente atribuídos a um grupo de conectores para um grupo de conectores diferente. |
|**Usuários e grupo atribuídos**||
| [Exibir usuários e grupos atribuídos a um aplicativo de proxy de aplicativo](scripts/powershell-display-users-group-of-app.md) | Lista os usuários e grupos atribuídos a um aplicativo de proxy de aplicativo específico. |
| [Atribuir um usuário a um aplicativo](scripts/powershell-assign-user-to-app.md) | Atribui um usuário específico a um aplicativo. |
| [Atribuir um grupo a um aplicativo](scripts/powershell-assign-group-to-app.md) | Atribui um grupo específico a um aplicativo. |
|**Configuração de URL externa**||
| [Obter todos os aplicativos de proxy de aplicativo usando domínios padrão (. msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Lista todos os aplicativos de proxy de aplicativo usando domínios padrão (. msappproxy.net). |
| [Obter todos os aplicativos de proxy de aplicativo usando a publicação curinga](scripts/powershell-get-all-wildcard-apps.md) | Lista todos os aplicativos de proxy de aplicativo usando a publicação curinga. |
|**Configuração de domínio personalizado**||
| [Obter todos os aplicativos de proxy de aplicativo usando informações de certificado e domínios personalizados](scripts/powershell-get-all-custom-domains-and-certs.md) | Lista todos os aplicativos de proxy de aplicativo que estão usando domínios personalizados e as informações de certificado associadas aos domínios personalizados. |
| [Obter todos os aplicativos de aplicativo proxy do Azure AD publicados sem nenhum certificado carregado](scripts/powershell-get-all-custom-domain-no-cert.md) | Lista todos os aplicativos de proxy de aplicativo que estão usando domínios personalizados, mas que não têm um certificado SSL válido carregado. |
| [Obter todos os aplicativos de aplicativo de proxy do Azure AD publicados com o certificado idêntico](scripts/powershell-get-custom-domain-identical-cert.md) | Lista todos os aplicativos de aplicativo de proxy do Azure AD publicados com o certificado idêntico. |
| [Obter todos os aplicativos de aplicativo de proxy do Azure AD publicados com o certificado idêntico e substituí-lo](scripts/powershell-get-custom-domain-replace-cert.md) | Para aplicativos de proxy do Azure AD que são publicados com um certificado idêntico, o permite substituir o certificado em massa. |
