---
title: Fichas de ID da plataforma de identidade da Microsoft | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como utilizar id_tokens emitidos pelos pontos finais Azure AD v1.0 e Microsoft (v2.0).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 21e0b800e06b7a5ad0351ff53d26a5e2cd2ba71c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102175393"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Fichas de ID da plataforma de identidade da Microsoft

`id_tokens` são enviados para a aplicação do cliente como parte de um fluxo [OpenID Connect](v2-protocols-oidc.md) (OIDC). Podem ser enviados ao lado ou em vez de um token de acesso, e são usados pelo cliente para autenticar o utilizador.

## <a name="using-the-id_token"></a>Usando o id_token

Os Tokens ID devem ser usados para validar que um utilizador é quem eles afirmam ser e obter informações úteis adicionais sobre eles - não deve ser usado para autorização em vez de um [token](access-tokens.md)de acesso . As alegações que fornece podem ser utilizadas para UX dentro da sua aplicação, como [chaves numa base de dados,](#using-claims-to-reliably-identify-a-user-subject-and-object-id)e fornecer acesso à aplicação do cliente.  

## <a name="claims-in-an-id_token"></a>Reclamações num id_token

`id_tokens` são [JWTs](https://tools.ietf.org/html/rfc7519) (JSON Web Tokens), o que significa que consistem num cabeçalho, carga útil e porção de assinatura. Pode utilizar o cabeçalho e a assinatura para verificar a autenticidade do token, enquanto a carga útil contém as informações sobre o utilizador solicitada pelo seu cliente. Exceto quando notados, todas as reclamações JWT listadas aqui aparecem em fichas v1.0 e v2.0.

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

|Afirmação | Formato | Descrição |
|-----|--------|-------------|
|`typ` | String - sempre "JWT" | Indica que o símbolo é um símbolo JWT.|
|`alg` | String | Indica o algoritmo que foi usado para assinar o símbolo. Exemplo: "RS256" |
|`kid` | String | Impressão digital para a chave pública usada para verificar este símbolo. Emitidos em v1.0 e v2.0 `id_tokens` . |
|`x5t` | String | O mesmo (em uso e valor) como `kid` . No entanto, trata-se de uma alegação de legado emitida apenas em v1.0 `id_tokens` para efeitos de compatibilidade. |

### <a name="payload-claims"></a>Reclamações de carga útil

Esta lista mostra as alegações do JWT que estão na maioria id_tokens por padrão (exceto quando anotado).  No entanto, a sua aplicação pode usar [reclamações opcionais](active-directory-optional-claims.md) para solicitar reclamações adicionais do JWT no id_token.  Estes podem ir desde a `groups` reclamação até informações sobre o nome do utilizador.

|Afirmação | Formato | Descrição |
|-----|--------|-------------|
|`aud` |  String, um ID URI de aplicativo | Identifica o destinatário pretendido do token. Em `id_tokens` , o público é o ID de aplicação da sua aplicação, atribuído à sua aplicação no portal Azure. A sua aplicação deve validar este valor e rejeitar o token se o valor não corresponder. |
|`iss` |  String, um STS URI | Identifica o serviço de fichas de segurança (STS) que constrói e devolve o token, e o inquilino AD AZure em que o utilizador foi autenticado. Se o token foi emitido pelo ponto final v2.0, o URI terminará em `/v2.0` .  O GUID que indica que o utilizador é um utilizador consumidor de uma conta microsoft é `9188040d-6c67-4c5b-b112-36a304b66dad` . A sua aplicação deve utilizar a parte GUID da reivindicação para restringir o conjunto de inquilinos que podem iniciar sôm na app, se aplicável. |
|`iat` |  int, um timetamp UNIX | "Emitida At" indica quando ocorreu a autenticação para este token.  |
|`idp`|String, geralmente um STS URI | Regista o fornecedor de identidade que autenticou o requerente do token. Este valor é idêntico ao valor da reclamação do Emitente a menos que a conta de utilizador não esteja no mesmo inquilino que o emitente - os hóspedes, por exemplo. Se a reclamação não estiver presente, significa que o valor `iss` pode ser usado em vez disso.  Para contas pessoais utilizadas num contexto organizacional (por exemplo, uma conta pessoal convidada a um inquilino da AD Azure), a `idp` reclamação pode ser "live.com" ou uma STS URI contendo o inquilino da conta `9188040d-6c67-4c5b-b112-36a304b66dad` Microsoft. |
|`nbf` |  int, um timetamp UNIX | A alegação "nbf" (não antes) identifica o tempo anterior ao qual o JWT NÃO DEVE ser aceite para processamento.|
|`exp` |  int, um timetamp UNIX | A alegação "exp" (tempo de validade) identifica o tempo de validade no ou após o qual o JWT NÃO DEVE ser aceite para processamento.  É importante notar que um recurso pode rejeitar o token antes deste tempo também - se, por exemplo, for necessária uma alteração na autenticação ou se tiver sido detetada uma revogação simbólica. |
| `c_hash`| String |O haxixe de código só está incluído em fichas de identificação quando o token de ID é emitido com um código de autorização OAuth 2.0. Pode ser usado para validar a autenticidade de um código de autorização. Para obter mais informações sobre a realização desta validação, consulte a [especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| String |O hash de acesso só está incluído em fichas de identificação quando o token de ID é emitido a partir do ponto final com um token de `/authorize` acesso OAuth 2.0. Pode ser usado para validar a autenticidade de um token de acesso. Para obter mais informações sobre a realização desta validação, consulte a [especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken). Isto não é devolvido em fichas de identificação do `/token` ponto final. |
|`aio` | Corda Opaca | Uma alegação interna usada pela Azure AD para registar dados para reutilização de fichas. Deve ser ignorado.|
|`preferred_username` | String | O nome de utilizador primário que representa o utilizador. Pode ser um endereço de e-mail, número de telefone ou um nome de utilizador genérico sem um formato especificado. O seu valor é mutável e pode mudar com o tempo. Uma vez que é mutável, este valor não deve ser utilizado para tomar decisões de autorização. O `profile` âmbito de aplicação é necessário para receber esta reclamação.|
|`email` | String | A `email` reclamação está presente por padrão para contas de hóspedes que tenham um endereço de e-mail.  A sua aplicação pode solicitar a reclamação de e-mail para utilizadores geridos (os do mesmo inquilino que o recurso) utilizando a `email` [reclamação opcional.](active-directory-optional-claims.md)  No ponto final v2.0, a sua aplicação também pode solicitar o `email` âmbito OpenID Connect - não precisa de solicitar tanto a reclamação opcional como o âmbito para obter a reclamação.  A alegação de e-mail apenas suporta correio endereçada a partir de informações de perfil do utilizador. |
|`name` | String | A `name` alegação fornece um valor legível pelo homem que identifica o sujeito do símbolo. O valor não é garantido ser único, é mutável, e é projetado para ser usado apenas para fins de exibição. O `profile` âmbito de aplicação é necessário para receber esta reclamação. |
|`nonce`| String | O nó corresponde ao parâmetro incluído no pedido original /autorizado ao IDP. Se não corresponder, o seu pedido deve rejeitar o token. |
|`oid` | String, um GUID | O identificador imutável de um objeto no sistema de identidade da Microsoft, neste caso, uma conta de utilizador. Este ID identifica exclusivamente o utilizador através de aplicações - duas aplicações diferentes que assinam no mesmo utilizador receberão o mesmo valor na `oid` reclamação. O Microsoft Graph devolverá este ID como `id` propriedade para uma determinada conta de utilizador. Uma vez `oid` que permite que várias aplicações correlacionem os utilizadores, o `profile` âmbito é necessário para receber esta reclamação. Note que se um único utilizador existir em vários inquilinos, o utilizador conterá um ID de objeto diferente em cada inquilino - são considerados contas diferentes, mesmo que o utilizador faça logins em cada conta com as mesmas credenciais. A `oid` alegação é um GUID e não pode ser reutilizada. |
|`roles`| Matriz de cordas | O conjunto de funções que foram atribuídas ao utilizador que está a iniciar sessão. |
|`rh` | Corda Opaca |Uma alegação interna usada por Azure para revalidar fichas. Deve ser ignorado. |
|`sub` | String | O principal sobre o qual o token afirma informações, como o utilizador de uma aplicação. Este valor é imutável e não pode ser reatribuído ou reutilizado. O sujeito é um identificador de pares - é exclusivo de um determinado ID de aplicação. Se um único utilizador assinar duas aplicações diferentes usando dois IDs de clientes diferentes, essas aplicações receberão dois valores diferentes para a reclamação do assunto. Isto pode ou não ser desejado dependendo da sua arquitetura e requisitos de privacidade. |
|`tid` | String, um GUID | Um GUID que representa o inquilino Azure AD de que o utilizador é. Para contas de trabalho e escola, o GUID é o imutável ID de inquilino da organização a que o utilizador pertence. Para contas pessoais, o valor `9188040d-6c67-4c5b-b112-36a304b66dad` é. O `profile` âmbito de aplicação é necessário para receber esta reclamação. |
|`unique_name` | String | Fornece um valor legível por humanos que identifica o requerente do token. Este valor é único em qualquer momento, mas como os e-mails e outros identificadores podem ser reutilizados, este valor pode reaparecer noutras contas, devendo, portanto, ser utilizado apenas para fins de exibição. Apenas emitida em v1.0 `id_tokens` . |
|`uti` | Corda Opaca | Uma alegação interna usada por Azure para revalidar fichas. Deve ser ignorado. |
|`ver` | Corda, 1.0 ou 2.0 | Indica a versão do id_token. |
|`hasgroups`|Booleano|Se presente, sempre verdadeiro, denotar o utilizador está em pelo menos um grupo. Utilizados em vez dos grupos que reclamam os JWTs em fluxos de subvenções implícitos se os grupos completos alegarem que alargariam o fragmento URI para além dos limites de comprimento do URL (atualmente 6 ou mais grupos). Indica que o cliente deve utilizar a API do Microsoft Graph para determinar os grupos do utilizador `https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects` ().|
|`groups:src1`|Objeto JSON | Para pedidos simbólicos que não sejam limitados (ver `hasgroups` acima) mas ainda demasiado grandes para o token, será incluído um link para a lista completa de grupos para o utilizador. Para os JWTs como uma reclamação distribuída, para a SAML como uma nova reivindicação em vez da `groups` reclamação. <br><br>**Exemplo JWT Valor**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> Para obter mais informações, consulte [a reclamação de overage de Grupos.](#groups-overage-claim)|

> [!NOTE]
> Os id_token v1.0 e v2.0 têm diferenças na quantidade de informação que transportarão, como se pode ver nos exemplos acima referidos. A versão baseia-se no ponto final de onde foi solicitada. Embora as aplicações existentes utilizem provavelmente o ponto final Azure AD, as novas aplicações devem utilizar a "plataforma de identidade da Microsoft".
>
> - v1.0: Pontos finais Azure AD: `https://login.microsoftonline.com/common/oauth2/authorize`
> - v2.0: Pontos finais da Plataforma de Identidade da Microsoft: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>Utilização de alegações para identificar de forma fiável um utilizador (Assunto e ID de Objeto)

Ao identificar um utilizador (por exemplo, procurá-lo numa base de dados ou decidir que permissões têm), é fundamental utilizar informações que permanecerão constantes e únicas ao longo do tempo. As aplicações antigas às vezes usam campos como o endereço de e-mail, um número de telefone ou a UPN.  Tudo isto pode mudar ao longo do tempo, e também pode ser reutilizado ao longo do tempo - quando um empregado muda de nome, ou um empregado recebe um endereço de e-mail que corresponde ao de um funcionário anterior, que já não é presente). Assim, é **fundamental** que a sua aplicação não utilize dados legíveis pelo homem para identificar um utilizador - o ser humano legível geralmente significa que alguém vai lê-lo, e quer mudá-lo. Em vez disso, utilize as reclamações fornecidas pela norma OIDC, ou as alegações de extensão fornecidas pela Microsoft - as `sub` `oid` alegações.

Para armazenar corretamente as informações por utilizador, utilização `sub` ou `oid` sozinho (que, como GUIDs, são únicos), com `tid` a utilização para encaminhamento ou fragmento, se necessário.  Se precisar de partilhar dados através dos serviços, `oid` + `tid` é melhor que todas as aplicações obtenham o mesmo `oid` e as `tid` reclamações para um determinado utilizador.  A `sub` alegação na plataforma de identidade da Microsoft é "em termos de par" - é única com base numa combinação do destinatário, inquilino e utilizador simbólico.  Assim, duas aplicações que solicitam fichas de ID para um determinado utilizador receberão `sub` reclamações diferentes, mas as `oid` mesmas reclamações para esse utilizador.

>[!NOTE]
> Não utilize a `idp` reclamação para armazenar informações sobre um utilizador numa tentativa de correlacionar os utilizadores entre os inquilinos.  Não funcionará, como o `oid` pedido de mudança de utilizador entre os `sub` inquilinos, por design, para garantir que as aplicações não podem rastrear os utilizadores através dos inquilinos.  
>
> Os cenários dos hóspedes, em que um utilizador está ao nosso lugar num inquilino, e autentica-se noutro, devem tratar o utilizador como se fosse um novo utilizador para o serviço.  Seus documentos e privilégios no inquilino contoso não devem ser aplicados no inquilino de Fabrikam. Isto é importante para evitar fugas acidentais de dados entre os inquilinos.

### <a name="groups-overage-claim"></a>Reivindicação de excesso de idade de grupos
Para garantir que o tamanho do token não exceda os limites de tamanho do cabeçalho HTTP, o Azure AD limita o número de IDs de objeto que inclui na `groups` alegação. Se um utilizador for membro de mais grupos do que o limite de excesso de idade (150 para fichas SAML, 200 para fichas JWT), então a Azure AD não emite os grupos reclamando no token. Em vez disso, inclui uma reclamação de excesso de tempo no token que indica a aplicação para consultar a API do Microsoft Graph para recuperar a adesão do grupo do utilizador.

```json
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

## <a name="validating-an-id_token"></a>Validação de um id_token

Validar um `id_token` é semelhante ao primeiro passo para [validar um token](access-tokens.md#validating-tokens) de acesso - o seu cliente pode validar que o emitente correto enviou de volta o token e que não foi adulterado. Porque `id_tokens` são sempre um símbolo JWT, muitas bibliotecas existem para validar estes tokens - recomendamos que use uma destas em vez de fazê-lo por si mesmo.  Note que apenas os clientes confidenciais (aqueles com um segredo) devem validar fichas de identificação.  As aplicações públicas (código que funciona inteiramente num dispositivo ou rede que não controla - por exemplo, o navegador de um utilizador ou a sua rede doméstica) não beneficiam de validar o token de ID, uma vez que um utilizador malicioso pode intercetar e editar as chaves utilizadas para validação do token.

Para validar manualmente o token, consulte os detalhes dos passos na [validação de um token de acesso.](access-tokens.md#validating-tokens) Após validação da assinatura no token, as seguintes reclamações do JWT devem ser validadas no id_token (estas também podem ser feitas pela sua biblioteca de validação simbólica):

* Os intervalos de tempo: os `iat` `nbf` , e os `exp` tempos devem cair todos antes ou depois do tempo atual, conforme apropriado.
* Público: a `aud` reclamação deve coincidir com o ID da aplicação para a sua aplicação.
* Nonce: a `nonce` reclamação na carga útil deve corresponder ao parâmetro nonce passado para o ponto final /autorizado durante o pedido inicial.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [fichas de acesso](access-tokens.md)
* Personalize as reclamações do JWT na sua id_token utilizando [reclamações opcionais.](active-directory-optional-claims.md)
