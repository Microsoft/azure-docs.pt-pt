---
title: Configure aplicações daemon que chamam APIs web - Plataforma de identidade microsoft | Rio Azure
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
ms.openlocfilehash: 3c52d4d80fd3c77cff5e335967fc9d109212ce29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578435"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>App Daemon que chama APIs web - configuração de código

Saiba como configurar o código para a sua aplicação daemon que chama APIs web.

## <a name="microsoft-libraries-supporting-daemon-apps"></a>Bibliotecas da Microsoft que suportam apps daemon

As seguintes bibliotecas da Microsoft suportam aplicações daemon:

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="configure-the-authority"></a>Configure a autoridade

As aplicações da Daemon usam permissões de aplicação em vez de permissões delegadas. Assim, o tipo de conta suportada não pode ser uma conta em qualquer diretório organizacional ou qualquer conta pessoal da Microsoft (por exemplo, Skype, Xbox, Outlook.com). Não há nenhum administrador inquilino que conceda consentimento a um pedido de daemon para uma conta pessoal da Microsoft. Terá de escolher *contas na minha organização* ou *contas em qualquer organização.*

A autoridade especificada na configuração da aplicação deve ser arrendada (especificando um ID do inquilino ou um nome de domínio associado à sua organização).

Mesmo que pretenda fornecer uma ferramenta multitenante, deve utilizar um ID ou nome de domínio do inquilino, e **não** `common` ou com este `organizations` fluxo, porque o serviço não pode inferir de forma fiável qual o inquilino deve ser usado.

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

# <a name="java"></a>[Java](#tab/java)

```Java
 private final static String CLIENT_ID = "";
 private final static String AUTHORITY = "https://login.microsoftonline.com/<tenant>/";
 private final static String CLIENT_SECRET = "";
 private final static Set<String> SCOPE = Collections.singleton("https://graph.microsoft.com/.default");
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Os parâmetros de configuração da [amostra deNode.js daemon](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console/) estão localizados num ficheiro *.env:*

```Text 
# Credentials
TENANT_ID=Enter_the_Tenant_Info_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
```

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

---

### <a name="instantiate-the-msal-application"></a>Instantaneamente a aplicação MSAL

Para instantaneaizar a aplicação MSAL, adicionar, referir ou importar o pacote MSAL (dependendo da língua).

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

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Basta instalar as embalagens executando `npm install` na pasta onde residepackage.js *no* ficheiro. Em seguida, importar pacote **de nó msal:**

```JavaScript 
const msal = require('@azure/msal-node');
```

# <a name="python"></a>[Python](#tab/python)

```python
import msal
import json
import sys
import logging
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

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```JavaScript

const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + 'v1.0/users',
};

const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'],
};

const cca = new msal.ConfidentialClientApplication(msalConfig);
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

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

O pedido de amostra não implementa a inicialização com certificados no momento.

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

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Cenário avançado: Instantiizar a aplicação confidencial do cliente com afirmações do cliente

# <a name="net"></a>[.NET](#tab/dotnet)

Em vez de um segredo de cliente ou de um certificado, a aplicação confidencial do cliente também pode provar a sua identidade utilizando afirmações do cliente.

MSAL.NET tem dois métodos para fornecer afirmações assinadas à aplicação confidencial do cliente:

- `.WithClientAssertion()`
- `.WithClientClaims()`

Quando `WithClientAssertion` utilizar, forneça um JWT assinado. Este cenário avançado é detalhado nas [afirmações do Cliente.](msal-net-client-assertions.md)

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

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromClientAssertion(assertion);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A aplicação da amostra não implementa a inicialização com afirmações no momento.

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

---

## <a name="next-steps"></a>Passos seguintes

# <a name="net"></a>[.NET](#tab/dotnet)

Passe para o próximo artigo neste cenário, [adquira um símbolo para a app.](./scenario-daemon-acquire-token.md?tabs=dotnet)

# <a name="java"></a>[Java](#tab/java)

Passe para o próximo artigo neste cenário, [adquira um símbolo para a app.](./scenario-daemon-acquire-token.md?tabs=java)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Passe para o próximo artigo neste cenário, [adquira um símbolo para a app.](./scenario-daemon-acquire-token.md?tabs=nodejs)

# <a name="python"></a>[Python](#tab/python)

Passe para o próximo artigo neste cenário, [adquira um símbolo para a app.](./scenario-daemon-acquire-token.md?tabs=python)

---
