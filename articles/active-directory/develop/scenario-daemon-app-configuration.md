---
title: Aplicativo de daemon chamando APIs Web (configuração de aplicativo)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo daemon que chama APIs da Web (configuração de aplicativo)
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
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 394137a1b7901a3272e36f6a6d74944b87f30082
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056491"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Aplicativo daemon que chama a configuração de código de APIs da Web

Saiba como configurar o código para seu aplicativo daemon que chama APIs da Web.

## <a name="msal-libraries-supporting-daemon-apps"></a>Bibliotecas de MSAL com suporte a aplicativos de daemon

As bibliotecas da Microsoft que oferecem suporte a aplicativos daemon são:

  Biblioteca MSAL | Descrição
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | As plataformas com suporte para criar um aplicativo daemon são .NET Framework e plataformas .NET Core (não UWP, Xamarin. iOS e Xamarin. Android, pois essas plataformas são usadas para criar aplicativos cliente públicos)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Desenvolvimento em andamento-em visualização pública
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Desenvolvimento em andamento-em visualização pública

## <a name="configuration-of-the-authority"></a>Configuração da autoridade

Considerando que os aplicativos de daemon não usam permissões delegadas, mas permissões de aplicativo, seu *tipo de conta com suporte* não pode ser *contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)* . Na verdade, não há nenhum administrador de locatários para conceder consentimento ao aplicativo de daemon para contas pessoais da Microsoft. Você precisará escolher *contas em minha organização* ou *contas em qualquer organização*.

Portanto, a autoridade especificada na configuração do aplicativo deve ser o locatário-Ed (especificando uma ID de locatário ou um nome de domínio associado à sua organização).

Se você for um ISV e quiser fornecer uma ferramenta multilocatário, poderá usar `organizations`o. Mas tenha em mente que você também precisará explicar aos seus clientes como conceder o consentimento do administrador. Consulte [solicitando consentimento para um locatário inteiro](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) para obter detalhes. Há, atualmente, uma limitação no MSAL: `organizations` só é permitido quando as credenciais do cliente são um segredo do aplicativo (não um certificado).

## <a name="application-configuration-and-instantiation"></a>Configuração e instanciação do aplicativo

Em bibliotecas MSAL, as credenciais do cliente (segredo ou certificado) são passadas como um parâmetro da construção do aplicativo cliente confidencial.

> [!IMPORTANT]
> Mesmo que seu aplicativo seja um aplicativo de console em execução como um serviço, se for um aplicativo de daemon, ele precisará ser um aplicativo cliente confidencial.

### <a name="configuration-file"></a>Arquivo de configuração

O arquivo de configuração define:

- a autoridade ou a instância de nuvem e a tenantid
- o ClientID que você obteve do registro do aplicativo
- um segredo do cliente ou um certificado
 
# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[appSettings. JSON](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) da amostra do [daemon do console do .NET Core](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) .

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Você fornece um clientSecret ou um CertificateName. Ambas as configurações são exclusivas.

# <a name="pythontabpython"></a>[Python](#tab/python)

Ao criar um cliente confidencial com segredos do cliente, o arquivo de configuração seria como essa extração do exemplo [confidential_client_secret_sample. py # L4-L15](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/0.6.1/sample/confidential_client_secret_sample.py#L4-L15) no github.

```Json
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "scope": ["https://graph.microsoft.com/.default"],
    "secret": "The secret generated by AAD during your confidential app registration"
}
```

Ao criar um cliente confidencial com certificados, será como essa extração do exemplo [confidential_client_certificate_sample. py # L4-L15](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/4b34fd660e5574eb876cfba63a1b927ae375efd6/sample/confidential_client_certificate_sample.py#L4-L15) no github.

```Json
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "scope": ["https://graph.microsoft.com/.default"],
    "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
    "private_key_file": "filename.pem"
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Aqui está a classe usada em exemplos de dev msal4j para configurar os exemplos: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
public class TestData {

    final static String TENANT_SPECIFIC_AUTHORITY = "https://login.microsoftonline.com/<TenantId>/";
    final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
    final static String CONFIDENTIAL_CLIENT_ID = "";
    final static String CONFIDENTIAL_CLIENT_SECRET = "";
}
```

Você fornece um CONFIDENTIAL_CLIENT_ID ou um CONFIDENTIAL_CLIENT_SECRET. Ambas as configurações são exclusivas.

---

### <a name="instantiation-of-the-msal-application"></a>Instanciação do aplicativo MSAL

Para criar uma instância do aplicativo MSAL, você precisa:

- Adicionar, referenciar ou importar o pacote MSAL (dependendo do idioma)
- Em seguida, a construção será diferente dependendo se você estiver usando os certificados ou os segredos do cliente (ou, como um cenário avançado, as asserções assinadas)

O aplicativo daemon será apresentado por um`IConfidentialClientApplication`

#### <a name="reference-the-package"></a>Referenciar o pacote

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Adicione o pacote NuGet [Microsoft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) ao seu aplicativo.
No MSAL.net, o aplicativo cliente confidencial é representado pela `IConfidentialClientApplication` interface.
Usar o namespace MSAL.NET no código-fonte

```CSharp
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

#### <a name="instantiate-the-confidential-client-application-with-client-secrets"></a>Criar uma instância do aplicativo cliente confidencial com segredos do cliente

Este é o código para instanciar o aplicativo cliente confidencial com um segredo do cliente:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
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

#### <a name="instantiate-the-confidential-client-application-with-client-certificate"></a>Criar uma instância do aplicativo cliente confidencial com o certificado do cliente

Este é o código para criar um aplicativo com um certificado:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
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

Em MSAL. Java há dois construtores para criar uma instância do aplicativo cliente confidencial com certificados:

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

#### <a name="advanced-scenario---instantiate-the-confidential-client-application-with-client-assertions"></a>Cenário avançado – instanciar o aplicativo cliente confidencial com declarações de cliente

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Em vez de um segredo do cliente ou um certificado, o aplicativo cliente confidencial também pode provar sua identidade usando as declarações do cliente.

MSAL.NET tem dois métodos para fornecer asserções assinadas ao aplicativo cliente confidencial:

- `.WithClientAssertion()`
- `.WithClientClaims()`

Ao usar `WithClientAssertion`o, você precisa fornecer um JWT assinado. Este cenário avançado é detalhado em [asserções do cliente](msal-net-client-assertions.md)

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Quando você usar `WithClientClaims`, o MSAL.net computará uma asserção assinada contendo as declarações esperadas pelo Azure ad mais declarações de cliente adicionais que você deseja enviar.
Aqui está um trecho de código sobre como fazer isso:

```CSharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

Novamente, para obter detalhes, consulte [declarações do cliente](msal-net-client-assertions.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

No MSAL Python, você pode fornecer declarações de cliente usando as declarações que serão assinadas por `ConfidentialClientApplication`essa chave privada.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {“client_ip”: “x.x.x.x”}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Para obter detalhes, consulte MSAL. Documentação de referência do Python para [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)

# <a name="javatabjava"></a>[Java](#tab/java)

msal4j está em visualização pública. As declarações assinadas ainda não têm suporte

---

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aplicativo de daemon-adquirindo tokens para o aplicativo](./scenario-daemon-acquire-token.md)
