---
title: Referência de tokens de acesso à plataforma de identidade da Microsoft | Azure
description: Saiba mais sobre tokens de acesso emitidos pelos pontos de extremidade do Azure AD v 1.0 e da plataforma Microsoft Identity (v 2.0).
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39b727d14419634d413e0f649a43d455c4aeba20
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803546"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Tokens de acesso da plataforma Microsoft Identity

Os tokens de acesso permitem que os clientes chamem APIs protegidas pelo Azure com segurança. Os tokens de acesso da plataforma de identidade da Microsoft são [JWTs](https://tools.ietf.org/html/rfc7519), objetos JSON codificados em base64 assinados pelo Azure. Os clientes devem tratar tokens de acesso como cadeias de caracteres opacas, pois o conteúdo do token destina-se apenas ao recurso. Para fins de validação e depuração, os desenvolvedores podem decodificar JWTs usando um site como [JWT.ms](https://jwt.ms). O cliente pode obter um token de acesso do ponto de extremidade v 1.0 ou do ponto de extremidade v 2.0 usando uma variedade de protocolos.

Quando o cliente solicita um token de acesso, o Azure AD também retorna alguns metadados sobre o token de acesso para o consumo do aplicativo. Essas informações incluem o tempo de expiração do token de acesso e os escopos para os quais ele é válido. Esses dados permitem que seu aplicativo faça o cache inteligente de tokens de acesso sem precisar analisar o token de acesso em si.

Se seu aplicativo for um recurso (API Web) ao qual os clientes podem solicitar acesso, os tokens de acesso fornecerão informações úteis para uso em autenticação e autorização, como o usuário, o cliente, o emissor, as permissões e muito mais.

Consulte as seções a seguir para saber como um recurso pode validar e usar as declarações dentro de um token de acesso.

> [!IMPORTANT]
> Os tokens de acesso são criados com base no *público* do token, o que significa que o aplicativo que possui os escopos no token.  É assim que uma configuração de recurso `accessTokenAcceptedVersion` no [manifesto do aplicativo](reference-app-manifest.md#manifest-reference) para `2` permite que um cliente chamando o ponto de extremidade v 1.0 receba um token de acesso v 2.0.  Da mesma forma, é por isso que alterar as [declarações opcionais](active-directory-optional-claims.md) do token de acesso para o cliente não altera o token de acesso recebido quando um token é solicitado para `user.read`, que pertence ao recurso MS Graph.  
> Pelo mesmo motivo, ao testar o aplicativo cliente com uma conta pessoal (como hotmail.com ou outlook.com), você pode descobrir que o token de acesso recebido pelo seu cliente é uma cadeia de caracteres opaca. Isso ocorre porque o recurso que está sendo acessado solicitou tíquetes MSA (conta Microsoft) herdados que são criptografados e não podem ser compreendidos pelo cliente.

## <a name="sample-tokens"></a>Tokens de exemplo

os tokens v 1.0 e v 2.0 parecem semelhantes e contêm muitas das mesmas declarações. Um exemplo de cada um é fornecido aqui.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Exibir este token v 1.0 em [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Exibir este token v 2.0 em [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Declarações em tokens de acesso

JWTs são divididos em três partes:

* **Cabeçalho** -fornece informações sobre como [validar o token](#validating-tokens) , incluindo informações sobre o tipo de token e como ele foi assinado.
* **Payload** -contém todos os dados importantes sobre o usuário ou aplicativo que está tentando chamar seu serviço.
* **Assinatura** – o material bruto usado para validar o token.

Cada parte é separada por um ponto (`.`) e codificado em base64 separadamente.

As declarações estarão presentes somente se houver um valor para preenchê-lo. Portanto, seu aplicativo não deve assumir uma dependência de uma declaração estar presente. Os exemplos incluem `pwd_exp` (nem todos os locatários exigem que as senhas expirem) ou `family_name` (os fluxos de[credencial do cliente](v1-oauth2-client-creds-grant-flow.md) estão em nome dos aplicativos, que não têm nomes). As declarações usadas para validação de token de acesso sempre estarão presentes.

> [!NOTE]
> Algumas declarações são usadas para ajudar os tokens de segurança do Azure AD no caso de reutilização. Eles são marcados como não sendo para consumo público na descrição como "opaco". Essas declarações podem ou não aparecer em um token, e novas podem ser adicionadas sem aviso prévio.

### <a name="header-claims"></a>Declarações de cabeçalho

|Afirmação | Formato | Descrição |
|--------|--------|-------------|
| `typ` | Cadeia de caracteres – sempre "JWT" | Indica que o token é um JWT.|
| `nonce` | String | Um identificador exclusivo usado para proteger contra ataques de reprodução de token. O recurso pode registrar esse valor para proteger contra repetições. |
| `alg` | String | Indica o algoritmo que foi usado para assinar o token, por exemplo, "RS256" |
| `kid` | String | Especifica a impressão digital para a chave pública que é usada para assinar esse token. Emitido em tokens de acesso v 1.0 e v 2.0. |
| `x5t` | String | Funções iguais (em uso e valor) como `kid`. `x5t` é uma declaração herdada emitida somente em tokens de acesso v 1.0 para fins de compatibilidade. |

### <a name="payload-claims"></a>Declarações de carga

| Afirmação | Formato | Descrição |
|-----|--------|-------------|
| `aud` | Cadeia de caracteres, um URI de ID do aplicativo | Identifica o destinatário pretendido do token. Em tokens de ID, o público é a ID do aplicativo do aplicativo, atribuído ao seu aplicativo no portal do Azure. Seu aplicativo deve validar esse valor e rejeitar o token se o valor não corresponder. |
| `iss` | Cadeia de caracteres, um URI do STS | Identifica o serviço de token de segurança (STS) que constrói e retorna o token e o locatário do Azure AD no qual o usuário foi autenticado. Se o token emitido for um token v 2.0 (consulte a declaração `ver`), o URI terminará em `/v2.0`. O GUID que indica que o usuário é um usuário consumidor de um conta Microsoft é `9188040d-6c67-4c5b-b112-36a304b66dad`. Seu aplicativo deve usar a parte GUID da declaração para restringir o conjunto de locatários que podem entrar no aplicativo, se aplicável. |
|`idp`| Cadeia de caracteres, geralmente um URI do STS | Regista o fornecedor de identidade que autenticou o requerente do token. Esse valor é idêntico ao valor da declaração do emissor, a menos que a conta de usuário não esteja no mesmo locatário que o emissor-Guests, por exemplo. Se a declaração não estiver presente, significa que o valor de `iss` pode ser usado em vez disso.  Para contas pessoais sendo usadas em um contexto organizacional (por exemplo, uma conta pessoal convidada para um locatário do Azure AD), a declaração `idp` pode ser ' live.com ' ou um URI STS que contenha o locatário conta Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |  
| `iat` | int, um carimbo de data/hora do UNIX | "Emitido em" indica quando ocorreu a autenticação para esse token. |
| `nbf` | int, um carimbo de data/hora do UNIX | A declaração "NBF" (não antes) identifica o tempo antes do qual o JWT não deve ser aceito para processamento. |
| `exp` | int, um carimbo de data/hora do UNIX | A declaração "exp" (tempo de expiração) identifica o tempo de expiração em ou após o qual o JWT não deve ser aceito para processamento. É importante observar que um recurso pode rejeitar o token antes dessa hora também, como quando uma alteração na autenticação é necessária ou uma revogação de token foi detectada. |
| `aio` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure AD para registrar dados para reutilização de token. Os recursos não devem usar essa declaração. |
| `acr` | Cadeia de caracteres, um "0" ou "1" | Presente apenas em tokens v 1.0. A declaração "classe de contexto de autenticação". Um valor de "0" indica que a autenticação do usuário final não atendeu aos requisitos do ISO/IEC 29115. |
| `amr` | Matriz JSON de cadeias de caracteres | Presente apenas em tokens v 1.0. Identifica como o assunto do token foi autenticado. Consulte [a seção declaração Amr](#the-amr-claim) para obter mais detalhes. |
| `appid` | Cadeia de caracteres, um GUID | Presente apenas em tokens v 1.0. A ID do aplicativo do cliente usando o token. O aplicativo pode atuar como ele mesmo ou em nome de um usuário. A ID do aplicativo normalmente representa um objeto de aplicativo, mas também pode representar um objeto de entidade de serviço no Azure AD. |
| `appidacr` | "0", "1" ou "2" | Presente apenas em tokens v 1.0. Indica como o cliente foi autenticado. Para um cliente público, o valor é "0". Se a ID do cliente e o segredo do cliente forem usados, o valor será "1". Se um certificado de cliente foi usado para autenticação, o valor será "2". |
| `azp` | Cadeia de caracteres, um GUID | Somente presentes em tokens v 2.0, uma substituição para `appid`. A ID do aplicativo do cliente usando o token. O aplicativo pode atuar como ele mesmo ou em nome de um usuário. A ID do aplicativo normalmente representa um objeto de aplicativo, mas também pode representar um objeto de entidade de serviço no Azure AD. |
| `azpacr` | "0", "1" ou "2" | Somente presentes em tokens v 2.0, uma substituição para `appidacr`. Indica como o cliente foi autenticado. Para um cliente público, o valor é "0". Se a ID do cliente e o segredo do cliente forem usados, o valor será "1". Se um certificado de cliente foi usado para autenticação, o valor será "2". |
| `preferred_username` | String | O nome de usuário primário que representa o usuário. Pode ser um endereço de email, número de telefone ou um nome de usuário genérico sem um formato especificado. Seu valor é mutável e pode mudar ao longo do tempo. Como é mutável, esse valor não deve ser usado para tomar decisões de autorização.  No entanto, ele pode ser usado para dicas de nome de usuário. O escopo `profile` é necessário para receber essa declaração. |
| `name` | String | Fornece um valor legível que identifica o assunto do token. Não há garantia de que o valor seja exclusivo, ele é mutável e foi projetado para ser usado somente para fins de exibição. O escopo `profile` é necessário para receber essa declaração. |
| `scp` | Cadeia de caracteres, uma lista de escopos separados por espaço | O conjunto de escopos expostos por seu aplicativo para o qual o aplicativo cliente solicitou (e recebeu) consentimento. Seu aplicativo deve verificar se esses escopos são válidos expostos por seu aplicativo e tomar decisões de autorização com base no valor desses escopos. Incluído apenas para [tokens de usuário](#user-and-application-tokens). |
| `roles` | Matriz de cadeias de caracteres, uma lista de permissões | O conjunto de permissões expostas por seu aplicativo que o aplicativo ou usuário solicitante recebeu permissão para chamar. Para [tokens de aplicativo](#user-and-application-tokens), isso é usado durante o fluxo de [credenciais do cliente](v1-oauth2-client-creds-grant-flow.md) no lugar de escopos do usuário.  Para [tokens de usuário](#user-and-application-tokens) , isso é populado com as funções às quais o usuário foi atribuído no aplicativo de destino. |
| `wids` | Matriz de GUIDs [RoleTemplateID](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) | Denota as funções de todo o locatário atribuídas a esse usuário, da seção de funções presentes na [página funções de administrador](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids).  Essa declaração é configurada em uma base por aplicativo, por meio da propriedade `groupMembershipClaims` do [manifesto do aplicativo](reference-app-manifest.md).  Configurá-lo como "All" ou "DirectoryRole" é necessário.  Pode não estar presente em tokens obtidos por meio do fluxo implícito devido a problemas de comprimento do token. |
| `groups` | Matriz JSON de GUIDs | Fornece IDs de objeto que representam as associações de grupo da entidade. Esses valores são exclusivos (consulte a ID do objeto) e podem ser usados com segurança para gerenciar o acesso, como impor a autorização para acessar um recurso. Os grupos incluídos na declaração grupos são configurados em uma base por aplicativo, por meio da propriedade `groupMembershipClaims` do [manifesto do aplicativo](reference-app-manifest.md). Um valor de NULL excluirá todos os grupos, um valor de "grupo de segurança" incluirá apenas Active Directory associações de grupos de segurança, e um valor de "All" incluirá grupos de segurança e listas de distribuição do Office 365. <br><br>Consulte a declaração `hasgroups` abaixo para obter detalhes sobre como usar a declaração `groups` com a concessão implícita. <br>Para outros fluxos, se o número de grupos em que o usuário está ultrapassar um limite (150 para SAML, 200 para JWT), uma declaração excedente será adicionada às fontes de declaração que apontam para o ponto de extremidade do AAD Graph que contém a lista de grupos para o usuário. |
| `hasgroups` | Booleano | Se estiver presente, sempre `true`, denotando que o usuário está em pelo menos um grupo. Usado no lugar da declaração `groups` para JWTs em fluxos de concessão implícitas se a declaração de grupos completos estender o fragmento de URI além dos limites de comprimento da URL (atualmente, 6 ou mais grupos). Indica que o cliente deve usar o grafo para determinar os grupos do usuário (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` | Objeto JSON | Para solicitações de token que não têm comprimento limitado (consulte `hasgroups` acima), mas que ainda são muito grandes para o token, um link para a lista de grupos completos do usuário será incluído. Para JWTs como uma declaração distribuída, para SAML como uma nova declaração no lugar da declaração `groups`. <br><br>**Valor de exemplo de JWT**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }` |
| `sub` | Cadeia de caracteres, um GUID | A entidade de segurança sobre a qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído ou reutilizado. Ele pode ser usado para executar verificações de autorização com segurança, como quando o token é usado para acessar um recurso e pode ser usado como uma chave em tabelas de banco de dados. Como o assunto está sempre presente nos tokens que o Azure AD emite, é recomendável usar esse valor em um sistema de autorização de uso geral. O assunto é, no entanto, um identificador emparelhado-ele é exclusivo para uma ID de aplicativo específica. Portanto, se um único usuário entrar em dois aplicativos diferentes usando duas IDs de cliente diferentes, esses aplicativos receberão dois valores diferentes para a declaração de assunto. Isso pode ou não ser desejado dependendo dos requisitos de arquitetura e privacidade. Consulte também a declaração `oid` (que permanece a mesma em aplicativos dentro de um locatário). |
| `oid` | Cadeia de caracteres, um GUID | O identificador imutável de um objeto na plataforma de identidade da Microsoft, nesse caso, uma conta de usuário. Ele também pode ser usado para executar verificações de autorização com segurança e como uma chave em tabelas de banco de dados. Essa ID identifica exclusivamente o usuário em aplicativos-dois aplicativos diferentes que se conectam ao mesmo usuário receberão o mesmo valor na declaração `oid`. Assim, `oid` pode ser usado ao fazer consultas ao Microsoft serviços online, como o Microsoft Graph. O Microsoft Graph retornará essa ID como a propriedade `id` para uma determinada [conta de usuário](/graph/api/resources/user). Como o `oid` permite que vários aplicativos correlacionem os usuários, o escopo de `profile` é necessário para receber essa declaração. Observe que, se um único usuário existir em vários locatários, o usuário conterá uma ID de objeto diferente em cada locatário-eles serão considerados contas diferentes, mesmo que o usuário faça logon em cada conta com as mesmas credenciais. |
| `tid` | Cadeia de caracteres, um GUID | Representa o locatário do Azure AD do qual o usuário é. Para contas corporativas e de estudante, o GUID é a ID de locatário imutável da organização à qual o usuário pertence. Para contas pessoais, o valor é `9188040d-6c67-4c5b-b112-36a304b66dad`. O escopo `profile` é necessário para receber essa declaração. |
| `unique_name` | String | Presente apenas em tokens v 1.0. Fornece um valor legível por humanos que identifica o requerente do token. Não há garantia de que esse valor seja exclusivo dentro de um locatário e deve ser usado somente para fins de exibição. |
| `uti` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure para revalidar tokens. Os recursos não devem usar essa declaração. |
| `rh` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure para revalidar tokens. Os recursos não devem usar essa declaração. |
| `ver` | Cadeia de caracteres, `1.0` ou `2.0` | Indica a versão do token de acesso. |


> [!NOTE]
> **Declaração de excedente de grupos**
>
> Para garantir que o tamanho do token não exceda os limites de tamanho do cabeçalho HTTP, o Azure AD limita o número de IDs de objeto que ele inclui na declaração de grupos. Se um usuário for membro de mais grupos do que o limite excedente (150 para tokens SAML, 200 para tokens JWT), o Azure AD não emitirá a declaração de grupos no token. Em vez disso, ele inclui uma declaração excedente no token que indica ao aplicativo consultar o API do Graph para recuperar a associação de grupo do usuário.
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
> Você pode usar o `BulkCreateGroups.ps1` fornecido na pasta [scripts de criação de aplicativo](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims/blob/master/AppCreationScripts/) para ajudar a testar cenários excedentes.

#### <a name="v10-basic-claims"></a>declarações básicas do v 1.0

As declarações a seguir serão incluídas em tokens v 1.0, se aplicável, mas não são incluídas em tokens v 2.0 por padrão. Se você estiver usando a v 2.0 e precisar de uma dessas declarações, solicite-as usando [declarações opcionais](active-directory-optional-claims.md).

| Afirmação | Formato | Descrição |
|-----|--------|-------------|
| `ipaddr`| String | O endereço IP do qual o usuário se autenticou. |
| `onprem_sid`| Cadeia de caracteres, no [formato Sid](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | Nos casos em que o usuário tem uma autenticação local, essa declaração fornece seu SID. Você pode usar `onprem_sid` para autorização em aplicativos herdados.|
| `pwd_exp`| int, um carimbo de data/hora do UNIX | Indica quando a senha do usuário expira. |
| `pwd_url`| String | Uma URL onde os usuários podem ser enviados para redefinir sua senha. |
| `in_corp`| boolean | Sinaliza se o cliente está fazendo logon da rede corporativa. Se não forem, a declaração não será incluída. |
| `nickname`| String | Um nome adicional para o usuário, separado do nome ou sobrenome.|
| `family_name` | String | Fornece o sobrenome, sobrenome ou nome de família do usuário, conforme definido no objeto de usuário. |
| `given_name` | String | Fornece o primeiro ou o nome do usuário fornecido, conforme definido no objeto de usuário. |
| `upn` | String | O nome do usuário. Pode ser um número de telefone, endereço de email ou cadeia de caracteres não formatada. Só deve ser usado para fins de exibição e fornecer dicas de nome de usuário em cenários de reautenticação. |

#### <a name="the-amr-claim"></a>A declaração `amr`

As identidades da Microsoft podem ser autenticadas de maneiras diferentes, o que pode ser relevante para seu aplicativo. A declaração de `amr` é uma matriz que pode conter vários itens, como `["mfa", "rsa", "pwd"]`, para uma autenticação que usou uma senha e o aplicativo autenticador.

| Valor | Descrição |
|-----|-------------|
| `pwd` | Autenticação de senha, a senha da Microsoft de um usuário ou o segredo do cliente de um aplicativo. |
| `rsa` | A autenticação foi baseada na prova de uma chave RSA, por exemplo, com o [aplicativo Microsoft Authenticator](https://aka.ms/AA2kvvu). Isso inclui se a autenticação foi feita por um JWT autoassinado com um certificado X509 de Propriedade do serviço. |
| `otp` | Senha de uso único usando um email ou uma mensagem de texto. |
| `fed` | Uma asserção de autenticação federada (como JWT ou SAML) foi usada. |
| `wia` | Autenticação integrada do Windows |
| `mfa` | A autenticação multifator foi usada. Quando isso estiver presente, os outros métodos de autenticação também serão incluídos. |
| `ngcmfa` | Equivalente a `mfa`, usado para provisionamento de determinados tipos de credencial avançada. |
| `wiaormfa`| O usuário usou o Windows ou uma credencial MFA para autenticar. |
| `none` | Nenhuma autenticação foi feita. |

## <a name="validating-tokens"></a>Validando tokens

Para validar um id_token ou um access_token, seu aplicativo deve validar a assinatura do token e as declarações. Para validar tokens de acesso, seu aplicativo também deve validar o emissor, o público e os tokens de assinatura. Eles precisam ser validados em relação aos valores no documento de descoberta de OpenID. Por exemplo, a versão independente de locatário do documento está localizada em [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration).

O middleware do Azure AD tem recursos internos para validar tokens de acesso e você pode navegar pelos nossos [exemplos](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) para encontrar um no idioma de sua escolha. Para obter mais informações sobre como validar explicitamente um token JWT, consulte o [exemplo de validação de JWT manual](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation).

Fornecemos bibliotecas e exemplos de código que mostram como lidar facilmente com a validação de token. As informações abaixo são fornecidas para aqueles que desejam entender o processo subjacente. Há também várias bibliotecas de software livre de terceiros disponíveis para validação de JWT – há pelo menos uma opção para quase todas as plataformas e idiomas. Para obter mais informações sobre as bibliotecas de autenticação e exemplos de código do Azure AD, consulte bibliotecas de autenticação [v 1.0](active-directory-authentication-libraries.md) e [bibliotecas de autenticação v 2.0](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Validando a assinatura

Um JWT contém três segmentos, que são separados pelo caractere `.`. O primeiro segmento é conhecido como o **cabeçalho**, o segundo como o **corpo**e o terceiro como a **assinatura**. O segmento de assinatura pode ser usado para validar a autenticidade do token para que ele possa ser confiável para seu aplicativo.

Tokens emitidos pelo Azure AD são assinados usando algoritmos de criptografia assimétrica padrão do setor, como o RSA 256. O cabeçalho do JWT contém informações sobre a chave e o método de criptografia usados para assinar o token:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

A declaração `alg` indica o algoritmo que foi usado para assinar o token, enquanto a declaração `kid` indica a chave pública específica que foi usada para validar o token.

Em qualquer momento determinado, o Azure AD pode assinar um id_token usando qualquer um determinado conjunto de pares de chaves públicas-privadas. O Azure AD gira o possível conjunto de chaves em uma base periódica, de modo que seu aplicativo deve ser escrito para tratar essas alterações de chave automaticamente. Uma frequência razoável para verificar se há atualizações para as chaves públicas usadas pelo Azure AD é a cada 24 horas.

Você pode adquirir os dados de chave de assinatura necessários para validar a assinatura usando o [documento de metadados do OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) localizado em:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Experimente esta [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) em um navegador!

Este documento de metadados:

* É um objeto JSON que contém várias informações úteis, como o local dos vários pontos de extremidade necessários para fazer a autenticação do OpenID Connect.
* Inclui um `jwks_uri`, que fornece o local do conjunto de chaves públicas usadas para assinar tokens. A chave da Web JSON (JWK) localizada na `jwks_uri` contém todas as informações de chave pública em uso naquele momento específico.  O formato JWK é descrito em [RFC 7517](https://tools.ietf.org/html/rfc7517).  Seu aplicativo pode usar a declaração `kid` no cabeçalho JWT para selecionar qual chave pública neste documento foi usada para assinar um token específico. Em seguida, ele pode fazer a validação de assinatura usando a chave pública correta e o algoritmo indicado.

> [!NOTE]
> O ponto de extremidade v 1.0 retorna as declarações `x5t` e `kid`, enquanto o ponto de extremidade v 2.0 responde apenas à declaração `kid`. No futuro, é recomendável usar a declaração `kid` para validar seu token.

A validação da assinatura está fora do escopo deste documento. há muitas bibliotecas de software livre disponíveis para ajudá-lo a fazer isso, se necessário.  No entanto, a plataforma de identidade da Microsoft tem uma extensão de assinatura de token para os padrões-chaves de assinatura personalizadas.  

Se seu aplicativo tiver chaves de assinatura personalizadas como resultado do uso do recurso de [mapeamento de declarações](active-directory-claims-mapping.md) , você deverá acrescentar um `appid` parâmetro de consulta contendo a ID do aplicativo para obter uma `jwks_uri` apontando para as informações de chave de assinatura do aplicativo, que devem ser usadas para validação. Por exemplo: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` contém uma `jwks_uri` de `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

### <a name="claims-based-authorization"></a>Autorização baseada em declarações

A lógica de negócios do seu aplicativo determinará essa etapa, alguns métodos de autorização comuns são dispostos abaixo.

* Verifique a declaração `scp` ou `roles` para verificar se todos os escopos presentes correspondem aos expostos por sua API e permitir que o cliente faça a ação solicitada.
* Verifique se o cliente de chamada tem permissão para chamar sua API usando a declaração `appid`.
* Valide o status de autenticação do cliente de chamada usando `appidacr`-ele não deverá ser 0 se os clientes públicos não tiverem permissão para chamar sua API.
* Verifique em uma lista de declarações anteriores `nonce` para verificar se o token não está sendo repetido.
* Verifique se o `tid` corresponde a um locatário que tem permissão para chamar sua API.
* Use a declaração `acr` para verificar se o usuário executou a MFA. Isso deve ser imposto usando o [acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Se você solicitou as declarações `roles` ou `groups` no token de acesso, verifique se o usuário está no grupo com permissão para executar esta ação.
  * Para tokens recuperados usando o fluxo implícito, provavelmente você precisará consultar o [Microsoft Graph](https://developer.microsoft.com/graph/) para esses dados, pois geralmente é grande demais para caber no token.

## <a name="user-and-application-tokens"></a>Tokens de usuário e aplicativo

Seu aplicativo pode receber tokens em nome de um usuário (o fluxo normal) ou diretamente de um aplicativo (por meio do [fluxo de credenciais do cliente](v1-oauth2-client-creds-grant-flow.md)). Esses tokens somente de aplicativo indicam que essa chamada é proveniente de um aplicativo e não tem um usuário que o faz. Esses tokens são tratados em grande parte, com algumas diferenças:

* Tokens somente de aplicativo não terão uma declaração `scp` e, em vez disso, poderão ter uma declaração de `roles`. É aí que a permissão de aplicativo (em oposição às permissões delegadas) será registrada. Para obter mais informações sobre permissões delegadas e de aplicativo, consulte permissão e consentimento em [v 1.0](v1-permissions-and-consent.md) e [v 2.0](v2-permissions-and-consent.md).
* Muitas declarações específicas do humano estarão ausentes, como `name` ou `upn`.
* As declarações `sub` e `oid` serão as mesmas. 

## <a name="token-revocation"></a>Revogação de token

Os tokens de atualização podem ser invalidados ou revogados a qualquer momento, por diferentes motivos. Elas se enquadram em duas categorias principais: tempos limite e revogações.

### <a name="token-timeouts"></a>Tempos limite de token

* MaxInactiveTime: se o token de atualização não tiver sido usado dentro do tempo determinado pelo MaxInactiveTime, o token de atualização não será mais válido.
* MaxSessionAge: se MaxAgeSessionMultiFactor ou MaxAgeSessionSingleFactor tiver sido definido como algo diferente de seu padrão (até revogado), a reautenticação será necessária depois que o tempo definido em MaxAgeSession * for decorrido.
* Exemplos:
  * O locatário tem um MaxInactiveTime de cinco dias e o usuário entrou em férias por uma semana e, portanto, o Azure AD não viu uma nova solicitação de token do usuário em 7 dias. Na próxima vez que o usuário solicitar um novo token, ele encontrará seu token de atualização foi revogado e deverá inserir suas credenciais novamente.
  * Um aplicativo confidencial tem um MaxAgeSessionSingleFactor de um dia. Se um usuário fizer logon na segunda-feira e na terça-feira (depois que 25 horas tiverem decorrido), será necessário autenticar novamente.

### <a name="revocation"></a>Revogação

|   | Cookie baseado em senha | Token baseado em senha | Cookie não baseado em senha | Token não baseado em senha | Token de cliente confidencial |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| A senha expira | Permanece ativo | Permanece ativo | Permanece ativo | Permanece ativo | Permanece ativo |
| Senha alterada pelo usuário | Revogado | Revogado | Permanece ativo | Permanece ativo | Permanece ativo |
| O usuário SSPR | Revogado | Revogado | Permanece ativo | Permanece ativo | Permanece ativo |
| Administrador redefine a senha | Revogado | Revogado | Permanece ativo | Permanece ativo | Permanece ativo |
| O usuário revoga seus tokens de atualização [por meio do PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Revogado | Revogado | Revogado | Revogado | Revogado |
| O administrador revoga todos os tokens de atualização para o locatário [por meio do PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Revogado | Revogado |Revogado | Revogado | Revogado |
| [Saída única](v1-protocols-openid-connect-code.md#single-sign-out) na Web | Revogado | Permanece ativo | Revogado | Permanece ativo | Permanece ativo |

> [!NOTE]
> Um logon "não baseado em senha" é aquele em que o usuário não digitou uma senha para obtê-lo. Por exemplo, usando seu rosto com o Windows Hello, uma chave FIDO ou um PIN.
>
> Existe um problema conhecido com o token de atualização principal do Windows. Se o PRT for obtido por meio de uma senha e, em seguida, o usuário fizer logon por meio de Olá, isso não alterará a origem do PRT e ele será revogado se o usuário alterar sua senha.
>
> Os tokens de atualização não são invalidados ou revogados quando usados para buscar um novo token de acesso e um token de atualização.  

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [`id_tokens` no Azure ad](id-tokens.md).
* Saiba mais sobre permissão e consentimento em [v 1.0](v1-permissions-and-consent.md) e [v 2.0](v2-permissions-and-consent.md).
