---
title: Referência de token da ID da plataforma de identidade da Microsoft | Microsoft Docs
description: Saiba como utilizar id_tokens emitidos pelos pontos finais da Azure AD V1.0 e microsoft (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 912287200097906af7a8a9d6d12eb1421f3edadc
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696763"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Tokens de ID da plataforma Microsoft Identity

`id_tokens` são enviados para a aplicação do cliente como parte de um fluxo [OpenID Connect.](v1-protocols-openid-connect-code.md) Eles podem ser enviados ao lado ou em vez de um token de acesso e são usados pelo cliente para autenticar o usuário.

## <a name="using-the-id_token"></a>Usando o id_token

Os tokens de ID devem ser usados para validar que um usuário é quem alega ser e obter informações úteis adicionais sobre eles – ele não deve ser usado para autorização no lugar de um [token de acesso](access-tokens.md). As declarações fornecidas por ele podem ser usadas para UX dentro de seu aplicativo, como chaves em um banco de dados e para fornecer acesso ao aplicativo cliente.  Ao criar chaves para um banco de dados, `idp` não deve ser usado porque ele se bagunça em cenários de convidado.  O chaveamento deve ser feito em `sub` sozinho (que é sempre exclusivo), com `tid` usado para roteamento, se necessário.  Se você precisar compartilhar dados entre serviços, `oid`+`sub`+`tid` funcionará, já que vários serviços obtêm o mesmo `oid`.

## <a name="claims-in-an-id_token"></a>Reclamações num id_token

`id_tokens` para uma identidade da Microsoft são [JWTs](https://tools.ietf.org/html/rfc7519), o que significa que consistem numa parte de cabeçalho, carga útil e assinatura. Você pode usar o cabeçalho e a assinatura para verificar a autenticidade do token, enquanto a carga contém as informações sobre o usuário solicitado pelo cliente. Exceto quando indicado, todas as declarações listadas aqui aparecem nos tokens v 1.0 e v 2.0.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Exiba este token de exemplo v 1.0 em [JWT.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Exiba este token de exemplo v 2.0 em [JWT.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Declarações de cabeçalho

|Afirmação | Formato | Descrição |
|-----|--------|-------------|
|`typ` | Cadeia de caracteres – sempre "JWT" | Indica que o token é um JWT.|
|`alg` | String | Indica o algoritmo que foi usado para assinar o token. Exemplo: "RS256" |
|`kid` | String | Impressão digital para a chave pública usada para assinar este token. Emitido tanto em v1.0 como v2.0 `id_tokens`. |
|`x5t` | String | O mesmo (em uso e valor) que `kid`. No entanto, trata-se de uma reivindicação de legado emitida apenas em v1.0 `id_tokens` para fins de compatibilidade. |

### <a name="payload-claims"></a>Declarações de carga

Esta lista mostra as alegações que estão na maioria das id_tokens por defeito (exceto quando anotada).  No entanto, a sua aplicação pode utilizar [reclamações opcionais](active-directory-optional-claims.md) para solicitar reclamações adicionais no id_token.  Elas podem variar da declaração de `groups` para obter informações sobre o nome do usuário.

|Afirmação | Formato | Descrição |
|-----|--------|-------------|
|`aud` |  Cadeia de caracteres, um URI de ID do aplicativo | Identifica o destinatário pretendido do token. Em `id_tokens`, o público é o ID de aplicação da sua aplicação, atribuído à sua app no portal Azure. Seu aplicativo deve validar esse valor e rejeitar o token se o valor não corresponder. |
|`iss` |  Cadeia de caracteres, um URI do STS | Identifica o serviço de token de segurança (STS) que constrói e retorna o token e o locatário do Azure AD no qual o usuário foi autenticado. Se o token foi emitido pelo ponto de extremidade v 2.0, o URI terminará em `/v2.0`.  O GUID que indica que o usuário é um usuário consumidor de um conta Microsoft é `9188040d-6c67-4c5b-b112-36a304b66dad`. Seu aplicativo deve usar a parte GUID da declaração para restringir o conjunto de locatários que podem entrar no aplicativo, se aplicável. |
|`iat` |  int, um carimbo de data/hora do UNIX | "Emitido em" indica quando ocorreu a autenticação para esse token.  |
|`idp`|Cadeia de caracteres, geralmente um URI do STS | Regista o fornecedor de identidade que autenticou o requerente do token. Esse valor é idêntico ao valor da declaração do emissor, a menos que a conta de usuário não esteja no mesmo locatário que o emissor-Guests, por exemplo. Se a declaração não estiver presente, significa que o valor de `iss` pode ser usado em vez disso.  Para contas pessoais sendo usadas em um contexto organizacional (por exemplo, uma conta pessoal convidada para um locatário do Azure AD), a declaração de `idp` pode ser ' live.com ' ou um URI STS que contenha o `9188040d-6c67-4c5b-b112-36a304b66dad`de locatário conta Microsoft. |
|`nbf` |  int, um carimbo de data/hora do UNIX | A declaração "NBF" (não antes) identifica o tempo antes do qual o JWT não deve ser aceito para processamento.|
|`exp` |  int, um carimbo de data/hora do UNIX | A declaração "exp" (tempo de expiração) identifica o tempo de expiração em ou após o qual o JWT não deve ser aceito para processamento.  É importante observar que um recurso pode rejeitar o token antes dessa hora também, se, por exemplo, uma alteração na autenticação for necessária ou se uma revogação de token tiver sido detectada. |
| `c_hash`| String |O hash de código é incluído em tokens de ID somente quando o token de ID é emitido com um código de autorização OAuth 2,0. Ele pode ser usado para validar a autenticidade de um código de autorização. Para obter detalhes sobre como executar essa validação, consulte a [especificação do OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| String |O hash de token de acesso é incluído em tokens de ID somente quando o token de ID é emitido com um token de acesso OAuth 2,0. Ele pode ser usado para validar a autenticidade de um token de acesso. Para obter detalhes sobre como executar essa validação, consulte a [especificação do OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure AD para registrar dados para reutilização de token. Deve ser ignorado.|
|`preferred_username` | String | O nome de usuário primário que representa o usuário. Pode ser um endereço de email, número de telefone ou um nome de usuário genérico sem um formato especificado. Seu valor é mutável e pode mudar ao longo do tempo. Como é mutável, esse valor não deve ser usado para tomar decisões de autorização. O escopo de `profile` é necessário para receber essa declaração.|
|`email` | String | A declaração de `email` está presente por padrão para contas de convidado que têm um endereço de email.  Seu aplicativo pode solicitar a declaração de email para usuários gerenciados (aqueles do mesmo locatário que o recurso) usando a [declaração opcional](active-directory-optional-claims.md)`email`.  No ponto de extremidade v 2.0, seu aplicativo também pode solicitar o `email` o escopo do OpenID Connect-você não precisa solicitar a declaração opcional e o escopo para obter a declaração.  A declaração de email dá suporte apenas a email endereçável das informações de perfil do usuário. |
|`name` | String | A declaração de `name` fornece um valor legível que identifica o assunto do token. O valor não é garantido como exclusivo, é mutável e foi projetado para ser usado somente para fins de exibição. O escopo de `profile` é necessário para receber essa declaração. |
|`nonce`| String | O nonce corresponde ao parâmetro incluído na solicitação original de/Authorize para o IDP. Se não corresponder, seu aplicativo deverá rejeitar o token. |
|`oid` | Cadeia de caracteres, um GUID | O identificador imutável de um objeto no sistema de identidade da Microsoft, nesse caso, uma conta de usuário. Essa ID identifica exclusivamente o usuário em aplicativos-dois aplicativos diferentes que se conectam ao mesmo usuário receberão o mesmo valor na declaração de `oid`. O Microsoft Graph retornará essa ID como a propriedade `id` para uma determinada conta de usuário. Como o `oid` permite que vários aplicativos correlacionem os usuários, o escopo de `profile` é necessário para receber essa declaração. Observe que, se um único usuário existir em vários locatários, o usuário conterá uma ID de objeto diferente em cada locatário. eles são considerados contas diferentes, mesmo que o usuário faça logon em cada conta com as mesmas credenciais. A declaração de `oid` é um GUID e não pode ser reutilizada. |
|`roles`| matriz de cadeias de caracteres | O conjunto de funções que foram atribuídas ao usuário que está fazendo logon. |
|`rh` | Cadeia de caracteres opaca |Uma declaração interna usada pelo Azure para revalidar tokens. Deve ser ignorado. |
|`sub` | Cadeia de caracteres, um GUID | A entidade de segurança sobre a qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído ou reutilizado. O assunto é um identificador emparelhado-ele é exclusivo para uma ID de aplicativo específica. Se um único usuário entrar em dois aplicativos diferentes usando duas IDs de cliente diferentes, esses aplicativos receberão dois valores diferentes para a declaração de assunto. Isso pode ou não ser desejado dependendo dos requisitos de arquitetura e privacidade. |
|`tid` | Cadeia de caracteres, um GUID | Um GUID que representa o locatário do Azure AD do qual o usuário é. Para contas corporativas e de estudante, o GUID é a ID de locatário imutável da organização à qual o usuário pertence. Para contas pessoais, o valor é `9188040d-6c67-4c5b-b112-36a304b66dad`. O escopo de `profile` é necessário para receber essa declaração. |
|`unique_name` | String | Fornece um valor legível por humanos que identifica o requerente do token. Esse valor é exclusivo em qualquer momento determinado, mas como emails e outros identificadores podem ser reutilizados, esse valor pode reaparecer em outras contas e, portanto, deve ser usado somente para fins de exibição. Só emitida em v1.0 `id_tokens`. |
|`uti` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure para revalidar tokens. Deve ser ignorado. |
|`ver` | Cadeia de caracteres, 1,0 ou 2,0 | Indica a versão do id_token. |


> [!NOTE]
> Os id_token v1 e v2 têm diferenças na quantidade de informação que irão transportar como visto dos exemplos acima referidos. A versão especifica basicamente o ponto de extremidade da plataforma do Azure AD de onde ele foi emitido. A [implementação do Azure ad OAuth](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) evoluiu durante os anos. Atualmente, temos dois pontos de extremidade oAuth diferentes para aplicativos AzureAD. Você pode usar qualquer um dos novos pontos de extremidade que são categorizados como v2 ou o antigo que é considerado v1. Os pontos de extremidade OAuth para ambos são diferentes. O ponto de extremidade V2 é o mais recente em que estamos tentando migrar todos os recursos do ponto de extremidade v1 e recomendamos que novos desenvolvedores usem o ponto de extremidade v2. 
> - V1: Azure Active Directory pontos de extremidade: `https://login.microsoftonline.com/common/oauth2/authorize`
> - V2: pontos de extremidade da plataforma Microsoft Identity: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>Validando um id_token

Validar um `id_token` é semelhante ao primeiro passo de [validação](access-tokens.md#validating-tokens) de um sinal de acesso - o seu cliente deve validar que o emitente correto enviou o símbolo de volta e que não foi adulterado. Como `id_tokens` são sempre um JWT, existem muitas bibliotecas para validar estes tokens - recomendamos que use um destes em vez de fazê-lo por si mesmo.

Para validar manualmente o token, consulte os detalhes das etapas em [Validando um token de acesso](access-tokens.md#validating-tokens). Após a validação da assinatura no símbolo, as seguintes reclamações devem ser validadas no id_token (estas podem também ser feitas pela sua biblioteca de validação simbólica):

* Carimbos de data/hora: os carimbos de data/hora `iat`, `nbf`e `exp` devem ficar antes ou depois do horário atual, conforme apropriado. 
* Audiência: a declaração de `aud` deve corresponder à ID do aplicativo para seu aplicativo.
* Nonce: a declaração de `nonce` na carga deve corresponder ao parâmetro nonce passado para o ponto de extremidade/Authorize durante a solicitação inicial.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [tokens de acesso](access-tokens.md)
* Personalize as reclamações no seu id_token utilizando [reclamações opcionais.](active-directory-optional-claims.md)
