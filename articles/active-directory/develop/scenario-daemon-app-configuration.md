---
title: Configure aplicativos daemon que chamam APIs web - plataforma de identidade Microsoft / Azure
description: Saiba como configurar o código para a sua aplicação daemon que chama APIs web (configuração de aplicações)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f857cfabfcacc5bb11e152a53fddee612c63d75b
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702441"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>App Daemon que chama APIs web - configuração de código

Saiba como configurar o código para a sua aplicação daemon que chama APIs web.

## <a name="msal-libraries-supporting-daemon-apps"></a>Bibliotecas MSAL que apoiam aplicações daemon

As bibliotecas da Microsoft que suportam aplicações daemon são:

  Biblioteca MSAL | Descrição
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | As plataformas suportadas para construir uma aplicação daemon são .NET Framework e .NET Core plataformas (não UWP, Xamarin.iOS e Xamarin.Android, uma vez que essas plataformas são usadas para construir aplicações de clientes públicos)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Suporte para aplicativos daemon em Python
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Suporte para aplicativos daemon em Java

## <a name="configuration-of-the-authority"></a>Configuração da Autoridade

Dado que as aplicações dadaon não usam permissões delegadas, mas permissões de aplicação, o seu tipo de *conta suportada* não pode ser *Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)* . Na verdade, não há nenhum administrador de inquilino para conceder o consentimento ao pedido de daemon para contas pessoais da Microsoft. Terá de escolher *contas na minha organização* ou *contas em qualquer organização.*

Por isso, a autoridade especificada na configuração da aplicação deve ser arrendada (especificando um ID do Arrendatário ou um nome de domínio associado à sua organização).

Se você é um ISV e quer fornecer uma ferramenta multi-inquilino, você pode usar `organizations`. Mas lembre-se que também terá de explicar aos seus clientes como conceder o consentimento da administração. Consulte [o consentimento solicitado para um inquilino inteiro](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) para obter detalhes. Também existe atualmente uma limitação na MSAL: `organizations` só é permitida quando as credenciais do cliente são um segredo de aplicação (não um certificado).

## <a name="application-configuration-and-instantiation"></a>Configuração e instantânea da aplicação

Nas bibliotecas mSAL, as credenciais do cliente (segredo ou certificado) são passadas como parâmetro da construção de aplicações confidenciais do cliente.

> [!IMPORTANT]
> Mesmo que a sua aplicação seja uma aplicação de consola que funciona como um serviço, se for uma aplicação daemon, tem de ser uma aplicação de cliente confidencial.

### <a name="configuration-file"></a>Arquivo de configuração

O ficheiro de configuração define:

- a autoridade ou a instância de nuvem e inquilinoId
- o ClientID que obteve do registo de candidatura
- ou um segredo de cliente, ou um certificado

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[appssettings.json](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) da amostra de [daemon da consola .NET Core.](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Ou fornece um clienteSecret ou um nome de certificado. Ambas as definições são exclusivas.

# <a name="pythontabpython"></a>[Python](#tab/python)

Ao construir um cliente confidencial com segredos de cliente, o ficheiro config de [parâmetros.json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) na amostra [Python Daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) é o seguinte.

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Ao construir um cliente confidencial com certificados, o ficheiro [config de parâmetros.json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) na amostra [Python Daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) é o seguinte.

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Aqui está a classe usada em exemplos de desenvolvimento do MSAL Java para configurar os exemplos: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
public class TestData {

    final static String TENANT_SPECIFIC_AUTHORITY = "https://login.microsoftonline.com/<TenantId>/";
    final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
    final static String CONFIDENTIAL_CLIENT_ID = "";
    final static String CONFIDENTIAL_CLIENT_SECRET = "";
}
```

---

### <a name="instantiation-of-the-msal-application"></a>Instantânea da aplicação MSAL

Para instantaneamente a aplicação MSAL, é necessário:

- adicionar, referir ou importar o pacote MSAL (dependendo da língua)
- Em seguida, a construção é diferente dependendo de se estiver a usar segredos ou certificados de cliente (ou, como um cenário avançado, afirmações assinadas)

#### <a name="reference-the-package"></a>Referência ao pacote

Consulte o pacote MSAL no seu código de aplicação.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Adicione o pacote [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet à sua aplicação.
Em MSAL.NET, a aplicação cliente confidencial é representada pela interface `IConfidentialClientApplication`.
Utilize MSAL.NET espaço de nome no código fonte

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
import msal
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
```

---

#### <a name="instantiate-the-confidential-client-application-with-client-secrets"></a>Instantie a aplicação confidencial do cliente com segredos do cliente

Aqui está o código para instantaneamente a aplicação confidencial do cliente com um segredo de cliente:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(TestData.CONFIDENTIAL_CLIENT_SECRET))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-client-certificate"></a>Instantie a aplicação cliente confidencial com certificado de cliente

Aqui está o código para construir um pedido com um certificado:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

Na MSAL. Java existem dois construtores para instantaneamente a aplicação confidencial do cliente com certificados:

```Java

InputStream pkcs12Certificate = ... ; /* containing PCKS12 formatted certificate*/
string certificatePassword = ... ;    /* contains the password to access the certificate */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(pkcs12Certificate, certificatePassword))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

ou

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(rsaPrivateKey, publicKeyCertificate))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="advanced-scenario---instantiate-the-confidential-client-application-with-client-assertions"></a>Cenário avançado - instantaneamente a aplicação confidencial do cliente com afirmações do cliente

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Em vez de um segredo de cliente ou um certificado, a aplicação confidencial do cliente também pode provar a sua identidade usando afirmações do cliente.

MSAL.NET tem dois métodos para fornecer afirmações assinadas à aplicação confidencial do cliente:

- `.WithClientAssertion()`
- `.WithClientClaims()`

Quando utilizar `WithClientAssertion`, precisa de fornecer um JWT assinado. Este cenário avançado é detalhado em [asserções do cliente](msal-net-client-assertions.md)

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Quando utilizar `WithClientClaims`, MSAL.NET calculará uma afirmação assinada contendo as reclamações esperadas pela Azure AD mais alegações adicionais de clientes que pretende enviar.
Aqui está um código de snippet sobre como fazê-lo:

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

Mais uma vez, para mais detalhes, consulte [as afirmações do Cliente.](msal-net-client-assertions.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

Na MSAL Python, pode fornecer reclamações de clientes usando as reclamações que serão assinadas por esta `ConfidentialClientApplication`chave privada.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Para mais detalhes, consulte a documentação de referência da MSAL Python para [a ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

# <a name="javatabjava"></a>[Java](#tab/java)

A MSAL Java está em pré-estreia pública. As afirmações assinadas ainda não são apoiadas.

---

## <a name="next-steps"></a>Passos seguintes

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [App Daemon - aquisição de tokens para a app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [App Daemon - aquisição de tokens para a app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [App Daemon - aquisição de tokens para a app](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
