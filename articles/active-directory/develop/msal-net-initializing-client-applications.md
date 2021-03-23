---
title: Inicializar MSAL.NET aplicações de clientes | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como rubricar aplicações de cliente público e cliente confidencial utilizando a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/18/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 7ff61811e8b736f8f6d104a253cfe5dc5e76c428
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771367"
---
# <a name="initialize-client-applications-using-msalnet"></a>Inicialize as aplicações do cliente utilizando MSAL.NET
Este artigo descreve a inicialização de aplicações de cliente público e cliente confidencial utilizando a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).  Para saber mais sobre os tipos de aplicação do cliente, consulte [o cliente público e aplicações confidenciais de clientes.](msal-client-applications.md)

Com MSAL.NET 3.x, a forma recomendada de instantaneaizar uma aplicação é utilizando os construtores de aplicações: `PublicClientApplicationBuilder` e `ConfidentialClientApplicationBuilder` . Oferecem um mecanismo poderoso para configurar a aplicação a partir do código, ou a partir de um ficheiro de configuração, ou mesmo misturando ambas as abordagens.

[Documentação de](/dotnet/api/microsoft.identity.client)  |  referência da API [Pacote no NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/)  |  [Código fonte da biblioteca](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)  |  [Amostras de código](sample-v2-code.md)

## <a name="prerequisites"></a>Pré-requisitos
Antes de rubricar uma aplicação, primeiro precisa [de a registar](quickstart-register-app.md) para que a sua aplicação possa ser integrada na plataforma de identidade da Microsoft.  Após o registo, poderá necessitar das seguintes informações (que podem ser encontradas no portal Azure):

- O ID do cliente (uma corda que representa um GUID)
- O URL do fornecedor de identidade (nomeado o caso) e o público de inscrição para a sua aplicação. Estes dois parâmetros são coletivamente conhecidos como a autoridade.
- O ID do inquilino se estiver a escrever uma linha de aplicação de negócio apenas para a sua organização (também nomeado pedido de inquilino único).
- O segredo da aplicação (cadeia secreta do cliente) ou certificado (do tipo X509Certificate2) se for uma aplicação confidencial do cliente.
- Para aplicações web, e às vezes para aplicações de clientes públicos (em particular quando a sua aplicação precisa de usar um corretor), também terá definido o redirectUri onde o fornecedor de identidade irá contactar de volta a sua aplicação com os tokens de segurança.

## <a name="ways-to-initialize-applications"></a>Formas de inicializar aplicações
Existem muitas maneiras diferentes de instantanear aplicações de clientes.

### <a name="initializing-a-public-client-application-from-code"></a>Inicialização de uma aplicação de cliente público a partir do código

O código que se segue instantaneamente uma aplicação de cliente público, utilizadores de inscrição na nuvem pública do Microsoft Azure, com o seu trabalho e contas escolares, ou as suas contas pessoais da Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Inicialização de uma aplicação confidencial do cliente a partir do código

Da mesma forma, o seguinte código instantaneamente uma aplicação confidencial (uma aplicação Web localizada em `https://myapp.azurewebsites.net` ) manuseando fichas de utilizadores na nuvem pública microsoft Azure, com o seu trabalho e contas escolares, ou as suas contas pessoais da Microsoft. O pedido é identificado com o fornecedor de identidade partilhando um segredo do cliente:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Como deve saber, na produção, em vez de usar um segredo de cliente, talvez queira partilhar com a Azure um certificado. O código seria então o seguinte:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Inicializar uma aplicação de cliente público a partir de opções de configuração

O seguinte código instantaneamente uma aplicação de cliente público a partir de um objeto de configuração, que poderia ser preenchido programáticamente ou lido a partir de um ficheiro de configuração:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Inicializar uma aplicação confidencial do cliente a partir de opções de configuração

O mesmo tipo de padrão aplica-se a aplicações confidenciais de clientes. Também pode adicionar outros parâmetros utilizando `.WithXXX` modificadores (aqui um certificado).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Modificadores de construtores

Nos cortes de código que utilizam os construtores de aplicações, uma série de `.With` métodos podem ser aplicados como modificadores (por exemplo, `.WithCertificate` e `.WithRedirectUri` ). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modificadores comuns a aplicações de clientes públicos e confidenciais

Os modificadores que pode definir num cliente público ou um construtor de aplicações de clientes confidenciais são:

|Modificador | Descrição|
|--------- | --------- |
|`.WithAuthority()` 7 sobreposições | Define a autoridade de incumprimento do pedido a uma autoridade AD Azure, com a possibilidade de escolher a Nuvem Azure, o público, o inquilino (ID do inquilino ou nome de domínio), ou fornecer diretamente a autoridade URI.|
|`.WithAdfsAuthority(string)` | Define a autoridade por incumprimento do pedido como uma autoridade ADFS.|
|`.WithB2CAuthority(string)` | Define a autoridade por incumprimento do pedido como uma autoridade Azure AD B2C.|
|`.WithClientId(string)` | Substitui a identificação do cliente.|
|`.WithComponent(string)` | Define o nome da biblioteca utilizando MSAL.NET (por razões de telemetria). |
|`.WithDebugLoggingCallback()` | Se for chamada, a aplicação chamará `Debug.Write` simplesmente permitindo depurar vestígios. Consulte [o Registo registado](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) para obter mais informações.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Detenda os parâmetros de consulta extra de nível de aplicação que serão enviados em todos os pedidos de autenticação. Isto é primordial a cada nível de método de aquisição simbólico (com o mesmo `.WithExtraQueryParameters pattern` ).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Permite cenários avançados como configurar para um proxy HTTP, ou forçar o MSAL a usar um httpClient específico (por exemplo, em ASP.NET aplicações web Core/APIs).|
|`.WithLogging()` | Se for chamado, a aplicação ligará para uma chamada com vestígios de depuragem. Consulte [o Registo registado](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) para obter mais informações.|
|`.WithRedirectUri(string redirectUri)` | Substitui o URI de redirecionamento padrão. No caso das aplicações de clientes públicos, esta será útil para cenários que envolvam o corretor.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Define o delegado usado para enviar telemetria.|
|`.WithTenantId(string tenantId)` | Substitui a identificação do inquilino, ou a descrição do inquilino.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modificadores específicos para aplicações Xamarin.iOS

Os modificadores que pode definir num construtor de aplicações de cliente público em Xamarin.iOS são:

|Modificador | Descrição|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Apenas Xamarin.iOS**: Define o grupo de segurança do porta-chaves iOS (para a persistência da cache).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modificadores específicos para aplicações confidenciais de clientes

Os modificadores que pode definir num construtor de aplicações de cliente confidencial são:

|Modificador | Descrição|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Define o certificado que identifica o pedido com Azure AD.|
|`.WithClientSecret(string clientSecret)` | Define o segredo do cliente (palavra-passe de aplicação) identificando a aplicação com Azure AD.|

Estes modificadores são mutuamente exclusivos. Se fornecer ambos, a MSAL lançará uma exceção significativa.

### <a name="example-of-usage-of-modifiers"></a>Exemplo de utilização de modificadores

Vamos assumir que a sua candidatura é uma aplicação de linha de negócio, que é apenas para a sua organização.  Então pode escrever:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Onde se torna interessante é que a programação para as nuvens nacionais foi agora simplificada. Se quiser que a sua candidatura seja uma aplicação multi-inquilina numa nuvem nacional, pode escrever, por exemplo:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Há também uma sobreposição para a ADFS (ADFS 2019 não está atualmente suportada):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Finalmente, se você é um promotor Azure AD B2C, você pode especificar o seu inquilino assim:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

## <a name="next-steps"></a>Passos seguintes

Depois de rubricar a aplicação do cliente, a sua próxima tarefa é adicionar suporte para o login do utilizador, acesso autorizado a API, ou ambos.

A documentação do nosso cenário de aplicação fornece orientações para a assinatura de um utilizador e para adquirir um token de acesso para aceder a uma API em nome desse utilizador:

- [Aplicação web que assina nos utilizadores: Iniciar s-in e iniciar sê-out](scenario-web-app-sign-user-sign-in.md)
- [Aplicação web que chama APIs web: Adquirir um token](scenario-web-app-call-api-acquire-token.md)
