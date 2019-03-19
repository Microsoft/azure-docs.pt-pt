---
title: Referência de tokens de acesso ao Azure Active Directory | Documentos da Microsoft
description: Saiba mais sobre os tokens de acesso emitidos pelos pontos finais v1.0 e v2.0 do Azure AD.
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
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e960e06cc51cc4540a8360cefe90ce68fc7e1f17
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58009914"
---
# <a name="azure-active-directory-access-tokens"></a>Tokens de acesso do Azure Active Directory

Tokens de acesso permitem que os clientes com segurança chamar APIs protegidas pelo Azure. Os tokens de acesso do Azure Active Directory (Azure AD) são [JWTs](https://tools.ietf.org/html/rfc7519), objetos JSON iniciados pelo Azure de codificada em Base64. Os clientes devem tratar acesso destinam-se os tokens como cadeias de caracteres opacas, como o conteúdo do token para o recurso apenas. Para validação e fins de depuração, os desenvolvedores podem decodificar JWTs através de um site como [jwt.ms](https://jwt.ms). O cliente pode obter um token de acesso de qualquer ponto de extremidade (versão 1.0 ou v2.0) utilizando uma variedade de protocolos.

Quando solicita um token de acesso, o Azure AD devolve também alguns metadados sobre o token de acesso para consumo da sua aplicação. Estas informações incluem a hora de expiração do token de acesso e os âmbitos para a qual é válido. Estes dados permite à aplicação efetuar a colocação em cache inteligente de tokens de acesso sem ter de analisar o token de acesso em si.

Se seu aplicativo é um recurso (web API) que os clientes podem pedir acesso a, tokens de acesso fornecem informações úteis para utilização na autenticação e autorização, tais como o utilizador, cliente, emissor, permissões e muito mais. 

Veja as secções seguintes para saber como um recurso pode validar e utilizar as afirmações dentro de um token de acesso.

> [!NOTE]
> Ao testar a aplicação de cliente com uma conta pessoal (por exemplo, hotmail.com ou outlook.com), pode achar que o token de acesso recebido pelo seu cliente é uma cadeia opaca. Isto acontece porque o recurso que está sendo acessado solicitou herdados MSA (conta Microsoft) pedidos de suporte que são encriptados e não podem ser compreendidos pelo cliente.

## <a name="sample-tokens"></a>Tokens de exemplo

tokens v1.0 e v2.0 ter um aspeto semelhantes e contêm muitas das mesmas declarações. Um exemplo de cada um é fornecido aqui.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd
```

Ver este token v1.0 no [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Ver este token v2.0 no [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Afirmações nos tokens de acesso

JWTs são divididas em três partes:

* **Cabeçalho** -fornece informações sobre como [validar o token](#validating-tokens) incluindo informações sobre o tipo de token e como ele foi assinado.
* **Payload** -contém todos os dados importantes sobre o utilizador ou aplicação que está a tentar chamar o seu serviço.
* **Assinatura** -é o material de raw usado para validar o token.

Cada parte é separado por um período (`.`) e em separado com codificação Base64.

Afirmações estão presentes somente se um valor existe para preenchê-lo. Portanto, a aplicação não deve efetuar uma dependência numa afirmação que estejam presentes. Os exemplos incluem `pwd_exp` (nem todos os inquilinos requer palavras-passe a expirar) ou `family_name` ([credenciais de cliente](v1-oauth2-client-creds-grant-flow.md) fluxos são em nome dos aplicativos, que não têm nomes). Afirmações usadas para validação de token de acesso sempre estará presentes.

> [!NOTE]
> Algumas declarações são usadas para ajudar a proteger os tokens em caso de reutilização do Azure AD. Estas são marcadas como não sendo para consumo público na descrição de como "Opaco". Essas declarações podem, ou podem não aparecer num token e é possível adicionar novas etiquetas sem aviso prévio.

### <a name="header-claims"></a>Afirmações de cabeçalho

|Afirmação | Formato | Descrição |
|--------|--------|-------------|
| `typ` | Cadeia de caracteres - sempre "JWT" | Indica que o token é um JWT.|
| `nonce` | String | Um identificador exclusivo utilizado para proteger contra ataques de repetição de token. O recurso pode gravar este valor para proteger contra repetições. |
| `alg` | String | Indica o algoritmo que foi utilizado para assinar o token, por exemplo, "RS256" |
| `kid` | String | Especifica o thumbprint para a chave pública que é utilizado para assinar este token. Emitida na v1.0 e v2.0 em tokens de acesso. |
| `x5t` | String | Funciona da mesma (em utilização e valor) como `kid`. `x5t` é emitida uma afirmação herdada apenas na v1.0 tokens de acesso para efeitos de compatibilidade. |

### <a name="payload-claims"></a>Afirmações de payload

| Afirmação | Formato | Descrição |
|-----|--------|-------------|
| `aud` | Cadeia de caracteres, um URI de ID de aplicação | Identifica o destinatário do token. Nos tokens de acesso, o público-alvo é a ID da aplicação da sua aplicação, atribuído à sua aplicação no portal do Azure. A aplicação deve validar este valor e rejeitar o token, se o valor não corresponde. |
| `iss` | Cadeia de caracteres, um URI de STS | Identifica o serviço de token de segurança (STS) que constrói e devolve o token e o inquilino do Azure AD em que o utilizador foi autenticado. Se o token emitido é um token de versão 2.0 (consulte a `ver` afirmação), o URI terminará no prazo `/v2.0`. O GUID que indica que o utilizador é um utilizador de consumidor de uma conta Microsoft é `9188040d-6c67-4c5b-b112-36a304b66dad`. A aplicação deve utilizar a parte GUID de afirmação para restringir o conjunto de inquilinos que pode iniciar sessão na aplicação, se aplicável. |
|`idp`| Cadeia de caracteres, normalmente, um URI de STS | Regista o fornecedor de identidade que autenticou o requerente do token. Este valor é idêntico ao valor de afirmação do emissor, a menos que a conta de utilizador não está no mesmo inquilino, como o emissor - os convidados, por exemplo. Se a declaração não estiver presente, significa que o valor de `iss` pode ser utilizado em vez disso.  Para contas pessoais que está a ser utilizadas num contexto organizacional (por exemplo, uma conta pessoal convidado para um inquilino do Azure AD), o `idp` afirmação pode ser 'live.com' ou um URI de STS, que contém o inquilino da conta Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |  
| `iat` | int, um carimbo de UNIX | "Emitidos em" indica quando ocorreu a autenticação para este token. |
| `nbf` | int, um carimbo de UNIX | A afirmação "nbf" (não antes) identifica o tempo antes do qual o JWT não deve ser aceite para processamento. |
| `exp` | int, um carimbo de UNIX | A afirmação "exp" (hora de expiração) identifica a hora de expiração em ou depois que o JWT não deve ser aceite para processamento. É importante observar que um recurso pode rejeitar o token antes desta data, como quando é necessária uma alteração na autenticação ou que foi detetado um token revogado. |
| `acr` | Cadeia de caracteres, um "0" ou "1" | A afirmação de "Classe de contexto de autenticação". Um valor de "0" indica que a autenticação de utilizador final não cumpria os requisitos da norma ISO/IEC 29115. |
| `aio` | Cadeia opaca | Uma afirmação interna utilizada pelo Azure AD para registar dados para reutilização de token. Recursos não devem utilizar esta afirmação. |
| `amr` | Matriz JSON de cadeias de caracteres | Apresente apenas nos tokens de v1.0. Identifica a forma como o assunto do token foi autenticado. Ver [secção de afirmação a amr](#the-amr-claim) para obter mais detalhes. |
| `appid` | Cadeia de caracteres, um GUID | Apresente apenas nos tokens de v1.0. O ID de aplicação do cliente com o token. A aplicação pode agir em nome próprio, ou em nome de um utilizador. O ID da aplicação normalmente representa um objeto de aplicativo, mas também podem representar um objeto principal de serviço no Azure AD. |
| `appidacr` | "0", "1" ou "2" | Apresente apenas nos tokens de v1.0. Indica como o cliente foi autenticado. Para um cliente público, o valor é "0". Se o ID de cliente e segredo do cliente são usados, o valor é "1". Se um certificado de cliente foi utilizado para autenticação, o valor é "2". |
| `azp` | Cadeia de caracteres, um GUID | Apresente apenas nos tokens de v2.0. O ID de aplicação do cliente com o token. A aplicação pode agir em nome próprio, ou em nome de um utilizador. O ID da aplicação normalmente representa um objeto de aplicativo, mas também podem representar um objeto principal de serviço no Azure AD. |
| `azpacr` | "0", "1" ou "2" | Apresente apenas nos tokens de v2.0. Indica como o cliente foi autenticado. Para um cliente público, o valor é "0". Se o ID de cliente e segredo do cliente são usados, o valor é "1". Se um certificado de cliente foi utilizado para autenticação, o valor é "2". |
| `groups` | Matriz JSON de GUIDs | Fornece os IDs de objeto que representam as associações de grupo do requerente. Estes valores são exclusiva (veja a ID de objeto) e podem ser usados com segurança para gerir o acesso, como a imposição de autorização para aceder a um recurso. Os grupos incluídos na afirmação grupos são configurados numa base por aplicação, por meio do `groupMembershipClaims` propriedade do [manifesto do aplicativo](reference-app-manifest.md). Um valor nulo será excluir todos os grupos, um valor de "SecurityGroup" irá incluir apenas as associações de grupo de segurança do Active Directory e um valor de "All" irá incluem grupos de segurança e listas de distribuição do Office 365. <br><br>Consulte a `hasgroups` afirmação abaixo para obter detalhes sobre como utilizar o `groups` a afirmação com a concessão implícita. <br>Para outros fluxos, se o número de grupos que o usuário estiver no é feito por um limite de (150 para SAML, 200 para JWT), em seguida, uma afirmação de utilização excedida será adicionada às origens de afirmação apontando para o ponto de extremidade do gráfico que contém a lista de grupos do utilizador. |
| `hasgroups` | Booleano | Se estiver presente, sempre `true`, que indica o utilizador está em pelo menos um grupo. Utilizado em vez do `groups` afirmação para JWTs nos fluxos de concessão implícita se os grupos de totais de afirmação se estende o fragmento do URI para além dos limites de comprimento de URL (atualmente 6 ou mais grupos). Indica que o cliente deve usar o gráfico para determinar os grupos do utilizador (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` | Objeto JSON | Para pedidos de token que não estão limitado de comprimento (consulte `hasgroups` acima), mas ainda demasiado grande para o token, uma ligação para a lista de grupos de completa para o utilizador serão incluída. Para JWTs como uma afirmação distribuída, para SAML como uma nova afirmação em vez do `groups` de afirmação. <br><br>**Valor de JWT exemplo**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }` |
| `preferred_username` | String | O nome de utilizador principal que represente o usuário. Pode ser um endereço de e-mail, número de telefone ou um nome de utilizador genérica sem um formato especificado. Seu valor é mutável e podem ser alterados ao longo do tempo. Uma vez que é mutável, este valor não pode ser utilizado para tomar decisões de autorização.  Ele pode ser usado para sugestões de nome de utilizador no entanto. O `profile` âmbito é necessário para receber esta afirmação. |
| `name` | String | Fornece um valor legível por humanos que identifica o assunto do token. O valor não é garantido para que seja exclusivo, é mutável e foi concebido para ser utilizado apenas para fins de exibição. O `profile` âmbito é necessário para receber esta afirmação. |
| `oid` | Cadeia de caracteres, um GUID | O identificador imutável para um objeto no Microsoft identity platform, neste caso, uma conta de utilizador. Também pode ser utilizado para realizar verificações de autorização com segurança e como uma chave nas tabelas de base de dados. Este ID identifica exclusivamente o utilizador em todas as aplicações – dois aplicativos diferentes, iniciar sessão no mesmo utilizador irão receber o mesmo valor no `oid` de afirmação. Portanto, `oid` podem ser utilizados quando a tomada de consulta para o Microsoft online services, como o Microsoft Graph. O Microsoft Graph irá devolver este ID como o `id` propriedade para uma determinada conta de utilizador. Uma vez que o `oid` permite que várias aplicações correlacionar os utilizadores, o `profile` âmbito é necessário para receber esta afirmação. Tenha em atenção que, se existir um único utilizador em vários inquilinos, o utilizador irá conter um ID de objeto diferentes em cada inquilino - que são consideradas diferentes contas, mesmo que o usuário faz logon em cada conta com as mesmas credenciais. |
| `rh` | Cadeia opaca | Uma afirmação interna utilizada pelo Azure para revalide tokens. Recursos não devem utilizar esta afirmação. |
| `scp` | Cadeia de caracteres, um espaço separados por lista de âmbitos | O conjunto de âmbitos exposto pela sua aplicação para o qual o aplicativo cliente do pedido (e recebidos) de consentimento. Seu aplicativo deve verificar que esses âmbitos são aqueles válido expostos pela sua aplicação e tomar decisões de autorização com base no valor de nestes âmbitos. Incluído apenas para [tokens de utilizador](#user-and-application-tokens). |
| `roles` | Cadeia de caracteres, um espaço separados por lista de permissões | O conjunto de permissões exposto pela sua aplicação que a aplicação requerente, deu permissão para chamar. Isto é utilizado durante a [credenciais de cliente](v1-oauth2-client-creds-grant-flow.md) fluxo no lugar de âmbitos de utilizador e está presente apenas no [tokens de aplicação](#user-and-application-tokens). |
| `sub` | Cadeia de caracteres, um GUID | O principal sobre o qual o token declara informações, como o utilizador de uma aplicação. Este valor é imutável e não pode ser reatribuído ou reutilizado. Ele pode ser utilizado para realizar verificações de autorização com segurança, por exemplo, quando o token é utilizado para aceder a um recurso e pode ser utilizado como uma chave em tabelas de base de dados. Uma vez que o assunto é sempre presente os tokens que problemas do Azure AD, recomendamos que utilize este valor num sistema de autorização para fins gerais. O assunto é, no entanto, um identificador pairwise - é exclusivo para um ID de aplicação em particular. Por conseguinte, se um único utilizador se inscreve para duas aplicações diferentes com dois IDs de cliente diferentes, essas aplicações irão receber dois valores diferentes para a afirmação do requerente. Isto pode ou não pode ser desejável dependendo dos requisitos de arquitetura e a privacidade. |
| `tid` | Cadeia de caracteres, um GUID | Representa o que o utilizador é de inquilino do Azure AD. Para contas profissionais e escolares, o GUID é o ID de inquilino imutável da organização que o utilizador pertence. Contas pessoais, o valor é `9188040d-6c67-4c5b-b112-36a304b66dad`. O `profile` âmbito é necessário para receber esta afirmação. |
| `unique_name` | String | Apresente apenas nos tokens de v1.0. Fornece um valor legível por humanos que identifica o requerente do token. Este valor não é garantido de ser exclusivo dentro de um inquilino e deve ser utilizado apenas para fins de exibição. |
| `uti` | Cadeia opaca | Uma afirmação interna utilizada pelo Azure para revalide tokens. Recursos não devem utilizar esta afirmação. |
| `ver` | Cadeia de caracteres, optar por `1.0` ou `2.0` | Indica a versão do token de acesso. |

#### <a name="v10-basic-claims"></a>afirmações de básica v1.0

As seguintes declarações serão incluídas na v1.0 tokens se aplicável, mas não estão incluídas nos tokens de v2.0 por predefinição. Se estiver a utilizar a versão 2.0 e precisar de uma dessas declarações, pedi-los usando [afirmações opcionais](active-directory-optional-claims.md).

| Afirmação | Formato | Descrição |
|-----|--------|-------------|
| `ipaddr`| String | O endereço IP, o utilizador autenticado de. |
| `onprem_sid`| Cadeia de caracteres, em [formato do SID](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | Em casos em que o utilizador tem uma autenticação no local, esta afirmação fornece seu SID. Pode usar `onprem_sid` para autorização em aplicativos herdados.|
| `pwd_exp`| int, um carimbo de UNIX | Indica que a senha do usuário expira. |
| `pwd_url`| String | Um URL onde os utilizadores possam ser enviados para repor a palavra-passe. |
| `in_corp`|boolean | Sinais, se o cliente está a iniciar sessão da rede empresarial. Se não forem, a afirmação não está incluída. |
| `nickname`| String | Um nome adicional para o utilizador, separado do primeiro ou último nome.|
| `family_name` | String | Fornece o último nome, sobrenome ou nome de família do utilizador, conforme definido no objeto user. |
| `given_name` | String | Fornece o primeiro ou o nome dado do utilizador, conforme definido no objeto user. |
| `upn` | String | O nome de utilizador do utilizador. Pode ser um número de telefone, endereço de e-mail ou uma cadeia não formatada. Só deve ser utilizada para fins de exibição e fornecer sugestões de nome de utilizador em cenários de reautenticação. |

#### <a name="the-amr-claim"></a>O `amr` de afirmação

As identidades da Microsoft podem autenticar numa variedade de formas, que podem ser relevantes ao seu aplicativo. O `amr` afirmação é uma matriz que pode conter vários itens, tais como `["mfa", "rsa", "pwd"]`, para uma autenticação que utilizou uma palavra-passe e a aplicação de autenticador.

| Value | Descrição |
|-----|-------------|
| `pwd` | Autenticação de palavra-passe, a palavra-passe da Microsoft de um utilizador ou o segredo do cliente de uma aplicação. |
| `rsa` | Autenticação tiveram como base a prova de uma chave RSA, por exemplo com o [aplicação Microsoft Authenticator](https://aka.ms/AA2kvvu). Isto inclui se a autenticação foi realizada por um JWT autoassinado com um serviço pertencentes à empresa X509 certificado. |
| `otp` | O código de acesso através de uma mensagem de e-mail ou uma mensagem de texto único. |
| `fed` | Foi utilizada uma asserção de autenticação federada (como JWT ou SAML). |
| `wia` | Autenticação Integrada do Windows |
| `mfa` | Autenticação multifator foi utilizada. Quando ela estiver presente, os outros métodos de autenticação também serão incluídos. |
| `ngcmfa` | Equivalente a `mfa`, utilizado para o aprovisionamento de certos tipos de credencial avançadas. |
| `wiaormfa`| O utilizador utilizou o Windows ou uma credencial MFA para autenticar. |
| `none` | Sem autenticação foi efetuada. |

## <a name="validating-tokens"></a>Tokens de validação

Para validar uma id_token ou um access_token, a sua aplicação deve validar a assinatura do token e as declarações. Para validar os tokens de acesso, a aplicação também deve validar o emissor, o público-alvo e os tokens de assinatura. Estes têm de ser validadas em relação aos valores no documento de deteção do OpenID. Por exemplo, a versão independente de inquilino do documento está localizada em [ https://login.microsoftonline.com/common/.well-known/openid-configuration ](https://login.microsoftonline.com/common/.well-known/openid-configuration). 

O middleware do Azure AD possui capacidades incorporadas para validar os tokens de acesso, e pode navegar pelos nossos [amostras](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) para encontrar um no idioma da sua preferência. Para obter mais informações sobre como validar explicitamente um token JWT, consulte a [exemplo de validação do JWT manual](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation). 

Fornecemos bibliotecas e exemplos de código que mostram como tratar facilmente a validação do token. As informações abaixo é fornecido para aqueles que desejam compreender o processo de subjacente. Também existem várias bibliotecas de código-fonte aberto de terceiros disponíveis para a validação do JWT – existe, pelo menos, uma opção para quase todas as plataformas e a idiomas por aí. Para obter mais informações sobre bibliotecas de autenticação do Azure AD e exemplos de código, consulte [bibliotecas de autenticação v1.0](active-directory-authentication-libraries.md) e [bibliotecas de autenticação v2.0](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>A validar a assinatura

Um JWT contém três segmentos, que são separados pelo `.` caráter. O primeiro segmento é conhecido como o **cabeçalho**, o segundo como o **corpo**e a terceira, como o **assinatura**. O segmento de assinatura pode ser utilizado para validar a autenticidade do token, para que podem ser fidedignos pela sua aplicação.

Tokens emitidos pelo Azure AD são assinados com algoritmos de criptografia assimétrica padrão do setor, como RSA 256. O cabeçalho do JWT contém informações sobre o método de encriptação e a chave utilizada para assinar o token:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

O `alg` afirmação indica o algoritmo que foi utilizado para assinar o token, enquanto o `kid` afirmação indica a chave pública específica que foi utilizada para assinar o token.

Em qualquer determinada altura, do Azure AD pode iniciar sessão uma id_token usando qualquer um de um determinado conjunto de pares de chaves públicas-privadas. Azure AD gira o conjunto possível de chaves numa base periódica, para que a sua aplicação deve ser escrita para lidar com essas alterações principais automaticamente. Uma frequência razoável para procurar atualizações para as chaves públicas usadas pelo Azure AD é a cada 24 horas.

Pode adquirir os dados de chave assinatura necessários para validar a assinatura usando o documento de metadados OpenID Connect localizado em:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Experimente isto [URL](https://login.microsoftonline.com/common/.well-known/openid-configuration) num navegador!

Este documento de metadados:

* É um objeto JSON que contém várias partes úteis de informações, como a localização dos vários pontos finais necessários para efetuar a autenticação OpenID Connect. 
* Inclui um `jwks_uri`, que dá a localização do conjunto de chaves públicas usadas para assinar os tokens. O documento JSON localizado no `jwks_uri` contém todas as informações de chave públicas utilizada no momento específico. A aplicação pode utilizar o `kid` no cabeçalho do JWT para selecionar qual a chave pública neste documento foi utilizada para assinar um determinado token de afirmação. Em seguida, ele pode executar a validação de assinatura usando a chave pública correta e o algoritmo indicado.

> [!NOTE]
> O ponto de extremidade v1.0 devolve ambos os `x5t` e `kid` afirmações, enquanto o ponto final v2.0 responde com apenas o `kid` de afirmação. Daqui em diante, recomendamos que utilize o `kid` validar o token de afirmação.

Realizar validação de assinatura está fora do escopo deste documento: há muitas bibliotecas de código aberto disponíveis para ajudá-lo a fazê-lo se necessário.

### <a name="claims-based-authorization"></a>Autorização de afirmações com base

Lógica de negócios da sua aplicação vai ditar neste passo, alguns métodos de autorização comuns são apresentados abaixo.

* Verifique os `scp` ou `roles` alegam Certifique-se de que todos os âmbitos presentes correspondem às expostas pela sua API e permitir que o cliente executar a ação solicitada.
* Certifique-se de que o cliente da chamada pode chamar a sua API utilizando o `appid` de afirmação.
* Validar o estado da autenticação do cliente chamado com `appidacr` -não deve ser 0 se os clientes públicos não têm permissão para chamar a API.
* Verificar com uma lista de passado `nonce` afirmações verificar o token não está a ser reproduzido.
* Verifique se o `tid` corresponde a um inquilino que está autorizado a chamar a API.
* Utilize o `acr` de afirmação verificar se o utilizador realizou MFA. Isso deve ser imposto por meio [acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Se pediu a `roles` ou `groups` afirmações no token de acesso, certifique-se de que o utilizador está no grupo de permissão para efetuar esta ação.
  * Para os tokens obtidos com o fluxo implícito, provavelmente terá de consultar o [Microsoft Graph](https://developer.microsoft.com/graph/) para estes dados, como ele costuma ser demasiado grande para caber no token.

## <a name="user-and-application-tokens"></a>Tokens de utilizador e da aplicação

A aplicação pode receber tokens de nome de um utilizador (o fluxo normal) ou diretamente a partir de uma aplicação (através do [fluxo de credenciais de cliente](v1-oauth2-client-creds-grant-flow.md)). Estes tokens só de aplicação indicam que essa chamada é proveniente de um aplicativo e não tem um utilizador cópias. Estes tokens são processados em grande parte da mesma forma, com algumas diferenças:

* Tokens de só de aplicação não terá uma `scp` de afirmação e, em vez disso, terá um `roles` de afirmação. Isso é onde será gravado permissão da aplicação (em oposição às permissões delegadas). Para obter mais informações sobre as permissões delegadas e da aplicação, veja permissões e consentimento no [v1.0](v1-permissions-and-consent.md) e [v2.0](v2-permissions-and-consent.md).
* Muitas declarações de humanos específicas estará em falta, tais como `name`.

## <a name="token-revocation"></a>Revogar token

Atualizar tokens podem ser invalidados ou revogados em qualquer altura, por vários motivos. Elas enquadram-se em duas categorias principais: tempos limite e revogações.

### <a name="token-timeouts"></a>Tempos limite de token

* MaxInactiveTime: Se o token de atualização não foi utilizado no tempo ditado pelo MaxInactiveTime, o Token de atualização já não será válido. 
* MaxSessionAge: Se MaxAgeSessionMultiFactor ou MaxAgeSessionSingleFactor foram definido para algo que não seja o padrão (até-revogados), em seguida, a reautenticação serão necessária após o período de tempo definido no MaxAgeSession *. 
* Exemplos:
  * O inquilino tem uma MaxInactiveTime de 5 dias e o utilizador foi em férias durante uma semana e, então, AAD não teve um novo pedido de token do usuário em 7 dias. Da próxima vez que o usuário solicita um novo token, eles encontrarão seu Token de atualização foi revogado e tem de introduzir as credenciais novamente.
  * Aplicações confidenciais tem um MaxAgeSessionSingleFactor de 1 dia. Se um utilizador inicia sessão na segunda-feira e na Terça-feira (depois de decorridos 25 horas), será necessário para autenticar.

### <a name="revocation"></a>Revogação

|   | Cookies baseada em palavra-passe | Token baseado em palavra-passe | Cookie não-palavra-passe | Token de não-palavra-passe | Token de cliente confidencial |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Palavra-passe expirar | Fique vivo | Fique vivo | Fique vivo | Fique vivo | Fique vivo |
| Palavra-passe alterada pelo utilizador | Revogado | Revogado | Fique vivo | Fique vivo | Fique vivo |
| Utilizador realiza SSPR | Revogado | Revogado | Fique vivo | Fique vivo | Fique vivo |
| Administrador repõe a palavra-passe | Revogado | Revogado | Fique vivo | Fique vivo | Fique vivo |
| Utilizador revoga os tokens de atualização [através do PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Revogado | Revogado | Revogado | Revogado | Revogado |
| Administrador revoga todos os tokens de atualização para o inquilino [através do PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Revogado | Revogado |Revogado | Revogado | Revogado |
| [Fim de sessão único](v1-protocols-openid-connect-code.md#single-sign-out) na web | Revogado | Fique vivo | Revogado | Fique vivo | Fique vivo |

> [!NOTE]
> Um início de sessão "não-palavra-passe com base" é aquele em que o utilizador não escreva uma palavra-passe para obtê-la. Por exemplo, utilizar o rosto com o Windows Hello, uma chave de FIDO ou um PIN. 
>
> Existe um problema conhecido com o Windows primário atualizar Token. Se PRT é obtida por meio de uma palavra-passe e, em seguida, o usuário se conecta por meio de Hello, isso não altera a origem de PRT e vai ser revogada se o utilizador altera a palavra-passe.
>
> Tokens de atualização não são também invalidados ou revogadas quando usado para obter um novo token de acesso e atualizar o token.  

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [ `id_tokens` no Azure AD](id-tokens.md).
* Saiba mais sobre permissões e consentimento no [v1.0](v1-permissions-and-consent.md) e [v2.0](v2-permissions-and-consent.md).
