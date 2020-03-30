---
title: Migrar aplicações Xamarin com mediadores para o MSAL.NET
titleSuffix: Microsoft identity platform
description: Saiba como migrar aplicações do IOS Xamarin que usam o Microsoft Authenticator de ADAL.NET a MSAL.NET.
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: de259daa7fd27cc4f138c294a7f347502ca482a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185834"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migrar aplicações iOS que usam o Autenticador Microsoft de ADAL.NET a MSAL.NET

Tem usado a Biblioteca de Autenticação de Diretório Ativo Azure para .NET (ADAL.NET) e o corretor iOS. Agora é hora de migrar para a [Microsoft Authentication Library](msal-overview.md) para .NET (MSAL.NET), que suporta o corretor no iOS a partir do lançamento 4.3. 

Por onde deve começar? Este artigo ajuda-o a migrar a sua aplicação Xamarin iOS de ADAL para MSAL.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que já tem uma aplicação Xamarin iOS que está integrada com o corretor iOS. Se não o fizer, dirija-se diretamente para MSAL.NET e inicie a implementação do corretor lá. Para obter informações sobre como invocar o corretor iOS em MSAL.NET com uma nova aplicação, consulte [esta documentação.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)

## <a name="background"></a>Segundo plano

### <a name="what-are-brokers"></a>O que são corretores?

Os corretores são aplicações fornecidas pela Microsoft no Android e iOS. (Consulte a aplicação [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) no iOS e Android e a aplicação Intune Company Portal no Android.) 

Permitem:

- Início de sessão único.
- Identificação do dispositivo, que é exigida por algumas políticas de [Acesso Condicional.](../conditional-access/overview.md) Para mais informações, consulte a [gestão do dispositivo.](../conditional-access/concept-conditional-access-conditions.md#device-platforms)
- Verificação de identificação de aplicações, que também é necessária em alguns cenários empresariais. Para mais informações, consulte a gestão de [aplicações móveis Intune (MAM)](https://docs.microsoft.com/intune/mam-faq).

## <a name="migrate-from-adal-to-msal"></a>Migrar da ADAL para a MSAL

### <a name="step-1-enable-the-broker"></a>Passo 1: Ativar o corretor

<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida mSAL:</td></tr>
<tr><td>
Em ADAL.NET, o suporte do corretor foi ativado numa base de contexto por autenticação. É desativado por defeito. Tinha que definir um 

`useBroker`bandeira verdadeira no `PlatformParameters` construtor para chamar o corretor:

```csharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Além disso, no código específico da plataforma, neste exemplo, na página renderizador para iOS, definir o`useBroker` 
bandeira verdadeira:
```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Em seguida, inclua os parâmetros na chamada simbólica adquirida:
```csharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
Em MSAL.NET, o suporte de corretor é ativado numa base por PublicClientApplication. É desativado por defeito. Para o ativar, use o 

`WithBroker()`Parâmetro (definido por defeito) para chamar o corretor:

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
Na chamada simbólica adquirida:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Passo 2: Definir um UIViewController()
Em ADAL.NET, passou num UIViewController `PlatformParameters`como parte de . (Veja o exemplo no passo 1.) Em MSAL.NET, para dar mais flexibilidade aos desenvolvedores, é utilizada uma janela de objeto, mas não é necessária no uso regular do iOS. Para utilizar o corretor, detete a janela do objeto para enviar e receber respostas do corretor. 
<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida mSAL:</td></tr>
<tr><td>
Um UIViewController é passado para 

`PlatformParameters`na plataforma específica do iOS.

```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
Em MSAL.NET, faz duas coisas para definir a janela do objeto para o iOS:

1. Dentro, `AppDelegate.cs` `App.RootViewController` definido para `UIViewController()`um novo . Esta atribuição garante que há um UIViewController com a chamada para o corretor. Se não estiver corretamente definido, poderá ter este erro:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Na chamada AcquireTokenInteractive, `.WithParentActivityOrWindow(App.RootViewController)`utilize e passe na referência à janela do objeto que utilizará.

**Por exemplo:**

Em `App.cs`:
```csharp
   public static object RootViewController { get; set; }
```
Em `AppDelegate.cs`:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
Na chamada simbólica adquirida:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Passo 3: Atualizar appDelegate para lidar com o backback
Tanto a ADAL como a MSAL ligam para o corretor, `OpenUrl` e `AppDelegate` o corretor, por sua vez, volta para a sua aplicação através do método da classe. Para mais informações, consulte [esta documentação.](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback)

Não há alterações aqui entre ADAL.NET e MSAL.NET.

### <a name="step-4-register-a-url-scheme"></a>Passo 4: Registar um esquema de URL
ADAL.NET e MSAL.NET usam URLs para invocar o corretor e devolver a resposta do corretor à aplicação. Registe o esquema `Info.plist` DEURL no ficheiro da sua aplicação da seguinte forma:

<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida mSAL:</td></tr>
<tr><td>
O esquema de URL é exclusivo da sua aplicação.
</td><td>
O 

`CFBundleURLSchemes`nome deve incluir 

`msauth.`

como prefixo, seguido pelo seu`CFBundleURLName`

Por exemplo: `$"msauth.(BundleId")`

```csharp
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

> [!NOTE]
> Este esquema de URL torna-se parte do URI redirecionado que é usado para identificar exclusivamente a app quando recebe a resposta do corretor.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Passo 5: Adicionar o identificador de corretor à secção LSApplicationQueriesSchemes

ADAL.NET e MSAL.NET `-canOpenURL:` ambos usam para verificar se o corretor está instalado no dispositivo. Adicione o identificador correto para o corretor iOS à secção LSApplicationQueriesSchemes do ficheiro info.plist da seguinte forma:

<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida mSAL:</td></tr>
<tr><td>
Utiliza 

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Utiliza 

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>Passo 6: Registe o seu URI redirecionado no portal

ADAL.NET e MSAL.NET ambos adicionam um requisito extra no URI redirecionamento quando visa o corretor. Registe o URI redirecionamento com a sua aplicação no portal.
<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida mSAL:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Exemplo: 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Exemplo:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Para obter mais informações sobre como registar o URI redirecionado no portal, consulte [Alavancagem do corretor nas aplicações Xamarin.iOS](msal-net-use-brokers-with-xamarin-apps.md#step-8-make-sure-the-redirect-uri-is-registered-with-your-app).

## <a name="next-steps"></a>Passos seguintes

Conheça [as considerações específicas do Xamarin iOS com MSAL.NET](msal-net-xamarin-ios-considerations.md). 
