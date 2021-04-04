---
title: Configurações de cookies Proxy de aplicação - Azure Ative Directory | Microsoft Docs
description: O Azure Ative Directory (Azure AD) tem cookies de acesso e sessão para aceder a aplicações no local através do Application Proxy. Neste artigo, você vai descobrir como usar e configurar as definições de cookies.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 01/16/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62afe97b44f45bc0b7aa12b33b6a65dd94ecf095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99252207"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Definições de cookies para aceder a aplicações no local no Azure Ative Directory

O Azure Ative Directory (Azure AD) tem cookies de acesso e sessão para aceder a aplicações no local através do Application Proxy. Descubra como utilizar as definições de cookies Proxy da Aplicação. 

## <a name="what-are-the-cookie-settings"></a>Quais são as definições de cookies?

[O Application Proxy](application-proxy.md) utiliza as seguintes definições de cookies de acesso e sessão.

| Definição de cookies | Predefinição | Description | Recomendações |
| -------------- | ------- | ----------- | --------------- |
| Use HTTP-Only Cookie | **Não** | **Sim** permite que o Application Proxy inclua a bandeira HTTPOnly em cabeçalhos de resposta HTTP. Esta bandeira proporciona benefícios adicionais de segurança, por exemplo, evita que a script (CSS) do lado do cliente copie ou modifique os cookies.<br></br><br></br>Antes de suportarmos a definição de HTTP-Only, o Application Proxy encriptava e transmitia cookies através de um canal TLS seguro para proteger contra modificações. | Use **Sim** por causa dos benefícios adicionais de segurança.<br></br><br></br>Use **O** para clientes ou agentes de utilizador que necessitem de acesso ao cookie da sessão. Por exemplo, utilize **O** para um cliente RDP ou MTSC que se conecta a um servidor Remote Desktop Gateway através do Application Proxy.|
| Use Secure Cookie | **Não** | **Sim** permite que o Application Proxy inclua a bandeira Secure em cabeçalhos de resposta HTTP. Os Cookies Seguros aumentam a segurança transmitindo cookies através de um canal protegido TLS, como HTTPS. Isto impede que os cookies sejam observados por partes não autorizadas devido à transmissão do cookie em texto claro. | Use **Sim** por causa dos benefícios adicionais de segurança.|
| Use cookie persistente | **Não** | **Sim** permite que o Application Proxy desemproputa os seus cookies de acesso para não expirar quando o navegador está fechado. A persistência dura até que o token de acesso expire, ou até que o utilizador elimine manualmente os cookies persistentes. | Utilização **Nº** devido ao risco de segurança associado à autenticação dos utilizadores.<br></br><br></br>Sugerimos apenas usar **Sim** para aplicações mais antigas que não podem partilhar cookies entre processos. É melhor atualizar a sua aplicação para lidar com cookies de partilha entre processos em vez de usar cookies persistentes. Por exemplo, poderá necessitar de cookies persistentes para permitir que um utilizador abra documentos do Office na vista do explorador a partir de um site do SharePoint. Sem cookies persistentes, esta operação pode falhar se os cookies de acesso não forem partilhados entre o navegador, o processo de explorador e o processo do Office. |

## <a name="samesite-cookies"></a>SameSite Cookies
A partir da versão Chrome 80 e eventualmente nos navegadores que alavancam o Chromium, os cookies que não especificarem o atributo [SameSite](https://web.dev/samesite-cookies-explained) serão tratados como se estivessem definidos para **SameSite=Lax**. O atributo SameSite declara como os cookies devem ser restringidos a um contexto no mesmo site. Quando definido para Lax, o cookie é apenas para enviar para pedidos no mesmo site ou navegação de nível superior. No entanto, a Application Proxy exige que estes cookies sejam preservados no contexto de terceiros, de forma a manter os utilizadores devidamente informados durante a sessão. Devido a isso, estamos a fazer atualizações para o acesso ao Application Proxy e cookies de sessão para evitar impactos adversos desta mudança. As atualizações incluem:

* Definição do atributo **SameSite** a **Nenhum**. Isto permite que os cookies de acesso e sessões de aplicação proxy sejam corretamente enviados no contexto de terceiros.
* Configurar a definição **de Cookie Seguro de Utilização** para usar **Sim** como padrão. O Chrome também requer que os cookies especifiquem a bandeira Secure ou será rejeitado. Esta alteração aplicar-se-á a todas as aplicações existentes publicadas através do Application Proxy. Note que os cookies de acesso proxy de aplicação sempre foram definidos para Secure e apenas transmitidos através de HTTPS. Esta alteração aplica-se apenas aos cookies de sessão.

Estas alterações aos cookies Proxy da Aplicação serão lançadas ao longo das próximas semanas antes da data de lançamento do Chrome 80.

Além disso, se a sua aplicação back-end tiver cookies que precisam de estar disponíveis num contexto de terceiros, deve optar explicitamente alterando a sua aplicação para utilizar o SameSite=Nenhum para estes cookies. Application Proxy traduz o cabeçalho Set-Cookie para o seu URLS e respeitará as definições para estes cookies definidos pela aplicação back-end.



## <a name="set-the-cookie-settings---azure-portal"></a>Definir as definições de cookies - Portal Azure
Para definir as definições de cookies utilizando o portal Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Navegue para aplicações **da Azure Ative Directory**   >  **Enterprise**   >  **Todas as aplicações**.
3. Selecione a aplicação para a qual deseja ativar uma definição de cookies.
4. Clique **em Aplicação Proxy**.
5. Em **Definições Adicionais**, defina a definição de cookies para **Sim** ou **Não**.
6. Clique em **Guardar** para aplicar as suas alterações. 

## <a name="view-current-cookie-settings---powershell"></a>Ver definições de cookies atuais - PowerShell

Para ver as definições de cookies atuais para a aplicação, utilize este comando PowerShell:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Definir definições de cookies - PowerShell

Nos seguintes comandos PowerShell, ```<ObjectId>``` está o ObjectId da aplicação. 

**Cookie apenas http** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Cookie Seguro**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Cookies Persistentes**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
