---
title: Autenticação de serviço a serviço do Active Directory do Azure que utiliza o OAuth2.0 especificação em-nome-de rascunho | Documentos da Microsoft
description: Este artigo descreve como utilizar mensagens HTTP para implementar a autenticação serviço a serviço com o OAuth2.0 fluxo em-nome-de.
services: active-directory
documentationcenter: .net
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: celested
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53f8ec8a6833446663d7f142deefd595eed13136
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116270"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Serviço a serviço chama essa identidade de utilizador delegada de utilização no fluxo em-nome-de

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

O fluxo do OAuth 2.0 On-Behalf-Of (OBO) permite que um aplicativo que invoca um serviço ou a API para passar a autenticação de utilizador para outro serviço web ou web API. O fluxo OBO propaga a identidade de utilizador delegado e permissões através da cadeia de pedido. Para o serviço de camada intermediária fazer pedidos autenticados para o serviço downstream, ele tem de proteger um token de acesso do Azure Active Directory (Azure AD) em nome do utilizador.

> [!IMPORTANT]
> A partir de Maio de 2018, um `id_token` não pode ser utilizado para o fluxo em-nome-de.  Aplicações de página única (SPAs) tem de passar um token de acesso a um cliente confidencial da camada intermediária para executar fluxos OBO. Para obter mais detalhes sobre os clientes que pode realizar em-nome-de chamadas, consulte [limitações](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>Diagrama de fluxo em-nome-de

O fluxo OBO começa depois do utilizador ser autenticado num aplicativo que utiliza a [fluxo de concessão de código de autorização de OAuth 2.0](v1-protocols-oauth-code.md). Nesse ponto, a aplicação envia um token de acesso (token A) para a web API (API A) de camada intermediária que contêm afirmações do utilizador e o consentimento para aceder a API. Em seguida, API A faz um pedido autenticado para o web API (API B) a jusante.

Estes passos constituem o fluxo em-nome-de: ![OAuth2.0 em-nome-de fluxo](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. A aplicação cliente faz um pedido para API A com o token de r.
1. API A se autentica o ponto de final de emissão de token do Azure AD e solicita um token para aceder a b de API.
1. O ponto de final de emissão de token do Azure AD valida as credenciais de uma API com um token e emite o token de acesso para a API B (token B).
1. O pedido para o B de API contém B token no cabeçalho de autorização.
1. B de API devolve dados a partir do recurso protegido.

>[!NOTE]
>A afirmação de audiência num token de acesso utilizado para pedir um token para um serviço downstream deve ser o ID do serviço que efetua o pedido OBO. O token também deve ser assinado com a chave de assinatura global do Azure Active Directory (que é o padrão para aplicativos registrados **registos das aplicações** no portal).

## <a name="register-the-application-and-service-in-azure-ad"></a>Registar a aplicação e o serviço no Azure AD

Registe o serviço de camada intermediária e o aplicativo de cliente no Azure AD.

### <a name="register-the-middle-tier-service"></a>Registre-se o serviço de camada intermediária

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na barra superior, selecione a sua conta e procure o **Directory** lista para selecionar um inquilino do Active Directory para a sua aplicação.
1. Selecione **mais serviços** no painel da esquerda e escolha **Azure Active Directory**.
1. Selecione **registos de aplicações** e, em seguida **novo registo de aplicação**.
1. Introduza um nome amigável para a aplicação e selecione o tipo de aplicação.
    1. Consoante o tipo de aplicação, defina o URL de início de sessão ou o URL de redirecionamento para o URL base.
    1. Selecione **criar** para criar o aplicativo.
1. Gere um segredo do cliente antes de sair do portal do Azure.
   1. No portal do Azure, escolha a sua aplicação e selecione **definições**.
   1. Selecione **chaves** no menu de definições e adicionar uma chave com uma duração de chave de um ano ou dois anos.
   1. Quando guardar nesta página, o portal do Azure mostra o valor da chave. Copie e guarde o valor da chave numa localização segura.

      > [!IMPORTANT]
      > Precisa da chave para configurar as definições de aplicação em sua implementação. Este valor de chave não é novamente apresentada e não é recuperável de outros meios. Registe-a assim que é visível no portal do Azure.

### <a name="register-the-client-application"></a>Registar a aplicação de cliente

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na barra superior, selecione a sua conta e procure o **Directory** lista para selecionar um inquilino do Active Directory para a sua aplicação.
1. Selecione **mais serviços** no painel da esquerda e escolha **Azure Active Directory**.
1. Selecione **registos de aplicações** e, em seguida **novo registo de aplicação**.
1. Introduza um nome amigável para a aplicação e selecione o tipo de aplicação.
   1. Consoante o tipo de aplicação, defina o URL de início de sessão ou o URL de redirecionamento para o URL base.
   1. Selecione **criar** para criar o aplicativo.
1. Configure permissões para a sua aplicação.
   1. No menu de definições, escolha o **permissões obrigatórias** secção e, em seguida, selecione **Add** e **selecionar uma API**.
   1. Escreva o nome do serviço de camada intermediária no campo de texto.
   1. Escolher **selecionar permissões** e, em seguida, selecione **nome do serviço de acesso**.

### <a name="configure-known-client-applications"></a>Configurar aplicações de cliente conhecidos

Neste cenário, o serviço de camada intermediária precisa obter o consentimento do utilizador para aceder à API jusante sem interação do utilizador. A opção para conceder acesso à API de downstream deve ser apresentada com antecedência como parte do passo consentimento durante a autenticação.

Siga os passos abaixo para ligar explicitamente o registo da aplicação de cliente no Azure AD com o registo do serviço de camada intermediária. Esta operação mescla o necessário para o cliente e a camada intermediária numa única caixa de diálogo de consentimento.

1. Vá para o registo do serviço de camada intermediária e selecione **manifesto** para abrir o editor de manifesto.
1. Localize o `knownClientApplications` propriedade de matriz e adicione o ID de cliente da aplicação cliente como um elemento.
1. Guardar o manifesto, selecionando **guardar**.

## <a name="service-to-service-access-token-request"></a>Pedido de token de acesso de serviço a serviço

Para pedir um token de acesso, crie um HTTP POST para o inquilino específico ponto final do Azure com os seguintes parâmetros:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

A aplicação de cliente é protegida por um segredo partilhado ou por um certificado.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: Pedido de token de acesso com um segredo partilhado

Ao usar um segredo partilhado, um pedido de token de acesso de serviço para serviço contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |obrigatório | O tipo de pedido de token. Um pedido OBO utiliza um JSON Web Token (JWT) para que o valor tem de ser **urn: ietf:params:oauth:grant-tipo: jwt-portador**. |
| asserção |obrigatório | O valor do token de acesso utilizado no pedido. |
| client_id |obrigatório | O ID de aplicação atribuído para o serviço de chamada durante o registo com o Azure AD. Para localizar o ID de aplicação no portal do Azure, selecione **do Active Directory**, escolha o diretório e, em seguida, selecione o nome da aplicação. |
| client_secret |obrigatório | A chave registada para o serviço de chamada no Azure AD. Este valor deve ter sido apontado no momento do registo. |
| Recurso |obrigatório | A aplicação de URI de ID do serviço de recebimento (recurso protegido). Para localizar o URI de ID de aplicação no portal do Azure, selecione **do Active Directory** e escolha o diretório. Selecione o nome da aplicação, escolha **todas as definições**e, em seguida, selecione **propriedades**. |
| requested_token_use |obrigatório | Especifica a forma como a solicitação deve ser processada. Fluxo em-nome-de, o valor tem de ser **on_behalf_of**. |
| scope |obrigatório | Lista de âmbitos para o pedido de token separados por um espaço. Para o OpenID Connect, o escopo **openid** tem de ser especificado.|

#### <a name="example"></a>Exemplo

A seguinte mensagem de HTTP solicita um token de acesso para o https://graph.windows.net web API. O `client_id` identifica o serviço que solicita o token de acesso.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: Pedido de token de acesso com um certificado

Um pedido de token de acesso de serviço para serviço com um certificado contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |obrigatório | O tipo de pedido de token. Um pedido OBO usa um token de acesso do JWT, portanto, o valor tem de ser **urn: ietf:params:oauth:grant-tipo: jwt-portador**. |
| asserção |obrigatório | O valor do token utilizado no pedido. |
| client_id |obrigatório | O ID de aplicação atribuído para o serviço de chamada durante o registo com o Azure AD. Para localizar o ID de aplicação no portal do Azure, selecione **do Active Directory**, escolha o diretório e, em seguida, selecione o nome da aplicação. |
| client_assertion_type |obrigatório |O valor tem de ser `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |obrigatório | Um JSON Web Token que crie e inicie sessão com o certificado é registado como as credenciais para a sua aplicação. Ver [credenciais de certificado](active-directory-certificate-credentials.md) para saber mais sobre o formato de asserção e sobre como registar o seu certificado.|
| Recurso |obrigatório | A aplicação de URI de ID do serviço de recebimento (recurso protegido). Para localizar o URI de ID de aplicação no portal do Azure, selecione **do Active Directory** e escolha o diretório. Selecione o nome da aplicação, escolha **todas as definições**e, em seguida, selecione **propriedades**. |
| requested_token_use |obrigatório | Especifica a forma como a solicitação deve ser processada. Fluxo em-nome-de, o valor tem de ser **on_behalf_of**. |
| scope |obrigatório | Lista de âmbitos para o pedido de token separados por um espaço. Para o OpenID Connect, o escopo **openid** tem de ser especificado.|

Esses parâmetros são quase os mesmos tal como acontece com o pedido de segredo partilhado, exceto que o `client_secret parameter` é substituído por dois parâmetros: `client_assertion_type` e `client_assertion`.

#### <a name="example"></a>Exemplo

A seguinte mensagem de HTTP solicita um token de acesso para o https://graph.windows.net API web com um certificado. O `client_id` identifica o serviço que solicita o token de acesso.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Resposta de token de acesso de serviço a serviço

Uma resposta de êxito é uma resposta JSON OAuth 2.0 com os seguintes parâmetros:

| Parâmetro | Descrição |
| --- | --- |
| token_type |Indica o valor de tipo de token. O único tipo, que é o Azure AD suporta **portador**. Para obter mais informações sobre os tokens de portador, consulte o [Framework de autorização do OAuth 2.0: Utilização de Token de portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |O âmbito de acesso concedido no token. |
| expires_in |O período de tempo o token de acesso é válido (em segundos). |
| expires_on |O tempo que o token de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de expiração. Este valor é utilizado para determinar o tempo de vida de tokens em cache. |
| Recurso |A aplicação de URI de ID do serviço de recebimento (recurso protegido). |
| access_token |O token de acesso solicitado. O serviço de chamada pode utilizar este token para autenticar para o serviço de recebimento. |
| id_token |O token de ID de pedido. O serviço de chamada pode utilizar este token para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. |
| refresh_token |O token de atualização para o token de acesso solicitado. O serviço de chamada pode utilizar este token para solicitar outro token de acesso após o token de acesso atual expira. |

### <a name="success-response-example"></a>Exemplo de resposta de êxito

O exemplo seguinte mostra uma resposta de êxito para um pedido de um acesso token para o https://graph.windows.net web API.

```
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Exemplo de resposta de erro

O ponto de final de token do Azure AD devolve uma resposta de erro quando tentar adquirir um token de acesso para uma API de downstream que está definida com uma política de acesso condicional (por exemplo, autenticação multifator). O serviço de camada intermediária deve solucionar este erro para o aplicativo cliente, para que a aplicação cliente pode fornecer a interação do usuário para satisfazer a política de acesso condicional.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Utilizar o token de acesso para aceder ao recurso protegido

O serviço de camada intermediária pode usar o token de acesso adquirida para fazer pedidos autenticados para a API web a jusante, definindo o token no `Authorization` cabeçalho.

### <a name="example"></a>Exemplo

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Asserções SAML obtidas com um fluxo de OAuth2.0 OBO

Alguns serviços da web com base em OAuth precisam de aceder a outro APIs que aceitam asserções SAML em fluxos de não-interativa do web service. O Azure Active Directory pode fornecer uma asserção de SAML em resposta a um fluxo em-nome-de que utiliza um serviço web baseado em SAML como um recurso de destino.

>[!NOTE]
>Esta é uma extensão não padrão para o fluxo do OAuth 2.0 On-Behalf-Of que permite que um aplicativo baseado no OAuth2 acessar a web API pontos finais de serviço que consomem SAML tokens.

> [!TIP]
> Quando chama um serviço web protegido de SAML a partir de uma aplicação web front-end, pode simplesmente chamar a API e iniciar um fluxo de autenticação interativa normais com a sessão do utilizador existente. Só tem de utilizar um fluxo de OBO quando uma chamada de serviço para serviço requer um token SAML para fornecer o contexto de utilizador.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Obter um token SAML, utilizando um pedido OBO com um segredo partilhado

Um pedido de serviço a serviço para uma asserção de SAML contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |obrigatório | O tipo de pedido de token. Para um pedido que utiliza um JWT, o valor tem de ser **urn: ietf:params:oauth:grant-tipo: jwt-portador**. |
| asserção |obrigatório | O valor do token de acesso utilizado no pedido.|
| client_id |obrigatório | O ID de aplicação atribuído para o serviço de chamada durante o registo com o Azure AD. Para localizar o ID de aplicação no portal do Azure, selecione **do Active Directory**, escolha o diretório e, em seguida, selecione o nome da aplicação. |
| client_secret |obrigatório | A chave registada para o serviço de chamada no Azure AD. Este valor deve ter sido apontado no momento do registo. |
| Recurso |obrigatório | A aplicação de URI de ID do serviço de recebimento (recurso protegido). Este é o recurso que será o público-alvo do SAML token. Para localizar o URI de ID de aplicação no portal do Azure, selecione **do Active Directory** e escolha o diretório. Selecione o nome da aplicação, escolha **todas as definições**e, em seguida, selecione **propriedades**. |
| requested_token_use |obrigatório | Especifica a forma como a solicitação deve ser processada. Fluxo em-nome-de, o valor tem de ser **on_behalf_of**. |
| requested_token_type | obrigatório | Especifica o tipo de pedido de token. O valor pode ser **urn: ietf:params:oauth:token-tipo: saml2** ou **urn: ietf:params:oauth:token-tipo: saml1** dependendo dos requisitos do recurso acedido. |

A resposta contém um token SAML com codificação UTF8 e Base64url.

- **SubjectConfirmationData para uma asserção de SAML obtido de uma chamada OBO**: Se a aplicação de destino requer um valor de destinatário no **SubjectConfirmationData**, em seguida, o valor tem de ser um URL de resposta de carateres não universais na configuração da aplicação de recurso.
- **O nó de SubjectConfirmationData**: O nó não pode conter uma **InResponseTo** de atributos, uma vez que não faz parte de uma resposta SAML. O aplicativo receber o token SAML deve ser capaz de aceitar a asserção de SAML sem uma **InResponseTo** atributo.

- **Consentir**: Consentimento deve ter sido concedido para receber um token SAML que contém dados de usuário num fluxo de OAuth. Para obter informações sobre permissões e obter consentimento de administrador, consulte [permissões e consentimento no ponto de final do Azure Active Directory v1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-permissions-and-consent).

### <a name="response-with-saml-assertion"></a>Resposta com asserção de SAML

| Parâmetro | Descrição |
| --- | --- |
| token_type |Indica o valor de tipo de token. O único tipo, que é o Azure AD suporta **portador**. Para obter mais informações sobre os tokens de portador, consulte [Framework de autorização do OAuth 2.0: Utilização de Token de portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |O âmbito de acesso concedido no token. |
| expires_in |O período de tempo o token de acesso é válido (em segundos). |
| expires_on |O tempo que o token de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de expiração. Este valor é utilizado para determinar o tempo de vida de tokens em cache. |
| Recurso |A aplicação de URI de ID do serviço de recebimento (recurso protegido). |
| access_token |O parâmetro que retorna a asserção de SAML. |
| refresh_token |O token de atualização. O serviço de chamada pode utilizar este token para solicitar outro token de acesso após a asserção de SAML atual expira. |

- token_type: Portador
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- recurso: `https://api.contoso.com`
- access_token: \<Asserção de SAML\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<Atualizar token\>

## <a name="client-limitations"></a>Limitações de cliente

Os clientes públicos com URLs de resposta de caráter universal não podem utilizar um `id_token` para OBO fluxos. No entanto, um cliente confidencial ainda pode resgatar **acesso** registado de URI de redirecionamento de tokens adquiridos por meio do fluxo de concessão implícita, mesmo que o cliente público tem um caráter universal.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o protocolo de OAuth 2.0 e a outra forma de efetuar a autenticação serviço a serviço que utiliza credenciais de cliente:

* [Serviço de autenticação de serviço com a concessão de credenciais de cliente OAuth 2.0 no Azure AD](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 no Azure AD](v1-protocols-oauth-code.md)
