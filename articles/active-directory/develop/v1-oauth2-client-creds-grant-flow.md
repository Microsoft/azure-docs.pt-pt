---
title: Serviço do Azure AD para autenticação de serviço usando OAuth 2.0 | Microsoft Docs
description: Este artigo descreve como usar mensagens HTTP para implementar a autenticação de serviço para serviço usando o fluxo de concessão de credenciais de cliente do OAuth 2.0.
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
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ac618b28fae7410a773012e390dcd6b3a63b966
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834779"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Chamadas de serviço a serviço usando credenciais do cliente (segredo compartilhado ou certificado)

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

O fluxo de concessão de credenciais de cliente do OAuth 2,0 permite que um serviço Web (*cliente confidencial*) Use suas próprias credenciais em vez de representar um usuário, para autenticar ao chamar outro serviço Web. Nesse cenário, o cliente é normalmente um serviço Web de camada intermediária, um serviço de daemon ou um site da Web. Para um nível mais alto de garantia, o Azure AD também permite que o serviço de chamada use um certificado (em vez de um segredo compartilhado) como uma credencial.

## <a name="client-credentials-grant-flow-diagram"></a>Diagrama de fluxo de concessão de credenciais de cliente
O diagrama a seguir explica como o fluxo de concessão de credenciais de cliente funciona no Azure Active Directory (AD do Azure).

![Fluxo de concessão de credenciais de cliente do OAuth 2.0](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. O aplicativo cliente é autenticado no ponto de extremidade de emissão de token do Azure AD e solicita um token de acesso.
2. O ponto de extremidade de emissão de token do Azure AD emite o token de acesso.
3. O token de acesso é usado para autenticar o recurso protegido.
4. Os dados do recurso protegido são retornados ao aplicativo cliente.

## <a name="register-the-services-in-azure-ad"></a>Registrar os serviços no Azure AD
Registre o serviço de chamada e o serviço de recebimento no Azure Active Directory (Azure AD). Para obter instruções detalhadas, consulte [integrando aplicativos com Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="request-an-access-token"></a>Solicitar um token de acesso
Para solicitar um token de acesso, use um HTTP POST para o ponto de extremidade do Azure AD específico do locatário.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Solicitação de token de acesso de serviço a serviço
Há dois casos, dependendo se o aplicativo cliente opta por ser protegido por um segredo compartilhado ou por um certificado.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: Solicitação de token de acesso com um segredo compartilhado
Ao usar um segredo compartilhado, uma solicitação de token de acesso de serviço a serviço contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |obrigatório |Especifica o tipo de concessão solicitado. Em um fluxo de concessão de credenciais de cliente, o valor deve ser **client_credentials**. |
| client_id |obrigatório |Especifica a ID do cliente do Azure AD do serviço Web de chamada. Para localizar a ID do cliente do aplicativo de chamada, na [portal do Azure](https://portal.azure.com), clique em **Azure Active Directory**, clique em **registros de aplicativo**, clique no aplicativo. O client_id é a *ID do aplicativo* |
| client_secret |obrigatório |Insira uma chave registrada para o serviço Web de chamada ou aplicativo de daemon no Azure AD. Para criar uma chave, na portal do Azure, clique em **Azure Active Directory**, clique em **registros de aplicativo**, clique no aplicativo, clique em **configurações**, clique em **chaves**e adicione uma chave.  URL-codifique esse segredo ao fornecer. |
| resource |obrigatório |Insira o URI da ID do aplicativo do serviço Web de recebimento. Para localizar o URI da ID do aplicativo, na portal do Azure, clique em **Azure Active Directory**, clique em **registros de aplicativo**, clique no aplicativo de serviço e, em seguida, clique em **configurações** e **Propriedades**. |

#### <a name="example"></a>Exemplo
O http post a seguir solicita um [token](access-tokens.md) de acesso https://service.contoso.com/ para o serviço Web. O `client_id` identifica o serviço Web que solicita o token de acesso.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: Solicitação de token de acesso com um certificado
Uma solicitação de token de acesso de serviço a serviço com um certificado contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |obrigatório |Especifica o tipo de resposta solicitado. Em um fluxo de concessão de credenciais de cliente, o valor deve ser **client_credentials**. |
| client_id |obrigatório |Especifica a ID do cliente do Azure AD do serviço Web de chamada. Para localizar a ID do cliente do aplicativo de chamada, na [portal do Azure](https://portal.azure.com), clique em **Azure Active Directory**, clique em **registros de aplicativo**, clique no aplicativo. O client_id é a *ID do aplicativo* |
| client_assertion_type |obrigatório |O valor deve ser`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |obrigatório | Uma asserção (um token Web JSON) que você precisa para criar e assinar com o certificado que você registrou como credenciais para seu aplicativo. Leia sobre [as credenciais do certificado](active-directory-certificate-credentials.md) para saber como registrar seu certificado e o formato da asserção.|
| resource | obrigatório |Insira o URI da ID do aplicativo do serviço Web de recebimento. Para localizar o URI da ID do aplicativo, na portal do Azure, clique em **Azure Active Directory**, clique em **registros de aplicativo**, clique no aplicativo de serviço e, em seguida, clique em **configurações** e **Propriedades**. |

Observe que os parâmetros são quase iguais aos do caso da solicitação por segredo compartilhado, exceto pelo fato de que o parâmetro client_secret é substituído por dois parâmetros: um client_assertion_type e client_assertion.

#### <a name="example"></a>Exemplo
O http post a seguir solicita um token de acesso https://service.contoso.com/ para o serviço Web com um certificado. O `client_id` identifica o serviço Web que solicita o token de acesso.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Resposta de token de acesso de serviço a serviço

Uma resposta de êxito contém uma resposta JSON OAuth 2,0 com os seguintes parâmetros:

| Parâmetro | Descrição |
| --- | --- |
| access_token |O token de acesso solicitado. O serviço Web de chamada pode usar esse token para se autenticar no serviço Web de recebimento. |
| token_type |Indica o valor do tipo de token. O único tipo ao qual o Azure ADdá suporte é portador. Para obter mais informações sobre tokens de portador [, consulte a estrutura de autorização do OAuth 2,0: Uso de token de portador (RFC](https://www.rfc-editor.org/rfc/rfc6750.txt)6750). |
| expires_in |Por quanto tempo o token de acesso é válido (em segundos). |
| expires_on |A hora em que o token de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0: 0Z UTC até a hora de expiração. Esse valor é usado para determinar o tempo de vida dos tokens armazenados em cache. |
| not_before |A hora a partir da qual o token de acesso se torna utilizável. A data é representada como o número de segundos de 1970-01-01T0:0: 0Z UTC até o momento da validade do token.|
| resource |O URI da ID do aplicativo do serviço Web de recebimento. |

#### <a name="example-of-response"></a>Exemplo de resposta
O exemplo a seguir mostra uma resposta de êxito a uma solicitação de um token de acesso para um serviço Web.

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
* [OAuth 2,0 no Azure AD](v1-protocols-oauth-code.md)
* [Exemplo na C# chamada de serviço a serviço com um segredo compartilhado](https://github.com/Azure-Samples/active-directory-dotnet-daemon) e um [exemplo no C# do serviço para chamada de serviço com um certificado](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
