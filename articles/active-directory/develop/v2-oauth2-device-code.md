---
title: Usar a plataforma de identidade da Microsoft para conectar usuários em dispositivos sem navegador | Azure
description: Crie fluxos de autenticação incorporados e sem navegador usando a concessão de autorização do dispositivo.
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
ms.date: 10/24/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90922a48f213ecd506f08f616fe8c28ab44776a2
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893885"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Plataforma de identidade da Microsoft e o fluxo de concessão de autorização de dispositivo OAuth 2,0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

A plataforma de identidade da Microsoft dá suporte à [concessão de autorização de dispositivo](https://tools.ietf.org/html/rfc8628), que permite aos usuários entrar em dispositivos com restrição de entrada, como uma TV inteligente, um dispositivo IOT ou uma impressora.  Para habilitar esse fluxo, o dispositivo tem o usuário visita uma página da Web em seu navegador em outro dispositivo para entrar.  Depois que o usuário entra, o dispositivo é capaz de obter tokens de acesso e atualizar tokens conforme necessário.  

> [!NOTE]
> O ponto de extremidade da plataforma Microsoft Identity não dá suporte a todos os cenários e recursos de Azure Active Directory. Para determinar se você deve usar o ponto de extremidade da plataforma de identidade da Microsoft, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagrama de protocolo

Todo o fluxo de código do dispositivo é semelhante ao próximo diagrama. Descrevemos cada uma das etapas mais adiante neste artigo.

![Fluxo de código do dispositivo](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Solicitação de autorização do dispositivo

O cliente deve primeiro verificar o servidor de autenticação para obter um código de usuário e de dispositivo usado para iniciar a autenticação. O cliente coleta essa solicitação do ponto de extremidade `/devicecode`. Nessa solicitação, o cliente também deve incluir as permissões que ele precisa para adquirir do usuário. A partir do momento em que essa solicitação é enviada, o usuário tem apenas 15 minutos para entrar (o valor comum para `expires_in`), portanto, faça essa solicitação apenas quando o usuário indicou que está pronto para entrar.

> [!TIP]
> Tente executar esta solicitação no postmaster!
> [![tentar executar esta solicitação no postmaster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | Pode ser/Common,/consumers ou/Organizations.  Ele também pode ser o locatário de diretório do qual você deseja solicitar permissão no formato GUID ou nome amigável.  |
| `client_id` | Obrigatório | A **ID do aplicativo (cliente)** que a [portal do Azure – registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo. |
| `scope` | Recomendado | Uma lista separada por espaços de [escopos](v2-permissions-and-consent.md) aos quais você deseja que o usuário concorde.  |

### <a name="device-authorization-response"></a>Resposta de autorização do dispositivo

Uma resposta bem-sucedida será um objeto JSON que contém as informações necessárias para permitir que o usuário entre.  

| Parâmetro | Formato | Descrição |
| ---              | --- | --- |
|`device_code`     | String | Uma cadeia de caracteres longa usada para verificar a sessão entre o cliente e o servidor de autorização. O cliente usa esse parâmetro para solicitar o token de acesso do servidor de autorização. |
|`user_code`       | String | Uma cadeia de caracteres curta mostrada para o usuário que é usado para identificar a sessão em um dispositivo secundário.|
|`verification_uri`| URI | O URI ao qual o usuário deve ir com o `user_code` para entrar. |
|`expires_in`      | int | O número de segundos antes da `device_code` e `user_code` expirar. |
|`interval`        | int | O número de segundos que o cliente deve aguardar entre solicitações de sondagem. |
| `message`        | String | Uma cadeia de caracteres legível por humanos com instruções para o usuário. Isso pode ser localizado com a inclusão de um **parâmetro de consulta** na solicitação do formulário `?mkt=xx-XX`, preenchendo o código de cultura do idioma apropriado. |

> [!NOTE]
> O campo de resposta `verification_uri_complete` não está incluído ou tem suporte no momento.  Mencionamos isso porque, se você ler o [padrão](https://tools.ietf.org/html/rfc8628) , verá que `verification_uri_complete` está listado como uma parte opcional do padrão de fluxo de código do dispositivo.

## <a name="authenticating-the-user"></a>Autenticando o usuário

Depois de receber o `user_code` e `verification_uri`, o cliente os exibe para o usuário, instruindo-os a entrar usando seu telefone celular ou navegador de PC.

Se o usuário se autenticar com uma conta pessoal (em/Common ou/consumers), será solicitado que você entre novamente para transferir o estado de autenticação para o dispositivo.  Eles também serão solicitados a fornecer consentimento, para garantir que estejam cientes das permissões que estão sendo concedidas.  Isso não se aplica a contas corporativas ou de estudante usadas para autenticação. 

Enquanto o usuário está Autenticando no `verification_uri`, o cliente deve sondar o ponto de extremidade de `/token` para o token solicitado usando o `device_code`.

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parâmetro | Obrigatório | Descrição|
| -------- | -------- | ---------- |
| `tenant`  | Obrigatório | O mesmo alias de locatário ou locatário usado na solicitação inicial. | 
| `grant_type` | Obrigatório | Deve ser `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Obrigatório | Deve corresponder ao `client_id` usado na solicitação inicial. |
| `device_code`| Obrigatório | O `device_code` retornado na solicitação de autorização do dispositivo.  |

### <a name="expected-errors"></a>Erros esperados

O fluxo de código do dispositivo é um protocolo de sondagem, de modo que o cliente deve esperar receber erros antes de concluir a autenticação do usuário.  

| Erro | Descrição | Ação do cliente |
| ------ | ----------- | -------------|
| `authorization_pending` | O usuário não concluiu a autenticação, mas não cancelou o fluxo. | Repita a solicitação após pelo menos `interval` segundos. |
| `authorization_declined` | O usuário final negou a solicitação de autorização.| Pare a sondagem e reverta para um estado não autenticado.  |
| `bad_verification_code`| O `device_code` enviado ao ponto de extremidade de `/token` não foi reconhecido. | Verifique se o cliente está enviando o `device_code` correto na solicitação. |
| `expired_token` | Pelo menos `expires_in` segundos passaram e a autenticação não é mais possível com esse `device_code`. | Pare a sondagem e a reversão para um estado não autenticado. |   

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
| `token_type` | String| Sempre "portador". |
| `scope` | Cadeias de caracteres separadas por espaço | Se um token de acesso for retornado, isso listará os escopos para os quais o token de acesso é válido. |
| `expires_in`| int | Número de segundos antes que o token de acesso incluído seja válido para. |
| `access_token`| Cadeia de caracteres opaca | Emitido para os [escopos](v2-permissions-and-consent.md) que foram solicitados.  |
| `id_token`   | JWT | Emitido se o parâmetro `scope` original incluía o escopo `openid`.  |
| `refresh_token` | Cadeia de caracteres opaca | Emitido se o parâmetro de `scope` original for incluído `offline_access`.  |

Você pode usar o token de atualização para adquirir novos tokens de acesso e atualizar tokens usando o mesmo fluxo documentado na [documentação do fluxo de código OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
