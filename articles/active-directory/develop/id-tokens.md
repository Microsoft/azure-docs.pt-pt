---
title: Fichas de ID da plataforma de identidade da Microsoft / Azure
titleSuffix: Microsoft identity platform
description: Saiba como utilizar id_tokens emitidos pelos pontos finais da Azure AD V1.0 e microsoft (v2.0).
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
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926719"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Fichas de ID da plataforma de identidade da Microsoft

`id_tokens`são enviados para a aplicação do cliente como parte de um fluxo [OpenID Connect](v2-protocols-oidc.md) (OIDC). Podem ser enviados juntamente ou em vez de um sinal de acesso, e são utilizados pelo cliente para autenticar o utilizador.

## <a name="using-the-id_token"></a>Usando o id_token

Os tokens de id devem ser usados para validar que um utilizador é quem eles afirmam ser e obter informações úteis adicionais sobre eles - não deve ser usado para autorização em vez de um sinal de [acesso](access-tokens.md). As alegações que fornece podem ser usadas para UX dentro da sua aplicação, como chaves numa base de dados, e fornecer acesso à aplicação do cliente.  Ao criar chaves para `idp` uma base de dados, não deve ser usado porque estraga os cenários dos hóspedes.  A chave deve `sub` ser feita sozinha (o `tid` que é sempre único), com utilizado para o encaminhamento, se necessário.  Se precisar de partilhar dados `oid` + `sub` + `tid` através de serviços, `oid`funcionará, uma vez que vários serviços recebem todos os mesmos .

## <a name="claims-in-an-id_token"></a>Reclamações num id_token

`id_tokens`para uma identidade da Microsoft são [JWTs](https://tools.ietf.org/html/rfc7519) (JSON Web Tokens), o que significa que consistem numa parte de cabeçalho, carga útil e assinatura. Pode utilizar o cabeçalho e a assinatura para verificar a autenticidade do símbolo, enquanto a carga útil contém as informações sobre o utilizador solicitadas pelo seu cliente. Exceto quando indicado, todos os pedidos jWT listados aqui aparecem em fichas v1.0 e v2.0.

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

### <a name="header-claims"></a>Reclamações de cabeçalho

|Afirmação | Formato | Descrição |
|-----|--------|-------------|
|`typ` | String - sempre "JWT" | Indica que o símbolo é um símbolo jWT.|
|`alg` | String | Indica o algoritmo que foi usado para assinar o símbolo. Exemplo: "RS256" |
|`kid` | String | Impressão digital para a chave pública usada para assinar este símbolo. Emitido tanto em v1.0 como `id_tokens`v2.0 . |
|`x5t` | String | O mesmo (em uso `kid`e valor) que . No entanto, esta é uma reivindicação `id_tokens` de legado emitida apenas em v1.0 para fins de compatibilidade. |

### <a name="payload-claims"></a>Reclamações de carga útil

Esta lista mostra as alegações do JWT que estão na maioria das id_tokens por defeito (exceto quando anotada).  No entanto, a sua aplicação pode utilizar [reclamações opcionais](active-directory-optional-claims.md) para solicitar reclamações adicionais de JWT no id_token.  Estes podem ir `groups` desde a reclamação até à informação sobre o nome do utilizador.

|Afirmação | Formato | Descrição |
|-----|--------|-------------|
|`aud` |  String, um App ID URI | Identifica o destinatário pretendido do símbolo. Em `id_tokens`, o público é o ID de aplicação da sua aplicação, atribuído à sua app no portal Azure. A sua aplicação deverá validar este valor e rejeitar o símbolo se o valor não corresponder. |
|`iss` |  String, um STS URI | Identifica o serviço de token de segurança (STS) que constrói e devolve o símbolo, e o inquilino da AD Azure no qual o utilizador foi autenticado. Se o símbolo tiver sido emitido pelo ponto final v2.0, o URI terminará em `/v2.0`.  O GUID que indica que o utilizador é `9188040d-6c67-4c5b-b112-36a304b66dad`um utilizador de uma conta Microsoft é . A sua aplicação deve utilizar a parte GUID da reivindicação para restringir o conjunto de inquilinos que podem iniciar sessão na app, se aplicável. |
|`iat` |  int, um carimbo de tempo UNIX | "Emitido At" indica quando ocorreu a autenticação deste símbolo.  |
|`idp`|String, geralmente um STS URI | Regista o fornecedor de identidade que autenticou o requerente do token. Este valor é idêntico ao valor da reclamação do Emitente, a menos que a conta de utilizador não esteja no mesmo inquilino que o emitente - hóspedes, por exemplo. Se a reclamação não estiver presente, `iss` significa que o valor pode ser usado em vez disso.  Para contas pessoais que sejam utilizadas num contexto organizacional (por exemplo, uma conta `idp` pessoal convidada a um inquilino da AD Azure), a reclamação pode ser "live.com" ou um STS URI contendo o inquilino `9188040d-6c67-4c5b-b112-36a304b66dad`da conta microsoft. |
|`nbf` |  int, um carimbo de tempo UNIX | A alegação "nbf" (não antes) identifica o tempo anterior ao qual o JWT NÃO deve ser aceite para processamento.|
|`exp` |  int, um carimbo de tempo UNIX | A alegação "exp" (tempo de validade) identifica o tempo de validade em ou após o qual o JWT NÃO deve ser aceite para processamento.  É importante notar que um recurso pode rejeitar o símbolo antes deste tempo também - se, por exemplo, for necessária uma alteração na autenticação ou se tiver sido detetada uma revogação simbólica. |
| `c_hash`| String |O hash código só está incluído em fichas de identificação quando o token de identificação é emitido com um código de autorização OAuth 2.0. Pode ser usado para validar a autenticidade de um código de autorização. Para mais detalhes sobre a realização desta validação, consulte a [especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| String |O hash de acesso é incluído em tokens de id apenas quando o token id é emitido com um token de acesso OAuth 2.0. Pode ser usado para validar a autenticidade de um símbolo de acesso. Para mais detalhes sobre a realização desta validação, consulte a [especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`aio` | Corda Opaca | Uma alegação interna utilizada pela Azure AD para registar dados para reutilização simbólica. Deve ser ignorado.|
|`preferred_username` | String | O nome de utilizador primário que representa o utilizador. Pode ser um endereço de e-mail, número de telefone ou um nome de utilizador genérico sem um formato especificado. O seu valor é mutável e pode mudar com o tempo. Uma vez que é mutável, este valor não deve ser utilizado para tomar decisões de autorização. O `profile` âmbito é necessário para receber esta reclamação.|
|`email` | String | A `email` reclamação está presente por padrão para contas de hóspedes que têm um endereço de e-mail.  A sua aplicação pode solicitar a reclamação de e-mail para `email` utilizadores geridos (os do mesmo inquilino que o recurso) usando a [reclamação opcional](active-directory-optional-claims.md).  No ponto final v2.0, a sua `email` aplicação também pode solicitar o âmbito OpenID Connect - não precisa de solicitar tanto a reclamação opcional como o âmbito para obter a reclamação.  A reclamação de e-mail apenas suporta correio endereçado a partir da informação de perfil do utilizador. |
|`name` | String | A `name` alegação fornece um valor legível pelo homem que identifica o objeto do símbolo. O valor não é garantido ser único, é mutável, e é projetado para ser usado apenas para fins de exibição. O `profile` âmbito é necessário para receber esta reclamação. |
|`nonce`| String | O nonce corresponde ao parâmetro incluído no pedido original /autorizar ao IDP. Se não corresponder, o seu pedido deve rejeitar o símbolo. |
|`oid` | String, um GUID | O identificador imutável para um objeto no sistema de identidade da Microsoft, neste caso, uma conta de utilizador. Este ID identifica exclusivamente o utilizador através de aplicações - duas aplicações diferentes que assinam no mesmo utilizador receberão o mesmo valor na `oid` reclamação. O Microsoft Graph devolverá `id` este ID como propriedade para uma determinada conta de utilizador. Uma `oid` vez que permite que várias aplicações se relacionem com os utilizadores, o `profile` âmbito é necessário para receber esta reclamação. Note que se um único utilizador existir em vários inquilinos, o utilizador irá conter um ID de objeto diferente em cada inquilino - são considerados contas diferentes, mesmo que o utilizador entre em cada conta com as mesmas credenciais. A `oid` alegação é um GUID e não pode ser reutilizada. |
|`roles`| Conjunto de cordas | O conjunto de funções que foram atribuídas ao utilizador que está a fazer login. |
|`rh` | Corda Opaca |Uma reivindicação interna usada pelo Azure para revalidar fichas. Deve ser ignorado. |
|`sub` | String, um GUID | O principal sobre o qual o símbolo afirma informações, como o utilizador de uma aplicação. Este valor é imutável e não pode ser reatribuído ou reutilizado. O sujeito é um identificador em pares - é exclusivo de um determinado ID de aplicação. Se um único utilizador assinar em duas aplicações diferentes usando duas iDs de cliente diferentes, essas aplicações receberão dois valores diferentes para a reclamação do assunto. Isto pode ou não ser desejado dependendo da sua arquitetura e requisitos de privacidade. |
|`tid` | String, um GUID | Um GUID que representa o inquilino azure ad que o utilizador é. Para contas de trabalho e escola, o GUID é a imutável identificação de inquilino da organização a que o utilizador pertence. Para contas pessoais, `9188040d-6c67-4c5b-b112-36a304b66dad`o valor é . O `profile` âmbito é necessário para receber esta reclamação. |
|`unique_name` | String | Fornece um valor legível por humanos que identifica o requerente do token. Este valor é único em qualquer ponto do tempo, mas como os e-mails e outros identificadores podem ser reutilizados, este valor pode reaparecer noutras contas, devendo, portanto, ser utilizado apenas para efeitos de exibição. Só emitida em v1.0. `id_tokens` |
|`uti` | Corda Opaca | Uma reivindicação interna usada pelo Azure para revalidar fichas. Deve ser ignorado. |
|`ver` | Corda, ou 1.0 ou 2.0 | Indica a versão do id_token. |

> [!NOTE]
> Os id_token v1 e v2 têm diferenças na quantidade de informação que irão transportar como visto dos exemplos acima referidos. A versão especifica essencialmente o ponto final da plataforma Azure AD a partir do local onde foi emitida. A implementação da [Azure AD Oauth](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) evoluiu ao longo dos anos. Atualmente temos dois pontos finais oAuth diferentes para aplicações AzureAD. Pode utilizar qualquer um dos novos pontos finais que são categorizados como v2 ou o antigo que se diz ser v1. Os pontos finais de Oauth para ambos são diferentes. O ponto final v2 é o mais recente onde estamos a tentar migrar todas as funcionalidades do ponto final v1 e recomendamos novos desenvolvedores para usar o ponto final v2.
>
> - V1: Pontos finais do Diretório Ativo Azure:`https://login.microsoftonline.com/common/oauth2/authorize`
> - V2: Microsoft Identity Platform Endpoints:`https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>Validando um id_token

Validar `id_token` um é semelhante ao primeiro passo de validação de [um token](access-tokens.md#validating-tokens) de acesso - o seu cliente deve validar que o emitente correto enviou o símbolo de volta e que não foi adulterado. Como `id_tokens` são sempre um símbolo jWT, existem muitas bibliotecas para validar estes tokens - recomendamos que use um destes em vez de fazê-lo por si mesmo.

Para validar manualmente o símbolo, consulte os detalhes dos passos na [validação de um sinal](access-tokens.md#validating-tokens)de acesso . Após a validação da assinatura no símbolo, as seguintes reclamações jWT devem ser validadas na id_token (estas também podem ser feitas pela sua biblioteca de validação simbólica):

* Carimbos temporais: os `iat`, `nbf`e `exp` os selos temporais devem cair todos antes ou depois do tempo atual, conforme apropriado.
* Público: `aud` a reclamação deve corresponder ao ID da aplicação para a sua aplicação.
* Nonce: `nonce` a reclamação na carga deve corresponder ao parâmetro nonce passado para o ponto final /autorizar durante o pedido inicial.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [fichas](access-tokens.md) de acesso
* Personalize as reclamações do JWT no seu id_token utilizando [reclamações opcionais.](active-directory-optional-claims.md)
