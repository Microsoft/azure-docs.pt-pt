---
title: Configurações de cookie do proxy de aplicativo-Azure Active Directory | Microsoft Docs
description: O Azure Active Directory (Azure AD) tem acesso e cookies de sessão para acessar aplicativos locais por meio do proxy de aplicativo. Neste artigo, você descobrirá como usar e definir as configurações de cookie.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca5f1b41e345caafdc465872c948be76c31d55e8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72928862"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Configurações de cookie para acessar aplicativos locais no Azure Active Directory

O Azure Active Directory (Azure AD) tem acesso e cookies de sessão para acessar aplicativos locais por meio do proxy de aplicativo. Descubra como usar as configurações de cookie do proxy de aplicativo. 

## <a name="what-are-the-cookie-settings"></a>Quais são as configurações de cookie?

O [proxy de aplicativo](application-proxy.md) usa as seguintes configurações de acesso e cookie de sessão.

| Configuração de cookie | Predefinição | Descrição | Recomendações |
| -------------- | ------- | ----------- | --------------- |
| Usar cookie somente HTTP | **Não** | **Sim** permite que o proxy de aplicativo inclua o sinalizador HTTPOnly nos cabeçalhos de resposta http. Esse sinalizador fornece benefícios de segurança adicionais, por exemplo, impede que os scripts do lado do cliente (CSS) copiem ou modifiquem os cookies.<br></br><br></br>Antes que tenhamos suporte a configuração somente HTTP, o proxy de aplicativo criptografou e transmitiu cookies em um canal SSL protegido para proteger contra modificação. | Use **Sim** devido aos benefícios de segurança adicionais.<br></br><br></br>Use **não** para clientes ou agentes de usuário que exigem acesso ao cookie de sessão. Por exemplo, use **não** para um cliente RDP ou MTSC que se conecta a um servidor gateway área de trabalho remota por meio do proxy de aplicativo.|
| Usar cookie seguro | **Não** | **Sim** permite que o proxy de aplicativo inclua o sinalizador seguro em cabeçalhos de resposta http. Cookies seguros aumentam a segurança por meio da transmissão de cookies em um canal protegido por TLS, como HTTPS. Isso impede que os cookies sejam observados por partes não autorizadas devido à transmissão do cookie em texto não criptografado. | Use **Sim** devido aos benefícios de segurança adicionais.|
| Usar cookie persistente | **Não** | **Sim** permite que o proxy de aplicativo defina seus cookies de acesso para não expirar quando o navegador da Web é fechado. A persistência dura até que o token de acesso expire ou até que o usuário exclua manualmente os cookies persistentes. | **Não** use devido ao risco de segurança associado à manutenção dos usuários autenticados.<br></br><br></br>Sugerimos apenas o uso de **Sim** para aplicativos mais antigos que não podem compartilhar cookies entre processos. É melhor atualizar seu aplicativo para lidar com cookies de compartilhamento entre processos em vez de usar cookies persistentes. Por exemplo, você pode precisar de cookies persistentes para permitir que um usuário abra documentos do Office na exibição do Explorer de um site do SharePoint. Sem cookies persistentes, essa operação poderá falhar se os cookies de acesso não forem compartilhados entre o navegador, o processo do Explorer e o processo do Office. |

## <a name="samesite-cookies"></a>SameSite cookies
A partir da versão [Chrome 80](https://support.google.com/chrome/a/answer/7679408?hl=en) e, eventualmente, em navegadores que aproveitam [Chromium](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html), os cookies que não especificam o atributo [SameSite](https://web.dev/samesite-cookies-explained) serão tratados como se estivessem definidos como **SameSite = LAX**. O atributo SameSite declara como os cookies devem ser restritos a um contexto de mesmo site. Quando definido como LAX, o cookie é enviado apenas para solicitações de mesmo site ou navegação de nível superior. No entanto, o proxy de aplicativo exige que esses cookies sejam preservados no contexto de terceiros para manter os usuários conectados corretamente durante a sessão. Devido a isso, estamos fazendo atualizações para o acesso ao proxy de aplicativo e cookies de sessão para evitar o impacto adverso dessa alteração. As atualizações incluem:

* Definir o atributo **SameSite** como **None**-permite que os cookies de acesso e sessões do proxy de aplicativo sejam enviados corretamente no contexto de terceiros.
* Definir a configuração **usar cookie seguro** para usar **Sim** como o padrão. O Chrome também exige que os cookies especifiquem o sinalizador de segurança ou que serão rejeitados. Essa alteração será aplicada a todos os aplicativos existentes publicados por meio do proxy de aplicativo. Observe que os cookies de acesso ao proxy de aplicativo sempre foram definidos como seguros e transmitidos somente por HTTPS. Essa alteração será aplicada somente aos cookies de sessão.

Essas alterações nos cookies de proxy de aplicativo serão implantadas ao longo das próximas semanas antes da data de lançamento do Chrome 80.

Além disso, se o aplicativo de back-end tiver cookies que precisam estar disponíveis em um contexto de terceiros, você deverá optar explicitamente por alterar seu aplicativo para usar SameSite = None para esses cookies. O proxy de aplicativo traduz o cabeçalho Set-cookie para suas URLS e respeitará as configurações para esses cookies definidos pelo aplicativo de back-end.



## <a name="set-the-cookie-settings---azure-portal"></a>Definir as configurações de cookie-portal do Azure
Para definir as configurações de cookie usando o portal do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Navegue até **Azure Active Directory** > **aplicativos empresariais** > **todos os aplicativos**.
3. Selecione o aplicativo para o qual você deseja habilitar uma configuração de cookie.
4. Clique em **proxy de aplicativo**.
5. Em **configurações adicionais**, defina a configuração de cookie como **Sim** ou **não**.
6. Clique em **salvar** para aplicar suas alterações. 

## <a name="view-current-cookie-settings---powershell"></a>Exibir configurações de cookie atuais-PowerShell

Para ver as configurações de cookie atuais para o aplicativo, use este comando do PowerShell:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Definir configurações de cookie-PowerShell

Nos comandos do PowerShell a seguir, ```<ObjectId>``` é o ObjectId do aplicativo. 

**Cookie somente http** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Cookie seguro**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Cookies persistentes**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
