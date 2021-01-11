---
title: Afirmações do cliente (MSAL.NET) Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o suporte de afirmações de clientes assinados para aplicações confidenciais de clientes na Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/30/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: f1ff679bddf2afc355516f2a04b3307d4a260a5c
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063625"
---
# <a name="confidential-client-assertions"></a>Afirmações confidenciais do cliente

Para provar a sua identidade, os pedidos confidenciais de clientes trocam um segredo com a Azure AD. O segredo pode ser:
- Um segredo do cliente (senha de aplicação).
- Um certificado, que é usado para construir uma afirmação assinada contendo reivindicações padrão.

Este segredo também pode ser uma afirmação assinada diretamente.

MSAL.NET tem quatro métodos para fornecer credenciais ou afirmações à aplicação confidencial do cliente:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Embora seja possível utilizar a `WithClientAssertion()` API para adquirir fichas para o cliente confidencial, não recomendamos a sua utilização por padrão, uma vez que é mais avançada e é projetada para lidar com cenários muito específicos que não são comuns. A utilização da `.WithCertificate()` API permitirá que MSAL.NET manuseie isto por si. Esta api oferece-lhe a capacidade de personalizar o seu pedido de autenticação se necessário, mas a afirmação padrão criada pela will basta para a `.WithCertificate()` maioria dos cenários de autenticação. Esta API também pode ser usada como uma solução alternativa em alguns cenários onde MSAL.NET não realiza a operação de assinatura internamente.

### <a name="signed-assertions"></a>Afirmações assinadas

Uma afirmação assinada pelo cliente assume a forma de um JWT assinado com a carga útil contendo os pedidos de autenticação exigidos pela Azure AD, Base64 codificada. Para utilizá-lo:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

As [alegações esperadas pela Azure AD](active-directory-certificate-credentials.md) são:

Tipo de afirmação | Valor | Descrição
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | A alegação "aud" (audiência) identifica os destinatários a que o JWT se destina (aqui Azure AD) Ver [RFC 7519, Secção 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  Neste caso, o destinatário é o servidor de login (login.microsoftonline.com).
exp | 1601519414 | A alegação "exp" (tempo de validade) identifica o tempo de validade no ou após o qual o JWT NÃO DEVE ser aceite para processamento. Consulte [RFC 7519, Secção 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  Isto permite que a afirmação seja usada até lá, por isso mantenha-a curta - 5-10 minutos `nbf` depois, no máximo.  A Azure AD não coloca restrições na `exp` hora atual. 
iss | {ClientID} | A alegação "iss" (emitente) identifica o principal que emitiu o JWT, neste caso o seu pedido de cliente.  Utilize o ID da aplicação GUID.
jti | (um Guia) | A alegação "jti" (JWT ID) fornece um identificador único para o JWT. O valor do identificador DEVE ser atribuído de forma a garantir que existe uma probabilidade negligenciável de que o mesmo valor será acidentalmente atribuído a um objeto de dados diferente; se a aplicação utilizar vários emitentes, as colisões devem ser evitadas entre os valores produzidos também por diferentes emitentes. O valor "jti" é uma corda sensível a casos. [RFC 7519, Secção 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
nbf | 1601519114 | A alegação "nbf" (não antes) identifica o tempo anterior ao qual o JWT NÃO DEVE ser aceite para processamento. [RFC 7519, Secção 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  A utilização do tempo atual é apropriada. 
sub | {ClientID} | A alegação "sub" (sujeito) identifica o sujeito do JWT, neste caso também o seu pedido. Use o mesmo valor que `iss` . 

Aqui está um exemplo de como elaborar estas alegações:

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

Eis como criar uma afirmação assinada pelo cliente:

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

Também tem a opção de utilizar [Microsoft.IdentityModel.JsnoWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) para criar a afirmação para si. O código será mais elegante, como mostra o exemplo abaixo:

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

Assim que tiver a sua afirmação assinada pelo cliente, pode usá-la com o apis MSAL, como mostrado abaixo.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` por defeito produzirá uma afirmação assinada contendo as alegações esperadas pela Azure AD mais alegações adicionais do cliente que pretende enviar. Aqui está um código sobre como fazê-lo.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Se uma das reclamações no dicionário que você passar é a mesma que uma das reclamações obrigatórias, o valor da reclamação adicional será tomado em consideração. Irá anular as alegações calculadas por MSAL.NET.

Se quiser fornecer as suas próprias reclamações, incluindo as reclamações obrigatórias esperadas pela Azure AD, passe `false` para o `mergeWithDefaultClaims` parâmetro.
