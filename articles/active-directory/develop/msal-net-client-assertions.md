---
title: Asserções de cliente (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as declarações de cliente assinadas com suporte para aplicativos cliente confidenciais na biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
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
ms.openlocfilehash: 3d73e803a31867bedbd0ff069b8c9321257b78cb
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695573"
---
# <a name="confidential-client-assertions"></a>Asserções confidenciais do cliente

Para provar sua identidade, os aplicativos cliente confidenciais trocam um segredo com o Azure AD. O segredo pode ser:
- Um segredo do cliente (senha do aplicativo).
- Um certificado, que é usado para criar uma asserção assinada contendo declarações padrão.

Esse segredo também pode ser uma declaração assinada diretamente.

O MSAL.NET tem quatro métodos para fornecer credenciais ou asserções ao aplicativo cliente confidencial:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Embora seja possível usar a API `WithClientAssertion()` para adquirir tokens para o cliente confidencial, não recomendamos usá-lo por padrão, pois ele é mais avançado e é projetado para lidar com cenários muito específicos que não são comuns. Usar a API de `.WithCertificate()` permitirá que o MSAL.NET manipule isso para você. Essa API oferece a capacidade de personalizar sua solicitação de autenticação, se necessário, mas a asserção padrão criada por `.WithCertificate()` será suficiente para a maioria dos cenários de autenticação. Essa API também pode ser usada como uma solução alternativa em alguns cenários em que o MSAL.NET falha ao executar a operação de assinatura internamente.

### <a name="signed-assertions"></a>Asserções assinadas

Uma declaração de cliente assinada assume a forma de um JWT assinado com a carga que contém as declarações de autenticação necessárias exigidas pelo Azure AD, codificada em base64. Para utilizá-lo:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

As declarações esperadas pelo Azure AD são:

Tipo de afirmação | Valor | Descrição
---------- | ---------- | ----------
aud | https://login.microsoftonline.com/{tenantId}/v2.0 | A alegação "aud" (audiência) identifica os destinatários para os quais o JWT se destina (aqui Azure AD) Ver [RFC 7519, Secção 4.1.3]
exp | Qui Jun 27 2019 15:04:17 GMT + 0200 (horário de Verão de romance) | A declaração "exp" (tempo de expiração) identifica o tempo de expiração em ou após o qual o JWT não deve ser aceito para processamento. Ver [RFC 7519, Secção 4.1.4]
iss | ClientID | A declaração "ISS" (emissor) identifica a entidade de segurança que emitiu o JWT. O processamento dessa declaração é específico do aplicativo. O valor "ISS" é uma cadeia de caracteres que diferencia maiúsculas de minúsculas contendo um valor StringOrURI. [RFC 7519, Secção 4.1.1]
jti | (um GUID) | A declaração "JTI" (ID JWT) fornece um identificador exclusivo para o JWT. O valor do identificador deve ser atribuído de maneira que garanta que haja uma probabilidade insignificante de que o mesmo valor será acidentalmente atribuído a um objeto de dados diferente; Se o aplicativo usar vários emissores, as colisões deverão ser evitadas entre os valores produzidos por diferentes emissores também. A declaração "JTI" pode ser usada para impedir que o JWT seja reproduzido. O valor "JTI" é uma cadeia de caracteres que diferencia maiúsculas de minúsculas. [RFC 7519, Secção 4.1.7]
NBF | Qui Jun 27 2019 14:54:17 GMT + 0200 (horário de Verão de romance) | A declaração "NBF" (não antes) identifica o tempo antes do qual o JWT não deve ser aceito para processamento. [RFC 7519, Secção 4.1.5]
projeto | ClientID | A declaração "sub" (Subject) identifica o assunto do JWT. As declarações em um JWT normalmente são instruções sobre o assunto. O valor da entidade deve ter o escopo definido para ser local exclusivo no contexto do emissor ou ser globalmente exclusivo. O ver [RFC 7519, Secção 4.1.2]

Aqui está um exemplo de como criar essas declarações:

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

Aqui está como criar uma declaração de cliente assinada:

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

Você também tem a opção de usar [Microsoft. IdentityModel. JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) para criar a asserção para você. O código será mais elegante, conforme mostrado no exemplo abaixo:

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

Depois de ter a declaração de cliente assinada, você pode usá-la com as APIs MSAL, conforme mostrado abaixo.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`, por padrão, produzirá uma asserção assinada contendo as declarações esperadas pelo Azure AD, além de declarações de cliente adicionais que você deseja enviar. Aqui está um trecho de código sobre como fazer isso.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Se uma das declarações no dicionário que você passa for igual a uma das declarações obrigatórias, o valor da declaração adicional será levado em conta. Ele substituirá as declarações computadas por MSAL.NET.

Se você quiser fornecer suas próprias declarações, incluindo as declarações obrigatórias esperadas pelo Azure AD, passe `false` para o parâmetro `mergeWithDefaultClaims`.
