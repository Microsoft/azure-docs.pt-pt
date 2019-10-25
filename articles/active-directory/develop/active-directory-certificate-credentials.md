---
title: Credenciais de certificado no Azure AD
titleSuffix: Microsoft identity platform
description: Este artigo discute o registro e o uso de credenciais de certificado para autenticação de aplicativo
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1184d210f5b7ea25b9f73cbd70b5f960402126a1
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803531"
---
# <a name="certificate-credentials-for-application-authentication"></a>Credenciais de certificado para autenticação de aplicativo

O Azure Active Directory (AD do Azure) permite que um aplicativo use suas próprias credenciais para autenticação, por exemplo, no fluxo de concessão de credenciais de cliente do OAuth 2,0 ([v 1.0](v1-oauth2-client-creds-grant-flow.md), [v 2.0](v2-oauth2-client-creds-grant-flow.md)) e no fluxo em nome de ([v 1.0](v1-oauth2-on-behalf-of-flow.md), [v 2.0](v2-oauth2-on-behalf-of-flow.md)).

Uma forma de credencial que um aplicativo pode usar para autenticação é uma asserção JWT (token Web JSON) assinada com um certificado que o aplicativo possui.

## <a name="assertion-format"></a>Formato de asserção
Para computar a asserção, você pode usar uma das muitas bibliotecas de [token Web JSON](https://jwt.ms/) no idioma de sua escolha. As informações transportadas pelo token são as seguintes:

### <a name="header"></a>Cabeçalho

| Parâmetro |  Observação |
| --- | --- |
| `alg` | Deve ser **RS256** |
| `typ` | Deve ser **JWT** |
| `x5t` | Deve ser a impressão digital SHA-1 do certificado X. 509 |

### <a name="claims-payload"></a>Declarações (carga)

| Parâmetro |  Observações |
| --- | --- |
| `aud` | Público-alvo: deve ser **https://login.microsoftonline.com/*tenant_Id*/oauth2/token** |
| `exp` | Data de validade: a data em que o token expira. O tempo é representado como o número de segundos de 1º de janeiro de 1970 (1970-01-01T0:0: 0Z) UTC até a hora em que a validade do token expira.|
| `iss` | Emissor: deve ser o client_id (ID do aplicativo do serviço do cliente) |
| `jti` | GUID: a ID do JWT |
| `nbf` | Não antes: a data antes da qual o token não pode ser usado. O tempo é representado como o número de segundos de 1º de janeiro de 1970 (1970-01-01T0:0: 0Z) UTC até a hora em que o token foi emitido. |
| `sub` | Assunto: como por `iss`, deve ser o client_id (ID do aplicativo do serviço do cliente) |

### <a name="signature"></a>Signature

A assinatura é computada aplicando o certificado conforme descrito na [especificação RFC7519 do token Web JSON](https://tools.ietf.org/html/rfc7519)

## <a name="example-of-a-decoded-jwt-assertion"></a>Exemplo de uma asserção JWT decodificada

```
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,  
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"

```

## <a name="example-of-an-encoded-jwt-assertion"></a>Exemplo de uma asserção JWT codificada

A cadeia de caracteres a seguir é um exemplo de asserção codificada. Se olhar com cuidado, você notará três seções separadas por pontos (.):
* A primeira seção codifica o cabeçalho
* A segunda seção codifica a carga
* A última seção é a assinatura computada com os certificados do conteúdo das duas primeiras seções

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-azure-ad"></a>Registrar seu certificado com o Azure AD

Você pode associar a credencial do certificado ao aplicativo cliente no Azure AD por meio do portal do Azure usando qualquer um dos seguintes métodos:

### <a name="uploading-the-certificate-file"></a>Carregando o arquivo de certificado

No registro do aplicativo do Azure para o aplicativo cliente:
1. Selecione **certificados & segredos**. 
2. Clique em **carregar certificado** e selecione o arquivo de certificado a ser carregado.
3. Clique em **Adicionar**.
  Depois que o certificado for carregado, os valores de impressão digital, data de início e expiração serão exibidos. 

### <a name="updating-the-application-manifest"></a>Atualizando o manifesto do aplicativo

Tendo tido um certificado, você precisa calcular:

- `$base64Thumbprint`, que é a codificação base64 do hash de certificado
- `$base64Value`, que é a codificação Base64 dos dados brutos do certificado

Você também precisa fornecer um GUID para identificar a chave no manifesto do aplicativo (`$keyId`).

No registro do aplicativo do Azure para o aplicativo cliente:
1. Selecione **manifesto** para abrir o manifesto do aplicativo.
2. Substitua a propriedade *Keycredentials* pelas suas novas informações de certificado usando o esquema a seguir.

   ```
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Salve as edições no manifesto do aplicativo e, em seguida, carregue o manifesto no Azure AD. 

   A propriedade `keyCredentials` tem valores múltiplos, portanto, você pode carregar vários certificados para um gerenciamento mais avançado de chaves.
   
## <a name="code-sample"></a>Exemplo de código

O exemplo de código sobre [autenticação no Azure AD em aplicativos daemon com certificados](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) mostra como um aplicativo usa suas próprias credenciais para autenticação. Ele também mostra como você pode [criar um certificado autoassinado](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential#create-a-self-signed-certificate) usando o comando `New-SelfSignedCertificate` PowerShell. Você também pode tirar proveito e usar os [scripts de criação de aplicativo](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/AppCreationScripts/AppCreationScripts.md) para criar os certificados, computar a impressão digital e assim por diante.
