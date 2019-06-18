---
title: Autenticação serviço a serviço do Azure AD, usando o OAuth2.0 | Documentos da Microsoft
description: Este artigo descreve como utilizar mensagens HTTP para implementar a autenticação de serviço para serviço com o fluxo de concessão de credenciais de cliente de OAuth2.0.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d734db7fbedaf3e3f3cd71c31f9391a2237f5b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545268"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Chamadas serviço a serviço com as credenciais de cliente (segredo partilhado ou certificado)

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

O OAuth 2.0 cliente credenciais de fluxo de concessão de permite um serviço web (*cliente confidencial*) para utilizar as suas próprias credenciais em vez de representar um utilizador, para autenticação ao chamar outro serviço web. Neste cenário, o cliente é normalmente um serviço web de camada intermediária, um serviço de daemon ou o web site. Para um nível mais elevado de garantia, do Azure AD também permite que o serviço de chamada utilizar um certificado (em vez de um segredo partilhado) como uma credencial.

## <a name="client-credentials-grant-flow-diagram"></a>Diagrama de fluxo de concessão de credenciais de cliente
O diagrama seguinte explica como as credenciais de cliente concedem o fluxo funciona no Azure Active Directory (Azure AD).

![Fluxo de concessão de credenciais de cliente de OAuth2.0](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. A aplicação cliente autentica para o ponto de final de emissão de token do Azure AD e pede um token de acesso.
2. O ponto de final de emissão de token do Azure AD emite o token de acesso.
3. O token de acesso é utilizado para autenticar para o recurso protegido.
4. Dados a partir do recurso protegido são retornados ao aplicativo cliente.

## <a name="register-the-services-in-azure-ad"></a>Registre-se os serviços no Azure AD
Registre-se o serviço de chamada e o serviço de recebimento no Azure Active Directory (Azure AD). Para obter instruções detalhadas, consulte [integrar aplicações com o Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="request-an-access-token"></a>Pedir um Token de acesso
Para pedir um token de acesso, utilize um HTTP POST para o inquilino específico ponto final do Azure.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Pedido de token de acesso de serviço a serviço
Existem dois casos, dependendo se o aplicativo cliente escolhe a ser protegido por um segredo partilhado ou um certificado.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: Pedido de token de acesso com um segredo partilhado
Ao usar um segredo partilhado, um pedido de token de acesso de serviço para serviço contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |Necessário |Especifica o tipo de pedido de concessão. Num fluxo de concessão de credenciais de cliente, o valor tem de ser **client_credentials**. |
| client_id |Necessário |Especifica o id de cliente do Azure AD do serviço web chamada. Para encontrar o ID de cliente da aplicação de chamada, além da [portal do Azure](https://portal.azure.com), clique em **Azure Active Directory**, clique em **registos das aplicações**, clique na aplicação. O client_id é o *ID da aplicação* |
| client_secret |Necessário |Introduza uma chave registada para o aplicativo de chamada web serviço ou daemon no Azure AD. Para criar uma chave, no portal do Azure, clique em **do Azure Active Directory**, clique em **registos das aplicações**, clique na aplicação, clique em **definições**, clique em **chaves** , e adicionar uma chave.  URL-codificar neste secreta ao fornecê-lo. |
| resource |Necessário |Introduza o URI de ID de aplicação do serviço web do recetor. Para encontrar o URI de ID de aplicação, no portal do Azure, clique em **do Azure Active Directory**, clique em **registos das aplicações**, clique na aplicação de serviço e, em seguida, clique em **definições** e  **Propriedades**. |

#### <a name="example"></a>Exemplo
Os seguintes pedidos de HTTP POST uma [token de acesso](access-tokens.md) para o https://service.contoso.com/ serviço web. O `client_id` identifica o serviço web que solicita o token de acesso.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: Pedido de token de acesso com um certificado
Um pedido de token de acesso de serviço para serviço com um certificado contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |Necessário |Especifica o tipo de resposta solicitada. Num fluxo de concessão de credenciais de cliente, o valor tem de ser **client_credentials**. |
| client_id |Necessário |Especifica o id de cliente do Azure AD do serviço web chamada. Para encontrar o ID de cliente da aplicação de chamada, além da [portal do Azure](https://portal.azure.com), clique em **Azure Active Directory**, clique em **registos das aplicações**, clique na aplicação. O client_id é o *ID da aplicação* |
| client_assertion_type |Necessário |O valor tem de ser `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |Necessário | Uma asserção (um JSON Web Token) que precisa para criar e assinar com o certificado é registado como as credenciais para a sua aplicação. Leia sobre [credenciais de certificado](active-directory-certificate-credentials.md) para saber como registar o seu certificado e o formato da asserção.|
| resource | Necessário |Introduza o URI de ID de aplicação do serviço web do recetor. Para encontrar o URI de ID de aplicação, no portal do Azure, clique em **do Azure Active Directory**, clique em **registos das aplicações**, clique na aplicação de serviço e, em seguida, clique em **definições** e  **Propriedades**. |

Tenha em atenção que os parâmetros são quase os mesmos que no caso do pedido de segredo partilhado, exceto que o parâmetro client_secret é substituído por dois parâmetros: um client_assertion_type e client_assertion.

#### <a name="example"></a>Exemplo
A seguinte mensagem de HTTP solicita um token de acesso para o https://service.contoso.com/ serviço com um certificado de web. O `client_id` identifica o serviço web que solicita o token de acesso.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Resposta de Token de acesso de serviço a serviço

Uma resposta de êxito contém uma resposta JSON OAuth 2.0 com os seguintes parâmetros:

| Parâmetro | Descrição |
| --- | --- |
| access_token |O token de acesso solicitado. O serviço de web chamada pode utilizar este token para autenticar para o serviço web de recebimento. |
| token_type |Indica o valor de tipo de token. O único tipo, que é o Azure AD suporta **portador**. Para obter mais informações sobre os tokens de portador, consulte o [Framework de autorização do OAuth 2.0: Utilização de Token de portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |O tempo que o token de acesso é válido (em segundos). |
| expires_on |O tempo que o token de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de expiração. Este valor é utilizado para determinar o tempo de vida de tokens em cache. |
| not_before |A hora a partir da qual se torna o token de acesso utilizável. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até o tempo de validade para o token.|
| resource |O URI de ID de aplicação do serviço web do recetor. |

#### <a name="example-of-response"></a>Exemplo de resposta
O exemplo seguinte mostra uma resposta de êxito a um pedido para um token de acesso a um serviço web.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Consulte também
* [OAuth 2.0 no Azure AD](v1-protocols-oauth-code.md)
* [Em c# da chamada de serviços com um segredo partilhado](https://github.com/Azure-Samples/active-directory-dotnet-daemon) e [em c# da chamada de serviços com um certificado](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
