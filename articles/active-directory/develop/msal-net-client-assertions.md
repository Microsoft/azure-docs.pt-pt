---
title: Afirmações do cliente (MSAL.NET) / Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o suporte de afirmações de clientes assinados para aplicações confidenciais de clientes na Microsoft Authentication Library para .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/18/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8c97387bfd2a362d3bf5a6b8a3252242f061da31
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80050297"
---
# <a name="confidential-client-assertions"></a>Afirmações confidenciais do cliente

Para provar a sua identidade, os pedidos confidenciais de clientes trocam um segredo com a AD Azure. O segredo pode ser:
- Um segredo de cliente (senha de aplicação).
- Um certificado, que é utilizado para construir uma afirmação assinada contendo reclamações padrão.

Este segredo também pode ser uma afirmação assinada diretamente.

MSAL.NET tem quatro métodos para fornecer credenciais ou afirmações à aplicação confidencial do cliente:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Embora seja possível `WithClientAssertion()` utilizar a API para adquirir fichas para o cliente confidencial, não recomendamos usá-lo por padrão, uma vez que é mais avançado e é projetado para lidar com cenários muito específicos que não são comuns. A `.WithCertificate()` utilização da API permitirá que MSAL.NET o consiga. Esta api oferece-lhe a capacidade de personalizar o seu `.WithCertificate()` pedido de autenticação se necessário, mas a afirmação predefinida criada será suficiente para a maioria dos cenários de autenticação. Esta API também pode ser usada como uma suver em alguns cenários em que MSAL.NET não realiza a operação de assinatura internamente.

### <a name="signed-assertions"></a>Afirmações assinadas

Uma afirmação de cliente assinada assume a forma de um JWT assinado com a carga útil contendo os pedidos de autenticação exigidos pela Azure AD, Base64 codificado. Para utilizá-lo:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

As alegações esperadas pela Azure AD são:

Tipo de afirmação | Valor | Descrição
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | A alegação "aud" (audiência) identifica os destinatários para os quais o JWT se destina (aqui Azure AD) Ver [RFC 7519, Secção 4.1.3]
exp | Thu jun 27 2019 15:04:17 GMT+0200 (Hora do Dia do Romance) | A alegação "exp" (tempo de validade) identifica o tempo de validade em ou após o qual o JWT NÃO deve ser aceite para processamento. Ver [RFC 7519, Secção 4.1.4]
iss | {ClientID} | A alegação "iss" (emitente) identifica o principal que emitiu o JWT. O processamento desta alegação é específico da aplicação. O valor "iss" é uma cadeia sensível a casos contendo um valor StringOrURI. [RFC 7519, Secção 4.1.1]
jti | (um Guia) | A alegação "jti" (JWT ID) fornece um identificador único para o JWT. O valor identificador DEVE ser atribuído de forma a garantir que existe uma probabilidade insignificante de que o mesmo valor será acidentalmente atribuído a um objeto de dados diferente; se a aplicação utilizar vários emitentes, as colisões DEVEM ser prevenidas entre os valores produzidos também por diferentes emitentes. A alegação "jti" pode ser usada para evitar que o JWT seja repetido. O valor "jti" é uma corda sensível a casos. [RFC 7519, Secção 4.1.7]
nbf | Thu jun 27 2019 14:54:17 GMT+0200 (Hora do Dia do Romance) | A alegação "nbf" (não antes) identifica o tempo anterior ao qual o JWT NÃO deve ser aceite para processamento. [RFC 7519, Secção 4.1.5]
sub | {ClientID} | A alegação "sub" (sujeito) identifica o objeto do JWT. As alegações num JWT são normalmente declarações sobre o assunto. O valor do assunto DEVE ser traçado para ser localmente único no contexto do emitente ou ser globalmente único. O ver [RFC 7519, Secção 4.1.2]

Aqui está um exemplo de como elaborar estas afirmações:

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

Aqui está como criar uma afirmação de cliente assinada:

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Método alternativo

Também tem a opção de utilizar o [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) para criar a afirmação para si. O código será mais elegante, como mostra o exemplo abaixo:

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

Assim que tiver a sua afirmação de cliente assinada, pode usá-la com os apis MSAL, como mostrado abaixo.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>ComReclamações com Clientes

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`por predefinição produzirá uma afirmação assinada contendo as reclamações esperadas pela Azure AD mais alegações adicionais de clientes que pretende enviar. Aqui está um código de snippet sobre como fazê-lo.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Se uma das reclamações no dicionário que você passa é a mesma que uma das reclamações obrigatórias, o valor da reclamação adicional será tomado em consideração. Irá anular as alegações calculadas por MSAL.NET.

Se quiser apresentar as suas próprias reclamações, incluindo as reclamações obrigatórias esperadas pela Azure AD, passe `false` para o `mergeWithDefaultClaims` parâmetro.
