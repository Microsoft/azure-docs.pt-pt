---
title: Referência de acesso à plataforma de identidade da Microsoft Azure
description: Saiba mais sobre os tokens de acesso emitidos pelos pontos finais da Azure AD v1.0 e microsoft (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: bacac67ddd7f379d679a149fe9574676ae0c7567
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834429"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Fichas de acesso à plataforma de identidade da Microsoft

Os tokens de acesso permitem que os clientes liguem de forma segura para apis protegidos pelo Azure. Os tokens de acesso à plataforma de identidade da Microsoft são [JWTs](https://tools.ietf.org/html/rfc7519), Base64 codificado objetos JSON assinados pelo Azure. Os clientes devem tratar os tokens de acesso como cordas opacas, uma vez que o conteúdo do token se destina apenas ao recurso. Para fins de validação e depuração, os desenvolvedores podem descodificar JWTs usando um site como [jwt.ms](https://jwt.ms). O seu cliente pode obter um sinal de acesso a partir do ponto final v1.0 ou do ponto final v2.0 usando uma variedade de protocolos.

Quando o seu cliente pede um token de acesso, o Azure AD também devolve alguns metadados sobre o sinal de acesso para consumo da sua aplicação. Estas informações incluem o tempo de validade do token de acesso e os âmbitos para os quais é válido. Estes dados permitem que a sua aplicação faça um cache inteligente de fichas de acesso sem ter que analisar o próprio token de acesso.

Se a sua aplicação for um recurso (Web API) a que os clientes possam solicitar acesso, os tokens de acesso fornecem informações úteis para uso na autenticação e autorização, tais como o utilizador, cliente, emitente, permissões e muito mais.

Consulte as seguintes secções para saber como um recurso pode validar e utilizar as reclamações dentro de um token de acesso.

> [!IMPORTANT]
> Os tokens de acesso são criados com base no *público* do símbolo, o que significa que a aplicação que possui os âmbitos no símbolo.  É assim que uma definição de recursos `accessTokenAcceptedVersion` no manifesto da [aplicação](reference-app-manifest.md#manifest-reference) para `2` permite que um cliente que liga para o ponto final v1.0 receba um sinal de acesso v2.0.  Da mesma forma, é por isso que a alteração dos [pedidos opcionais](active-directory-optional-claims.md) de acesso ao seu cliente não altera o token de acesso recebido quando é solicitado um token para `user.read`, que é propriedade do recurso MS Graph.  
> Pela mesma razão, ao testar a sua aplicação de cliente com uma conta pessoal (como hotmail.com ou outlook.com), poderá descobrir que o sinal de acesso recebido pelo seu cliente é uma corda opaca. Isto porque o recurso a ser acedido solicitou bilhetes legados da MSA (conta Microsoft) que são encriptados e não podem ser compreendidos pelo cliente.

## <a name="sample-tokens"></a>Fichas de amostra

v1.0 e v2.0 tokens parecem semelhantes e contêm muitas das mesmas reivindicações. Um exemplo de cada um é fornecido aqui.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Veja este símbolo v1.0 em [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Veja este símbolo v2.0 em [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Reclamações em fichas de acesso

Os JWTs são divididos em três pedaços:

* **Header** - Fornece informações sobre como [validar o símbolo,](#validating-tokens) incluindo informações sobre o tipo de ficha e como foi assinado.
* **Payload** - Contém todos os dados importantes sobre o utilizador ou app que está a tentar ligar para o seu serviço.
* **Assinatura** - É a matéria-prima utilizada para validar o símbolo.

Cada peça é separada por um período (`.`) e separadamente a Base64 codificada.

As reclamações só estão presentes se existir um valor para o preencher. Então, a sua aplicação não deve assumir uma dependência de uma reclamação estar presente. Exemplos incluem `pwd_exp` (nem todos os inquilinos exigem que as palavras-passe expirem) ou `family_name` (os fluxos[credenciais](v1-oauth2-client-creds-grant-flow.md) de cliente são em nome de aplicações, que não têm nomes). As reclamações utilizadas para a validação de fichas de acesso estarão sempre presentes.

> [!NOTE]
> Algumas alegações são usadas para ajudar a Azure AD a obter fichas seguras em caso de reutilização. Estes são marcados como não sendo para consumo público na descrição como "Opaco". Estas reclamações podem ou não aparecer num símbolo, e as novas podem ser adicionadas sem aviso prévio.

### <a name="header-claims"></a>Declarações de cabeçalho

|Afirmação | Formato | Descrição |
|--------|--------|-------------|
| `typ` | Cadeia de caracteres – sempre "JWT" | Indica que o token é um JWT.|
| `nonce` | Cadeia | Um identificador único usado para proteger contra ataques de repetição de tokens. O seu recurso pode registar este valor para proteger contra repetições. |
| `alg` | Cadeia | Indica o algoritmo que foi usado para assinar o símbolo, por exemplo, "RS256" |
| `kid` | Cadeia | Especifica a impressão digital para a chave pública que é usada para assinar este símbolo. Emitidos em fichas de acesso v1.0 e v2.0. |
| `x5t` | Cadeia | Funciona da mesma forma (em uso e valor) que `kid`. `x5t` é uma reivindicação de legado emitida apenas em fichas de acesso v1.0 para fins de compatibilidade. |

### <a name="payload-claims"></a>Declarações de carga

| Afirmação | Formato | Descrição |
|-----|--------|-------------|
| `aud` | Cadeia de caracteres, um URI de ID do aplicativo | Identifica o destinatário pretendido do token. Em fichas id, o público é o ID de aplicação da sua aplicação, atribuído à sua aplicação no portal Azure. A sua aplicação deverá validar este valor e rejeitar o símbolo se o valor não corresponder. |
| `iss` | Cadeia de caracteres, um URI do STS | Identifica o serviço de token de segurança (STS) que constrói e retorna o token e o locatário do Azure AD no qual o usuário foi autenticado. Se o símbolo emitido for um símbolo v2.0 (ver a reivindicação `ver`), o URI terminará em `/v2.0`. O GUID que indica que o usuário é um usuário consumidor de um conta Microsoft é `9188040d-6c67-4c5b-b112-36a304b66dad`. Seu aplicativo deve usar a parte GUID da declaração para restringir o conjunto de locatários que podem entrar no aplicativo, se aplicável. |
|`idp`| Cadeia de caracteres, geralmente um URI do STS | Regista o fornecedor de identidade que autenticou o requerente do token. Esse valor é idêntico ao valor da declaração do emissor, a menos que a conta de usuário não esteja no mesmo locatário que o emissor-Guests, por exemplo. Se a declaração não estiver presente, significa que o valor de `iss` pode ser usado em vez disso.  Para contas pessoais sendo usadas em um contexto organizacional (por exemplo, uma conta pessoal convidada para um locatário do Azure AD), a declaração de `idp` pode ser ' live.com ' ou um URI STS que contenha o `9188040d-6c67-4c5b-b112-36a304b66dad`de locatário conta Microsoft. |  
| `iat` | int, um carimbo de data/hora do UNIX | "Emitido em" indica quando ocorreu a autenticação para esse token. |
| `nbf` | int, um carimbo de data/hora do UNIX | A alegação "nbf" (não antes) identifica o tempo anterior para o qual o JWT não deve ser aceite para processamento. |
| `exp` | int, um carimbo de data/hora do UNIX | A alegação "exp" (tempo de validade) identifica o tempo de validade em ou após o qual o JWT não deve ser aceite para processamento. É importante notar que um recurso pode rejeitar o símbolo antes deste tempo também, como quando é necessária uma alteração na autenticação ou uma revogação simbólica. |
| `aio` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure AD para registrar dados para reutilização de token. Os recursos não devem utilizar esta alegação. |
| `acr` | String, um "0" ou "1" | Só presente em fichas v1.0. A alegação de "Autenticação do contexto". Um valor de "0" indica que a autenticação do utilizador final não satisfazia os requisitos da ISO/IEC 29115. |
| `amr` | Matriz jSON de cordas | Só presente em fichas v1.0. Identifica como o objeto do símbolo foi autenticado. Consulte [a secção de reclamação do Mr.](#the-amr-claim) para mais detalhes. |
| `appid` | Cadeia de caracteres, um GUID | Só presente em fichas v1.0. A identificação da aplicação do cliente usando o símbolo. A aplicação pode funcionar como si ou em nome de um utilizador. O ID da aplicação normalmente representa um objeto de aplicação, mas também pode representar um objeto principal de serviço em Azure AD. |
| `appidacr` | "0", "1", ou "2" | Só presente em fichas v1.0. Indica como o cliente foi autenticado. Para um cliente público, o valor é "0". Se o ID do cliente e o segredo do cliente forem usados, o valor é "1". Se um certificado de cliente foi utilizado para autenticação, o valor é "2". |
| `azp` | Cadeia de caracteres, um GUID | Só presente si em fichas v2.0, um substituto para `appid`. A identificação da aplicação do cliente usando o símbolo. A aplicação pode funcionar como si ou em nome de um utilizador. O ID da aplicação normalmente representa um objeto de aplicação, mas também pode representar um objeto principal de serviço em Azure AD. |
| `azpacr` | "0", "1", ou "2" | Só presente si em fichas v2.0, um substituto para `appidacr`. Indica como o cliente foi autenticado. Para um cliente público, o valor é "0". Se o ID do cliente e o segredo do cliente forem usados, o valor é "1". Se um certificado de cliente foi utilizado para autenticação, o valor é "2". |
| `preferred_username` | Cadeia | O nome de usuário primário que representa o usuário. Pode ser um endereço de email, número de telefone ou um nome de usuário genérico sem um formato especificado. Seu valor é mutável e pode mudar ao longo do tempo. Como é mutável, esse valor não deve ser usado para tomar decisões de autorização.  No entanto, pode ser utilizado para sugestões de nome de utilizador. O âmbito `profile` é necessário para receber esta reclamação. |
| `name` | Cadeia | Fornece um valor legível pelo homem que identifica o objeto do símbolo. O valor não é garantido ser único, é mutável, e é projetado para ser usado apenas para fins de exibição. O âmbito `profile` é necessário para receber esta reclamação. |
| `scp` | String, uma lista de âmbitos separados do espaço | O conjunto de âmbitos expostos pelo seu pedido para o qual a aplicação do cliente solicitou (e recebeu) o consentimento. A sua aplicação deve verificar se estes âmbitos são válidos expostos pela sua aplicação e tomar decisões de autorização com base no valor destes âmbitos. Apenas incluído para [fichas do utilizador](#user-and-application-tokens). |
| `roles` | Conjunto de cordas, uma lista de permissões | O conjunto de permissões expostas pela sua aplicação que o pedido ou utilizador foi autorizado a ligar. Para fichas de [aplicação,](#user-and-application-tokens)esta é utilizada durante o fluxo [de credenciais de cliente](v1-oauth2-client-creds-grant-flow.md) no lugar dos âmbitos do utilizador.  Para os [tokens](#user-and-application-tokens) do utilizador, este é preenchido com as funções a que o utilizador foi atribuído na aplicação-alvo. |
| `wids` | Matriz de GUIDS [RoleTemplateID](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) | Denota as funções atribuídas a este utilizador em todo o número de inquilinos, a partir da secção de funções presentes na página de [funções de administrador](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids).  Esta reclamação é configurada por aplicação, através da propriedade `groupMembershipClaims` do manifesto de [candidatura.](reference-app-manifest.md)  É necessário defini-lo para "All" ou "DirectyRole".  Não pode estar presente em fichas obtidas através do fluxo implícito devido a preocupações de comprimento simbólico. |
| `groups` | Matriz JSON de GUIDs | Fornece iDs de objetoque representam os membros do grupo do sujeito. Estes valores são únicos (ver Id do Objeto) e podem ser utilizados com segurança para gerir o acesso, como a aplicação da autorização para aceder a um recurso. Os grupos incluídos nos grupos alegam que são configurados por aplicação, através da propriedade `groupMembershipClaims` do manifesto de [candidatura.](reference-app-manifest.md) Um valor nulo excluirá todos os grupos, um valor de "SecurityGroup" incluirá apenas membros do Ative Directory Security Group, e um valor de "All" incluirá grupos de segurança e listas de distribuição do Office 365. <br><br>Consulte a reclamação `hasgroups` abaixo para obter detalhes sobre a utilização da reclamação `groups` com a subvenção implícita. <br>Para outros fluxos, se o número de grupos em que o utilizador se encontra for superior a um limite (150 para SAML, 200 para JWT), então será adicionada uma alegação de sobre-sobredição às fontes de reclamação que apontam para o ponto final do Gráfico AAD contendo a lista de grupos para o utilizador. |
| `hasgroups` | Booleano | Se presente, sempre `true`, denotar que o utilizador está em pelo menos um grupo. Utilizado sem `groups` pedido de JWTs em fluxos de subvenção implícitos se os grupos completos alegarem que alargaria o fragmento de URI para além dos limites de comprimento do URL (atualmente 6 ou mais grupos). Indica que o cliente deve utilizar o Gráfico para determinar os grupos do utilizador (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` | Objeto JSON | Para pedidos simbólicos que não sejam limitados de comprimento (ver `hasgroups` acima) mas ainda demasiado grandes para o símbolo, será incluído um link para a lista completa de grupos para o utilizador. Para os JWTs como reivindicação distribuída, para a SAML como uma nova reclamação em vez da reivindicação `groups`. <br><br>**Exemplo JWT Valor:** <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }` |
| `sub` | Cadeia de caracteres, um GUID | A entidade de segurança sobre a qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído ou reutilizado. Pode ser utilizado para efetuar controlos de autorização com segurança, como quando o símbolo é usado para aceder a um recurso, e pode ser usado como chave nas tabelas de bases de dados. Como o assunto está sempre presente nos tokens que a Azure AD emite, recomendamos a utilização deste valor num sistema de autorização geral. O sujeito é, no entanto, um identificador em pares - é exclusivo de um determinado ID de aplicação. Portanto, se um único utilizador assinar em duas aplicações diferentes usando duas iDs de cliente diferentes, essas aplicações receberão dois valores diferentes para a reclamação do assunto. Isto pode ou não ser desejado dependendo da sua arquitetura e requisitos de privacidade. Consulte também a reclamação `oid` (que continua a ser a mesma entre aplicações dentro de um inquilino). |
| `oid` | Cadeia de caracteres, um GUID | O identificador imutável para um objeto na plataforma de identidade da Microsoft, neste caso, uma conta de utilizador. Também pode ser utilizado para efetuar controlos de autorização com segurança e como chave nas tabelas de bases de dados. Essa ID identifica exclusivamente o usuário em aplicativos-dois aplicativos diferentes que se conectam ao mesmo usuário receberão o mesmo valor na declaração de `oid`. Assim, `oid` podem ser usados ao fazer consultas a serviços online da Microsoft, como o Microsoft Graph. O Microsoft Graph devolverá este ID como a propriedade `id` para uma determinada [conta de utilizador](/graph/api/resources/user). Uma vez que o `oid` permite que várias aplicações se relacionem com os utilizadores, o âmbito `profile` é necessário para receber esta reclamação. Note que se um único utilizador existir em vários inquilinos, o utilizador irá conter um ID de objeto diferente em cada inquilino - são considerados contas diferentes, mesmo que o utilizador entre em cada conta com as mesmas credenciais. |
| `tid` | Cadeia de caracteres, um GUID | Representa o inquilino da AD Azure de onde o utilizador é. Para contas corporativas e de estudante, o GUID é a ID de locatário imutável da organização à qual o usuário pertence. Para contas pessoais, o valor é `9188040d-6c67-4c5b-b112-36a304b66dad`. O âmbito `profile` é necessário para receber esta reclamação. |
| `unique_name` | Cadeia | Só presente em fichas v1.0. Fornece um valor legível por humanos que identifica o requerente do token. Este valor não é garantido ser único dentro de um inquilino e deve ser usado apenas para fins de exibição. |
| `uti` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure para revalidar tokens. Os recursos não devem usar esta reivindicação. |
| `rh` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure para revalidar tokens. Os recursos não devem utilizar esta alegação. |
| `ver` | String, `1.0` ou `2.0` | Indica a versão do sinal de acesso. |


> [!NOTE]
> **Reivindicação de excesso de grupos**
>
> Para garantir que o tamanho do token não exceda os limites de tamanho do cabeçalho HTTP, o Azure AD limita o número de ids do objeto que inclui na reivindicação dos grupos. Se um utilizador for membro de mais grupos do que o limite de sobresagem (150 para tokens SAML, 200 para fichas JWT), então a Azure AD não emite a reivindicação dos grupos no símbolo. Em vez disso, inclui uma alegação de excesso de idade no símbolo que indica a aplicação para consultar a API do Gráfico para recuperar a adesão do grupo do utilizador.
  ```csharp
  {
    ...
    "_claim_names": {
     "groups": "src1"
      },
      {
    "_claim_sources": {
      "src1": {
          "endpoint":"[Graph Url to get this user's group membership from]"
          }
         }
       }
    ...
   }
   ```
> Pode utilizar o `BulkCreateGroups.ps1` fornecido na pasta App [Creation Scripts](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims/blob/master/AppCreationScripts/) para ajudar a testar cenários de excesso de idade.

#### <a name="v10-basic-claims"></a>v1.0 reclamações básicas

As seguintes reclamações serão incluídas em fichas v1.0, se aplicável, mas não estão incluídas em fichas v2.0 por padrão. Se estiver a utilizar o v2.0 e precisar de uma destas reclamações, solicite-as com recurso a [reclamações opcionais](active-directory-optional-claims.md).

| Afirmação | Formato | Descrição |
|-----|--------|-------------|
| `ipaddr`| Cadeia | O endereço IP a que o utilizador autenido. |
| `onprem_sid`| String, em [formato SID](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | Nos casos em que o utilizador tenha uma autenticação no local, esta reclamação fornece o seu SID. Pode utilizar `onprem_sid` para autorização em aplicações antigas.|
| `pwd_exp`| int, um carimbo de data/hora do UNIX | Indica quando a palavra-passe do utilizador expira. |
| `pwd_url`| Cadeia | Um URL onde os utilizadores podem ser enviados para redefinir a sua palavra-passe. |
| `in_corp`| boolean | Sinaliza se o cliente está fazendo logon da rede corporativa. Se não estiverem, a reclamação não está incluída. |
| `nickname`| Cadeia | Um nome adicional para o utilizador, separado do primeiro ou do último nome.|
| `family_name` | Cadeia | Fornece o último nome, sobrenome ou nome de família do utilizador, conforme definido no objeto do utilizador. |
| `given_name` | Cadeia | Fornece o primeiro ou o nome do utilizador, conforme definido no objeto utilizador. |
| `upn` | Cadeia | O nome de utilizador do utilizador. Pode ser um número de telefone, endereço de e-mail ou corda não formatada. Só deve ser utilizado para efeitos de exibição e fornecendo indicações de nome de utilizador em cenários de reautenticação. |

#### <a name="the-amr-claim"></a>A reivindicação `amr`

As identidades da Microsoft podem autenticar de diferentes formas, o que pode ser relevante para a sua aplicação. A `amr` alegação é uma matriz que pode conter vários itens, como `["mfa", "rsa", "pwd"]`, para uma autenticação que usou tanto uma palavra-passe como a aplicação Authenticator.

| Valor | Descrição |
|-----|-------------|
| `pwd` | Autenticação de palavra-passe, quer a palavra-passe do Utilizador microsoft quer o segredo do cliente de uma aplicação. |
| `rsa` | A autenticação baseou-se na prova de uma chave RSA, por exemplo, com a [aplicação Microsoft Authenticator](https://aka.ms/AA2kvvu). Isto inclui se a autenticação foi feita por um JWT auto-assinado com um certificado X509 de serviço. |
| `otp` | Senha única usando um e-mail ou uma mensagem de texto. |
| `fed` | Foi utilizada uma afirmação de autenticação federada (como jWT ou SAML). |
| `wia` | Autenticação Integrada do Windows |
| `mfa` | Foi utilizada a autenticação de vários fatores. Quando isto estiver presente, serão também incluídos outros métodos de autenticação. |
| `ngcmfa` | Equivalente a `mfa`, utilizado para o fornecimento de certos tipos de credenciais avançadas. |
| `wiaormfa`| O utilizador utilizou o Windows ou uma credencial MFA para autenticar. |
| `none` | Não foi feita qualquer autenticação. |

## <a name="validating-tokens"></a>Fichas validadoras

Para validar uma id_token ou uma access_token, a sua aplicação deve validar tanto a assinatura do token como as reclamações. Para validar fichas de acesso, a sua aplicação também deve validar o emitente, o público e os tokens de assinatura. Estes precisam de ser validados contra os valores do documento de descoberta OpenID. Por exemplo, a versão independente do inquilino do documento está localizada em [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration).

O middleware Azure AD tem capacidades incorporadas para validar fichas de acesso, e você pode navegar através das [nossas amostras](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) para encontrar um na linguagem da sua escolha. Para obter mais informações sobre como validar explicitamente um símbolo JWT, consulte a amostra manual de [validação JWT](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation).

Fornecemos bibliotecas e amostras de código que mostram como lidar facilmente com validação de fichas. As informações abaixo são fornecidas para aqueles que desejam compreender o processo subjacente. Existem também várias bibliotecas de código aberto de terceiros disponíveis para validação jWT - há pelo menos uma opção para quase todas as plataformas e idiomas lá fora. Para obter mais informações sobre bibliotecas de autenticação Da Azure e amostras de código, consulte as bibliotecas de [autenticação v1.0](active-directory-authentication-libraries.md) e as bibliotecas de [autenticação v2.0.](reference-v2-libraries.md)

### <a name="validating-the-signature"></a>Validação da assinatura

Um JWT contém três segmentos, que são separados pelo carácter `.`. O primeiro segmento é conhecido como o **cabeçalho**, o segundo como o **corpo**, e o terceiro como **assinatura**. O segmento de assinatura pode ser usado para validar a autenticidade do símbolo para que possa ser confiado pela sua app.

Tokens emitidos pela Azure AD são assinados usando algoritmos de encriptação assimétrica padrão da indústria, como RSA 256. O cabeçalho do JWT contém informações sobre o método de chave e encriptação usado para assinar o símbolo:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

A alegação `alg` indica o algoritmo que foi usado para assinar o símbolo, enquanto a alegação `kid` indica a chave pública específica que foi usada para validar o símbolo.

Em qualquer momento, a Azure AD pode assinar um id_token usando qualquer um de um determinado conjunto de pares-chave público-privado. A AD Azure gira o possível conjunto de teclas numa base periódica, pelo que a sua aplicação deve ser escrita para lidar automaticamente com essas alterações de teclas. Uma frequência razoável para verificar se há atualizações das teclas públicas utilizadas pelo Azure AD é a cada 24 horas.

Pode adquirir os dados-chave de assinatura necessários para validar a assinatura utilizando o documento de [metadados OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) localizado em:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Experimente este [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) num browser!

Este documento de metadados:

* É um objeto JSON que contém várias peças de informação úteis, como a localização dos vários pontos finais necessários para a autenticação OpenID Connect.
* Inclui um `jwks_uri`, que dá a localização do conjunto de chaves públicas usadas para assinar fichas. A Chave Web JSON (JWK) localizada no `jwks_uri` contém todas as informações de chave pública em uso nesse momento específico.  O formato JWK é descrito em [RFC 7517](https://tools.ietf.org/html/rfc7517).  A sua aplicação pode utilizar a `kid` reclamação no cabeçalho JWT para selecionar qual a chave pública deste documento foi usada para assinar um símbolo específico. Pode então fazer validação de assinatura utilizando a chave pública correta e o algoritmo indicado.

> [!NOTE]
> O ponto final v1.0 devolve as reclamações de `x5t` e `kid`, enquanto o ponto final v2.0 responde apenas com a reivindicação `kid`. Para a frente, recomendamos que utilize a `kid` alegação para validar o seu símbolo.

Fazer validação de assinaturas está fora do âmbito deste documento - existem muitas bibliotecas de código aberto disponíveis para ajudá-lo a fazê-lo se necessário.  No entanto, a plataforma Microsoft Identity tem uma extensão de assinatura simbólica para os padrões - chaves de assinatura personalizadas.  

Se a sua aplicação tiver chaves de assinatura personalizadas como resultado da utilização da funcionalidade de [mapeamento de reclamações,](active-directory-claims-mapping.md) deve anexar um parâmetro de consulta de `appid` contendo o ID da aplicação para obter um `jwks_uri` apontando para as informações chave de assinatura da sua aplicação, que devem ser usadas para validação. Por exemplo: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` contém um `jwks_uri` de `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

### <a name="claims-based-authorization"></a>Autorização baseada em sinistros

A lógica comercial da sua aplicação ditará este passo, alguns métodos comuns de autorização são definidos abaixo.

* Verifique a `scp` ou `roles` alegação de verificar se todos os âmbitos presentes correspondem aos expostos pela sua API, e permita que o cliente faça a ação solicitada.
* Certifique-se de que o cliente que chama a atenção é autorizado a ligar para a sua API usando a reclamação `appid`.
* Valide o estado de autenticação do cliente que chama o `appidacr` - não deve ser 0 se os clientes públicos não estiverem autorizados a ligar para a sua API.
* Verifique contra uma lista de `nonce` alegações passadas para verificar se o símbolo não está a ser repetido.
* Verifique se o `tid` corresponde a um inquilino que pode ligar para a sua API.
* Utilize a alegação `acr` para verificar se o utilizador realizou MFA. Isto deve ser aplicado utilizando [o Acesso Condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Se solicitou as reclamações `roles` ou `groups` no token de acesso, verifique se o utilizador está no grupo autorizado a fazer esta ação.
  * Para fichas recuperadas usando o fluxo implícito, você provavelmente precisará consultar o [Microsoft Graph](https://developer.microsoft.com/graph/) para estes dados, uma vez que é muitas vezes muito grande para caber no token.

## <a name="user-and-application-tokens"></a>Fichas de utilizador e aplicação

A sua aplicação pode receber fichas em nome de um utilizador (o fluxo habitual) ou diretamente de uma aplicação (através do fluxo de credenciais do [cliente).](v1-oauth2-client-creds-grant-flow.md) Estes tokens apenas por aplicações indicam que esta chamada vem de uma aplicação e não tem um utilizador a apoiá-la. Estas fichas são tratadas em grande parte da mesma forma, com algumas diferenças:

* Os tokens apenas com aplicações não terão uma reclamação `scp`, podendo, em vez disso, ter uma reclamação `roles`. É aqui que será registada a autorização de pedido (em oposição às permissões delegadas). Para mais informações sobre permissões delegadas e de candidatura, consulte permissão e consentimento em [v1.0](v1-permissions-and-consent.md) e [v2.0](v2-permissions-and-consent.md).
* Muitas alegações específicas do homem desaparecerão, como `name` ou `upn`.
* As alegações `sub` e `oid` serão as mesmas. 

## <a name="token-revocation"></a>Revogação simbólica

As fichas de atualização podem ser invalidadas ou revogadas a qualquer momento, por diferentes razões. Estes enquadram-se em duas categorias principais: intervalos e revogações.

### <a name="token-timeouts"></a>Intervalos de tempo de token

* MaxInactiveTime: Se o token de atualização não tiver sido utilizado dentro do tempo ditado pelo MaxInactiveTime, o Token Refresh deixará de ser válido.
* MaxSessionAge: Se maxAgeSessionMultiFactor ou MaxAgeSessionSingleFactor tiverem sido definidos para algo diferente do seu padrão (até revogar), então será necessária uma reautenticação após o tempo definido nos decorridos MaxAgeSession*.
* Exemplos:
  * O inquilino tem um MaxInactiveTime de cinco dias, e o utilizador esteve de férias durante uma semana, pelo que a Azure AD não vê um novo pedido simbólico do utilizador há 7 dias. Da próxima vez que o utilizador pedir um novo símbolo, eles descobrirão que o seu Refresh Token foi revogado, e eles devem introduzir as suas credenciais novamente.
  * Uma aplicação sensível tem um MaxAgeSessionSingleFactor de um dia. Se um utilizador fizer login na segunda-feira, e na terça-feira (depois de decorridos 25 horas), será obrigado a reautenticar.

### <a name="revocation"></a>Revogação

|   | Cookie baseado em palavra-passe | Ficha baseada em palavra-passe | Cookie sem palavra-passe | Ficha não baseada em palavra-passe | Ficha confidencial do cliente |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| A palavra-passe expira | Mantém-se vivo | Mantém-se vivo | Mantém-se vivo | Mantém-se vivo | Mantém-se vivo |
| Palavra-passe alterada pelo utilizador | Revoked | Revoked | Mantém-se vivo | Mantém-se vivo | Mantém-se vivo |
| Utilizador faz SSPR | Revoked | Revoked | Mantém-se vivo | Mantém-se vivo | Mantém-se vivo |
| Administrador repõe palavra-passe | Revoked | Revoked | Mantém-se vivo | Mantém-se vivo | Mantém-se vivo |
| Utilizador revoga as suas fichas de atualização [via PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Revoked | Revoked | Revoked | Revoked | Revoked |
| Administrador revoga todas as fichas de atualização para o inquilino [via PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Revoked | Revoked |Revoked | Revoked | Revoked |
| [Inscrição única](v1-protocols-openid-connect-code.md#single-sign-out) na web | Revoked | Mantém-se vivo | Revoked | Mantém-se vivo | Mantém-se vivo |

> [!NOTE]
> Um login "sem palavra-passe" é aquele em que o utilizador não escreveu uma palavra-passe para obtê-lo. Por exemplo, usar o seu rosto com o Windows Hello, uma chave FIDO2 ou um PIN.
>
> Os Tokens De Atualização Primária (PRT) no Windows 10 são segregados com base na credencial. Por exemplo, o Windows Hello e a password têm os respetivos PRTs, isolados uns dos outros. Quando um utilizador se inscreve com uma credencial Hello (PIN ou biometria) e, em seguida, muda a palavra-passe, a PRT baseada na palavra-passe obtida anteriormente será revogada. Voltar a entrar com uma senha invalida o antigo PRT e pede uma nova.
>
> As fichas de atualização não são invalidadas ou revogadas quando usadas para obter um novo token de acesso e um token refrescante.  

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [`id_tokens` em Azure AD.](id-tokens.md)
* Conheça a permissão e o consentimento em [v1.0](v1-permissions-and-consent.md) e [v2.0](v2-permissions-and-consent.md).
