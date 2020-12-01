---
title: Tokens de acesso à plataforma de identidade da Microsoft Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os tokens de acesso emitidos pelos pontos finais Azure AD v1.0 e microsoft identity platform (v2.0).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: hirsin
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: b1ce076befc325fef7717c0404b31dadff952af6
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433296"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Fichas de acesso à plataforma de identidade da Microsoft

Os tokens de acesso permitem aos clientes ligar de forma segura para as APIs da web protegidas, e são usados por APIs web para realizar autenticação e autorização. De acordo com a especificação OAuth, os tokens de acesso são cordas opacas sem um formato definido - alguns fornecedores de identidade (IDPs) usam GUIDs, outros usam bolhas encriptadas. A plataforma de identidade da Microsoft utiliza uma variedade de formatos de token de acesso dependendo da configuração da API que aceita o token. [As APIs personalizadas registadas por desenvolvedores](quickstart-configure-app-expose-web-apis.md) na plataforma de identidade da Microsoft podem escolher entre dois formatos diferentes de JSON Web Tokens (JWTs), chamados "v1" e "v2", e APIs desenvolvidos pela Microsoft como Microsoft Graph ou APIs em Azure têm formatos de token proprietários adicionais. Estes formatos proprietários podem ser fichas encriptadas, JWTs ou fichas especiais semelhantes a JWT que não irão validar.

Os clientes devem tratar os tokens de acesso como cordas opacas porque o conteúdo do token se destina apenas ao recurso (a API). Apenas *only* para fins de validação e depuração, os desenvolvedores podem descodificar JWTs usando um site como [jwt.ms](https://jwt.ms). Esteja ciente, no entanto, que os tokens que recebe para uma API da Microsoft podem nem sempre ser um JWT, e que nem sempre pode descodificá-los.

Para mais detalhes sobre o que está dentro do token de acesso, os clientes devem usar os dados de resposta simbólica que são devolvidos com o token de acesso ao seu cliente. Quando o seu cliente solicita um token de acesso, a plataforma de identidade da Microsoft também devolve alguns metadados sobre o token de acesso para o consumo da sua aplicação. Esta informação inclui o prazo de validade do token de acesso e os âmbitos para os quais é válido. Estes dados permitem que a sua app faça o caching inteligente de tokens de acesso sem ter de analisar o próprio token de acesso.

Consulte as seguintes secções para saber como a sua API pode validar e utilizar as reclamações dentro de um token de acesso.  

> [!NOTE]
> Toda a documentação desta página, exceto quando anotado, aplica-se apenas aos tokens emitidos para APIs que tenha registado.  Não se aplica a fichas emitidas para APIs detidas pela Microsoft, nem esses tokens podem ser usados para validar como a plataforma de identidade da Microsoft emitirá fichas para uma API que cria.  

## <a name="token-formats-and-ownership"></a>Formatos e propriedade token

### <a name="v10-and-v20"></a>v1.0 e v2.0 

Existem duas versões de acesso disponíveis na plataforma de identidade da Microsoft: v1.0 e v2.0.  Estas versões regem o que as alegações estão no símbolo, garantindo que uma API web possa controlar como são os seus tokens. As APIs da Web têm uma destas selecionadas como padrão durante o registo - v1.0 para aplicações AD Azure e v2.0 para aplicações que suportam contas de consumidores.  Isto é controlável por aplicações que usam a `accessTokenAcceptedVersion` configuração no manifesto da [aplicação](reference-app-manifest.md#manifest-reference), onde `null` e `1` resultam em fichas v1.0, e `2` resulta em fichas v2.0.

### <a name="what-app-is-a-token-for"></a>Para que aplicativo é um símbolo "para"?

Há duas partes envolvidas num pedido de acesso simbólico: o cliente, que solicita o token, e o recurso (a API) que aceita o token quando a API é chamada. A `aud` reclamação num token indica o recurso para o qual o símbolo se destina (o seu *público).* Os clientes usam o símbolo, mas não devem compreendê-lo ou tentar analisá-lo. Os recursos aceitam o símbolo.  

A plataforma de identidade da Microsoft suporta a emissão de qualquer versão simbólica a partir de qualquer ponto final da versão - não estão relacionadas. É por isso que uma definição de recursos `accessTokenAcceptedVersion` significa que um cliente que liga para o ponto final `2` v1.0 para obter um símbolo para que a API receberá um token de acesso v2.0.  Os recursos são sempre donos dos seus tokens (aqueles com a sua `aud` reivindicação) e são as únicas aplicações que podem alterar os seus detalhes simbólicos. É por isso que a alteração das [reclamações opcionais](active-directory-optional-claims.md) de acesso ao seu *cliente* não altera o token de acesso recebido quando é solicitado um token `user.read` , que é propriedade do recurso Microsoft Graph.

### <a name="sample-tokens"></a>Fichas de amostra

os tokens v1.0 e v2.0 são semelhantes e contêm muitas das mesmas reivindicações. Um exemplo de cada um é fornecido aqui. Estes tokens de exemplo não [validam,](#validating-tokens)no entanto, uma vez que as teclas giraram antes da publicação e as informações pessoais foram removidas das suas.

#### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Veja este símbolo v1.0 em [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd).

#### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Veja este símbolo v2.0 em [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Reclamações em fichas de acesso

JWTs (JSON Web Tokens) são divididos em três peças:

* **Cabeçalho** - Fornece informações sobre como [validar o token,](#validating-tokens) incluindo informações sobre o tipo de token e como foi assinado.
* **Carga útil** - Contém todos os dados importantes sobre o utilizador ou app que está a tentar ligar para o seu serviço.
* **Assinatura** - É a matéria-prima utilizada para validar o token.

Cada peça é separada por um período `.` () e codificada separadamente base64.

As reclamações só estão presentes se existir um valor para preenchê-lo. A sua aplicação não deve depender da presença de uma reclamação. Exemplos incluem `pwd_exp` (nem todos os inquilinos exigem que as palavras-passe expirem) e `family_name` ([credenciais de clientes] (v2-oauth2-client-creds-grant-flow.md) os fluxos são em nome de aplicações que não têm nomes). As reclamações utilizadas para o acesso à validação do token estarão sempre presentes.

Algumas alegações são usadas para ajudar a Azure AD a proteger os tokens em caso de reutilização. Estes são marcados como não sendo para consumo público na descrição como "Opaco". Estas reclamações podem ou não aparecer num token, e as novas podem ser adicionadas sem aviso prévio.

### <a name="header-claims"></a>Reclamações de cabeçalho

|Afirmação | Formato | Descrição |
|--------|--------|-------------|
| `typ` | String - sempre "JWT" | Indica que o símbolo é um JWT.|
| `nonce` | String | Um identificador único usado para proteger contra ataques de repetição de símbolos. O seu recurso pode registar este valor para proteger contra repetições. |
| `alg` | String | Indica o algoritmo que foi usado para assinar o token, por exemplo, "RS256" |
| `kid` | String | Especifica a impressão digital para a chave pública que é usada para assinar este token. Emitidos em fichas de acesso v1.0 e v2.0. |
| `x5t` | String | Funciona da mesma forma (em uso e valor) como `kid` . `x5t` é uma reivindicação de legado emitida apenas em fichas de acesso v1.0 para fins de compatibilidade. |

### <a name="payload-claims"></a>Reclamações de carga útil

| Afirmação | Formato | Descrição |
|-----|--------|-------------|
| `aud` | String, um ID URI ou GUID | Identifica o destinatário pretendido do símbolo - o seu público.  A sua API deve validar este valor e rejeitar o token se o valor não corresponder. Nos tokens v2.0, este é sempre o ID do cliente da API, enquanto em fichas v1.0 pode ser o ID do cliente ou o recurso URI usado no pedido, dependendo de como o cliente solicitou o token.|
| `iss` | String, um STS URI | Identifica o serviço de fichas de segurança (STS) que constrói e devolve o token, e o inquilino AD AZure em que o utilizador foi autenticado. Se o token emitido for um sinal v2.0 (ver `ver` reclamação), o URI terminará em `/v2.0` . O GUID que indica que o utilizador é um utilizador consumidor de uma conta microsoft é `9188040d-6c67-4c5b-b112-36a304b66dad` . A sua aplicação pode utilizar a parte GUID da reivindicação para restringir o conjunto de inquilinos que podem iniciar sôm na app, se aplicável. |
|`idp`| String, geralmente um STS URI | Regista o fornecedor de identidade que autenticou o requerente do token. Este valor é idêntico ao valor da reclamação do Emitente a menos que a conta de utilizador não esteja no mesmo inquilino que o emitente - os hóspedes, por exemplo. Se a reclamação não estiver presente, significa que o valor `iss` pode ser usado em vez disso.  Para contas pessoais utilizadas num contexto organizacional (por exemplo, uma conta pessoal convidada a um inquilino da AD Azure), a `idp` reclamação pode ser "live.com" ou uma STS URI contendo o inquilino da conta `9188040d-6c67-4c5b-b112-36a304b66dad` Microsoft. |
| `iat` | int, um timetamp UNIX | "Emitida At" indica quando ocorreu a autenticação para este token. |
| `nbf` | int, um timetamp UNIX | A alegação "nbf" (não antes) identifica o tempo anterior ao qual o JWT não deve ser aceite para processamento. |
| `exp` | int, um timetamp UNIX | A alegação "exp" (tempo de validade) identifica o tempo de validade em ou após o qual o JWT não deve ser aceite para processamento. É importante notar que um recurso pode rejeitar o token antes também deste tempo, como quando é necessária uma alteração na autenticação ou foi detetada uma revogação simbólica. |
| `aio` | Corda Opaca | Uma alegação interna usada pela Azure AD para registar dados para reutilização de fichas. Os recursos não devem utilizar esta reivindicação. |
| `acr` | String, um "0" ou "1" | Só presentes em fichas v1.0. A alegação de "Classe de contexto de autenticação". Um valor de "0" indica que a autenticação do utilizador final não cumpriu os requisitos da ISO/IEC 29115. |
| `amr` | Conjunto de cordas JSON | Só presentes em fichas v1.0. Identifica como o objeto do símbolo foi autenticado. Consulte [a secção de reclamações da AMR](#the-amr-claim) para mais detalhes. |
| `appid` | String, um GUID | Só presentes em fichas v1.0. A identificação da aplicação do cliente usando o token. A aplicação pode funcionar como si mesma ou em nome de um utilizador. O ID da aplicação normalmente representa um objeto de aplicação, mas também pode representar um objeto principal de serviço em Azure AD. |
| `azp` | String, um GUID | Apenas presente em fichas v2.0, substituição de `appid` . A identificação da aplicação do cliente usando o token. A aplicação pode funcionar como si mesma ou em nome de um utilizador. O ID da aplicação normalmente representa um objeto de aplicação, mas também pode representar um objeto principal de serviço em Azure AD. |
| `appidacr` | "0", "1" ou "2" | Só presentes em fichas v1.0. Indica como o cliente foi autenticado. Para um cliente público, o valor é "0". Se a identificação do cliente e o segredo do cliente forem usados, o valor é "1". Se um certificado de cliente foi usado para autenticação, o valor é "2". |
| `azpacr` | "0", "1" ou "2" | Apenas presente em fichas v2.0, substituição de `appidacr` . Indica como o cliente foi autenticado. Para um cliente público, o valor é "0". Se a identificação do cliente e o segredo do cliente forem usados, o valor é "1". Se um certificado de cliente foi usado para autenticação, o valor é "2". |
| `preferred_username` | String | O nome de utilizador primário que representa o utilizador. Pode ser um endereço de e-mail, número de telefone ou um nome de utilizador genérico sem um formato especificado. O seu valor é mutável e pode mudar com o tempo. Uma vez que é mutável, este valor não deve ser utilizado para tomar decisões de autorização.  Pode ser usado para sugestões de nome de utilizador, no entanto, e em UI legível pelo homem como nome de utilizador. O `profile` âmbito é necessário para receber esta reclamação. Presente apenas em fichas v2.0. |
| `name` | String | Fornece um valor legível pelo homem que identifica o sujeito do símbolo. O valor não é garantido ser único, é mutável, e é projetado para ser usado apenas para fins de exibição. O `profile` âmbito é necessário para receber esta reclamação. |
| `scp` | String, uma lista de âmbitos separados do espaço | O conjunto de âmbitos expostos pelo seu pedido para o qual o pedido do cliente solicitou (e recebeu) consentimento. A sua aplicação deve verificar se estes âmbitos são válidos expostos pela sua app e tomar decisões de autorização com base no valor destes âmbitos. Incluído apenas para [fichas de utilizador](#user-and-application-tokens). |
| `roles` | Matriz de cordas, uma lista de permissões | O conjunto de permissões expostas pela sua aplicação que o pedido de pedido ou utilizador foi autorizado a ligar. Para [fichas de aplicação,](#user-and-application-tokens)esta é utilizada durante o fluxo de credencial do cliente[(v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) em vez de âmbitos de utilização.  Para [fichas de utilizador](#user-and-application-tokens) este é preenchido com as funções a que o utilizador foi atribuído na aplicação-alvo. |
| `wids` | Matriz de [GUIDs RoleTemplateID](../roles/permissions-reference.md#role-template-ids) | Denota as funções atribuídas a este utilizador, a partir da secção de funções presentes na [página de funções de administração](../roles/permissions-reference.md#role-template-ids).  Esta reclamação é configurada numa base por aplicação, através da `groupMembershipClaims` propriedade do manifesto de [aplicação.](reference-app-manifest.md)  É necessário defini-lo para "All" ou "DirectoryRole".  Não pode estar presente em fichas obtidas através do fluxo implícito devido a preocupações de comprimento simbólico. |
| `groups` | JSON array de GUIDs | Fornece iDs de objeto que representam os membros do grupo do sujeito. Estes valores são únicos (ver Object ID) e podem ser utilizados com segurança para gerir o acesso, como impor autorização de acesso a um recurso. Os grupos incluídos nos grupos afirmam que são configurados por aplicação, através `groupMembershipClaims` da propriedade do manifesto de [aplicação.](reference-app-manifest.md) Um valor de nulo excluirá todos os grupos, um valor de "SecurityGroup" incluirá apenas membros do Ative Directory Security Group, e um valor de "All" incluirá tanto grupos de segurança como Listas de Distribuição microsoft 365. <br><br>Consulte a `hasgroups` reclamação abaixo para obter mais informações sobre a utilização da `groups` reclamação com a subvenção implícita. <br>Para outros fluxos, se o número de grupos em que o utilizador se encontra ultrapassar um limite (150 para o SAML, 200 para o JWT), então será adicionada uma reclamação por excesso de informação às fontes de reclamação que apontam para o ponto final do Microsoft Graph contendo a lista de grupos para o utilizador. |
| `hasgroups` | Booleano | Se estiver presente, `true` denota sempre, o utilizador está em pelo menos um grupo. Utilizado em vez da reclamação de `groups` JWTs em fluxos de subvenções implícitos se os grupos completos alegarem estender o fragmento URI para além dos limites de comprimento do URL (atualmente 6 ou mais grupos). Indica que o cliente deve utilizar a API do Microsoft Graph para determinar os grupos do utilizador `https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects` (). |
| `groups:src1` | Objeto JSON | Para pedidos simbólicos que não sejam limitados (ver `hasgroups` acima) mas ainda demasiado grandes para o token, será incluído um link para a lista completa de grupos para o utilizador. Para os JWTs como uma reclamação distribuída, para a SAML como uma nova reivindicação em vez da `groups` reclamação. <br><br>**Exemplo JWT Valor**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | String | O principal sobre o qual o token afirma informações, como o utilizador de uma aplicação. Este valor é imutável e não pode ser reatribuído ou reutilizado. Pode ser usado para efetuar verificações de autorização com segurança, como quando o token é usado para aceder a um recurso, e pode ser usado como chave em tabelas de bases de dados. Como o assunto está sempre presente nos tokens que a Azure AD emite, recomendamos a utilização deste valor num sistema de autorização para fins gerais. O sujeito é, no entanto, um identificador de pares - é exclusivo de um determinado ID de aplicação. Portanto, se um único utilizador assinar duas aplicações diferentes usando dois IDs de clientes diferentes, essas aplicações receberão dois valores diferentes para a reivindicação do assunto. Isto pode ou não ser desejado dependendo da sua arquitetura e requisitos de privacidade. Consulte também a `oid` reclamação (que permanece a mesma entre aplicações dentro de um inquilino). |
| `oid` | String, um GUID | O identificador imutável de um objeto na plataforma de identidade da Microsoft, neste caso, uma conta de utilizador. Também pode ser utilizado para efetuar verificações de autorização com segurança e como chave nas tabelas de bases de dados. Este ID identifica exclusivamente o utilizador através de aplicações - duas aplicações diferentes que assinam no mesmo utilizador receberão o mesmo valor na `oid` reclamação. Assim, `oid` pode ser usado ao fazer consultas aos serviços online da Microsoft, como o Microsoft Graph. O Microsoft Graph devolverá este ID como `id` propriedade para uma determinada conta de [utilizador.](/graph/api/resources/user) Uma vez `oid` que permite que várias aplicações correlacionem os utilizadores, o `profile` âmbito é necessário para receber esta reclamação. Note que se um único utilizador existir em vários inquilinos, o utilizador conterá um ID de objeto diferente em cada inquilino - são considerados contas diferentes, mesmo que o utilizador faça logins em cada conta com as mesmas credenciais. |
| `tid` | String, um GUID | Representa o inquilino AZure AD de que o utilizador é. Para contas de trabalho e escola, o GUID é o imutável ID de inquilino da organização a que o utilizador pertence. Para contas pessoais, o valor `9188040d-6c67-4c5b-b112-36a304b66dad` é. O `profile` âmbito é necessário para receber esta reclamação. |
| `unique_name` | String | Só presentes em fichas v1.0. Fornece um valor legível por humanos que identifica o requerente do token. Este valor não é garantido ser único dentro de um inquilino e deve ser usado apenas para fins de exposição. |
| `uti` | Corda Opaca | Uma alegação interna usada por Azure para revalidar fichas. Os recursos não devem usar esta reivindicação. |
| `rh` | Corda Opaca | Uma alegação interna usada por Azure para revalidar fichas. Os recursos não devem utilizar esta reivindicação. |
| `ver` | String, ou `1.0` ou `2.0` | Indica a versão do token de acesso. |

**Reivindicação de excesso de idade de grupos**

Para garantir que o tamanho do token não exceda os limites de tamanho do cabeçalho HTTP, o Azure AD limita o número de IDs de objeto que inclui nos grupos afirmam. Se um utilizador for membro de mais grupos do que o limite de excesso de tempo (150 para fichas SAML, 200 para fichas JWT e apenas 6 se emitido através do fluxo implícito), então a Azure AD não emite a reivindicação dos grupos no token. Em vez disso, inclui uma reclamação de excesso de tempo no token que indica a aplicação para consultar a API do Microsoft Graph para recuperar a adesão do grupo do utilizador.

```JSON
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

Pode utilizar a `BulkCreateGroups.ps1` pasta de [Scripts de Criação de Aplicações](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts) para ajudar a testar cenários de overage.

#### <a name="v10-basic-claims"></a>v1.0 créditos básicos

As seguintes reclamações serão incluídas em fichas v1.0, se aplicável, mas não estão incluídas em fichas v2.0 por padrão. Se estiver a utilizar o v2.0 e precisar de uma destas reclamações, solicite-os usando [reclamações opcionais](active-directory-optional-claims.md).

| Afirmação | Formato | Descrição |
|-----|--------|-------------|
| `ipaddr`| String | O endereço IP do utilizador autenticado. |
| `onprem_sid`| String, em [formato SID](/windows/desktop/SecAuthZ/sid-components) | Nos casos em que o utilizador tenha uma autenticação no local, esta alegação fornece o seu SID. Pode usar `onprem_sid` para autorização em aplicações antigas.|
| `pwd_exp`| int, um timetamp UNIX | Indica quando a palavra-passe do utilizador expira. |
| `pwd_url`| String | Um URL onde os utilizadores podem ser enviados para redefinir a sua palavra-passe. |
| `in_corp`| boolean | Sinaliza se o cliente está a fazer login na rede corporativa. Se não estiverem, a reclamação não está incluída. |
| `nickname`| String | Um nome adicional para o utilizador, separado do primeiro ou último nome.|
| `family_name` | String | Fornece o sobrenome, apelido ou nome de família do utilizador, conforme definido no objeto do utilizador. |
| `given_name` | String | Fornece o primeiro ou dado nome do utilizador, conforme definido no objeto do utilizador. |
| `upn` | String | O nome de utilizador do utilizador. Pode ser um número de telefone, endereço de e-mail ou uma cadeia não-formatada. Deve ser utilizado apenas para visualização e fornecer indicações de nome de utilizador em cenários de reautornação. |

#### <a name="the-amr-claim"></a>A `amr` reivindicação

As identidades da Microsoft podem autenticar de diferentes formas, o que pode ser relevante para a sua aplicação. A `amr` alegação é um conjunto que pode conter vários itens, `["mfa", "rsa", "pwd"]` como, por exemplo, para uma autenticação que usou uma palavra-passe e a aplicação Authenticator.

| Valor | Descrição |
|-----|-------------|
| `pwd` | Autenticação de palavra-passe, senha microsoft de um utilizador ou segredo de cliente de uma aplicação. |
| `rsa` | A autenticação baseou-se na prova de uma chave RSA, por exemplo com a [aplicação Microsoft Authenticator](https://aka.ms/AA2kvvu). Isto inclui se a autenticação foi feita por um JWT auto-assinado com um certificado X509 de propriedade de serviço. |
| `otp` | Código de acesso único utilizando um e-mail ou uma mensagem de texto. |
| `fed` | Foi utilizada uma afirmação de autenticação federada (como jWT ou SAML). |
| `wia` | Autenticação Integrada do Windows |
| `mfa` | Foi utilizada [a autenticação de vários fatores.](../authentication/concept-mfa-howitworks.md) Quando esta estiver presente, os outros métodos de autenticação também serão incluídos. |
| `ngcmfa` | Equivalente a `mfa` , utilizado para o provisionamento de certos tipos de credenciais avançados. |
| `wiaormfa`| O utilizador utilizou o Windows ou uma credencial MFA para autenticar. |
| `none` | Não foi feita nenhuma autenticação. |

## <a name="validating-tokens"></a>Validação de fichas

Nem todas as aplicações devem validar fichas. Só em cenários específicos as aplicações devem validar um símbolo:

* [As APIs web](quickstart-configure-app-expose-web-apis.md) devem validar os tokens de acesso que lhes são enviados por um cliente.  Só devem aceitar fichas que contenham a sua `aud` reivindicação.
* Aplicações confidenciais como ASP.NET Core devem validar fichas de ID enviadas através do navegador do utilizador no fluxo híbrido, antes de permitir o acesso aos dados de um utilizador ou estabelecer uma sessão.

Se nenhum dos cenários acima referidos se aplicar, a sua aplicação não beneficiará da validação do token, podendo apresentar um risco de segurança e fiabilidade se as decisões forem tomadas com base na validade do token.  Clientes públicos como apps nativas ou SPAs não beneficiam de validar fichas - a aplicação comunica diretamente com o IDP, pelo que a proteção SSL garante que os tokens são válidos.

 As APIs e as aplicações web só devem validar tokens que tenham uma `aud` alegação que corresponda à sua aplicação; outros recursos podem ter regras de validação de fichas personalizadas. Por exemplo, os tokens para o Microsoft Graph não validam de acordo com estas regras devido ao seu formato proprietário. Validar e aceitar fichas destinadas a outro recurso é um exemplo do problema confuso do [deputado.](https://cwe.mitre.org/data/definitions/441.html)

Se a sua aplicação precisar de validar uma id_token ou uma access_token de acordo com o acima, a sua aplicação deve primeiro validar a assinatura e o emitente do token contra os valores no documento de descoberta OpenID. Por exemplo, a versão independente do inquilina do documento situa-se em [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration) .

São fornecidas as seguintes informações para aqueles que desejam compreender o processo subjacente. O middleware AD AD Azure tem capacidades incorporadas para validar tokens de acesso, e você pode navegar através das nossas [amostras](sample-v2-code.md) para encontrar um no idioma à sua escolha. Existem também várias bibliotecas de código aberto de terceiros disponíveis para validação JWT - há pelo menos uma opção para quase todas as plataformas e idiomas. Para obter mais informações sobre bibliotecas de autenticação AD AZure e amostras de código, consulte as [bibliotecas de autenticação.](reference-v2-libraries.md)

### <a name="validating-the-signature"></a>Validação da assinatura

Um JWT contém três segmentos, que são separados pelo `.` personagem. O primeiro segmento é conhecido como o **cabeçalho**, o segundo como o **corpo**, e o terceiro como **a assinatura**. O segmento de assinatura pode ser usado para validar a autenticidade do token para que possa ser confiável pela sua app.

Os tokens emitidos pela Azure AD são assinados usando algoritmos de encriptação assimétrica padrão da indústria, tais como RS256. O cabeçalho do JWT contém informações sobre a chave e o método de encriptação utilizado para assinar o token:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

A `alg` alegação indica o algoritmo que foi usado para assinar o token, enquanto a `kid` alegação indica a chave pública particular que foi usada para validar o token.

A qualquer momento, a Azure AD pode assinar um id_token usando qualquer um de um determinado conjunto de pares de chaves público-privado. O Azure AD roda o possível conjunto de teclas numa base periódica, pelo que a sua aplicação deve ser escrita para lidar automaticamente com essas alterações de teclas. Uma frequência razoável para verificar se há atualizações nas chaves públicas utilizadas pelo Azure AD a cada 24 horas.

Pode adquirir os dados-chave de assinatura necessários para validar a assinatura utilizando o [documento de metadados OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) localizado em:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Experimente este [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) num browser!

Este documento de metadados:

* É um objeto JSON que contém várias informações úteis, tais como a localização dos vários pontos finais necessários para a autenticação OpenID Connect.
* Inclui um `jwks_uri` , que dá a localização do conjunto de chaves públicas usadas para assinar fichas. A Chave Web JSON (JWK) localizada no `jwks_uri` local contém todas as informações chave do público em uso nesse momento específico.  O formato JWK é descrito no [RFC 7517](https://tools.ietf.org/html/rfc7517).  A sua aplicação pode utilizar a `kid` reclamação no cabeçalho JWT para selecionar qual a chave pública deste documento que foi usada para assinar um sinal particular. Em seguida, pode fazer validação de assinatura usando a chave pública correta e o algoritmo indicado.

> [!NOTE]
> Recomendamos a utilização da `kid` reclamação para validar o seu token. Embora os tokens v1.0 contenham tanto as `x5t` `kid` alegações, os tokens v2.0 contêm apenas a `kid` reclamação.

Fazer validação de assinaturas está fora do âmbito deste documento - existem muitas bibliotecas de código aberto disponíveis para ajudá-lo a fazê-lo, se necessário.  No entanto, a plataforma de identidade da Microsoft tem uma extensão de assinatura simbólica para os padrões - chaves de assinatura personalizadas.

Se a sua aplicação tiver teclas de assinatura personalizadas como resultado da utilização da funcionalidade [de mapeamento de sinistros,](active-directory-claims-mapping.md) tem de anexar um `appid` parâmetro de consulta que contenha o ID da aplicação para obter uma `jwks_uri` indicação para as informações-chave de assinatura da sua aplicação, que devem ser usadas para validação. Por exemplo: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` contém um `jwks_uri` de `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` .

### <a name="claims-based-authorization"></a>Autorização baseada em sinistros

A lógica de negócio da sua aplicação ditará este passo, alguns métodos de autorização comuns são estabelecidos abaixo.

* Verifique `scp` o ou `roles` pretender verificar se todos os âmbitos presentes correspondem aos expostos pela sua API e permita que o cliente faça a ação solicitada.
* Certifique-se de que o cliente de chamada está autorizado a ligar para a sua API usando a `appid` reclamação.
* Validar o estado de autenticação do cliente que telefona a usar `appidacr` - não deve ser 0 se os clientes públicos não estiverem autorizados a ligar para a sua API.
* Verifique contra uma lista de `nonce` alegações passadas para verificar se o símbolo não está a ser reproduzido.
* Verifique se `tid` o inquilino corresponde a um inquilino que pode ligar para a sua API.
* Utilize a `acr` reclamação para verificar se o utilizador realizou MFA. Isto deve ser aplicado utilizando [o Acesso Condicional](../conditional-access/overview.md).
* Se você solicitou as `roles` `groups` reclamações ou reclamações no token de acesso, verifique se o utilizador está no grupo autorizado a fazer esta ação.
  * Para os tokens recuperados usando o fluxo implícito, provavelmente terá de consultar o [Microsoft Graph](https://developer.microsoft.com/graph/) para estes dados, uma vez que é muitas vezes demasiado grande para caber no token.

## <a name="user-and-application-tokens"></a>Fichas de utilizador e aplicação

A sua aplicação pode receber fichas para o utilizador (o fluxo normalmente discutido) ou diretamente a partir de uma aplicação (através do fluxo de credenciais do [cliente).](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) Estes tokens apenas para aplicações indicam que esta chamada vem de uma aplicação e não tem um utilizador a apoiá-la. Estes tokens são manuseados em grande parte da mesma forma:

* Use `roles` para ver permissões que tenham sido concedidas ao tema do símbolo.
* Utilize `oid` ou `sub` valide que o principal do serviço de chamadas é o esperado.

Se a sua aplicação precisar de distinguir entre fichas de acesso apenas a aplicações e fichas de acesso para os utilizadores, utilize a `idtyp` [reivindicação opcional.](active-directory-optional-claims.md)  Ao adicionar a `idtyp` reclamação ao `accessToken` campo, e verificando o `app` valor, é possível detetar fichas de acesso apenas a aplicações.  Fichas de ID e fichas de acesso para utilizadores não terão a `idtyp` reclamação incluída.

## <a name="token-revocation"></a>Revogação simbólica

As fichas de atualização podem ser invalidadas ou revogadas a qualquer momento, por diferentes razões. Estes enquadram-se em duas categorias principais: intervalos e revogações.

### <a name="token-timeouts"></a>Intervalos de tempo simbólicos

Utilizando [a configuração de vida útil simbólica,](active-directory-configurable-token-lifetimes.md)a vida útil dos tokens de atualização pode ser alterada.  É normal e espera-se que alguns tokens não sejam utilizados (por exemplo, o utilizador não abre a app durante 3 meses) e, portanto, expira.  As aplicações vão encontrar cenários em que o servidor de login rejeita um token de atualização devido à sua idade.

* MaxInactiveTime: Se o token de atualização não tiver sido utilizado dentro do tempo ditado pelo MaxInactiveTime, o Token Refresh deixará de ser válido.
* MaxSessionAge: Se MaxAgeSessionMultiFactor ou MaxAgeSessionSingleFactor foram definidos para algo diferente do seu padrão (Até revogação), então a reautição será necessária após o tempo definido no MaxAgeSession* decorrer.
* Exemplos:
  * O inquilino tem um MaxInactiveTime de cinco dias, e o utilizador saiu de férias por uma semana, e assim a AZure AD não vê um novo pedido simbólico do utilizador em 7 dias. Da próxima vez que o utilizador pedir um novo token, eles vão descobrir que o seu Refresh Token foi revogado, e eles devem introduzir as suas credenciais novamente.
  * Uma aplicação sensível tem um MaxAgeSessionSingleFactor de um dia. Se um utilizador iniciar sessão na segunda-feira e na terça-feira (depois de 25 horas decorridos), será obrigado a reauthentar.

### <a name="revocation"></a>Revogação

As fichas de atualização podem ser revogadas pelo servidor devido a uma alteração de credenciais, ou devido à utilização ou ação de administração.  Os tokens de atualização caem em duas classes - as emitidas a clientes confidenciais (a coluna mais à direita) e as emitidas a clientes públicos (todas as outras colunas).

| Alterar | Cookie baseado em palavra-passe | Ficha baseada em palavra-passe | Cookie não baseado em palavra-passe | Token não baseado em palavra-passe | Ficha de cliente confidencial |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| A palavra-passe expira | Permanece vivo | Permanece vivo | Permanece vivo | Permanece vivo | Permanece vivo |
| Palavra-passe alterada pelo utilizador | Revoked | Revoked | Permanece vivo | Permanece vivo | Permanece vivo |
| O utilizador faz SSPR | Revoked | Revoked | Permanece vivo | Permanece vivo | Permanece vivo |
| Admin reinicia senha | Revoked | Revoked | Permanece vivo | Permanece vivo | Permanece vivo |
| Utilizador revoga os seus tokens de atualização [via PowerShell](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Revoked | Revoked | Revoked | Revoked | Revoked |
| Admin revoga todos os tokens de atualização para um utilizador [via PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Revoked | Revoked |Revoked | Revoked | Revoked |
| Único sign-out[(v1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out), [v2.0](v2-protocols-oidc.md#single-sign-out) ) na web | Revoked | Permanece vivo | Revoked | Permanece vivo | Permanece vivo |

#### <a name="non-password-based"></a>Não baseada em palavra-passe

Um *login não baseado em palavra-passe* é aquele em que o utilizador não digitou uma palavra-passe para obtê-lo. Exemplos de login não baseado em palavra-passe incluem:

- Usando o seu rosto com Windows Hello
- Chave FIDO2
- SMS
- Voz
- PIN

> [!NOTE]
> Os Tokens de Atualização Primária (PRT) no Windows 10 são segregados com base na credencial. Por exemplo, o Windows Hello e a palavra-passe têm os respetivos PRTs, isolados uns dos outros. Quando um utilizador se inscreve com uma credencial Hello (PIN ou biometria) e, em seguida, altera a palavra-passe, o PRT baseado na palavra-passe obtido anteriormente será revogado. Entrar de volta com uma senha invalida o antigo PRT e pede um novo.
>
> Os tokens de atualização não são invalidados ou revogados quando usados para obter um novo token de acesso e atualização token.  No entanto, a sua aplicação deve descartar a antiga assim que for utilizada e substituí-la pela nova, uma vez que o novo token tem um novo tempo de validade na sua.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais [ `id_tokens` em Azure AD](id-tokens.md).
* Saiba mais sobre permissão e consentimento [(v1.0,](../azuread-dev/v1-permissions-consent.md) [v2.0](v2-permissions-and-consent.md)).
