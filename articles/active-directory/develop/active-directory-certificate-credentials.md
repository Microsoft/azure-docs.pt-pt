---
title: Credenciais de certificado de plataforma de identidade da Microsoft
titleSuffix: Microsoft identity platform
description: Este artigo discute o registo e a utilização de credenciais de certificado para autenticação de pedidos.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 22352f6d0c7dc48b98be5fcb4101f262adc480d6
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98795615"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Credenciais de certificado de autenticação de aplicativos da plataforma de identidade da Microsoft

A plataforma de identidade da Microsoft permite que uma aplicação utilize as suas próprias credenciais para autenticação em qualquer lugar que um segredo do cliente possa ser usado, por exemplo, no fluxo de concessão de  [credenciais](v2-oauth2-client-creds-grant-flow.md) de cliente OAuth 2.0 e no fluxo [em nome de](v2-oauth2-on-behalf-of-flow.md) (OBO).

Uma forma de credencial que uma aplicação pode usar para autenticação é uma afirmação [JSON Web Token](./security-tokens.md#json-web-tokens-and-claims) (JWT) assinada com um certificado que a aplicação possui.

## <a name="assertion-format"></a>Formato de afirmação

Para calcular a afirmação, pode utilizar uma das muitas bibliotecas JWT no idioma à sua escolha - [a MSAL suporta esta utilização `.WithCertificate()` ](msal-net-client-assertions.md). A informação é transportada pelo símbolo no seu [Cabeçalho,](#header) [Reclamações](#claims-payload)e [Assinatura](#signature).

### <a name="header"></a>Cabeçalho

| Parâmetro |  Observação |
| --- | --- |
| `alg` | Deve ser **RS256** |
| `typ` | Deve ser **JWT** |
| `x5t` | O hash de certificado X.509 (também conhecido como impressão *digital* SHA-1 do cert ) representação hexa negra codificada como um valor de corda Base64url. Por exemplo, dado um haxixe de certificado X.509 `84E05C1D98BCE3A5421D225B140B36E86A3D5534` (Hex), a `x5t` reclamação seria `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64url). |

### <a name="claims-payload"></a>Sinistros (carga útil)

Tipo de afirmação | Valor | Descrição
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | A alegação "aud" (audiência) identifica os destinatários a que o JWT se destina (aqui Azure AD) Ver [RFC 7519, Secção 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  Neste caso, o destinatário é o servidor de login (login.microsoftonline.com).
exp | 1601519414 | A alegação "exp" (tempo de validade) identifica o tempo de validade no ou após o qual o JWT NÃO DEVE ser aceite para processamento. Consulte [RFC 7519, Secção 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  Isto permite que a afirmação seja usada até lá, por isso mantenha-a curta - 5-10 minutos `nbf` depois, no máximo.  A Azure AD não coloca restrições na `exp` hora atual. 
iss | {ClientID} | A alegação "iss" (emitente) identifica o principal que emitiu o JWT, neste caso o seu pedido de cliente.  Utilize o ID da aplicação GUID.
jti | (um Guia) | A alegação "jti" (JWT ID) fornece um identificador único para o JWT. O valor do identificador DEVE ser atribuído de forma a garantir que existe uma probabilidade negligenciável de que o mesmo valor será acidentalmente atribuído a um objeto de dados diferente; se a aplicação utilizar vários emitentes, as colisões devem ser evitadas entre os valores produzidos também por diferentes emitentes. O valor "jti" é uma corda sensível a casos. [RFC 7519, Secção 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
nbf | 1601519114 | A alegação "nbf" (não antes) identifica o tempo anterior ao qual o JWT NÃO DEVE ser aceite para processamento. [RFC 7519, Secção 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  A utilização do tempo atual é apropriada. 
sub | {ClientID} | A alegação "sub" (sujeito) identifica o sujeito do JWT, neste caso também o seu pedido. Use o mesmo valor que `iss` . 

### <a name="signature"></a>Assinatura

A assinatura é calculada aplicando o certificado conforme descrito na [especificação JSON Web Token RFC7519](https://tools.ietf.org/html/rfc7519).

## <a name="example-of-a-decoded-jwt-assertion"></a>Exemplo de uma afirmação descodificada do JWT

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

A seguinte corda é um exemplo de afirmação codificada. Se olhar com atenção, nota três secções separadas por pontos `.` ( ):

* A primeira secção codifica o *cabeçalho*
* A segunda secção codifica as *reclamações* (carga útil)
* A última secção é a *assinatura* calculada com os certificados do conteúdo das duas primeiras secções

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Registe o seu certificado com a plataforma de identidade microsoft

Pode associar a credencial de certificado à aplicação do cliente na plataforma de identidade da Microsoft através do portal Azure utilizando qualquer um dos seguintes métodos:

### <a name="uploading-the-certificate-file"></a>Upload do arquivo de certificados

No registo da app Azure para a aplicação do cliente:
1. Selecione **Certificados & segredos**.
2. Clique no **certificado de upload** e selecione o ficheiro de certificado para carregar.
3. Clique em **Adicionar**.
  Uma vez que o certificado é carregado, a impressão digital, a data de início e os valores de validade são apresentados.

### <a name="updating-the-application-manifest"></a>Atualizar o manifesto de aplicação

Tendo um certificado, precisa calcular:

- `$base64Thumbprint` - Valor codificado base64 do haxixe do certificado
- `$base64Value` - Valor codificado base64 dos dados brutos do certificado

Também é necessário fornecer um GUID para identificar a chave no manifesto de candidatura ( `$keyId` ).

No registo da app Azure para a aplicação do cliente:
1. Selecione **Manifesto** para abrir o manifesto de inscrição.
2. Substitua a propriedade *keyCredentials* com as informações do seu novo certificado utilizando o seguinte esquema.

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
3. Guarde as edições para o manifesto da aplicação e, em seguida, faça o upload do manifesto para a plataforma de identidade da Microsoft.

   A `keyCredentials` propriedade é multi-valorizada, para que você possa carregar vários certificados para uma gestão de chaves mais rica.
   
## <a name="using-a-client-assertion"></a>Usando a afirmação de um cliente

As afirmações dos clientes podem ser usadas em qualquer lugar que um segredo de cliente seja usado.  Assim, por exemplo, no fluxo de código de [autorização,](v2-oauth2-auth-code-flow.md)pode passar num `client_secret` para provar que o pedido vem da sua app. Pode substituir isto por `client_assertion` `client_assertion_type` e parâmetros. 

| Parâmetro | Valor | Descrição|
|-----------|-------|------------|
|`client_assertion_type`|`urn:ietf:params:oauth:client-assertion-type:jwt-bearer`| Trata-se de um valor fixo, indicando que está a utilizar uma credencial de certificado. |
|`client_assertion`| JWT |Este é o JWT criado acima. |

## <a name="next-steps"></a>Próximos passos

A [biblioteca MSAL.NET lida com este cenário](msal-net-client-assertions.md) numa única linha de código.

A [aplicação de consola daemon .NET Core utilizando a](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) amostra de código da plataforma de identidade da Microsoft no GitHub mostra como uma aplicação utiliza as suas próprias credenciais para autenticação. Também mostra como pode [criar um certificado auto-assinado](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) usando o `New-SelfSignedCertificate` cmdlet PowerShell. Também pode usar os [scripts de criação de aplicações](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) no repo da amostra para criar certificados, calcular a impressão digital, e assim por diante.
