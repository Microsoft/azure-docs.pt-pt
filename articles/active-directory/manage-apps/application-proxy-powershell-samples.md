---
title: Amostras powerShell para procuração de aplicação de AD Azure
description: Utilize estas amostras PowerShell para o Proxy de Aplicação AD Azure para obter informações sobre aplicações proxy e conectores no seu diretório, atribuir utilizadores e grupos a apps e obter informações sobre certificados.
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
ms.openlocfilehash: 476bdfdd2c0b81d86900cee18024cc261ee80a07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481267"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Exemplos de PowerShell da Azure AD para procuração de aplicação ad azure

A tabela seguinte inclui links para exemplos de scriptpowerShell para procuração de aplicação ad ad azure. Estas amostras requerem o [AzureAD V2 PowerShell para o módulo Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) ou o [AzureAD V2 PowerShell para](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)a versão de pré-visualização do módulo graph , salvo indicação em contrário.


Para obter mais informações sobre os cmdlets utilizados nestas amostras, consulte a [Gestão de Aplicações proxy](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) e gestão de [conector](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)proxy de aplicação.

| | |
|---|---|
|**Aplicações proxy de aplicação**||
| [Lista rindo informações básicas para todas as aplicações proxy de aplicação](scripts/powershell-get-all-app-proxy-apps-basic.md) | Lista informações básicas (AppId, DisplayName, ObjId) sobre todas as aplicações proxy de aplicação no seu diretório . |
| [Lista de informações estendidas para todas as aplicações proxy de aplicação](scripts/powershell-get-all-app-proxy-apps-extended.md) | Lista informações estendidas (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) sobre todas as aplicações proxy de aplicação no seu diretório.  |
| [Listar todas as aplicações proxy de aplicação por grupo de conector](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Lista informações sobre todas as aplicações proxy de aplicação no seu diretório e quais os grupos de conector a que as aplicações são atribuídas. |
| [Obtenha todas as aplicações de Procuração de Aplicação com uma política de vida simbólica](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Lista todas as aplicações de Procuração de Aplicação no seu diretório com uma política de vida simbólica e seus detalhes. Esta amostra requer o [AzureAD V2 PowerShell para](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)a versão de pré-visualização do módulo Graph . |
|**Grupos do conector**||
| [Obtenha todos os grupos e conectores de conectores no diretório](scripts/powershell-get-all-connectors.md) | Lista todos os grupos e conectores do conector no seu diretório. |
| [Mova todas as aplicações atribuídas a um grupo de conector para outro grupo de conector](scripts/powershell-move-all-apps-to-connector-group.md) | Move todas as aplicações atualmente atribuídas a um grupo de conector para um grupo de conector diferente. |
|**Utilizadores e grupo atribuído**||
| [Mostrar utilizadores e grupos atribuídos a uma aplicação proxy de aplicação](scripts/powershell-display-users-group-of-app.md) | Lista os utilizadores e grupos atribuídos a uma aplicação específica de Procuração de Aplicações. |
| [Atribuir um utilizador a uma aplicação](scripts/powershell-assign-user-to-app.md) | Atribui um utilizador específico a uma aplicação. |
| [Atribuir um grupo a uma aplicação](scripts/powershell-assign-group-to-app.md) | Atribui um grupo específico a uma aplicação. |
|**Configuração externa do URL**||
| [Obtenha todas as aplicações proxy de aplicação usando domínios padrão (.msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Lista todas as aplicações de Procuração de Aplicação utilizando domínios predefinidos (.msappproxy.net). |
| [Obtenha todas as aplicações proxy de aplicação usando a publicação wildcard](scripts/powershell-get-all-wildcard-apps.md) | Lista todas as aplicações de Procuração de Aplicação utilizando a publicação wildcard. |
|**Configuração de domínio personalizado**||
| [Obtenha todas as aplicações proxy de aplicação usando domínios personalizados e informações de certificado](scripts/powershell-get-all-custom-domains-and-certs.md) | Lista todas as aplicações de Procuração de Aplicação que estão a usar domínios personalizados e as informações do certificado associadas aos domínios personalizados. |
| [Faça com que todas as aplicações de aplicação Da DProxy Azure serão publicadas sem certificado carregado](scripts/powershell-get-all-custom-domain-no-cert.md) | Lista todas as aplicações de Procuração de Aplicação que estão a usar domínios personalizados mas não tem um certificado TLS/SSL válido carregado. |
| [Faça com que todas as aplicações de aplicação Azure AD Proxy serão publicadas com o certificado idêntico](scripts/powershell-get-custom-domain-identical-cert.md) | Lista todas as aplicações de aplicação Azure AD Proxy publicadas com o certificado idêntico. |
| [Faça com que todas as aplicações de aplicação Azure AD Proxy seja publicada com o certificado idêntico e substitua-a](scripts/powershell-get-custom-domain-replace-cert.md) | Para aplicações de aplicação Azure AD Proxy que são publicadas com um certificado idêntico, permite-lhe substituir o certificado a granel. |
