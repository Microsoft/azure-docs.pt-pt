---
title: Credenciais de certificado de plataforma de identidade da Microsoft
titleSuffix: Microsoft identity platform
description: Este artigo discute o registo e utilização de credenciais de certificado para autenticação de candidaturas.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d834d37f686279facf0052e3c8f4da3c8d259e0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309680"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Credenciais de certificado de autenticação de aplicação de plataforma de identidade microsoft

A plataforma de identidade microsoft permite que uma aplicação utilize as suas próprias credenciais para autenticação, por exemplo, no [OAuth 2.0 Client Credentials Grant flowv2.0](v2-oauth2-client-creds-grant-flow.md) e no [fluxo On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md)).

Uma forma de credencial que uma aplicação pode usar para autenticação é uma afirmação JSON Web Token(JWT) assinada com um certificado que a aplicação detém.

## <a name="assertion-format"></a>Formato de afirmação
Plataforma de identidade da Microsoft Para calcular a afirmação, pode utilizar uma das muitas bibliotecas [JSON Web Token](https://jwt.ms/) no idioma à sua escolha. As informações transportadas pelo símbolo são as seguintes:

### <a name="header"></a>Cabeçalho

| Parâmetro |  Observação |
| --- | --- |
| `alg` | Deve ser **RS256** |
| `typ` | Deve ser **JWT** |
| `x5t` | Deve ser a impressão digital do Certificado X.509 SHA-1 |

### <a name="claims-payload"></a>Reclamações (carga útil)

| Parâmetro |  Observações |
| --- | --- |
| `aud` | Público: Deve ser ** https://login.microsoftonline.com/ *tenant_Id*/oauth2/token** |
| `exp` | Data de validade: a data em que o token expira. O tempo é representado como o número de segundos de 1 de janeiro de 1970 (1970-01-01T0:0:0Z) UTC até ao momento em que a validade do símbolo expira.|
| `iss` | Emitente: deve ser o client_id (Id de aplicação do serviço ao cliente) |
| `jti` | GUID: o ID JWT |
| `nbf` | Não antes: a data anterior à qual o símbolo não pode ser utilizado. O tempo é representado como o número de segundos de 1 de janeiro de 1970 (1970-01-01T0:0:0Z) UTC até ao momento em que o token foi emitido. |
| `sub` | Objeto: Quanto `iss`a, deve ser o client_id (Id de aplicação do serviço ao cliente) |

### <a name="signature"></a>Assinatura

A assinatura é calculada aplicando o certificado conforme descrito na [especificação JSON Web Token RFC7519](https://tools.ietf.org/html/rfc7519)

## <a name="example-of-a-decoded-jwt-assertion"></a>Exemplo de uma afirmação deScodificada jWT

```JSON
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

## <a name="example-of-an-encoded-jwt-assertion"></a>Exemplo de uma afirmação codificada do JWT

A seguinte corda é um exemplo de afirmação codificada. Se olhar com atenção, note três secções separadas por pontos (.):
* A primeira secção codifica o cabeçalho
* A segunda secção codifica a carga útil
* A última secção é a assinatura calculada com os certificados do conteúdo das duas primeiras secções

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Registe o seu certificado com a plataforma de identidade microsoft

Pode associar a credencial de certificado à aplicação do cliente na plataforma de identidade da Microsoft através do portal Azure utilizando qualquer um dos seguintes métodos:

### <a name="uploading-the-certificate-file"></a>Upload do ficheiro de certificado

No registo da aplicação Azure para a aplicação do cliente:
1. Selecione **Certificados & segredos**.
2. Clique no **certificado de upload** e selecione o ficheiro de certificado para carregar.
3. Clique em **Adicionar**.
  Uma vez carregado o certificado, a impressão digital, a data de início e os valores de validade são apresentados.

### <a name="updating-the-application-manifest"></a>Atualizar o manifesto de candidatura

Tendo um certificado, precisa calcular:

- `$base64Thumbprint`, que é a codificação base64 do certificado hash
- `$base64Value`, que é a codificação base64 dos dados brutos do certificado

Também precisa de fornecer um GUID para identificar`$keyId`a chave no manifesto de aplicação ( ).

No registo da aplicação Azure para a aplicação do cliente:
1. Selecione **Manifesto** para abrir o manifesto de aplicação.
2. Substitua a propriedade *keyCredencias* pelo seu novo certificado utilizando o seguinte esquema.

   ```JSON
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
3. Guarde as edições para o manifesto da aplicação e, em seguida, faça upload do manifesto para a plataforma de identidade da Microsoft.

   A `keyCredentials` propriedade é multi-valiosa, por isso pode carregar vários certificados para uma gestão chave mais rica.

## <a name="code-sample"></a>Exemplo de código

> [!NOTE]
> Deve calcular o cabeçalho X5T convertendo-o numa corda base 64 utilizando o hash do certificado. O código para executar isto `System.Convert.ToBase64String(cert.GetCertHash());`em C# é .

A aplicação de consola da net [Core daemon utilizando](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) a plataforma de identidade da Microsoft mostra como uma aplicação utiliza as suas próprias credenciais para autenticação. Também mostra como pode criar um certificado `New-SelfSignedCertificate` [auto-assinado](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) utilizando o comando Powershell. Você também pode aproveitar e usar os scripts de criação de [aplicativos](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) para criar os certificados, calcular a impressão digital, e assim por diante.
