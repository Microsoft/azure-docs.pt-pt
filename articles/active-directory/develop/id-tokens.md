---
title: Fichas de ID da plataforma de identidade da Microsoft Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como utilizar id_tokens emitidos pelos pontos finais Azure AD v1.0 e Microsoft (v2.0).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: e0e327d169c246d023be1aca27d6844b9b92f03e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82926719"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Fichas de ID da plataforma de identidade da Microsoft

`id_tokens`são enviados para a aplicação do cliente como parte de um fluxo [OpenID Connect](v2-protocols-oidc.md) (OIDC). Podem ser enviados juntamente ou em vez de um token de acesso, e são usados pelo cliente para autenticar o utilizador.

## <a name="using-the-id_token"></a>Usando o id_token

Os Tokens ID devem ser usados para validar que um utilizador é quem eles afirmam ser e obter informações úteis adicionais sobre eles - não deve ser usado para autorização em vez de um [token](access-tokens.md)de acesso . As alegações que fornece podem ser usadas para UX dentro da sua aplicação, como chaves numa base de dados, e fornecer acesso à aplicação do cliente.  Ao criar chaves para uma base de `idp` dados, não deve ser usado porque estraga cenários de hóspedes.  A chavegem deve ser feita `sub` apenas (o que é sempre único), com `tid` a utilização para o encaminhamento, se necessário.  Se precisar de partilhar dados através de serviços, `oid` + `sub` + `tid` funcionará, uma vez que vários serviços recebem todos os `oid` mesmos.

## <a name="claims-in-an-id_token"></a>Reclamações num id_token

`id_tokens`para uma identidade da Microsoft são [JWTs](https://tools.ietf.org/html/rfc7519) (JSON Web Tokens), o que significa que consistem num cabeçalho, carga útil e porção de assinatura. Pode utilizar o cabeçalho e a assinatura para verificar a autenticidade do token, enquanto a carga útil contém as informações sobre o utilizador solicitada pelo seu cliente. Exceto quando notados, todas as reclamações JWT listadas aqui aparecem em fichas v1.0 e v2.0.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Veja esta amostra v1.0 na [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Veja esta amostra v2.0 na [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Reclamações de cabeçalho

|Afirmação | Formatar | Descrição |
|-----|--------|-------------|
|`typ` | String - sempre "JWT" | Indica que o símbolo é um símbolo JWT.|
|`alg` | String | Indica o algoritmo que foi usado para assinar o símbolo. Exemplo: "RS256" |
|`kid` | String | Impressão digital para a chave pública usada para assinar este símbolo. Emitidos em v1.0 e v2.0 `id_tokens` . |
|`x5t` | String | O mesmo (em uso e valor) como `kid` . No entanto, trata-se de uma alegação de legado emitida apenas em v1.0 `id_tokens` para efeitos de compatibilidade. |

### <a name="payload-claims"></a>Reclamações de carga útil

Esta lista mostra as alegações do JWT que estão na maioria id_tokens por padrão (exceto quando anotado).  No entanto, a sua aplicação pode usar [reclamações opcionais](active-directory-optional-claims.md) para solicitar reclamações adicionais do JWT no id_token.  Estes podem ir desde a `groups` reclamação até informações sobre o nome do utilizador.

|Afirmação | Formatar | Descrição |
|-----|--------|-------------|
|`aud` |  String, um ID URI de aplicativo | Identifica o destinatário pretendido do token. Em `id_tokens` , o público é o ID de aplicação da sua aplicação, atribuído à sua aplicação no portal Azure. A sua aplicação deve validar este valor e rejeitar o token se o valor não corresponder. |
|`iss` |  String, um STS URI | Identifica o serviço de fichas de segurança (STS) que constrói e devolve o token, e o inquilino AD AZure em que o utilizador foi autenticado. Se o token foi emitido pelo ponto final v2.0, o URI terminará em `/v2.0` .  O GUID que indica que o utilizador é um utilizador consumidor de uma conta microsoft é `9188040d-6c67-4c5b-b112-36a304b66dad` . A sua aplicação deve utilizar a parte GUID da reivindicação para restringir o conjunto de inquilinos que podem iniciar sôm na app, se aplicável. |
|`iat` |  int, um timetamp UNIX | "Emitida At" indica quando ocorreu a autenticação para este token.  |
|`idp`|String, geralmente um STS URI | Regista o fornecedor de identidade que autenticou o requerente do token. Este valor é idêntico ao valor da reclamação do Emitente a menos que a conta de utilizador não esteja no mesmo inquilino que o emitente - os hóspedes, por exemplo. Se a reclamação não estiver presente, significa que o valor `iss` pode ser usado em vez disso.  Para contas pessoais utilizadas num contexto organizacional (por exemplo, uma conta pessoal convidada a um inquilino da AD Azure), a `idp` reclamação pode ser "live.com" ou uma STS URI contendo o inquilino da conta `9188040d-6c67-4c5b-b112-36a304b66dad` Microsoft. |
|`nbf` |  int, um timetamp UNIX | A alegação "nbf" (não antes) identifica o tempo anterior ao qual o JWT NÃO DEVE ser aceite para processamento.|
|`exp` |  int, um timetamp UNIX | A alegação "exp" (tempo de validade) identifica o tempo de validade no ou após o qual o JWT NÃO DEVE ser aceite para processamento.  É importante notar que um recurso pode rejeitar o token antes deste tempo também - se, por exemplo, for necessária uma alteração na autenticação ou se tiver sido detetada uma revogação simbólica. |
| `c_hash`| String |O haxixe de código só está incluído em fichas de identificação quando o token de ID é emitido com um código de autorização OAuth 2.0. Pode ser usado para validar a autenticidade de um código de autorização. Para obter mais informações sobre a realização desta validação, consulte a [especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| String |O hash de acesso só está incluído em fichas de identificação quando o token de ID é emitido com um token de acesso OAuth 2.0. Pode ser usado para validar a autenticidade de um token de acesso. Para obter mais informações sobre a realização desta validação, consulte a [especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Corda Opaca | Uma alegação interna usada pela Azure AD para registar dados para reutilização de fichas. Deve ser ignorado.|
|`preferred_username` | String | O nome de utilizador primário que representa o utilizador. Pode ser um endereço de e-mail, número de telefone ou um nome de utilizador genérico sem um formato especificado. O seu valor é mutável e pode mudar com o tempo. Uma vez que é mutável, este valor não deve ser utilizado para tomar decisões de autorização. O `profile` âmbito de aplicação é necessário para receber esta reclamação.|
|`email` | String | A `email` reclamação está presente por padrão para contas de hóspedes que tenham um endereço de e-mail.  A sua aplicação pode solicitar a reclamação de e-mail para utilizadores geridos (os do mesmo inquilino que o recurso) utilizando a `email` [reclamação opcional.](active-directory-optional-claims.md)  No ponto final v2.0, a sua aplicação também pode solicitar o `email` âmbito OpenID Connect - não precisa de solicitar tanto a reclamação opcional como o âmbito para obter a reclamação.  A alegação de e-mail apenas suporta correio endereçada a partir de informações de perfil do utilizador. |
|`name` | String | A `name` alegação fornece um valor legível pelo homem que identifica o sujeito do símbolo. O valor não é garantido ser único, é mutável, e é projetado para ser usado apenas para fins de exibição. O `profile` âmbito de aplicação é necessário para receber esta reclamação. |
|`nonce`| String | O nó corresponde ao parâmetro incluído no pedido original /autorizado ao IDP. Se não corresponder, o seu pedido deve rejeitar o token. |
|`oid` | String, um GUID | O identificador imutável de um objeto no sistema de identidade da Microsoft, neste caso, uma conta de utilizador. Este ID identifica exclusivamente o utilizador através de aplicações - duas aplicações diferentes que assinam no mesmo utilizador receberão o mesmo valor na `oid` reclamação. O Microsoft Graph devolverá este ID como `id` propriedade para uma determinada conta de utilizador. Uma vez `oid` que permite que várias aplicações correlacionem os utilizadores, o `profile` âmbito é necessário para receber esta reclamação. Note que se um único utilizador existir em vários inquilinos, o utilizador conterá um ID de objeto diferente em cada inquilino - são considerados contas diferentes, mesmo que o utilizador faça logins em cada conta com as mesmas credenciais. A `oid` alegação é um GUID e não pode ser reutilizada. |
|`roles`| Matriz de cordas | O conjunto de funções que foram atribuídas ao utilizador que está a iniciar sessão. |
|`rh` | Corda Opaca |Uma alegação interna usada por Azure para revalidar fichas. Deve ser ignorado. |
|`sub` | String, um GUID | O principal sobre o qual o token afirma informações, como o utilizador de uma aplicação. Este valor é imutável e não pode ser reatribuído ou reutilizado. O sujeito é um identificador de pares - é exclusivo de um determinado ID de aplicação. Se um único utilizador assinar duas aplicações diferentes usando dois IDs de clientes diferentes, essas aplicações receberão dois valores diferentes para a reclamação do assunto. Isto pode ou não ser desejado dependendo da sua arquitetura e requisitos de privacidade. |
|`tid` | String, um GUID | Um GUID que representa o inquilino Azure AD de que o utilizador é. Para contas de trabalho e escola, o GUID é o imutável ID de inquilino da organização a que o utilizador pertence. Para contas pessoais, o valor `9188040d-6c67-4c5b-b112-36a304b66dad` é. O `profile` âmbito de aplicação é necessário para receber esta reclamação. |
|`unique_name` | String | Fornece um valor legível por humanos que identifica o requerente do token. Este valor é único em qualquer momento, mas como os e-mails e outros identificadores podem ser reutilizados, este valor pode reaparecer noutras contas, devendo, portanto, ser utilizado apenas para fins de exibição. Apenas emitida em v1.0 `id_tokens` . |
|`uti` | Corda Opaca | Uma alegação interna usada por Azure para revalidar fichas. Deve ser ignorado. |
|`ver` | Corda, 1.0 ou 2.0 | Indica a versão do id_token. |

> [!NOTE]
> Os id_token v1 e v2 têm diferenças na quantidade de informação que transportarão, como se vê nos exemplos acima referidos. A versão especifica essencialmente o ponto final da plataforma Azure AD a partir do local onde foi emitida. [A implementação do Azure AD Oauth](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) evoluiu ao longo dos anos. Atualmente temos dois pontos finais diferentes para aplicações AzureAD. Você pode usar qualquer um dos novos pontos finais que são categorizados como V2 ou o antigo que se diz ser v1. Os pontos finais do Oauth para ambos são diferentes. O ponto final V2 é o mais recente onde estamos a tentar migrar todas as características do ponto final v1 e recomendamos novos desenvolvedores para usar o ponto final v2.
>
> - V1: Azure Ative Directory Endpoints:`https://login.microsoftonline.com/common/oauth2/authorize`
> - V2: Pontos finais da Plataforma de Identidade da Microsoft:`https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>Validação de um id_token

Validar um `id_token` é semelhante ao primeiro passo para [validar um token](access-tokens.md#validating-tokens) de acesso - o seu cliente deve validar que o emitente correto enviou de volta o token e que não foi adulterado. Porque `id_tokens` são sempre um símbolo JWT, muitas bibliotecas existem para validar estes tokens - recomendamos que use uma destas em vez de fazê-lo por si mesmo.

Para validar manualmente o token, consulte os detalhes dos passos na [validação de um token de acesso.](access-tokens.md#validating-tokens) Após validação da assinatura no token, as seguintes reclamações do JWT devem ser validadas no id_token (estas também podem ser feitas pela sua biblioteca de validação simbólica):

* Os intervalos de tempo: os `iat` `nbf` , e os `exp` tempos devem cair todos antes ou depois do tempo atual, conforme apropriado.
* Público: a `aud` reclamação deve coincidir com o ID da aplicação para a sua aplicação.
* Nonce: a `nonce` reclamação na carga útil deve corresponder ao parâmetro nonce passado para o ponto final /autorizado durante o pedido inicial.

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [fichas de acesso](access-tokens.md)
* Personalize as reclamações do JWT na sua id_token utilizando [reclamações opcionais.](active-directory-optional-claims.md)
