---
title: Use plataforma de identidade Microsoft para iniciar sessão dos utilizadores em dispositivos sem browser | Azure
description: Crie fluxos de autenticação incorporado e sem navegador com a concessão do código de dispositivo.
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
ms.date: 04/20/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86e875108e0349c0ab08a7217074e2afe23bcacc
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544925"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Plataforma de identidade da Microsoft e o fluxo de código de dispositivo do OAuth 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

O suporte de plataforma de identidade do Microsoft da [concessão do código de dispositivo](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), que permite aos utilizadores iniciar sessão dispositivos de restrição de entrada, como uma smart TV, o dispositivo de IoT ou impressora.  Para ativar este fluxo, o dispositivo tem a visita de utilizador uma página Web no browser noutro dispositivo para iniciar sessão.  Assim que o utilizador inicia sessão, o dispositivo é capaz de obter os tokens de acesso e tokens de atualização, conforme necessário.  

> [!IMPORTANT]
> Neste momento, o ponto de extremidade de plataforma do Microsoft identity só suporta o fluxo de dispositivo para inquilinos do Azure AD, mas as contas pessoais não.  Isso significa que tem de utilizar um ponto final definido como um inquilino, ou o `organizations` ponto final.  
>
> Contas pessoais que estão convidadas a um inquilino do Azure AD será capazes de usar a concessão de fluxo de dispositivo, mas somente no contexto do inquilino.

> [!NOTE]
> O ponto de extremidade de plataforma de identidade Microsoft não suporta todos os cenários do Azure Active Directory e funcionalidades. Para determinar se deve utilizar o ponto de extremidade de plataforma do Microsoft identity, leia sobre [limitações de plataforma de identidade do Microsoft](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagrama de protocolo

O fluxo de código de dispositivo inteiro é semelhante ao seguinte diagrama. Nós descrevemos, cada um dos passos neste artigo.

![Fluxo de código do dispositivo](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Pedido de autorização de dispositivo

O cliente verifique primeiro com o servidor de autenticação para um código de dispositivo e utilizador que é utilizado para iniciar a autenticação. O cliente recolhe este pedido a partir do `/devicecode` ponto final. Este pedido, o cliente também deve incluir as permissões necessárias para adquirir do usuário. Desde o momento em que este pedido é enviado, o utilizador tem apenas de 15 minutos para iniciar sessão (o valor usual para `expires_in`), para que apenas fazer este pedido, quando o utilizador indicou que estão prontos para iniciar sessão.

> [!TIP]
> Experimente executar este pedido no Postman!
> [![Executar no Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Necessário |O inquilino do diretório que pretende pedir permissão do. Isso pode ser no formato de nome amigável ou de GUID.  |
| `client_id` | Necessário | O **ID da aplicação (cliente)** que o [portal do Azure – registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída à sua aplicação. |
| `scope` | Recomendado | Uma lista separada por espaço de [âmbitos](v2-permissions-and-consent.md) que pretende que o utilizador para autorizar.  |

### <a name="device-authorization-response"></a>Resposta de autorização de dispositivo

Uma resposta com êxito será um objeto JSON que contém as informações necessárias para permitir ao utilizador iniciar sessão.  

| Parâmetro | Formato | Descrição |
| ---              | --- | --- |
|`device_code`     | String | Uma cadeia longa utilizada para verificar a sessão entre o cliente e o servidor de autorização. O cliente utiliza este parâmetro para pedir o token de acesso do servidor de autorização. |
|`user_code`       | String | Uma cadeia de caracteres curta mostrada ao usuário que é utilizado para identificar a sessão num dispositivo secundário.|
|`verification_uri`| URI | O URI que o utilizador deve ir para com o `user_code` para iniciar sessão. |
|`verification_uri_complete`| URI | Um URI que combina os `user_code` e o `verification_uri`, utilizado para transmissão não textual ao usuário (por exemplo, via Bluetooth para um dispositivo ou por meio de um código QR).  |
|`expires_in`      | int | O número de segundos antes do `device_code` e `user_code` expirar. |
|`interval`        | int | O número de segundos que o cliente deve aguardar entre as solicitações de sondagem. |
| `message`        | String | Uma cadeia de caracteres legível por humanos com instruções para o utilizador. Este texto pode ser localizado, incluindo uma **parâmetro de consulta** no pedido do formulário `?mkt=xx-XX`, preenchendo o código de cultura do idioma apropriado. |

## <a name="authenticating-the-user"></a>Autenticar o utilizador

Após a receção a `user_code` e `verification_uri`, o cliente apresenta ao usuário, instruindo-los para iniciar sessão com o seu telemóvel ou browser de PC.  Além disso, o cliente pode utilizar um código QR ou mecanismo similar para apresentar os `verfication_uri_complete`, que o irá direcionar a etapa de introduzir o `user_code` para o utilizador.

Enquanto o utilizador está a autenticar no `verification_uri`, o cliente deve ser a sondagem da `/token` ponto final para utilizar token solicitado o `device_code`.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Parâmetro | Necessário | Descrição|
| -------- | -------- | ---------- |
| `grant_type` | Necessário | Tem de ser `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Necessário | Tem de corresponder a `client_id` utilizado no pedido inicial. |
| `device_code`| Necessário | O `device_code` devolvido no pedido de autorização de dispositivo.  |

### <a name="expected-errors"></a>Erros esperados

O fluxo de código de dispositivo é um protocolo de consulta para que o cliente deve esperar que vai receber erros antes do utilizador concluiu a autenticar.  

| Erro | Descrição | Ação de cliente |
| ------ | ----------- | -------------|
| `authorization_pending` | O utilizador não foi concluída de autenticação, mas ainda não foi cancelada o fluxo. | Repita o pedido depois de, pelo menos, `interval` segundos. |
| `authorization_declined` | O utilizador final negou o pedido de autorização.| Parar a consulta e reverter para um Estado de funcionamento não autenticado.  |
| `bad_verification_code`| O `device_code` enviada para o `/token` ponto final não foi reconhecido. | Certifique-se de que o cliente está a enviar o correto `device_code` no pedido. |
| `expired_token` | Pelo menos `expires_in` passaram segundos e autenticação já não é possível com esta `device_code`. | Parar a consulta e reverter para um Estado de funcionamento não autenticado. |

### <a name="successful-authentication-response"></a>Resposta de autenticação com êxito

Uma resposta com êxito de token terá o seguinte aspeto:

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
| `token_type` | String| Sempre "Bearer. |
| `scope` | Cadeias de caracteres de espaço separado | Se foi devolvido um token de acesso, lista os âmbitos que o token de acesso é válido para. |
| `expires_in`| int | É válido para o número de segundos antes do token de acesso incluído. |
| `access_token`| Cadeia opaca | Emitido para o [âmbitos](v2-permissions-and-consent.md) que foram solicitados.  |
| `id_token`   | JWT | If emitido original `scope` parâmetro incluído o `openid` âmbito.  |
| `refresh_token` | Cadeia opaca | If emitido original `scope` parâmetro incluído `offline_access`.  |

Pode usar o token de atualização para adquirir novos tokens de acesso e tokens utilizando o mesmo fluxo documentado em de atualização do [documentação de fluxo de código de OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
