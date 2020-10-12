---
title: Azure AD Service to Service Auth using OAuth2.0 / Microsoft Docs
description: Este artigo descreve como usar mensagens HTTP para implementar o serviço de autenticação de serviço utilizando o fluxo de concessão de credenciais de cliente OAuth2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 977dfea28c5c0dc3f34ada0c138556d70c979e04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85551713"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Serviço para chamadas de serviço usando credenciais de cliente (segredo ou certificado partilhado)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O OAuth 2.0 Client Credentials Grant Flow permite que um serviço web (*cliente confidencial*) utilize as suas próprias credenciais em vez de se fazer passar por utilizador, para autenticar quando liga para outro serviço web. Neste cenário, o cliente é tipicamente um serviço web de nível médio, um serviço daemon ou web site. Para um nível de garantia mais elevado, a Azure AD também permite que o serviço de chamada utilize um certificado (em vez de um segredo partilhado) como credencial.

## <a name="client-credentials-grant-flow-diagram"></a>Diagrama de fluxo de concessão de credenciais de cliente
O diagrama seguinte explica como funciona o fluxo de concessão de credenciais do cliente no Azure Ative Directory (Azure AD).

![OAuth2.0 Cliente Credenciais Grant Flow](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. A aplicação do cliente autentica-se no ponto final de emissão de ad Azure e solicita um token de acesso.
2. O ponto final de emissão de ad Ad Azure emite o token de acesso.
3. O token de acesso é utilizado para autenticar o recurso seguro.
4. Os dados do recurso seguro são devolvidos à aplicação do cliente.

## <a name="register-the-services-in-azure-ad"></a>Registar os Serviços em Azure AD
Registe-se tanto no serviço de chamadas como no serviço de receção no Azure Ative Directory (Azure AD). Para obter instruções detalhadas, consulte [a integração de aplicações com o Diretório Ativo Azure](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="request-an-access-token"></a>Solicite um token de acesso
Para solicitar um token de acesso, utilize um HTTP POST para o ponto final Azure AD específico do inquilino.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Pedido de acesso de serviço ao serviço
Existem dois casos dependendo se o pedido do cliente opta por ser protegido por um segredo partilhado, ou por um certificado.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: Pedido de acesso a um sinal com um segredo partilhado
Ao utilizar um segredo partilhado, um pedido de sinal de acesso de serviço contém os seguintes parâmetros:

| Parâmetro | Tipo | Descrição |
| --- | --- | --- |
| grant_type |obrigatório |Especifica o tipo de subvenção solicitado. Num fluxo de concessão de credenciais de cliente, o valor deve ser **client_credentials**. |
| client_id |obrigatório |Especifica o id do cliente Azure AD do serviço web de chamadas. Para encontrar o ID do cliente da aplicação de chamada, no [portal Azure,](https://portal.azure.com)clique em **Azure Ative Directory,** clique nas **inscrições da App,** clique na aplicação. O client_id é o *ID da aplicação* |
| client_secret |obrigatório |Introduza uma chave registada para o serviço web de chamadas ou aplicação Daemon em Azure AD. Para criar uma chave, no portal Azure, clique em **Azure Ative Directory**, clique nos **registos de Aplicações,** clique na aplicação, clique em **Definições,** clique em **Chaves**e adicione uma Chave.  URL codificar este segredo ao for-lhe fornecido. |
| recurso |obrigatório |Introduza a App ID URI do serviço web recetor. Para encontrar a App ID URI, no portal Azure, clique em **Azure Ative Directory,** clique nos **registos da App,** clique na aplicação de serviço e, em seguida, clique em **Definições** e **Propriedades**. |

#### <a name="example"></a>Exemplo
O seguinte HTTP POST solicita um [token de acesso para](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) o `https://service.contoso.com/` serviço web. Identifica `client_id` o serviço web que solicita o token de acesso.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: Pedido de token de acesso com certificado
Um pedido de sinal de acesso de serviço ao serviço com um certificado contém os seguintes parâmetros:

| Parâmetro | Tipo | Descrição |
| --- | --- | --- |
| grant_type |obrigatório |Especifica o tipo de resposta solicitado. Num fluxo de concessão de credenciais de cliente, o valor deve ser **client_credentials**. |
| client_id |obrigatório |Especifica o id do cliente Azure AD do serviço web de chamadas. Para encontrar o ID do cliente da aplicação de chamada, no [portal Azure,](https://portal.azure.com)clique em **Azure Ative Directory,** clique nas **inscrições da App,** clique na aplicação. O client_id é o *ID da aplicação* |
| client_assertion_type |obrigatório |O valor deve ser `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |obrigatório | Uma afirmação (um Token Web JSON) que precisa de criar e assinar com o certificado que registou como credenciais para a sua aplicação. Leia sobre [as credenciais de certificado](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) para saber como registar o seu certificado e o formato da afirmação.|
| recurso | obrigatório |Introduza a App ID URI do serviço web recetor. Para encontrar a App ID URI, no portal Azure, clique em **Azure Ative Directory,** clique nos **registos da App,** clique na aplicação de serviço e, em seguida, clique em **Definições** e **Propriedades**. |

Note que os parâmetros são quase os mesmos que no caso do pedido por segredo partilhado, exceto que o parâmetro client_secret é substituído por dois parâmetros: um client_assertion_type e client_assertion.

#### <a name="example"></a>Exemplo
O seguinte HTTP POST solicita um token de acesso para o `https://service.contoso.com/` serviço web com um certificado. Identifica `client_id` o serviço web que solicita o token de acesso.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Resposta ao token de acesso de serviço ao serviço

Uma resposta de sucesso contém uma resposta JSON OAuth 2.0 com os seguintes parâmetros:

| Parâmetro | Descrição |
| --- | --- |
| access_token |O sinal de acesso solicitado. O serviço web de chamada pode usar este token para autenticar o serviço web recetor. |
| token_type |Indica o valor do tipo símbolo. O único tipo que a Azure AD suporta é **o Portador.** Para obter mais informações sobre fichas ao portador, consulte o Quadro de [Autorização OAuth 2.0: Utilização do Token ao portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Quanto tempo o token de acesso é válido (em segundos). |
| expires_on |O tempo em que o sinal de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0Z UTC até ao prazo de validade. Este valor é usado para determinar a vida útil de fichas em cache. |
| not_before |O tempo a partir do qual o símbolo de acesso torna-se utilizável. A data é representada como o número de segundos de 1970-01-01T0:0Z UTC até à hora de validade para o token.|
| recurso |O ID URI da aplicação do serviço web recetor. |

#### <a name="example-of-response"></a>Exemplo de resposta
O exemplo a seguir mostra uma resposta de sucesso a um pedido de acesso a um serviço web.

```
{
"access_token":"eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```
## <a name="use-the-access-token-to-access-the-secured-resource"></a>Use o token de acesso para aceder ao recurso seguro

O serviço pode usar o token de acesso adquirido para fazer pedidos autenticados para a API web a jusante, definindo o símbolo no `Authorization` cabeçalho.

### <a name="example"></a>Exemplo

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="see-also"></a>Veja também
* [OAuth 2.0 em Azure AD](v1-protocols-oauth-code.md)
* [Amostra em C# do serviço para atender chamada com um segredo partilhado](https://github.com/Azure-Samples/active-directory-dotnet-daemon) e Amostra em [C# do serviço para chamada de serviço com um certificado](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
