---
title: Amostras powerShell para Proxy de aplicação AD AD Azure
description: Utilize estas amostras PowerShell para O Azure AD Application Proxy para obter informações sobre aplicações e conectores de aplicações no seu diretório, atribuir utilizadores e grupos a apps e obter informações sobre certificados.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5189df623325578bb3b579f5c4c3456067986ff
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709485"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Exemplos de Azure AD PowerShell para Proxy de Aplicação AD AZure

A tabela seguinte inclui links para exemplos de scripts PowerShell para Azure AD Application Proxy. Estas amostras requerem o [AzureAD V2 PowerShell para módulo Graph](/powershell/azure/active-directory/install-adv2) ou o [AzureAD V2 PowerShell para a versão de pré-visualização do módulo Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), salvo indicação em contrário.


Para obter mais informações sobre os cmdlets utilizados nestas amostras, consulte [a Gestão de Aplicações Proxy Application e](/powershell/module/azuread/#application_proxy_application_management) a [Gestão do Conector de Aplicação Proxy.](/powershell/module/azuread/#application_proxy_connector_management)

| Ligação | Description |
|---|---|
|**Aplicativos Proxy de aplicação**||
| [Listar informações básicas para todas as aplicações Proxy de aplicações](scripts/powershell-get-all-app-proxy-apps-basic.md) | Lista informações básicas (AppId, DisplayName, ObjId) sobre todas as aplicações Proxy de aplicação no seu diretório. |
| [Lista informações alargadas para todas as aplicações Proxy de aplicações](scripts/powershell-get-all-app-proxy-apps-extended.md) | Lista informações alargadas (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) sobre todas as aplicações Proxy de aplicação no seu diretório.  |
| [Listar todas as aplicações Proxy de aplicação por grupo de conector](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Lista informações sobre todas as aplicações Proxy da aplicação no seu diretório e quais os grupos de conector a que as aplicações são atribuídas. |
| [Obtenha todas as aplicações Proxy de aplicação com uma política de vida simbólica](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Lista todas as aplicações Proxy da aplicação no seu diretório com uma política de vida simbólica e seus detalhes. Esta amostra requer a [versão de pré-visualização do módulo AzureAD V2 para a versão de pré-visualização do módulo Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true). |
|**Grupos do conector**||
| [Obtenha todos os grupos de conector e conectores no diretório](scripts/powershell-get-all-connectors.md) | Lista todos os grupos de conector e conectores do seu diretório. |
| [Mover todas as aplicações atribuídas a um grupo de conector para outro grupo de conector](scripts/powershell-move-all-apps-to-connector-group.md) | Move todas as aplicações atualmente atribuídas a um grupo de conector para um grupo de conector diferente. |
|**Utilizadores e grupo atribuídos**||
| [Mostrar utilizadores e grupos atribuídos a uma aplicação Proxy de aplicação](scripts/powershell-display-users-group-of-app.md) | Lista os utilizadores e grupos atribuídos a uma aplicação específica de Procuração de Aplicações. |
| [Atribuir um utilizador a uma aplicação](scripts/powershell-assign-user-to-app.md) | Atribui um utilizador específico a uma aplicação. |
| [Atribuir um grupo a uma aplicação](scripts/powershell-assign-group-to-app.md) | Atribui um grupo específico a uma aplicação. |
|**Configuração de URL externa**||
| [Obtenha todas as aplicações Proxy da Aplicação usando domínios predefinidos (.msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Lista todas as aplicações Application Proxy utilizando domínios predefinidos (.msappproxy.net). |
| [Obtenha todas as aplicações Proxy da Aplicação usando a publicação wildcard](scripts/powershell-get-all-wildcard-apps.md) | Lista todas as aplicações Proxy da Aplicação usando a publicação wildcard. |
|**Configuração de domínio personalizado**||
| [Obtenha todas as aplicações Proxy da Aplicação usando domínios personalizados e informações de certificados](scripts/powershell-get-all-custom-domains-and-certs.md) | Lista todas as aplicações Proxy da Aplicação que estão a usar domínios personalizados e as informações de certificado associadas aos domínios personalizados. |
| [Receba todas as aplicações de aplicação Azure AD Proxy publicadas sem certificado carregado](scripts/powershell-get-all-custom-domain-no-cert.md) | Lista todas as aplicações Proxy da Aplicação que estão a usar domínios personalizados mas não têm um certificado TLS/SSL válido carregado. |
| [Obtenha todas as aplicações de aplicação Azure AD Proxy publicadas com o certificado idêntico](scripts/powershell-get-custom-domain-identical-cert.md) | Lista todas as aplicações de aplicação Azure AD Proxy publicadas com o certificado idêntico. |
| [Obtenha todas as aplicações de aplicação Azure AD Proxy publicadas com o certificado idêntico e substitua-a](scripts/powershell-get-custom-domain-replace-cert.md) | Para aplicações Azure AD Proxy que são publicadas com um certificado idêntico, permite-lhe substituir o certificado a granel. |
