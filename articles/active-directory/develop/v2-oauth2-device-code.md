---
title: Usar a plataforma de identidade da Microsoft para conectar usuários em dispositivos sem navegador | Azure
description: Crie fluxos de autenticação incorporados e sem navegador usando a concessão de código de dispositivo.
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
ms.date: 06/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 274c4e89ff3f996cc71cdacdfb7b5b72e813ae4b
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297667"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Plataforma de identidade da Microsoft e o fluxo de código do dispositivo OAuth 2,0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

A plataforma de identidade da Microsoft dá suporte à [concessão de código de dispositivo](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), que permite aos usuários entrar em dispositivos com restrição de entrada, como uma TV inteligente, um dispositivo IOT ou uma impressora.  Para habilitar esse fluxo, o dispositivo tem o usuário visita uma página da Web em seu navegador em outro dispositivo para entrar.  Depois que o usuário entra, o dispositivo é capaz de obter tokens de acesso e atualizar tokens conforme necessário.  

> [!IMPORTANT]
> Neste momento, o ponto de extremidade da plataforma Microsoft Identity só dá suporte ao fluxo do dispositivo para locatários do Azure AD, mas não a contas pessoais.  Isso significa que você deve usar um ponto de extremidade configurado como um locatário ou o `organizations` ponto de extremidade.  Esse suporte será habilitado em breve. 
>
> As contas pessoais que são convidadas para um locatário do Azure AD poderão usar a concessão de fluxo do dispositivo, mas somente no contexto do locatário.
>
> Como uma observação adicional, o `verification_uri_complete` campo de resposta não é incluído ou tem suporte no momento.  

> [!NOTE]
> O ponto de extremidade da plataforma Microsoft Identity não dá suporte a todos os cenários e recursos de Azure Active Directory. Para determinar se você deve usar o ponto de extremidade da plataforma de identidade da Microsoft, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagrama de protocolo

Todo o fluxo de código do dispositivo é semelhante ao próximo diagrama. Descrevemos cada uma das etapas mais adiante neste artigo.

![Fluxo de código do dispositivo](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Solicitação de autorização do dispositivo

O cliente deve primeiro verificar o servidor de autenticação para obter um código de usuário e de dispositivo usado para iniciar a autenticação. O cliente coleta essa solicitação do `/devicecode` ponto de extremidade. Nessa solicitação, o cliente também deve incluir as permissões que ele precisa para adquirir do usuário. A partir do momento em que essa solicitação é enviada, o usuário tem apenas 15 minutos para entrar (o valor `expires_in`comum para), portanto, faça essa solicitação apenas quando o usuário indicou que está pronto para entrar.

> [!TIP]
> Tente executar esta solicitação no postmaster!
> [![Tente executar esta solicitação no postmaster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Necessário |O locatário do diretório do qual você deseja solicitar permissão. Isso pode estar no formato de nome amigável ou GUID.  |
| `client_id` | Necessário | A **ID do aplicativo (cliente)** que a [portal do Azure – registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo. |
| `scope` | Recomendado | Uma lista separada por espaços de [](v2-permissions-and-consent.md) escopos aos quais você deseja que o usuário concorde.  |

### <a name="device-authorization-response"></a>Resposta de autorização do dispositivo

Uma resposta bem-sucedida será um objeto JSON que contém as informações necessárias para permitir que o usuário entre.  

| Parâmetro | Formato | Descrição |
| ---              | --- | --- |
|`device_code`     | Cadeia | Uma cadeia de caracteres longa usada para verificar a sessão entre o cliente e o servidor de autorização. O cliente usa esse parâmetro para solicitar o token de acesso do servidor de autorização. |
|`user_code`       | Cadeia | Uma cadeia de caracteres curta mostrada para o usuário que é usado para identificar a sessão em um dispositivo secundário.|
|`verification_uri`| URI | O URI ao qual o usuário deve ir com `user_code` o para entrar. |
|`expires_in`      | int | O número de segundos antes de `device_code` e `user_code` expirar. |
|`interval`        | int | O número de segundos que o cliente deve aguardar entre solicitações de sondagem. |
| `message`        | Cadeia | Uma cadeia de caracteres legível por humanos com instruções para o usuário. Isso pode ser localizado com a inclusão de um **parâmetro de consulta** na solicitação do `?mkt=xx-XX`formulário, preenchendo o código de cultura do idioma apropriado. |

## <a name="authenticating-the-user"></a>Autenticando o usuário

Depois de receber `user_code` o `verification_uri`e o, o cliente os exibe para o usuário, instruindo-os a entrar usando seu telefone celular ou navegador de PC.  Além disso, o cliente pode usar um código QR ou um mecanismo semelhante para `verfication_uri_complete`exibir o, que executará a etapa de `user_code` inserir o para o usuário.

Enquanto o usuário está Autenticando no `verification_uri`, o cliente deve sondar o `/token` ponto de extremidade para o token solicitado `device_code`usando o.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Parâmetro | Necessário | Descrição|
| -------- | -------- | ---------- |
| `grant_type` | Necessário | Deve ser`urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Necessário | Deve corresponder ao `client_id` usado na solicitação inicial. |
| `device_code`| Necessário | O `device_code` retornado na solicitação de autorização do dispositivo.  |

### <a name="expected-errors"></a>Erros esperados

O fluxo de código do dispositivo é um protocolo de sondagem, de modo que o cliente deve esperar receber erros antes de concluir a autenticação do usuário.  

| Erro | Descrição | Ação do cliente |
| ------ | ----------- | -------------|
| `authorization_pending` | O usuário não concluiu a autenticação, mas não cancelou o fluxo. | Repita a solicitação após pelo menos `interval` segundos. |
| `authorization_declined` | O usuário final negou a solicitação de autorização.| Pare a sondagem e reverta para um estado não autenticado.  |
| `bad_verification_code`| O `device_code` enviado para o `/token` ponto de extremidade não foi reconhecido. | Verifique se o cliente está enviando o correto `device_code` na solicitação. |
| `expired_token` | Pelo menos `expires_in` segundos passaram e a autenticação não é mais possível com isso `device_code`. | Pare a sondagem e a reversão para um estado não autenticado. |

### <a name="successful-authentication-response"></a>Resposta de autenticação bem-sucedida

Uma resposta de token bem-sucedida terá A seguinte aparência:

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
| `token_type` | Cadeia| Sempre "portador". |
| `scope` | Cadeias de caracteres separadas por espaço | Se um token de acesso for retornado, isso listará os escopos para os quais o token de acesso é válido. |
| `expires_in`| int | Número de segundos antes que o token de acesso incluído seja válido para. |
| `access_token`| Cadeia de caracteres opaca | Emitido para os [](v2-permissions-and-consent.md) escopos que foram solicitados.  |
| `id_token`   | JWT | Emitido se o parâmetro `scope` original incluía `openid` o escopo.  |
| `refresh_token` | Cadeia de caracteres opaca | Emitido se o parâmetro `scope` original estiver `offline_access`incluído.  |

Você pode usar o token de atualização para adquirir novos tokens de acesso e atualizar tokens usando o mesmo fluxo documentado na [documentação do fluxo de código OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
