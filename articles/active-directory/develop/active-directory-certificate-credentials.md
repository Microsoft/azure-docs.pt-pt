---
title: Credenciais de certificado no Azure AD | Documentos da Microsoft
description: Este artigo aborda o registo e a utilização de credenciais de certificado para autenticação de aplicação
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
ms.topic: article
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed4e7559ff6c3b76bbdf49b538ffebf3ad09cc58
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001224"
---
# <a name="certificate-credentials-for-application-authentication"></a>Credenciais de certificado para autenticação de aplicação

Azure Active Directory (Azure AD) permite que um aplicativo usar suas próprias credenciais para autenticação, por exemplo, o fluxo de concessão de credenciais de cliente do OAuth 2.0 ([v1.0](v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) e o On-nome-de fluxo ([v1.0](v1-oauth2-on-behalf-of-flow.md), [v2.0](v2-oauth2-on-behalf-of-flow.md)).

Uma forma de credencial que uma aplicação pode utilizar para a autenticação é uma asserção de JSON Web Token(JWT) assinada com um certificado que o aplicativo que detém.

## <a name="assertion-format"></a>Formato de asserção
Para computar a asserção, pode utilizar um dos muitos [JSON Web Token](https://jwt.ms/) bibliotecas na linguagem da sua preferência. As informações transportadas pelo token são os seguintes:

### <a name="header"></a>Cabeçalho

| Parâmetro |  Comentário |
| --- | --- |
| `alg` | Deve ser **RS256** |
| `typ` | Deve ser **JWT** |
| `x5t` | Deve ser o thumbprint do certificado de X.509 SHA-1 |

### <a name="claims-payload"></a>Afirmações (payload)

| Parâmetro |  Observações |
| --- | --- |
| `aud` | Público-alvo: Deve estar  **https://login.microsoftonline.com/ *tenant_Id*  /oauth2/token** |
| `exp` | Data de expiração: a data em que o token expira. O tempo é representado como o número de segundos a partir de 1 de Janeiro de 1970 (1970-01-01T0:0:0Z) UTC até a hora a validade do token expira.|
| `iss` | Emissor: deve ser o client_id (ID de aplicação do serviço de cliente) |
| `jti` | GUID: o ID do JWT |
| `nbf` | Não antes: a data antes do qual o token não pode ser utilizado. O tempo é representado como o número de segundos a partir de 1 de Janeiro de 1970 (1970-01-01T0:0:0Z) UTC até o momento o token foi emitido. |
| `sub` | Requerente: Como para `iss`, deve ser o client_id (ID de aplicação do serviço de cliente) |

### <a name="signature"></a>Assinatura

A assinatura é calculada a aplicar o certificado, conforme descrito no [especificação RFC7519 Token do JSON Web](https://tools.ietf.org/html/rfc7519)

## <a name="example-of-a-decoded-jwt-assertion"></a>Exemplo de uma asserção de JWT descodificada

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

## <a name="example-of-an-encoded-jwt-assertion"></a>Exemplo de uma asserção de JWT codificado

A seguinte cadeia de caracteres é um exemplo de asserção codificada. Se olhar com cuidado, que três seções separadas por pontos (.):
* A primeira secção codifica o cabeçalho
* A segunda secção codifica o payload
* A última seção é a assinatura calculada com os certificados do conteúdo das duas primeiras seções

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-azure-ad"></a>Registar o certificado com o Azure AD

Pode associar a credencial de certificado com a aplicação de cliente no Azure AD através do portal do Azure com qualquer um dos seguintes métodos:

### <a name="uploading-the-certificate-file"></a>Carregar o ficheiro de certificado

No registo de aplicações do Azure para a aplicação de cliente:
1. Selecione **certificados e segredos**. 
2. Clique em **carregar certificado** e selecione o ficheiro de certificado para carregar.
3. Clique em **Adicionar**.
  Depois do certificado é carregado, o thumbprint, data de início e valores de expiração são apresentados. 

### <a name="updating-the-application-manifest"></a>A atualizar o manifesto do aplicativo

Ter espera de um certificado, terá de computação:

- `$base64Thumbprint`, que é o base64 codificação do hash de certificado
- `$base64Value`, que é o base64 codificação dos dados não processados do certificado

Também tem de fornecer um GUID para identificar a chave no manifesto do aplicativo (`$keyId`).

No registo de aplicações do Azure para a aplicação de cliente:
1. Selecione **manifesto** para abrir o manifesto do aplicativo.
2. Substitua a *keyCredentials* propriedade com as suas informações de certificado novo com o esquema abaixo.

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
3. Guardar as edições ao manifesto do aplicativo e, em seguida, carregue o manifesto para o Azure AD. 

   O `keyCredentials` propriedade é com múltiplos valores, para que pode carregar vários certificados de gestão mais avançada de chaves.
   
## <a name="code-sample"></a>Exemplo de código

O código de exemplo na [autenticar no Azure AD em aplicações daemon com certificados](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) mostra como um aplicativo usa suas próprias credenciais para autenticação. Ela também mostra como é possível [criar um certificado autoassinado](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential#create-a-self-signed-certificate) usando o `New-SelfSignedCertificate` comando do Powershell. Também pode aproveitar e utilizar o [scripts de criação de aplicações](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/AppCreationScripts/AppCreationScripts.md) para criar os certificados, o thumbprint de computação e assim por diante.
