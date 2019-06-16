---
title: O daemon aplicação chamadas das APIs web (configuração da aplicação) - plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de daemon que chamadas de web APIs (configuração de aplicações)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd2da6baecdce3ab85a45347f27f573bf814445d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055763"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Aplicação de daemon que chamadas de web APIs - configuração de código

Saiba como configurar o código para a sua aplicação de daemon que chamadas de APIs web.

## <a name="msal-libraries-supporting-daemon-apps"></a>Aplicações de daemon suporte de bibliotecas de MSAL

As bibliotecas da Microsoft que suporta aplicações daemon são:

  Biblioteca MSAL | Descrição
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | As plataformas suportadas para criar um aplicativo de daemon são plataformas .NET Framework e .NET Core (não UWP, xamarin. IOS e xamarin. Android como essas plataformas são utilizados para criar aplicativos cliente público)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Desenvolvimento em curso - em pré-visualização pública
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Desenvolvimento em curso - em pré-visualização pública

## <a name="configuration-of-the-authority"></a>Configuração da autoridade de

Uma vez que os aplicativos de daemon não utilizam permissões delegadas, mas as permissões de aplicação, seus *tipo de conta suportado* não pode ser *(de contas Microsoft pessoa e de contas em qualquer diretório organizacional Por exemplo, o Skype, Xbox, Outlook.com)* . Na verdade, não há nenhum administrador de inquilino para conceder o consentimento para a aplicação de daemon de contas pessoais da Microsoft. Precisará escolher *contas na minha organização* ou *contas em qualquer organização*.

Portanto, a autoridade especificada na configuração da aplicação deve ser inquilino-ed (especificar um ID de inquilino ou um nome de domínio associados com a sua organização).

Se for um ISV e pretende fornecer uma ferramenta de multi-inquilino, pode utilizar `organizations`. Mas tenha em atenção que também será necessário explicar aos seus clientes, como pode conceder o consentimento de administrador. Ver [pedir consentimento para um inquilino todo](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) para obter detalhes. Também existe atualmente uma limitação no MSAL que `organizations` só é permitida quando as credenciais de cliente são um segredo de aplicação (não um certificado). Consulte [MSAL.NET bug #891](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/891)

## <a name="application-configuration-and-instantiation"></a>Configuração da aplicação e a instanciação

Em bibliotecas MSAL, as credenciais de cliente (segredo ou certificado) são transmitidas como um parâmetro de construção de aplicativo cliente confidencial.

> [!IMPORTANT]
> Mesmo que seu aplicativo é uma aplicação de consola em execução como um serviço, se ele é um aplicativo de daemon ele precisa ser um aplicativo de cliente confidencial.

### <a name="msalnet"></a>MSAL.NET

Adicionar a [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) pacote NuGet ao seu aplicativo.

Utilizar o espaço de nomes MSAL.NET

```CSharp
using Microsoft.Identity.Client;
```

O aplicativo de daemon irá ser apresentado por um `IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

Aqui está o código para criar uma aplicação com um segredo de aplicação:

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

Aqui está o código para criar uma aplicação com um certificado:

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

### <a name="msalpython"></a>MSAL.Python

```Python
# Create a preferably long-lived app instance which maintains a token cache.

app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache

    )
```

### <a name="msaljava"></a>MSAL.Java

```Java
PrivateKey key = getPrivateKey();
X509Certificate publicCertificate = getPublicCertificate();

// create clientCredential with public and private key
IClientCredential credential = ClientCredentialFactory.create(key, publicCertificate);

ConfidentialClientApplication cca = ConfidentialClientApplication
  .builder(CLIENT_ID, credential)
  .authority(AUTHORITY_MICROSOFT)
  .build();
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aplicação de daemon - aquisição de tokens para a aplicação](./scenario-daemon-acquire-token.md)