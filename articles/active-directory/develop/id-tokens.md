---
title: Referência de token de ID do Active Directory do Azure | Documentos da Microsoft
description: Saiba como utilizar id_tokens emitida pelos pontos finais v1.0 e v2.0 do Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 687b25ea5d792edf2f582c9929a0ae5f0c2426db
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442078"
---
# <a name="id-tokens"></a>Tokens de ID

`id_tokens` são enviados para a aplicação de cliente como parte de um [OpenID Connect](v1-protocols-openid-connect-code.md) fluxo. Eles podem ser enviados no lado ou em vez de um token de acesso e são utilizados pelo cliente para autenticar o utilizador.

## <a name="using-the-idtoken"></a>Usando o id_token

Tokens de ID deve ser usado para validar que um usuário é quem eles alegam ser e obter informações adicionais úteis sobre os mesmos – não deve ser utilizado para autorização em vez de um [token de acesso](access-tokens.md). As declarações fornece podem ser utilizadas para a experiência do Usuário dentro do seu aplicativo, uma base de dados de keying e o fornecimento de acesso à aplicação cliente.

## <a name="claims-in-an-idtoken"></a>Afirmações num id_token

`id_tokens` para um Microsoft identidade são [JWTs](https://tools.ietf.org/html/rfc7519), que significa que eles consistem numa parte do cabeçalho de payload e assinatura. Pode utilizar o cabeçalho e a assinatura para verificar a autenticidade do token, enquanto o payload contém informações sobre o usuário solicitado pelo seu cliente. Exceto onde indicado, todas as afirmações aqui listadas são apresentados nos tokens v1.0 e v2.0.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Ver este token de exemplo v1.0 no [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0=.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Ver este token de v2.0 de exemplo na [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTE4ODA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjMzMzgwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0=.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Afirmações de cabeçalho

|Afirmação | Formato | Descrição |
|-----|--------|-------------|
|`typ` | Cadeia de caracteres - sempre "JWT" | Indica que o token é um JWT.|
|`alg` | String | Indica o algoritmo que foi utilizado para assinar o token. Exemplo: "RS256" |
|`kid` | String | Thumbprint para a chave pública utilizada para assinar este token. Emitida na v1.0 e v2.0 `id_tokens`. |
|`x5t` | String | O mesmo (em utilização e valor) que `kid`. No entanto, essa é uma afirmação de legado emitida apenas na versão 1.0 `id_tokens` para fins de compatibilidade. |

### <a name="payload-claims"></a>Afirmações de payload

|Afirmação | Formato | Descrição |
|-----|--------|-------------|
|`aud` |  Cadeia de caracteres, um URI de ID de aplicação | Identifica o destinatário do token. No `id_tokens`, o público-alvo é a ID da aplicação da sua aplicação, atribuído à sua aplicação no portal do Azure. A aplicação deve validar este valor e rejeitar o token, se o valor não corresponde. |
|`iss` |  Cadeia de caracteres, um URI de STS | Identifica o serviço de token de segurança (STS) que constrói e devolve o token e o inquilino do Azure AD em que o utilizador foi autenticado. Se o token foi emitido pelo ponto final v2.0, o URI terminará no prazo `/v2.0`.  O GUID que indica que o utilizador é um utilizador de consumidor de uma conta Microsoft é `9188040d-6c67-4c5b-b112-36a304b66dad`. A aplicação deve utilizar a parte GUID de afirmação para restringir o conjunto de inquilinos que pode iniciar sessão na aplicação, se aplicável. |
|`iat` |  int, um carimbo de UNIX | "Emitidos em" indica quando ocorreu a autenticação para este token.  |
|`idp`|Cadeia de caracteres, normalmente, um URI de STS | Regista o fornecedor de identidade que autenticou o requerente do token. Este valor é idêntico ao valor de afirmação do emissor, a menos que a conta de utilizador não está no mesmo inquilino, como o emissor - os convidados, por exemplo. Se a declaração não estiver presente, significa que o valor de `iss` pode ser utilizado em vez disso.  Para contas pessoais que está a ser utilizadas num contexto organizacional (por exemplo, uma conta pessoal convidado para um inquilino do Azure AD), o `idp` afirmação pode ser 'live.com' ou um URI de STS, que contém o inquilino da conta Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  int, um carimbo de UNIX | A afirmação "nbf" (não antes) identifica o tempo antes do qual o JWT não deve ser aceite para processamento.|
|`exp` |  int, um carimbo de UNIX | A afirmação "exp" (hora de expiração) identifica a hora de expiração em ou depois que o JWT não deve ser aceite para processamento.  É importante observar que um recurso pode rejeitar o token antes desta data também - se por exemplo, é necessária uma alteração na autenticação ou foi detetado um token revogado. |
| `c_hash`| String |O hash de código está incluído nos tokens de ID apenas quando o token de ID é emitido com um código de autorização de OAuth 2.0. Ele pode ser usado para validar a autenticidade de um código de autorização. Para obter detalhes sobre como efetuar esta validação, consulte a [especificação do OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| String |O hash de token está incluído no ID de acesso tokens apenas quando o token de ID é emitido com um token de acesso de OAuth 2.0. Ele pode ser usado para validar a autenticidade de um token de acesso. Para obter detalhes sobre como efetuar esta validação, consulte a [especificação do OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Cadeia opaca | Uma afirmação interna utilizada pelo Azure AD para registar dados para reutilização de token. Devem ser ignorados.|
|`preferred_username` | String | O nome de utilizador principal que represente o usuário. Pode ser um endereço de e-mail, número de telefone ou um nome de utilizador genérica sem um formato especificado. Seu valor é mutável e podem ser alterados ao longo do tempo. Uma vez que é mutável, este valor não pode ser utilizado para tomar decisões de autorização. O `profile` âmbito é necessário para receber esta afirmação.|
|`email` | String | O `email` declaração estiver presente, por predefinição, para contas de convidado que têm um endereço de e-mail.  A aplicação pode pedir a afirmação de e-mail para utilizadores geridos (os partir do mesmo inquilino, como o recurso) utilizando o `email` [afirmação opcional](active-directory-optional-claims.md).  No ponto final v2.0, a sua aplicação também pode solicitar o `email` âmbito OpenID Connect - não precisa de pedir a afirmação opcional e o âmbito para obter a afirmação.  A afirmação de e-mail só suporta endereçável correio a partir das informações de perfil do usuário. |
|`name` | String | O `name` afirmação fornece um valor legível por humanos que identifica o assunto do token. O valor não é garantido para que seja exclusivo, é mutável e foi concebido para ser utilizado apenas para fins de exibição. O `profile` âmbito é necessário para receber esta afirmação. |
|`nonce`| String | O valor de uso único corresponde ao parâmetro incluído no original / autorizar a solicitação para o IDP. Se não houver, seu aplicativo deve rejeitar o token. |
|`oid` | Cadeia de caracteres, um GUID | O identificador imutável para um objeto no Microsoft identity system, neste caso, uma conta de utilizador. Este ID identifica exclusivamente o utilizador em todas as aplicações – dois aplicativos diferentes, iniciar sessão no mesmo utilizador irão receber o mesmo valor no `oid` de afirmação. O Microsoft Graph irá devolver este ID como o `id` propriedade para uma determinada conta de utilizador. Uma vez que o `oid` permite que várias aplicações correlacionar os utilizadores, o `profile` âmbito é necessário para receber esta afirmação. Tenha em atenção que, se existir um único utilizador em vários inquilinos, o utilizador irá conter um ID de objeto diferentes em cada inquilino - que são consideradas diferentes contas, mesmo que o usuário faz logon em cada conta com as mesmas credenciais. |
|`rh` | Cadeia opaca |Uma afirmação interna utilizada pelo Azure para revalide tokens. Devem ser ignorados. |
|`sub` | Cadeia de caracteres, um GUID | O principal sobre o qual o token declara informações, como o utilizador de uma aplicação. Este valor é imutável e não pode ser reatribuído ou reutilizado. O assunto é um identificador de pairwise - é exclusivo para um ID de aplicação em particular. Por conseguinte, se um único utilizador se inscreve para duas aplicações diferentes com dois IDs de cliente diferentes, essas aplicações irão receber dois valores diferentes para a afirmação do requerente. Isto pode ou não pode ser desejável dependendo dos requisitos de arquitetura e a privacidade. |
|`tid` | Cadeia de caracteres, um GUID | Um GUID que representa o que o utilizador é de inquilino do Azure AD. Para contas profissionais e escolares, o GUID é o ID de inquilino imutável da organização que o utilizador pertence. Contas pessoais, o valor é `9188040d-6c67-4c5b-b112-36a304b66dad`. O `profile` âmbito é necessário para receber esta afirmação. |
|`unique_name` | String | Fornece um valor legível por humanos que identifica o requerente do token. Este valor não é garantido de ser exclusivo dentro de um inquilino e deve ser utilizado apenas para fins de exibição. Apenas são emitidas em v1.0 `id_tokens`. |
|`uti` | Cadeia opaca | Uma afirmação interna utilizada pelo Azure para revalide tokens. Devem ser ignorados. |
|`ver` | Cadeia de caracteres, 1.0 ou 2.0 | Indica a versão do id_token. |

## <a name="validating-an-idtoken"></a>A validar um id_token

A validar um `id_token` é muito parecido com a primeira etapa da [validar um token de acesso](access-tokens.md#validating-tokens) - o cliente deve validar que o emissor correto foi enviada de volta o token e não foi adulterada. Uma vez que `id_tokens` são sempre um JWT, muitas bibliotecas existem para validar esses tokens –, recomendamos que utilize um destes em vez de fazê-lo por conta própria. 

Para validar manualmente o token, consulte os detalhes de passos no [validar um token de acesso](access-tokens.md#validating-tokens). Após validar a assinatura no token, as seguintes declarações devem ser validadas na id_token (eles podem também ser feitos por sua biblioteca de validação do token):

* Carimbos de data /: os `iat`, `nbf`, e `exp` carimbos de data / deve enquadram-se antes ou depois da hora atual, conforme apropriado. 
* Público-alvo: o `aud` afirmação deve corresponder ao ID da aplicação para a sua aplicação.
* Valor de uso único: o `nonce` afirmação no payload tem de coincidir com o parâmetro de valor de uso único passado para o /autorizar ponto final durante o pedido inicial.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [tokens de acesso do Azure AD](access-tokens.md)
