---
title: Inicializar aplicações de cliente (biblioteca de autenticação da Microsoft para .NET) | Azure
description: Saiba mais sobre a inicializar o cliente público e aplicações de cliente confidencial com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f22ff41e380a16af2aa45df9a61eefbf293ff83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544323"
---
# <a name="initialize-client-applications-using-msalnet"></a>Inicializar aplicações de cliente com MSAL.NET
Este artigo descreve a inicializar o cliente público e aplicações de cliente confidencial com biblioteca de autenticação da Microsoft para .NET (MSAL.NET).  Para saber mais sobre os tipos de aplicativos de cliente e as opções de configuração de aplicação, veja a [descrição geral](msal-client-applications.md).

Com MSAL.NET 3.x, a forma recomendada para criar uma instância de um aplicativo está usando os construtores de aplicação: `PublicClientApplicationBuilder` e `ConfidentialClientApplicationBuilder`. Eles oferecem um mecanismo poderoso para configurar a aplicação a partir do código, ou de um ficheiro de configuração ou até mesmo pela mistura de ambas as abordagens.

## <a name="prerequisites"></a>Pré-requisitos
Antes de inicializar uma aplicação, primeiro tem de [registá-lo](quickstart-register-app.md) para que a sua aplicação pode ser integrada com a plataforma de identidade da Microsoft.  Após o registo, poderá ter as seguintes informações (que podem ser encontradas no portal do Azure):

- O ID de cliente (uma cadeia que representa um GUID)
- URL do fornecedor de identidade (com o nome da instância) e o início de sessão público-alvo para a sua aplicação. Esses dois parâmetros são coletivamente conhecidos como a autoridade.
- O ID de inquilino se estiver escrevendo um aplicativo de linha de negócio unicamente para a sua organização (também denominada aplicação inquilino único).
- O segredo de aplicação (cadeia de caracteres secreta do cliente) ou o certificado (do tipo X509Certificate2) se for uma aplicação de cliente confidencial.
- Para aplicações web e, às vezes, para aplicações de cliente público (em especial quando a aplicação tem de utilizar um mediador), tem também definirá o redirectUri onde o fornecedor de identidade irá contactar back seu aplicativo com os tokens de segurança.

## <a name="ways-to-initialize-applications"></a>Formas de inicialização de aplicativos
Existem várias formas diferentes de criar uma instância de aplicativos cliente.

### <a name="initializing-a-public-client-application-from-code"></a>A inicializar uma aplicação de cliente pública a partir do código

O código a seguir cria uma instância de um aplicativo cliente pública, os utilizadores iniciar sessão na cloud pública do Microsoft Azure, com o seu trabalho e contas escolares ou contas pessoais da Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>A inicializar um aplicativo de cliente confidencial a partir do código

Da mesma forma, o código a seguir cria uma instância de um aplicativo confidencial (uma aplicação Web localizado em `https://myapp.azurewebsites.net`) lidar com tokens dos utilizadores na cloud pública do Microsoft Azure, com o seu trabalho e contas escolares ou contas pessoais da Microsoft. O aplicativo esteja identificado com o fornecedor de identidade ao partilhar um segredo do cliente:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Como deve saber, na produção, em vez de utilizar um segredo do cliente, pode querer compartilhar com o Azure AD de um certificado. O código, em seguida, seria o seguinte:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>A inicializar uma aplicação de cliente pública de opções de configuração

O código a seguir cria uma instância de uma aplicação de cliente pública de um objeto de configuração, o que poderia ser preenchido-no através de programação ou ler a partir de um ficheiro de configuração:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>A inicializar um aplicativo de cliente confidencial de opções de configuração

O mesmo tipo de padrão aplica-se às aplicações cliente confidencial. Também pode adicionar outros parâmetros usando `.WithXXX` Modificadores (aqui um certificado).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Modificadores de construtor

Nos fragmentos de código com construtores de aplicativo, diversas `.With` métodos podem ser aplicados como modificadores (por exemplo, `.WithCertificate` e `.WithRedirectUri`). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modificadores comuns para as aplicações cliente públicos e confidencial

Os modificadores que pode definir um cliente público ou o construtor do aplicativo de cliente confidencial são:

|Parâmetro | Descrição|
|--------- | --------- |
|`.WithAuthority()` 7 substituições | Define a autoridade de padrão de aplicação a uma autoridade do Azure AD, com a possibilidade de escolher a nuvem do Azure, o público-alvo, o inquilino (inquilino ID ou nome de domínio), ou fornecer diretamente o URI de autoridade.|
|`.WithAdfsAuthority(string)` | Define a autoridade de padrão de aplicação para ser uma autoridade ADFS.|
|`.WithB2CAuthority(string)` | Define a autoridade de padrão de aplicação para ser uma autoridade do Azure AD B2C.|
|`.WithClientId(string)` | Substitui o ID de cliente.|
|`.WithComponent(string)` | Define o nome da biblioteca com MSAL.NET (por motivos de telemetria). |
|`.WithDebugLoggingCallback()` | Se chamado, a aplicação irá chamar `Debug.Write` simplesmente ativar a depuração rastreios. Ver [registo](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) para obter mais informações.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Defina os parâmetros de consulta adicionais ao nível de aplicativo que serão enviados em todos os pedidos de autenticação. Isso é substituível. em cada nível de método de aquisição do token (com o mesmo `.WithExtraQueryParameters pattern`).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Possibilita a cenários como a configuração para um proxy de HTTP, ou para forçar a MSAL para utilizar um HttpClient específico (por exemplo, em aplicações de web de ASP.NET Core/APIs).|
|`.WithLogging()` | Se chamado, o aplicativo chamará um retorno de chamada com a depuração rastreios. Ver [registo](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) para obter mais informações.|
|`.WithRedirectUri(string redirectUri)` | Substitui o URI de redirecionamento do padrão. No caso de aplicativos cliente público, isso será útil para cenários que envolvem o mediador.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Define o delegado usado para enviar telemetria.|
|`.WithTenantId(string tenantId)` | Substitui o ID do inquilino ou a descrição do inquilino.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modificadores específicos para aplicações xamarin. IOS

Os modificadores que pode definir um construtor de aplicação de cliente público no xamarin. IOS são:

|Parâmetro | Descrição|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Xamarin. IOS apenas**: Define o grupo de segurança da chave de cadeia de iOS (para a persistência da cache).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modificadores específicos para aplicações confidenciais do cliente

Os modificadores que pode definir um construtor de aplicação de cliente confidencial são:

|Parâmetro | Descrição|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Define o certificado de identificar a aplicação com o Azure AD.|
|`.WithClientSecret(string clientSecret)` | Define o segredo de cliente (palavra-passe de aplicação), identificar a aplicação com o Azure AD.|

Estes Modificadores são mutuamente exclusivas. Se fornecer ambos, MSAL lançará uma exceção relevante.

### <a name="example-of-usage-of-modifiers"></a>Exemplo de utilização de modificadores

Vamos supor que a aplicação é um aplicativo de linha de negócio, que é apenas para a sua organização.  Em seguida, pode escrever:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Onde se torna interessante é que a programação para nuvens nacionais agora simplificou. Se pretender que o aplicativo seja um aplicativo de vários inquilino numa cloud nacional, poderia escrever, por exemplo:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Também existe uma substituição para o AD FS (2019 do ADFS não é atualmente suportado):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Por fim, se for um programador do Azure AD B2C, pode especificar o seu inquilino como este:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
