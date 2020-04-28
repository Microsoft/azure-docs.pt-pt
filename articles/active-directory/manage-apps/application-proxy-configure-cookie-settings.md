---
title: Definições de cookies proxy de aplicação - Diretório Ativo Azure / Microsoft Docs
description: O Azure Ative Directory (Azure AD) tem acesso e cookies de sessão para aceder a aplicações no local através do Application Proxy. Neste artigo, você vai descobrir como usar e configurar as definições de cookies.
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
ms.openlocfilehash: bcedb24a0efdbabaaef150fc3d5aff07d210ce23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481369"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Configurações de cookies para aceder a aplicações no local no Diretório Ativo do Azure

O Azure Ative Directory (Azure AD) tem acesso e cookies de sessão para aceder a aplicações no local através do Application Proxy. Descubra como utilizar as definições de cookies proxy de aplicação. 

## <a name="what-are-the-cookie-settings"></a>Quais são as definições de cookies?

[Aplicação Proxy](application-proxy.md) utiliza as seguintes definições de cookies de acesso e sessão.

| Definição de cookies | Predefinição | Descrição | Recomendações |
| -------------- | ------- | ----------- | --------------- |
| Utilizar cookie apenas http | **Não** | **Sim** permite que o Application Proxy inclua a bandeira HTTPOnly em cabeçalhos de resposta HTTP. Esta bandeira fornece benefícios adicionais de segurança, por exemplo, impede que o script do lado do cliente (CSS) copie ou modifique os cookies.<br></br><br></br>Antes de apoiarmos a definição HTTP-Only, application Proxy encriptado e transmitido cookies através de um canal TLS seguro para proteger contra modificações. | Use **Sim** por causa dos benefícios adicionais de segurança.<br></br><br></br>Use **Não** para clientes ou agentes de utilizador que necessitem de acesso ao cookie de sessão. Por exemplo, utilize **não** para um cliente RDP ou MTSC que se conecta a um servidor gateway de ambiente de trabalho remoto através de Proxy de aplicação.|
| Use cookie seguro | **Não** | **Sim** permite que o Application Proxy inclua a bandeira segura em cabeçalhos de resposta HTTP. Os Cookies Seguros aumentam a segurança transmitindo cookies através de um canal seguro TLS, como HTTPS. Isto impede que os cookies sejam observados por partes não autorizadas devido à transmissão do cookie em texto claro. | Use **Sim** por causa dos benefícios adicionais de segurança.|
| Use cookie persistente | **Não** | **Sim** permite que o Application Proxy defina os seus cookies de acesso para não expirar quando o navegador da Web está fechado. A persistência dura até que o token de acesso expire, ou até que o utilizador apague manualmente os cookies persistentes. | Não **No** utilize devido ao risco de segurança associado à manutenção dos utilizadores autenticados.<br></br><br></br>Sugerimos apenas usar **Sim** para aplicações mais antigas que não podem partilhar cookies entre processos. É melhor atualizar a sua aplicação para lidar com a partilha de cookies entre processos em vez de usar cookies persistentes. Por exemplo, pode precisar de cookies persistentes para permitir que um utilizador abra documentos do Office na vista do explorador a partir de um site do SharePoint. Sem cookies persistentes, esta operação pode falhar se os cookies de acesso não forem partilhados entre o navegador, o processo do explorador e o processo do Office. |

## <a name="samesite-cookies"></a>Cookies do mesmo local
A partir da versão Chrome 80 e eventualmente nos navegadores que alavancam o Crómio, os cookies que não especificam o atributo [do SameSite](https://web.dev/samesite-cookies-explained) serão tratados como se tivessem sido definidos para **sameSite=Lax**. O atributo do SameSite declara como os cookies devem ser restringidos a um contexto do mesmo local. Quando definido para Lax, o cookie é apenas para enviar para pedidos do mesmo local ou navegação de alto nível. No entanto, a Application Proxy exige que estes cookies sejam preservados no contexto de terceiros, de forma a manter os utilizadores devidamente assinados durante a sua sessão. Devido a isso, estamos a fazer atualizações para o acesso ao Proxy de aplicação e cookies de sessão para evitar o impacto adverso desta mudança. As atualizações incluem:

* Definição do atributo **sameSite** a **None**. Isto permite que os cookies de acesso e sessões de procuração de aplicação sejam devidamente enviados no contexto de terceiros.
* Definir a definição de **Cookie seguro de utilização** para utilizar **sim** como padrão. O Chrome também requer que os cookies especifiquem a bandeira Secure ou será rejeitado. Esta alteração aplicar-se-á a todas as aplicações existentes publicadas através do Application Proxy. Note que os cookies de acesso proxy de aplicação sempre foram definidos para Secure e apenas transmitidos através de HTTPS. Esta alteração só se aplicará aos cookies da sessão.

Estas alterações aos cookies proxy de aplicação serão lançadas ao longo das próximas semanas antes da data de lançamento do Chrome 80.

Além disso, se a sua aplicação back-end tiver cookies que precisam de estar disponíveis num contexto de terceiros, deve optar explicitamente por alterar a sua aplicação para utilizar o SameSite=None para estes cookies. Application Proxy traduz o cabeçalho Set-Cookie para o seu URLS e respeitará as definições para estes cookies definidas pela aplicação back-end.



## <a name="set-the-cookie-settings---azure-portal"></a>Definir as definições de cookies - Portal Azure
Para definir as definições de cookies utilizando o portal Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Navegue para **a Azure Ative Directory** > **Enterprise aplica** > **todas as aplicações.**
3. Selecione a aplicação para a qual pretende ativar uma definição de cookies.
4. Clique em **Procuração de Aplicação**.
5. Em **Definições Adicionais,** defina a definição do cookie para **Sim** ou **Não**.
6. Clique em **Guardar** para aplicar as suas alterações. 

## <a name="view-current-cookie-settings---powershell"></a>Ver as definições de cookies atuais - PowerShell

Para ver as definições de cookies atuais para a aplicação, utilize este comando PowerShell:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Definir definições de cookies - PowerShell

Nos seguintes comandos ```<ObjectId>``` PowerShell, encontra-se o ObjectId da aplicação. 

**Http-Only Cookie** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Cookie seguro**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Cookies Persistentes**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
