---
title: OAuth 2.0 device code flow | Rio Azure
titleSuffix: Microsoft identity platform
description: Inscreva-se nos utilizadores sem navegador. Construa fluxos de autenticação incorporados e sem navegador utilizando o subsídio de autorização do dispositivo.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 8c757f3e067aeac5d8145ca47b2eac145daba574
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88272455"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Plataforma de identidade da Microsoft e o fluxo de concessão de autorização de dispositivo OAuth 2.0

A plataforma de identidade da Microsoft suporta a [concessão de autorização](https://tools.ietf.org/html/rfc8628)do dispositivo , que permite aos utilizadores iniciar sedutor em dispositivos com restrições de entrada, como uma smart TV, dispositivo IoT ou impressora.  Para ativar este fluxo, o dispositivo tem o utilizador a visitar uma página web no seu navegador noutro dispositivo para iniciar sôm.  Uma vez que o utilizador entra, o dispositivo é capaz de obter tokens de acesso e atualizar fichas conforme necessário.

Este artigo descreve como programar diretamente contra o protocolo na sua aplicação.  Quando possível, recomendamos que utilize as Bibliotecas de Autenticação da Microsoft (MSAL) suportadas para [adquirir fichas e chamar APIs web seguras](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Dê também uma olhada nas aplicações de [amostra que utilizam o MSAL.](sample-v2-code.md)

## <a name="protocol-diagram"></a>Diagrama de protocolo

Todo o fluxo de código do dispositivo é semelhante ao diagrama seguinte. Descrevemos cada um dos passos mais tarde neste artigo.

![Fluxo de código do dispositivo](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Pedido de autorização do dispositivo

O cliente deve primeiro verificar com o servidor de autenticação um dispositivo e código de utilizador que seja utilizado para iniciar a autenticação. O cliente recolhe este pedido do `/devicecode` ponto final. Neste pedido, o cliente também deve incluir as permissões que necessita para adquirir ao utilizador. A partir do momento em que este pedido é enviado, o utilizador tem apenas 15 minutos para iniciar singing (o valor habitual `expires_in` para), por isso só faça este pedido quando o utilizador tiver indicado que está pronto para iniciar sing.

> [!TIP]
> Tente executar este pedido no Carteiro!
> [![Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | Pode ser /comum, /consumidores, ou /organizações.  Também pode ser o inquilino do diretório que você deseja solicitar permissão em formato GUID ou nome amigável.  |
| `client_id` | Necessário | O **ID da Aplicação (cliente)** que o [portal Azure – Experiência de registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) atribuído à sua app. |
| `scope` | Necessário | Uma lista de [âmbitos separados](v2-permissions-and-consent.md) pelo espaço a que o utilizador consinta.  |

### <a name="device-authorization-response"></a>Resposta à autorização do dispositivo

Uma resposta bem sucedida será um objeto JSON contendo as informações necessárias para permitir que o utilizador faça o seu sposição.

| Parâmetro | Formato | Descrição |
| ---              | --- | --- |
|`device_code`     | String | Uma longa corda usada para verificar a sessão entre o cliente e o servidor de autorização. O cliente utiliza este parâmetro para solicitar o sinal de acesso do servidor de autorização. |
|`user_code`       | String | Uma corda curta mostrada ao utilizador que é usada para identificar a sessão num dispositivo secundário.|
|`verification_uri`| URI | O URI a que o utilizador deve ir com o `user_code` para iniciar sinsus. |
|`expires_in`      | int | O número de segundos antes `device_code` do e `user_code` expirar. |
|`interval`        | int | O número de segundos que o cliente deve esperar entre os pedidos de sondagens. |
| `message`        | String | Uma cadeia legível pelo homem com instruções para o utilizador. Isto pode ser localizado através da inclusão de um **parâmetro de consulta** no pedido do `?mkt=xx-XX` formulário, preenchendo o código de cultura linguística apropriado. |

> [!NOTE]
> O `verification_uri_complete` campo de resposta não está incluído ou suportado neste momento.  Mencionamos isto porque se ler o [padrão](https://tools.ietf.org/html/rfc8628) vê que `verification_uri_complete` está listado como uma parte opcional do padrão de fluxo de código do dispositivo.

## <a name="authenticating-the-user"></a>Autenticação do utilizador

Depois de receber o `user_code` `verification_uri` e, o cliente exibe-os ao utilizador, instruindo-os a iniciar singing usando o seu telemóvel ou browser pc.

Se o utilizador autenticar com uma conta pessoal (em /comum ou /consumidores), será solicitado que volte a inscrever-se para transferir o estado de autenticação para o dispositivo.  Será também solicitado que dêem o seu consentimento, para garantir que estão cientes das permissões que estão a ser concedidas.  Isto não se aplica às contas de trabalho ou escolas utilizadas para autenticação.

Enquanto o utilizador está a autenticar no `verification_uri` , o cliente deve estar a sondar o ponto final para o `/token` token solicitado utilizando o `device_code` .

```HTTP
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parâmetro | Obrigatório | Descrição|
| -------- | -------- | ---------- |
| `tenant`  | Obrigatório | O mesmo pseudónimo de inquilino ou inquilino usado no pedido inicial. |
| `grant_type` | Necessário | Deve ser `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Necessário | Deve corresponder ao `client_id` usado no pedido inicial. |
| `device_code`| Necessário | O `device_code` devolvido no pedido de autorização do dispositivo.  |

### <a name="expected-errors"></a>Erros esperados

O fluxo de código do dispositivo é um protocolo de votação, pelo que o seu cliente deve esperar receber erros antes de o utilizador terminar a autenticação.

| Erro | Descrição | Ação do Cliente |
| ------ | ----------- | -------------|
| `authorization_pending` | O utilizador ainda não terminou a autenticação, mas não cancelou o fluxo. | Repita o pedido após pelo menos `interval` segundos. |
| `authorization_declined` | O utilizador final negou o pedido de autorização.| Pare de votar, e volte a um estado não autenticado.  |
| `bad_verification_code`| O `device_code` enviado para o ponto final não foi `/token` reconhecido. | Verifique se o cliente está a enviar o correto `device_code` no pedido. |
| `expired_token` | Pelo menos `expires_in` passaram-se segundos, e a autenticação já não é possível com isto `device_code` . | Pare de votar e volte a um estado não autenticado. |

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
| `token_type` | String| Sempre "Portador. |
| `scope` | Cadeias separadas do espaço | Se um token de acesso foi devolvido, este lista os âmbitos para os para oses para os qual o token de acesso é válido. |
| `expires_in`| int | O número de segundos antes do token de acesso incluído é válido para. |
| `access_token`| Corda opaca | Emitido para os [âmbitos](v2-permissions-and-consent.md) que foram solicitados.  |
| `id_token`   | JWT | Emitido se o parâmetro original `scope` incluísse o `openid` âmbito.  |
| `refresh_token` | Corda opaca | Emitido se o parâmetro original `scope` `offline_access` incluído.  |

Pode utilizar o token refresh para adquirir novos tokens de acesso e fichas de atualização utilizando o mesmo fluxo documentado na documentação de fluxo do [Código OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).
