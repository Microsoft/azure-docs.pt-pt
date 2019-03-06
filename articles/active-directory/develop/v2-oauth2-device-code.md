---
title: Utilizar do Azure AD v2.0 para iniciar sessão dos utilizadores em dispositivos sem browser | Documentos da Microsoft
description: Crie fluxos de autenticação incorporado e sem navegador com a concessão do código de dispositivo.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 780eec4d-7ee1-48b7-b29f-cd0b8cb41ed3
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1f34796a98acb3ed4241edff57e506990479d1a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443271"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-device-code-flow"></a>O Azure Active Directory v 2.0 e o fluxo de código de dispositivo do OAuth 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

O Azure AD suporta o [concessão do código de dispositivo](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), que permite aos utilizadores iniciar sessão dispositivos de restrição de entrada, como uma smart TV, o dispositivo de IoT ou impressora.  Para ativar este fluxo, o dispositivo tem a visita de utilizador uma página Web no browser noutro dispositivo para iniciar sessão.  Assim que o utilizador inicia sessão, o dispositivo é capaz de obter os tokens de acesso e tokens de atualização, conforme necessário.  

> [!Important] 
> Neste momento, o ponto final v2.0 só suporta o fluxo de dispositivo para inquilinos do Azure AD, mas as contas pessoais não.  Isso significa que tem de utilizar um ponto final definido como um inquilino ou o ponto final de organizações.  
>
> Contas pessoais que estão convidadas a um inquilino do Azure AD será capazes de usar a concessão de fluxo de dispositivo, mas somente no contexto do inquilino.

> [!NOTE]
> O ponto final v2.0 não suporta todos os cenários do Azure Active Directory e funcionalidades. Para determinar se deve utilizar o ponto final v2.0, leia sobre [v2.0 limitações](active-directory-v2-limitations.md).
>

## <a name="protocol-diagram"></a>Diagrama de protocolo

O fluxo de código de dispositivo inteiro é semelhante ao seguinte diagrama. Nós descrevemos, cada um dos passos neste artigo.

![Fluxo de código de dispositivo](media/v2-oauth2-device-flow/v2-oauth-device-flow.png)

## <a name="device-authorization-request"></a>Pedido de autorização de dispositivo

O cliente verifique primeiro com o servidor de autenticação para um código de dispositivo e utilizador, utilizado para iniciar a autenticação.  O cliente recolhe este pedido a partir do `/devicecode` ponto final. Este pedido, o cliente também deve incluir as permissões necessárias para adquirir do usuário.  Desde o momento em que este pedido é enviado, o utilizador tem apenas de 15 minutos para iniciar sessão (o valor usual para `expires_in`), para que apenas fazer este pedido, quando o utilizador indicou que estão prontos para iniciar sessão.

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| inquilino |Necessário |O inquilino do diretório que pretende pedir permissão do. Isso pode ser no formato de nome amigável ou de GUID.  |
| client_id |Necessário |ID de aplicação que o [Portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) atribuído à sua aplicação. |
| scope | Recomendado | Uma lista separada por espaço de [âmbitos](v2-permissions-and-consent.md) que pretende que o utilizador para autorizar.  |

### <a name="device-authorization-response"></a>Resposta de autorização de dispositivo

Uma resposta com êxito será um objeto JSON que contém as informações necessárias para permitir ao utilizador iniciar sessão.  

| Parâmetro | Formato | Descrição |
| ---              | --- | --- |
|`device_code`     |String| Uma cadeia longa utilizada para verificar a sessão entre o cliente e o servidor de autorização.  Isso é utilizado pelo cliente para pedir o token de acesso do servidor de autorização. |
|`user_code`       |String| Uma cadeia de caracteres curta mostrada ao usuário, utilizado para identificar a sessão num dispositivo secundário.|
|`verification_uri`|URI| O URI que o utilizador deve ir para com o `user_code` para iniciar sessão. |
|`verification_uri_complete`|URI| Combinar um URI de `user_code` e o `verification_uri`, utilizado para transmissão não textual ao usuário (por exemplo, via Bluetooth para um dispositivo ou por meio de um código QR).  |
|`expires_in`      |int| O número de segundos antes do `device_code` e `user_code` expirar. |
|`interval`        |int| O número de segundos que o cliente deve aguardar entre as solicitações de sondagem. |
| `message`        |String| Uma cadeia de caracteres legível por humanos com instruções para o utilizador.  Este texto pode ser localizado, incluindo uma **parâmetro de consulta** no pedido do formulário `?mkt=xx-XX`, preenchendo o código de cultura do idioma apropriado. |

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

|Parâmetro | Necessário | Descrição|
| -------- | -------- | ---------- |
|`grant_type` | Necessário| Tem de ser `urn:ietf:params:oauth:grant-type:device_code`|
|`client_id`  | Necessário| Tem de corresponder a `client_id` utilizado no pedido inicial. |
|`device_code`| Necessário| O `device_code` devolvido no pedido de autorização de dispositivo.  |

### <a name="expected-errors"></a>Erros esperados

Como o fluxo de código de dispositivo é um protocolo de consulta, o cliente deve esperar que vai receber erros antes do utilizador concluiu a autenticar.  

| Erro | Descrição | Ação de cliente |
|------ | ----------- | -------------|
| `authorization_pending` |  O utilizador não ainda acabou autenticar, mas não cancelou o fluxo. | Repita o pedido depois de, pelo menos, `interval` segundos. |
| `authorization_declined`|  O utilizador final negou o pedido de autorização.| Parar a consulta e reverter para um Estado de funcionamento não autenticado.  |
| `bad_verification_code`|O `device_code` enviada para o `/token` ponto final não foi reconhecido. | Certifique-se de que o cliente está a enviar o correto `device_code` no pedido. |
| `expired_token`|  Pelo menos `expires_in` passaram segundos e autenticação já não é possível com esta `device_code`. | Parar a consulta e reverter para um Estado de funcionamento não autenticado. |


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
|`token_type` | String| Sempre "Bearer. |
|`scope` | Cadeias de caracteres de espaço separado | Se foi devolvido um token de acesso, lista os âmbitos que o token de acesso é válido para. |
|`expires_in`| int | É válido para o número de segundos antes do token de acesso incluído. |
|`access_token`| Cadeia opaca | Emitido para o [âmbitos](v2-permissions-and-consent.md) que foram solicitados.  |
|`id_token`   | JWT | If emitido original `scope` parâmetro incluído o `openid` âmbito.  |
|`refresh_token` | Cadeia opaca | If emitido original `scope` parâmetro incluído `offline_access`.  |

O token de atualização pode ser utilizado para adquirir novos tokens de acesso e tokens utilizando o mesmo fluxo detalhado de atualização do [documentação de fluxo de código de OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
