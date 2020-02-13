---
title: Serviço Azure AD para servir Auth usando OAuth2.0  Microsoft Docs
description: Este artigo descreve como usar mensagens HTTP para implementar o serviço de autenticação de serviço utilizando o fluxo de concessão de credenciais de cliente OAuth2.0.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 24c9c4385f23b68e9a3efb65d2582457219fa10d
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164126"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Serviço a chamadas de serviço utilizando credenciais de cliente (segredo ou certificado partilhado)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O OAuth 2.0 Client Credentials Grant Flow permite que um serviço web *(cliente confidencial*) utilize as suas próprias credenciais em vez de se fazer passar por um utilizador, para autenticar quando chama outro serviço web. Neste cenário, o cliente é tipicamente um serviço web de nível médio, um serviço daemon ou web site. Para um nível mais elevado de garantia, a Azure AD também permite que o serviço de chamadas utilize um certificado (em vez de um segredo partilhado) como credencial.

## <a name="client-credentials-grant-flow-diagram"></a>Credenciais de cliente concedem diagrama de fluxo
O diagrama seguinte explica como as credenciais de cliente concedem fluxo funciona em Azure Ative Directory (Azure AD).

![OAuth2.0 Credenciais de cliente Grant Flow](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. A aplicação do cliente autentica-se no ponto final de emissão de fichas da AD Azure e solicita um sinal de acesso.
2. O ponto de extremidade de emissão de token do Azure AD emite o token de acesso.
3. O sinal de acesso é usado para autenticar o recurso seguro.
4. Os dados do recurso seguro são devolvidos à aplicação do cliente.

## <a name="register-the-services-in-azure-ad"></a>Registe os Serviços em Azure AD
Registe tanto o serviço de chamada como o serviço de receção no Azure Ative Directory (Azure AD). Para obter instruções detalhadas, consulte a integração de [aplicações com o Diretório Ativo Azure](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="request-an-access-token"></a>Solicite um Token de Acesso
Para solicitar um sinal de acesso, utilize um POST HTTP para o ponto final da Azure AD específica do inquilino.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Pedido de acesso serviço ao serviço
Existem dois casos dependendo se a aplicação do cliente opta por ser assegurada por um segredo partilhado, ou um certificado.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: Pedido de acesso com um segredo partilhado
Ao utilizar um segredo partilhado, um pedido de acesso serviço-a-serviço contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |Necessário |Especifica o tipo de subvenção solicitado. Num fluxo de concessão de credenciais de cliente, o valor deve ser **client_credentials**. |
| client_id |Necessário |Especifica a identificação do cliente da AD Azure do serviço web de chamada. Para encontrar o ID do cliente da aplicação de chamada, no [portal Azure,](https://portal.azure.com)clique no **Azure Ative Directory,** clique nos registos da **App,** clique na aplicação. O client_id é o ID de *aplicação* |
| client_secret |Necessário |Introduza uma chave registada para o serviço web de chamada ou aplicação daemon em Azure AD. Para criar uma chave, no portal Azure, clique no **Diretório Ativo do Azure,** clique nas inscrições da **App,** clique na aplicação, clique em **Definições,** clique em **Teclas**e adicione uma Chave.  Url-codifica este segredo ao fornecê-lo. |
| resource |Necessário |Introduza o ID da aplicação URI do serviço web recetor. Para encontrar o ID da aplicação URI, no portal Azure, clique no **Diretório Ativo do Azure,** clique nas inscrições da **App,** clique na aplicação de serviço e clique em **Definições** e **Propriedades**. |

#### <a name="example"></a>Exemplo
O seguinte HTTP POST solicita um [sinal de acesso](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) para o serviço web https://service.contoso.com/. O `client_id` identifica o serviço web que solicita o sinal de acesso.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: Pedido de acesso aceso com certificado
Um pedido de acesso serviço-a-serviço com um certificado contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |Necessário |Especifica o tipo de resposta solicitado. Num fluxo de concessão de credenciais de cliente, o valor deve ser **client_credentials**. |
| client_id |Necessário |Especifica a identificação do cliente da AD Azure do serviço web de chamada. Para encontrar o ID do cliente da aplicação de chamada, no [portal Azure,](https://portal.azure.com)clique no **Azure Ative Directory,** clique nos registos da **App,** clique na aplicação. O client_id é o ID de *aplicação* |
| client_assertion_type |Necessário |O valor deve ser `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |Necessário | Uma afirmação (um Token Web JSON) que precisa de criar e assinar com o certificado que registou como credenciais para a sua aplicação. Leia sobre [credenciais](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) de certificado para saber como registar o seu certificado e o formato da afirmação.|
| resource | Necessário |Introduza o ID da aplicação URI do serviço web recetor. Para encontrar o ID da aplicação URI, no portal Azure, clique no **Diretório Ativo do Azure,** clique nas inscrições da **App,** clique na aplicação de serviço e clique em **Definições** e **Propriedades**. |

Note-se que os parâmetros são quase os mesmos que no caso do pedido por segredo partilhado, exceto que o parâmetro client_secret é substituído por dois parâmetros: um client_assertion_type e client_assertion.

#### <a name="example"></a>Exemplo
O seguinte HTTP POST solicita um sinal de acesso para o serviço web https://service.contoso.com/ com um certificado. O `client_id` identifica o serviço web que solicita o sinal de acesso.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Resposta de acesso ao serviço

Uma resposta de sucesso contém uma resposta JSON OAuth 2.0 com os seguintes parâmetros:

| Parâmetro | Descrição |
| --- | --- |
| access_token |O sinal de acesso solicitado. O serviço web de chamada pode usar este símbolo para autenticar o serviço web recetor. |
| token_type |Indica o valor do tipo simbólico. O único tipo que a Azure AD suporta é **o Bearer.** Para obter mais informações sobre fichas ao portador, consulte o Quadro de [Autorização OAuth 2.0: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Quanto tempo o token de acesso é válido (em segundos). |
| expires_on |O tempo em que o sinal de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até ao tempo de validade. Este valor é usado para determinar a vida útil de fichas em cache. |
| not_before |O tempo a partir do qual o sinal de acesso se torna utilizável. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até à hora de validade para o símbolo.|
| resource |O App ID URI do serviço web recetor. |

#### <a name="example-of-response"></a>Exemplo de resposta
O exemplo seguinte mostra uma resposta de sucesso a um pedido de acesso a um serviço web.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Veja também
* [OAuth 2.0 em Azure AD](v1-protocols-oauth-code.md)
* [Amostra C# do serviço para chamada de serviço com um segredo partilhado](https://github.com/Azure-Samples/active-directory-dotnet-daemon) e Amostra no [ C# serviço para chamada de serviço com certificado](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
