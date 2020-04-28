---
title: Inicializar MSAL.NET aplicações de clientes / Azure
titleSuffix: Microsoft identity platform
description: Saiba sobre a inicialização de aplicações de clientes públicos e confidenciais utilizando a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 57ce6ab31421cd4016f7e204eeabce82f2f7e6a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77083980"
---
# <a name="initialize-client-applications-using-msalnet"></a>Inicializar aplicações de clientes usando MSAL.NET
Este artigo descreve a inicialização de aplicações de clientes públicos e confidenciais utilizando a Microsoft Authentication Library para .NET (MSAL.NET).  Para saber mais sobre os tipos de aplicação do cliente e as opções de configuração de aplicações, leia a [visão geral](msal-client-applications.md).

Com MSAL.NET 3.x, a forma recomendada de instantaneamente uma `PublicClientApplicationBuilder` `ConfidentialClientApplicationBuilder`aplicação é utilizando os construtores de aplicações: e . Oferecem um mecanismo poderoso para configurar a aplicação a partir do código, ou a partir de um ficheiro de configuração, ou até misturando ambas as abordagens.

## <a name="prerequisites"></a>Pré-requisitos
Antes de inicializar uma aplicação, primeiro é necessário [registá-la](quickstart-register-app.md) para que a sua aplicação possa ser integrada na plataforma de identidade da Microsoft.  Após o registo, poderá necessitar das seguintes informações (que podem ser encontradas no portal Azure):

- O ID do cliente (uma corda que representa um GUID)
- O URL do fornecedor de identidade (nome ou a instância) e o público de inscrição para a sua aplicação. Estes dois parâmetros são coletivamente conhecidos como autoridade.
- O ID do inquilino se estiver a escrever uma linha de aplicação de negócios exclusivamente para a sua organização (também nomeada candidatura de inquilino único).
- O segredo da aplicação (string secreto do cliente) ou certificado (do tipo X509Certificate2) se for uma aplicação de cliente confidencial.
- Para aplicações web, e por vezes para aplicações de clientes públicos (em particular quando a sua aplicação precisa de usar um corretor), também terá definido o redirectUri onde o fornecedor de identidade irá contactar de volta a sua aplicação com os tokens de segurança.

## <a name="ways-to-initialize-applications"></a>Formas de inicializar aplicações
Existem muitas maneiras diferentes de instantaneamente aplicações de clientes.

### <a name="initializing-a-public-client-application-from-code"></a>Inicializar uma aplicação de cliente público a partir do código

O código seguinte instantaneamente uma aplicação de cliente público, utilizadores de login na nuvem pública do Microsoft Azure, com o seu trabalho e contas escolares, ou as suas contas pessoais da Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Inicializar uma aplicação confidencial do cliente a partir do código

Da mesma forma, o código seguinte instantaneamente uma aplicação `https://myapp.azurewebsites.net`confidencial (uma aplicação Web localizada em ) manipulando fichas de utilizadores na nuvem pública do Microsoft Azure, com o seu trabalho e contas escolares, ou as suas contas pessoais da Microsoft. O pedido é identificado com o fornecedor de identidade através da partilha de um segredo de cliente:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Como deve saber, na produção, em vez de usar um segredo de cliente, talvez queira partilhar com a Azure AD um certificado. O código seria então o seguinte:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Inicializar uma aplicação de cliente público a partir de opções de configuração

O código seguinte instantaneamente uma aplicação do cliente público a partir de um objeto de configuração, que poderia ser preenchido programáticamente ou lido a partir de um ficheiro de configuração:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Inicializar uma aplicação confidencial do cliente a partir de opções de configuração

O mesmo tipo de padrão aplica-se a aplicações confidenciais de clientes. Também pode adicionar outros `.WithXXX` parâmetros utilizando modificadores (aqui um certificado).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Modificadores de construtores

Nos fragmentos de código que utilizam `.With` construtores de aplicações, uma série `.WithCertificate` `.WithRedirectUri`de métodos pode ser aplicado como modificadores (por exemplo, e ). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modificadores comuns a aplicações públicas e confidenciais de clientes

Os modificadores que pode definir num cliente público ou no construtor de aplicações confidenciais do cliente são:

|Modificador | Descrição|
|--------- | --------- |
|`.WithAuthority()`7 sobreposições | Define a autoridade de incumprimento da aplicação a uma autoridade da AD Azure, com a possibilidade de escolher a Nuvem Azure, o público, o inquilino (ID do inquilino ou nome de domínio), ou fornecer diretamente a autoridade URI.|
|`.WithAdfsAuthority(string)` | Define a autoridade por incumprimento da aplicação como uma autoridade ADFS.|
|`.WithB2CAuthority(string)` | Define a autoridade por incumprimento da aplicação como uma autoridade Azure AD B2C.|
|`.WithClientId(string)` | Substitui a identificação do cliente.|
|`.WithComponent(string)` | Define o nome da biblioteca usando MSAL.NET (por razões de telemetria). |
|`.WithDebugLoggingCallback()` | Se for chamado, `Debug.Write` a aplicação irá simplesmente ligar para permitir a depuração de vestígios. Consulte [o Registo para](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) obter mais informações.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Defina os parâmetros de consulta extra do nível de candidatura que serão enviados em todos os pedidos de autenticação. Isto é demasiado diúguável a cada nível do método de aquisição de fichas (com o mesmo `.WithExtraQueryParameters pattern`).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Permite cenários avançados como configurar para um proxy HTTP, ou forçar o MSAL a usar um httpClient particular (por exemplo, em ASP.NET aplicações web/APIs do Core).|
|`.WithLogging()` | Se for chamado, o pedido chamará uma chamada com vestígios de depuração. Consulte [o Registo para](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) obter mais informações.|
|`.WithRedirectUri(string redirectUri)` | Substitui o URI de redirecionamento padrão. No caso de aplicações de clientes públicos, isso será útil para cenários envolvendo o corretor.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Define o delegado usado para enviar telemetria.|
|`.WithTenantId(string tenantId)` | Substitui a identificação do inquilino, ou a descrição do inquilino.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modificadores específicos das aplicações Xamarin.iOS

Os modificadores que pode definir num construtor de aplicações de cliente público em Xamarin.iOS são:

|Modificador | Descrição|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Apenas Xamarin.iOS**: Define o grupo de segurança da cadeia de chaves iOS (para a persistência do cache).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modificadores específicos de aplicações confidenciais de clientes

Os modificadores que pode definir num construtor de aplicações de cliente confidencial são:

|Modificador | Descrição|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Define o certificado identificando o pedido com a AD Azure.|
|`.WithClientSecret(string clientSecret)` | Define o segredo do cliente (palavra-passe da aplicação) identificando a aplicação com a AD Azure.|

Estes modificadores são mutuamente exclusivos. Se fornecer os dois, a MSAL lançará uma exceção significativa.

### <a name="example-of-usage-of-modifiers"></a>Exemplo de utilização de modificadores

Vamos assumir que a sua aplicação é uma aplicação de linha de negócio, que é apenas para a sua organização.  Então pode escrever:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Onde se torna interessante é que a programação das nuvens nacionais já se simplificou. Se quiser que a sua candidatura seja uma candidatura multi-arrendatária numa nuvem nacional, pode escrever, por exemplo:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Existe também uma sobreposição para a ADFS (a ADFS 2019 não é atualmente suportada):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Finalmente, se você é um desenvolvedor Azure AD B2C, você pode especificar o seu inquilino assim:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
