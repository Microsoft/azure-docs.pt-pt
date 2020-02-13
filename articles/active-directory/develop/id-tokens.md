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
ms.openlocfilehash: 1efd027edb85cabcfdc2a170771ef19182b5c9f8
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160955"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Tokens de ID da plataforma Microsoft Identity

`id_tokens` são enviados para a aplicação do cliente como parte de um fluxo [OpenID Connect.](v2-protocols-oidc.md) Eles podem ser enviados ao lado ou em vez de um token de acesso e são usados pelo cliente para autenticar o usuário.

## <a name="using-the-id_token"></a>Usando o id_token

Os tokens de id devem ser usados para validar que um utilizador é quem eles afirmam ser e obter informações úteis adicionais sobre eles - não deve ser usado para autorização em vez de um sinal de [acesso](access-tokens.md). As declarações fornecidas por ele podem ser usadas para UX dentro de seu aplicativo, como chaves em um banco de dados e para fornecer acesso ao aplicativo cliente.  Ao criar chaves para uma base de dados, `idp` não deve ser usado porque estraga os cenários dos hóspedes.  A chave deve ser feita apenas em `sub` (o que é sempre único), com `tid` usado seleções em encaminhamento, se necessário.  Se precisar de partilhar dados através de serviços, `oid`+ `sub`+`tid` funcionará, uma vez que vários serviços recebem todos os mesmos `oid`.

## <a name="claims-in-an-id_token"></a>Reclamações num id_token

`id_tokens` para uma identidade da Microsoft são [JWTs](https://tools.ietf.org/html/rfc7519), o que significa que consistem numa parte de cabeçalho, carga útil e assinatura. Você pode usar o cabeçalho e a assinatura para verificar a autenticidade do token, enquanto a carga contém as informações sobre o usuário solicitado pelo cliente. Exceto quando indicado, todas as declarações listadas aqui aparecem nos tokens v 1.0 e v 2.0.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Veja este símbolo de amostra v1.0 em [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Veja este símbolo de amostra v2.0 em [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Declarações de cabeçalho

|Afirmação | Formato | Descrição |
|-----|--------|-------------|
|`typ` | Cadeia de caracteres – sempre "JWT" | Indica que o token é um JWT.|
|`alg` | Cadeia | Indica o algoritmo que foi usado para assinar o token. Exemplo: "RS256" |
|`kid` | Cadeia | Impressão digital para a chave pública usada para assinar este token. Emitido tanto em v1.0 como v2.0 `id_tokens`. |
|`x5t` | Cadeia | O mesmo (em uso e valor) que `kid`. No entanto, trata-se de uma reivindicação de legado emitida apenas em v1.0 `id_tokens` para fins de compatibilidade. |

### <a name="payload-claims"></a>Declarações de carga

Esta lista mostra as alegações que estão na maioria das id_tokens por defeito (exceto quando anotada).  No entanto, a sua aplicação pode utilizar [reclamações opcionais](active-directory-optional-claims.md) para solicitar reclamações adicionais no id_token.  Estes podem ir desde a `groups` a informação sobre o nome do utilizador.

|Afirmação | Formato | Descrição |
|-----|--------|-------------|
|`aud` |  Cadeia de caracteres, um URI de ID do aplicativo | Identifica o destinatário pretendido do token. Em `id_tokens`, o público é o ID de aplicação da sua aplicação, atribuído à sua app no portal Azure. Seu aplicativo deve validar esse valor e rejeitar o token se o valor não corresponder. |
|`iss` |  Cadeia de caracteres, um URI do STS | Identifica o serviço de token de segurança (STS) que constrói e retorna o token e o locatário do Azure AD no qual o usuário foi autenticado. Se o símbolo tiver sido emitido pelo ponto final v2.0, o URI terminará em `/v2.0`.  O GUID que indica que o utilizador é um utilizador de uma conta Microsoft é `9188040d-6c67-4c5b-b112-36a304b66dad`. Seu aplicativo deve usar a parte GUID da declaração para restringir o conjunto de locatários que podem entrar no aplicativo, se aplicável. |
|`iat` |  int, um carimbo de data/hora do UNIX | "Emitido em" indica quando ocorreu a autenticação para esse token.  |
|`idp`|Cadeia de caracteres, geralmente um URI do STS | Regista o fornecedor de identidade que autenticou o requerente do token. Esse valor é idêntico ao valor da declaração do emissor, a menos que a conta de usuário não esteja no mesmo locatário que o emissor-Guests, por exemplo. Se a alegação não estiver presente, significa que o valor da `iss` pode ser usado.  Para contas pessoais que sejam utilizadas num contexto organizacional (por exemplo, uma conta pessoal convidada a um inquilino da AD Azure), a `idp` alegação pode ser "live.com" ou um STS URI contendo o inquilino da conta microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  int, um carimbo de data/hora do UNIX | A alegação "nbf" (não antes) identifica o tempo anterior ao qual o JWT NÃO deve ser aceite para processamento.|
|`exp` |  int, um carimbo de data/hora do UNIX | A alegação "exp" (tempo de validade) identifica o tempo de validade em ou após o qual o JWT NÃO deve ser aceite para processamento.  É importante observar que um recurso pode rejeitar o token antes dessa hora também, se, por exemplo, uma alteração na autenticação for necessária ou se uma revogação de token tiver sido detectada. |
| `c_hash`| Cadeia |O hash de código é incluído em tokens de ID somente quando o token de ID é emitido com um código de autorização OAuth 2,0. Ele pode ser usado para validar a autenticidade de um código de autorização. Para mais detalhes sobre a realização desta validação, consulte a [especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| Cadeia |O hash de token de acesso é incluído em tokens de ID somente quando o token de ID é emitido com um token de acesso OAuth 2,0. Ele pode ser usado para validar a autenticidade de um token de acesso. Para mais detalhes sobre a realização desta validação, consulte a [especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure AD para registrar dados para reutilização de token. Deve ser ignorado.|
|`preferred_username` | Cadeia | O nome de usuário primário que representa o usuário. Pode ser um endereço de email, número de telefone ou um nome de usuário genérico sem um formato especificado. Seu valor é mutável e pode mudar ao longo do tempo. Como é mutável, esse valor não deve ser usado para tomar decisões de autorização. O âmbito `profile` é necessário para receber esta reclamação.|
|`email` | Cadeia | A `email` reclamação está presente por defeito para contas de hóspedes que têm um endereço de e-mail.  A sua aplicação pode solicitar a reclamação de e-mail para utilizadores geridos (os do mesmo inquilino que o recurso) usando a `email` [reclamação opcional.](active-directory-optional-claims.md)  No ponto final da v2.0, a sua aplicação também pode solicitar o âmbito `email` OpenID Connect - não precisa de solicitar tanto a reclamação opcional como o âmbito para obter a reclamação.  A declaração de email dá suporte apenas a email endereçável das informações de perfil do usuário. |
|`name` | Cadeia | A alegação `name` fornece um valor legível pelo homem que identifica o objeto do símbolo. O valor não é garantido como exclusivo, é mutável e foi projetado para ser usado somente para fins de exibição. O âmbito `profile` é necessário para receber esta reclamação. |
|`nonce`| Cadeia | O nonce corresponde ao parâmetro incluído na solicitação original de/Authorize para o IDP. Se não corresponder, seu aplicativo deverá rejeitar o token. |
|`oid` | Cadeia de caracteres, um GUID | O identificador imutável de um objeto no sistema de identidade da Microsoft, nesse caso, uma conta de usuário. Este ID identifica exclusivamente o utilizador através de aplicações - duas aplicações diferentes que assinam no mesmo utilizador receberão o mesmo valor na `oid` reclamação. O Microsoft Graph devolverá este ID como a propriedade `id` para uma determinada conta de utilizador. Uma vez que o `oid` permite que várias aplicações se relacionem com os utilizadores, o âmbito `profile` é necessário para receber esta reclamação. Observe que, se um único usuário existir em vários locatários, o usuário conterá uma ID de objeto diferente em cada locatário. eles são considerados contas diferentes, mesmo que o usuário faça logon em cada conta com as mesmas credenciais. A alegação `oid` é um GUID e não pode ser reutilizada. |
|`roles`| matriz de cadeias de caracteres | O conjunto de funções que foram atribuídas ao usuário que está fazendo logon. |
|`rh` | Cadeia de caracteres opaca |Uma declaração interna usada pelo Azure para revalidar tokens. Deve ser ignorado. |
|`sub` | Cadeia de caracteres, um GUID | A entidade de segurança sobre a qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído ou reutilizado. O assunto é um identificador emparelhado-ele é exclusivo para uma ID de aplicativo específica. Se um único usuário entrar em dois aplicativos diferentes usando duas IDs de cliente diferentes, esses aplicativos receberão dois valores diferentes para a declaração de assunto. Isso pode ou não ser desejado dependendo dos requisitos de arquitetura e privacidade. |
|`tid` | Cadeia de caracteres, um GUID | Um GUID que representa o locatário do Azure AD do qual o usuário é. Para contas corporativas e de estudante, o GUID é a ID de locatário imutável da organização à qual o usuário pertence. Para contas pessoais, o valor é `9188040d-6c67-4c5b-b112-36a304b66dad`. O âmbito `profile` é necessário para receber esta reclamação. |
|`unique_name` | Cadeia | Fornece um valor legível por humanos que identifica o requerente do token. Esse valor é exclusivo em qualquer momento determinado, mas como emails e outros identificadores podem ser reutilizados, esse valor pode reaparecer em outras contas e, portanto, deve ser usado somente para fins de exibição. Só emitida em v1.0 `id_tokens`. |
|`uti` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure para revalidar tokens. Deve ser ignorado. |
|`ver` | Cadeia de caracteres, 1,0 ou 2,0 | Indica a versão do id_token. |


> [!NOTE]
> Os id_token v1 e v2 têm diferenças na quantidade de informação que irão transportar como visto dos exemplos acima referidos. A versão especifica basicamente o ponto de extremidade da plataforma do Azure AD de onde ele foi emitido. A implementação da [Azure AD Oauth](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) evoluiu ao longo dos anos. Atualmente, temos dois pontos de extremidade oAuth diferentes para aplicativos AzureAD. Você pode usar qualquer um dos novos pontos de extremidade que são categorizados como v2 ou o antigo que é considerado v1. Os pontos de extremidade OAuth para ambos são diferentes. O ponto de extremidade V2 é o mais recente em que estamos tentando migrar todos os recursos do ponto de extremidade v1 e recomendamos que novos desenvolvedores usem o ponto de extremidade v2. 
> - V1: Pontos finais do Diretório Ativo Azure: `https://login.microsoftonline.com/common/oauth2/authorize`
> - V2: Microsoft Identity Platform Endpoints: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>Validando um id_token

Validar um `id_token` é semelhante ao primeiro passo de [validação](access-tokens.md#validating-tokens) de um sinal de acesso - o seu cliente deve validar que o emitente correto enviou o símbolo de volta e que não foi adulterado. Como `id_tokens` são sempre um JWT, existem muitas bibliotecas para validar estes tokens - recomendamos que use um destes em vez de fazê-lo por si mesmo.

Para validar manualmente o símbolo, consulte os detalhes dos passos na [validação de um sinal](access-tokens.md#validating-tokens)de acesso . Após a validação da assinatura no símbolo, as seguintes reclamações devem ser validadas no id_token (estas podem também ser feitas pela sua biblioteca de validação simbólica):

* Carimbos temporais: os `iat`, `nbf`e selos temporais `exp` devem cair antes ou depois do tempo atual, conforme adequado. 
* Público: a reivindicação `aud` deve corresponder ao ID da aplicação para a sua aplicação.
* Nonce: a `nonce` reclamação na carga útil deve corresponder ao parâmetro nonce passado para o ponto final /autorizar durante o pedido inicial.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [fichas](access-tokens.md) de acesso
* Personalize as reclamações no seu id_token utilizando [reclamações opcionais.](active-directory-optional-claims.md)
