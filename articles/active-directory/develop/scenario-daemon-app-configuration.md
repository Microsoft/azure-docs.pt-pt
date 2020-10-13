---
title: Configure aplicações daemon que chamam APIs web - Plataforma de identidade da Microsoft Rio Azure
description: Saiba como configurar o código para a sua aplicação Daemon que chama APIs web (configuração de aplicação)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/19/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 8e065651a5527c0ab425614197ce128325454942
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91257678"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>App Daemon que chama APIs web - configuração de código

Saiba como configurar o código para a sua aplicação daemon que chama APIs web.

## <a name="msal-libraries-that-support-daemon-apps"></a>Bibliotecas MSAL que suportam apps daemon

Estas bibliotecas da Microsoft suportam aplicações daemon:

  Biblioteca MSAL | Descrição
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | As plataformas .NET Framework e .NET Core são suportadas para a construção de aplicações daemon. (UWP, Xamarin.iOS e Xamarin.Android não são suportados porque essas plataformas são usadas para construir aplicações de clientes públicos.)
  ![Python](media/sample-v2-code/logo_python.png) <br/> Pitão MSAL | Apoio a aplicações daemon em Python.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Apoio a aplicações daemon em Java.

## <a name="configure-the-authority"></a>Configure a autoridade

As aplicações da Daemon usam permissões de aplicação em vez de permissões delegadas. Assim, o tipo de conta suportada não pode ser uma conta em qualquer diretório organizacional ou qualquer conta pessoal da Microsoft (por exemplo, Skype, Xbox, Outlook.com). Não há nenhum administrador inquilino que conceda consentimento a um pedido de daemon para uma conta pessoal da Microsoft. Terá de escolher *contas na minha organização* ou *contas em qualquer organização.*

Assim, a autoridade especificada na configuração da aplicação deve ser arrendada (especificando um ID do inquilino ou um nome de domínio associado à sua organização).

Se é um ISV e quer fornecer uma ferramenta multitenant, pode usar `organizations` . Mas tenha em mente que também terá de explicar aos seus clientes como conceder o consentimento administrativo. Para mais informações, consulte [o Pedido de Consentimento para um inquilino inteiro.](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) Além disso, existe atualmente uma limitação no MSAL: `organizations` só é permitido quando as credenciais do cliente são um segredo de aplicação (não um certificado).

## <a name="configure-and-instantiate-the-application"></a>Configure e instantaneamente a aplicação

Nas bibliotecas DAMAL, as credenciais do cliente (segredo ou certificado) são passadas como parâmetro da construção de aplicações confidenciais de clientes.

> [!IMPORTANT]
> Mesmo que a sua aplicação seja uma aplicação de consola que funciona como um serviço, se for uma aplicação daemon, tem de ser uma aplicação confidencial do cliente.

### <a name="configuration-file"></a>Ficheiro de configuração

O ficheiro de configuração define:

- O caso da nuvem e a identificação do inquilino, que juntos compõem a *autoridade.*
- A identificação do cliente que obteve do registo de inscrição.
- Ou um segredo de cliente ou um certificado.

# <a name="net"></a>[.NET](#tab/dotnet)

Aqui está um exemplo de definição da configuração numa [*appsettings.jsno*](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) ficheiro. Este exemplo é retirado da amostra de código [daemon da consola .NET Core](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) no GitHub.

```json
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Fornece um `ClientSecret` ou um `CertificateName` . Estas configurações são exclusivas.

# <a name="python"></a>[Python](#tab/python)

Quando se constrói um cliente confidencial com segredos de clientes, o [parameters.jsno](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) ficheiro config na amostra [do daemon Python](https://github.com/Azure-Samples/ms-identity-python-daemon) é o seguinte:

```Json
{
  "authority": "https://login.microsoftonline.com/<your_tenant_id>",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Quando se constrói um cliente confidencial com certificados, o [parameters.jsno](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) ficheiro config na amostra [do daemon Python](https://github.com/Azure-Samples/ms-identity-python-daemon) é o seguinte:

```Json
{
  "authority": "https://login.microsoftonline.com/<your_tenant_id>",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
 private final static String CLIENT_ID = "";
 private final static String AUTHORITY = "https://login.microsoftonline.com/<tenant>/";
 private final static String CLIENT_SECRET = "";
 private final static Set<String> SCOPE = Collections.singleton("https://graph.microsoft.com/.default");
```

---

### <a name="instantiate-the-msal-application"></a>Instantaneamente a aplicação MSAL

Para instantaneaizar a aplicação MSAL, é necessário adicionar, fazer referência ou importar o pacote MSAL (dependendo da língua).

A construção é diferente, dependendo se você está usando segredos ou certificados de cliente (ou, como um cenário avançado, afirmações assinadas).

#### <a name="reference-the-package"></a>Referenciar o pacote

Consulte o pacote MSAL no seu código de aplicação.

# <a name="net"></a>[.NET](#tab/dotnet)

Adicione o pacote [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet à sua aplicação e, em seguida, adicione uma `using` diretiva no seu código para a referenciar.

Em MSAL.NET, a aplicação confidencial do cliente é representada pela `IConfidentialClientApplication` interface.

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="python"></a>[Python](#tab/python)

```python
import msal
import json
import sys
import logging
```

# <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.IClientCredential;
import com.microsoft.aad.msal4j.MsalException;
import com.microsoft.aad.msal4j.SilentParameters;
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>Instantaneamente a aplicação confidencial do cliente com um segredo de cliente

Aqui está o código para instantaneaizar a aplicação confidencial do cliente com um segredo de cliente:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

É `Authority` uma concatenação da instância da nuvem e da identificação do inquilino, por exemplo `https://login.microsoftonline.com/contoso.onmicrosoft.com` ou `https://login.microsoftonline.com/eb1ed152-0000-0000-0000-32401f3f9abd` . Na *appsettings.jsno* ficheiro indicado na secção [de ficheiros De Configuração,](#configuration-file) estes são representados pelos `Instance` valores e `Tenant` valores, respectivamente.

Na amostra de código do corte anterior, `Authority` é uma propriedade da classe  [AuthenticationConfig,](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/ffc4a9f5d9bdba5303e98a1af34232b434075ac7/1-Call-MSGraph/daemon-console/AuthenticationConfig.cs#L61-L70) e é definida como tal:

```csharp
/// <summary>
/// URL of the authority
/// </summary>
public string Authority
{
    get
    {
        return String.Format(CultureInfo.InvariantCulture, Instance, Tenant);
    }
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>Instantaneamente o pedido de cliente confidencial com um certificado de cliente

Aqui está o código para construir uma aplicação com um certificado:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

Na MSAL Java, existem dois construtores para instantaneaizar o pedido de cliente confidencial com certificados:

```Java

InputStream pkcs12Certificate = ... ; /* Containing PCKS12-formatted certificate*/
string certificatePassword = ... ;    /* Contains the password to access the certificate */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(pkcs12Certificate, certificatePassword);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

ou

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(key, publicCertificate);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Cenário avançado: Instantiizar a aplicação confidencial do cliente com afirmações do cliente

# <a name="net"></a>[.NET](#tab/dotnet)

Em vez de um segredo de cliente ou de um certificado, a aplicação confidencial do cliente também pode provar a sua identidade utilizando afirmações do cliente.

MSAL.NET tem dois métodos para fornecer afirmações assinadas à aplicação confidencial do cliente:

- `.WithClientAssertion()`
- `.WithClientClaims()`

Quando `WithClientAssertion` utilizar, tem de fornecer um JWT assinado. Este cenário avançado é detalhado nas [afirmações do Cliente.](msal-net-client-assertions.md)

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Quando `WithClientClaims` utilizar, MSAL.NET produzirá uma afirmação assinada que contenha as reclamações esperadas pela Azure AD, além de alegações adicionais do cliente que pretende enviar.
Este código mostra como fazer isto:

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();
```

Mais uma vez, para mais detalhes, consulte [as afirmações do Cliente.](msal-net-client-assertions.md)

# <a name="python"></a>[Python](#tab/python)

Na MSAL Python, pode fornecer reclamações ao cliente utilizando as reclamações que serão assinadas por esta `ConfidentialClientApplication` chave privada.

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Para mais informações, consulte a documentação de referência da MSAL Python para [ConfidencialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromClientAssertion(assertion);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

## <a name="next-steps"></a>Passos seguintes

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon app - aquisição de fichas para a app](./scenario-daemon-acquire-token.md?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon app - aquisição de fichas para a app](./scenario-daemon-acquire-token.md?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon app - aquisição de fichas para a app](./scenario-daemon-acquire-token.md?tabs=java)

---