---
title: Fluxo de código de dispositivo OAuth 2.0 / Azure
titleSuffix: Microsoft identity platform
description: Inscreva-se em utilizadores sem browser. Construa fluxos de autenticação incorporados e sem navegador utilizando a concessão de autorização do dispositivo.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b45ba0c0b417be9cf308fedbb7fad2f6ad5fceaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159736"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Plataforma de identidade da Microsoft e fluxo de autorização de autorização de dispositivo OAuth 2.0

A plataforma de identidade da Microsoft suporta a [concessão](https://tools.ietf.org/html/rfc8628)de autorização do dispositivo , que permite que os utilizadores inscrevam em dispositivos limitados de entrada, como uma smart TV, dispositivo IoT ou impressora.  Para ativar este fluxo, o dispositivo tem o utilizador a visitar uma página web no seu navegador noutro dispositivo para iniciar sessão.  Assim que o utilizador entrar, o dispositivo é capaz de obter fichas de acesso e de atualização de fichas conforme necessário.  

Este artigo descreve como programar diretamente contra o protocolo na sua aplicação.  Sempre que possível, recomendamos que utilize as Bibliotecas de Autenticação da Microsoft (MSAL) suportadas em vez de adquirir fichas e ligar para [APIs web protegidos](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Veja também as [aplicações de amostra que utilizam o MSAL.](sample-v2-code.md)

> [!NOTE]
> O ponto final da plataforma de identidade da Microsoft não suporta todos os cenários e funcionalidades do Azure Ative Directory. Para determinar se deve utilizar o ponto final da plataforma de identidade da Microsoft, leia sobre [as limitações](active-directory-v2-limitations.md)da plataforma de identidade da Microsoft .

## <a name="protocol-diagram"></a>Diagrama de protocolo

Todo o fluxo de código do dispositivo parece semelhante ao próximo diagrama. Descrevemos cada um dos passos mais tarde neste artigo.

![Fluxo de código do dispositivo](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Pedido de autorização do dispositivo

O cliente deve primeiro verificar com o servidor de autenticação um dispositivo e código de utilizador que seja utilizado para iniciar a autenticação. O cliente recolhe este `/devicecode` pedido do ponto final. Neste pedido, o cliente deve incluir também as permissões que necessita para adquirir ao utilizador. A partir do momento em que este pedido é enviado, o `expires_in`utilizador tem apenas 15 minutos para iniciar sessão (o valor habitual para), pelo que só faça este pedido quando o utilizador tiver indicado que está pronto para iniciar sessão.

> [!TIP]
> Tente executar este pedido no Carteiro!
> [![Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Necessário | Pode ser /comum, /consumidores ou /organizações.  Também pode ser o inquilino de diretório que pretende solicitar permissão em formato DE NOME GUIA ou amigável.  |
| `client_id` | Necessário | O ID de **Aplicação (cliente)** que o [portal Azure – App registra](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída à sua app. |
| `scope` | Recomendado | Uma lista de [âmbitos separados](v2-permissions-and-consent.md) pelo espaço que pretende que o utilizador consinta.  |

### <a name="device-authorization-response"></a>Resposta à autorização do dispositivo

Uma resposta bem sucedida será um objeto JSON contendo as informações necessárias para permitir que o utilizador assine a sua inscrição.  

| Parâmetro | Formato | Descrição |
| ---              | --- | --- |
|`device_code`     | Cadeia | Uma longa corda usada para verificar a sessão entre o cliente e o servidor de autorização. O cliente utiliza este parâmetro para solicitar o sinal de acesso do servidor de autorização. |
|`user_code`       | Cadeia | Uma curta corda mostrada ao utilizador que é usada para identificar a sessão num dispositivo secundário.|
|`verification_uri`| URI | O URI a que o `user_code` utilizador deve ir com o para iniciar sessão. |
|`expires_in`      | int | O número de `device_code` segundos antes do e `user_code` expirar. |
|`interval`        | int | O número de segundos que o cliente deve esperar entre os pedidos de sondagens. |
| `message`        | Cadeia | Uma corda legível pelo homem com instruções para o utilizador. Isto pode ser localizado através da inclusão de um `?mkt=xx-XX`parâmetro de **consulta** a pedido do formulário, preenchendo o código de cultura linguística apropriado. |

> [!NOTE]
> O `verification_uri_complete` campo de resposta não está incluído ou apoiado neste momento.  Mencionamos isto porque se ler o `verification_uri_complete` [padrão,](https://tools.ietf.org/html/rfc8628) vê que está listado como uma parte opcional da norma de fluxo de código do dispositivo.

## <a name="authenticating-the-user"></a>Autenticação do utilizador

Depois de `user_code` receber `verification_uri`o e , o cliente apresenta-os ao utilizador, instruindo-os a iniciar em sessão utilizando o seu telemóvel ou navegador pc.

Se o utilizador autenticar com uma conta pessoal (em /comum ou /consumidores), será-lhes solicitado que volte a inscrever-se para transferir o estado de autenticação para o dispositivo.  Será também solicitado que dêem o seu consentimento, para garantir que estão cientes das permissões que estão a ser concedidas.  Isto não se aplica ao trabalho ou às contas escolares utilizadas para autenticar. 

Enquanto o utilizador estiver `verification_uri`a autenticar no `/token` , o cliente deve estar `device_code`a sondar o ponto final do token solicitado utilizando o .

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parâmetro | Necessário | Descrição|
| -------- | -------- | ---------- |
| `tenant`  | Necessário | O mesmo inquilino ou pseudónimo de inquilino utilizado no pedido inicial. | 
| `grant_type` | Necessário | Deve ser`urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Necessário | Deve coincidir `client_id` com o utilizado no pedido inicial. |
| `device_code`| Necessário | O `device_code` devolvido no pedido de autorização do dispositivo.  |

### <a name="expected-errors"></a>Erros esperados

O fluxo de código do dispositivo é um protocolo de votação, pelo que o seu cliente deve esperar receber erros antes de o utilizador terminar de autenticar.  

| Erro | Descrição | Ação do Cliente |
| ------ | ----------- | -------------|
| `authorization_pending` | O utilizador ainda não terminou de autenticar, mas não cancelou o fluxo. | Repita o pedido `interval` após pelo menos segundos. |
| `authorization_declined` | O utilizador final negou o pedido de autorização.| Pare de votar e volte para um estado não autenticado.  |
| `bad_verification_code`| O `device_code` enviado `/token` para o ponto final não foi reconhecido. | Verifique se o cliente `device_code` está a enviar o correto no pedido. |
| `expired_token` | Pelo `expires_in` menos segundos se passaram, e a `device_code`autenticação já não é possível com isto . | Pare de votar e volte para um estado não autenticado. |   

### <a name="successful-authentication-response"></a>Resposta de autenticação bem sucedida

Uma resposta simbólica bem sucedida será como:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parâmetro | Formato | Descrição |
| --------- | ------ | ----------- |
| `token_type` | Cadeia| Sempre "Portador. |
| `scope` | Cordas separadas do espaço | Se um token de acesso foi devolvido, isto lista os âmbitos para os que o token de acesso é válido. |
| `expires_in`| int | Número de segundos antes do token de acesso incluído é válido para. |
| `access_token`| Corda opaca | Emitido para os [âmbitos solicitados.](v2-permissions-and-consent.md)  |
| `id_token`   | JWT | Emitido se o `scope` parâmetro original `openid` incluía o âmbito.  |
| `refresh_token` | Corda opaca | Emitido se o `scope` parâmetro original `offline_access`incluído .  |

Pode utilizar o token de atualização para adquirir novos tokens de acesso e fichas de atualização utilizando o mesmo fluxo documentado na documentação de fluxo do [Código OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
