---
title: Aplicativo móvel que chama APIs Web (configuração de código) – plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo móvel que chama APIs da Web (configuração de código do aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0d0550dd92b786ec540bae6ae6da7322d4fb629
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175491"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>Aplicativo móvel que chama as APIs da Web-configuração de código

Depois de criar seu aplicativo, você aprenderá a configurar o código usando os parâmetros de registro do aplicativo. Os aplicativos móveis também têm algumas especificidades complexas, que precisam se ajustar à estrutura usada para criar esses aplicativos

## <a name="msal-libraries-supporting-mobile-apps"></a>Bibliotecas de MSAL que dão suporte a aplicativos móveis

As bibliotecas da Microsoft que dão suporte a aplicativos móveis são:

  Biblioteca MSAL | Descrição
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Para desenvolver aplicativos portáteis. As plataformas com suporte do MSAL.NET para criar um aplicativo móvel são UWP, Xamarin. iOS e Xamarin. Android.
  ![MSAL. iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL. iOS | Para desenvolver aplicativos iOS nativos com Objective-C ou Swift
  ![MSAL. Android](media/sample-v2-code/logo_android.png) <br/> MSAL. Android | Para desenvolver aplicativos Android nativos em Java para Android

## <a name="instantiating-the-application"></a>Instanciando o aplicativo

### <a name="android"></a>Android

Os aplicativos móveis usam a classe `PublicClientApplication`. Veja como instanciá-lo:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Os aplicativos móveis no iOS precisam criar uma instância da classe `MSALPublicClientApplication`.

Objective-C:

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

Há [Propriedades MSALPublicClientApplicationConfig adicionais](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) que podem substituir a autoridade padrão, especificar um URI de redirecionamento ou alterar o comportamento de cache de token MSAL. 

### <a name="xamarin-or-uwp"></a>Xamarin ou UWP

O parágrafo a seguir explica como instanciar o aplicativo para aplicativos Xamarin. iOS, Xamarin. Android e UWP.

#### <a name="instantiating-the-application"></a>Instanciando o aplicativo

No Xamarin, ou UWP, a maneira mais simples de instanciar o aplicativo é a seguinte, em que o `ClientId` é o GUID do seu aplicativo registrado.

```CSharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Há outros métodos de*parâmetro* que definem o pai da interface do usuário, substituem a autoridade padrão, especificam um nome de cliente e uma versão (para telemetria), especificam um URI de redirecionamento, especificam o alocador http a ser usado (por exemplo, para manipular proxies, especificar telemetria e registro em log). Este é o tópico dos parágrafos a seguir.

##### <a name="specifying-the-parent-uiwindowactivity"></a>Especificando a interface do usuário/janela/atividade pai

No Android, você precisa passar a atividade pai antes de fazer a autenticação interativa. No iOS, ao usar um agente, você precisa passar o ViewController. Da mesma forma no UWP, talvez você queira passar a janela pai. Isso é possível quando você adquire o token, mas também é possível especificar um retorno de chamada no momento da criação do aplicativo um delegado que retorna o UIParent.

```CSharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

No Android, recomendamos que você use o `CurrentActivityPlugin` [aqui](https://github.com/jamesmontemagno/CurrentActivityPlugin).  Em seguida, o código do construtor de `PublicClientApplication` ficaria assim:

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>Mais parâmetros de compilação de aplicativo

- Para obter a lista de todos os modificadores disponíveis em `PublicClientApplicationBuilder`, consulte a documentação de referência [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Para obter a descrição de todas as opções expostas em `PublicClientApplicationOptions` consulte [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), na documentação de referência

## <a name="xamarin-ios-specific-considerations"></a>Considerações específicas do Xamarin iOS

No Xamarin iOS, há várias considerações que você deve levar em conta ao usar o MSAL.NET:

1. [Substituir e implementar a função `OpenUrl` no `AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [Habilitar grupos de conjunto de chaves](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [Habilitar compartilhamento de cache de token](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [Habilitar o acesso ao conjunto de chaves](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Os detalhes são fornecidos em [Considerações sobre o Xamarin Ios](msal-net-xamarin-ios-considerations.md)

## <a name="msal-for-ios-and-macos-specific-considerations"></a>Considerações específicas sobre o MSAL para iOS e macOS

Considerações semelhantes se aplicam ao usar o MSAL para iOS e macOS:

1. [Implementar o retorno de chamada `openURL`](#brokered-authentication-for-msal-for-ios-and-macos)
2. [Habilitar grupos de acesso do conjunto de chaves](howto-v2-keychain-objc.md)
3. [Personalizar navegadores e webviews](customize-webviews.md)

## <a name="xamarin-android-specific-considerations"></a>Considerações específicas do Xamarin Android

Aqui estão as especificações do Xamarin Android:

- [Garantindo que o controle volte para MSAL depois que a parte interativa do fluxo de autenticação for encerrada](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [Atualizar o manifesto do Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Usar a exibição da Web inserida (opcional)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Resolução de problemas](msal-net-xamarin-android-considerations.md#troubleshooting)

Os detalhes são fornecidos em [Considerações sobre o Xamarin Android](msal-net-xamarin-android-considerations.md)

Por fim, há algumas especificidades a saber sobre os navegadores no Android. Eles são explicados em [Considerações específicas do Xamarin Android com MSAL.net](msal-net-system-browser-android-considerations.md)

#### <a name="uwp-specific-considerations"></a>Considerações específicas de UWP

No UWP, você pode usar redes corporativas. Para obter informações adicionais sobre como usar a biblioteca MSAL com UWP, consulte [Considerações específicas de plataforma universal do Windows com MSAL.net](msal-net-uwp-considerations.md).

## <a name="configuring-the-application-to-use-the-broker"></a>Configurando o aplicativo para usar o agente

### <a name="why-use-brokers-in-ios-and-android-applications"></a>Por que usar agentes em aplicativos iOS e Android?

No Android e no iOS, os agentes habilitam:

- SSO (logon único) quando o dispositivo é registrado com o AAD. Os usuários não precisarão entrar em cada aplicativo.
- Identificação do dispositivo. Habilita as políticas de acesso condicional relacionadas ao dispositivo do Azure AD, acessando o certificado do dispositivo que foi criado no dispositivo quando ele foi ingressado no local de trabalho.
- Verificação da identificação do aplicativo. Quando um aplicativo chama o agente, ele passa sua URL de redirecionamento e o agente o verifica.

### <a name="enable-the-broker-on-xamarin"></a>Habilitar o agente no Xamarin

Para habilitar um desses recursos, use o parâmetro `WithBroker()` ao chamar o método `PublicClientApplicationBuilder.CreateApplication`. `.WithBroker()` é definido como true por padrão. Siga as etapas abaixo para o [Xamarin. Ios](#brokered-authentication-for-xamarinios).

### <a name="enable-the-broker-for-msal-for-android"></a>Habilitar o agente para MSAL para Android

Consulte [autenticação orientada no Android](brokered-auth.md) para obter informações sobre como habilitar um agente no Android. 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Habilitar o agente para MSAL para iOS e macOS

A autenticação orientada é habilitada por padrão para cenários do AAD no MSAL para iOS e macOS. Siga as etapas abaixo para configurar seu aplicativo para suporte de autenticação orientada para [MSAL para IOS e MacOS](#brokered-authentication-for-msal-for-ios-and-macos). Observe que algumas etapas são diferentes entre [MSAL para Xamarin. Ios](#brokered-authentication-for-xamarinios) e [MSAL para IOS e MacOS](#brokered-authentication-for-msal-for-ios-and-macos).

### <a name="brokered-authentication-for-xamarinios"></a>Autenticação orientada para Xamarin. iOS

Siga as etapas abaixo para habilitar seu aplicativo Xamarin. iOS para conversar com o aplicativo [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

#### <a name="step-1-enable-broker-support"></a>Etapa 1: habilitar o suporte do Broker

O suporte do Broker é habilitado por`PublicClientApplication`. Ele é desabilitado por padrão. Você deve usar o parâmetro `WithBroker()` (definido como true por padrão) ao criar o `PublicClientApplication` por meio do `PublicClientApplicationBuilder`.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Etapa 2: atualizar AppDelegate para manipular o retorno de chamada

Quando o MSAL.NET chama o agente, o agente, por sua vez, retorna a chamada para seu aplicativo por meio do método `AppDelegate.OpenUrl`. Como o MSAL aguardará a resposta do agente, seu aplicativo precisará cooperar para chamar MSAL.NET de volta. Faça isso atualizando o arquivo de `AppDelegate.cs` para substituir o método abaixo.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

Esse método é invocado toda vez que o aplicativo é iniciado e é usado como uma oportunidade para processar a resposta do agente e concluir o processo de autenticação iniciado pelo MSAL.NET.

#### <a name="step-3-set-a-uiviewcontroller"></a>Etapa 3: definir um UIViewController ()

Com o Xamarin iOS, normalmente você não precisa definir uma janela de objeto, mas, nesse caso, você deve fazer para enviar e receber respostas de um agente. Ainda em `AppDelegate.cs`, defina um ViewController.

Faça o seguinte para definir a janela de objeto:

1) Em `AppDelegate.cs`, defina o `App.RootViewController` como um novo `UIViewController()`. Isso verificará se há um `UIViewController` com a chamada para o agente. Se não estiver definido corretamente, você poderá receber esse erro: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) Na chamada AcquireTokenInteractive, use o `.WithParentActivityOrWindow(App.RootViewController)` e passe a referência para a janela de objeto que você usará.

**Por exemplo:**

Em `App.cs`:
```CSharp
   public static object RootViewController { get; set; }
```
Em `AppDelegate.cs`:
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
Na chamada de aquisição de token:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

#### <a name="step-4-register-a-url-scheme"></a>Etapa 4: registrar um esquema de URL

O MSAL.NET usa URLs para invocar o agente e, em seguida, retornar a resposta do agente de volta para seu aplicativo. Para concluir a viagem de ida e volta, você precisa registrar um esquema de URL para seu aplicativo no arquivo de `Info.plist`.

Prefixe a `CFBundleURLSchemes` com `msauth`. Em seguida, adicione `CFBundleURLName` ao final.

`$"msauth.(BundleId)"`

**Por exemplo:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Esse esquema de URL se tornará parte do RedirectUri usado para identificar exclusivamente seu aplicativo ao receber a resposta do agente.

```XML
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

#### <a name="step-5-lsapplicationqueriesschemes"></a>Etapa 5: LSApplicationQueriesSchemes

O MSAL usa `–canOpenURL:` para verificar se o agente está instalado no dispositivo. No iOS 9, a Apple bloqueou o que os esquemas que um aplicativo pode consultar.

**Adicione** **`msauthv2`** à seção `LSApplicationQueriesSchemes` do arquivo de `Info.plist`.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Autenticação orientada para MSAL para iOS e macOS

A autenticação orientada é habilitada por padrão para cenários do AAD.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Etapa 1: atualizar AppDelegate para manipular o retorno de chamada

Quando o MSAL para iOS e macOS chamar o agente, o agente, por sua vez, chamará de volta para seu aplicativo por meio do método `openURL`. Como o MSAL aguardará a resposta do agente, seu aplicativo precisará cooperar para chamar MSAL de volta. Faça isso atualizando o arquivo de `AppDelegate.m` para substituir o método abaixo.

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

Observe que, se você adotou UISceneDelegate no iOS 13 +, o retorno de chamada MSAL precisará ser colocado na `scene:openURLContexts:` de UISceneDelegate (consulte a [documentação da Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)). MSAL `handleMSALResponse:sourceApplication:` deve ser chamado apenas uma vez para cada URL.

#### <a name="step-2-register-a-url-scheme"></a>Etapa 2: registrar um esquema de URL

O MSAL para iOS e macOS usa URLs para invocar o agente e, em seguida, retornar a resposta do agente de volta para seu aplicativo. Para concluir a viagem de ida e volta, você precisa registrar um esquema de URL para seu aplicativo no arquivo de `Info.plist`.

Prefixe seu esquema de URL personalizado com `msauth`. Em seguida, adicione **seu identificador de pacote** ao final.

`msauth.(BundleId)`

**Por exemplo:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Esse esquema de URL se tornará parte do RedirectUri usado para identificar exclusivamente seu aplicativo ao receber a resposta do agente. Verifique se o RedirectUri no formato de `msauth.(BundleId)://auth` está registrado para seu aplicativo no [portal do Azure](https://portal.azure.com).

```XML
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-lsapplicationqueriesschemes"></a>Etapa 3: LSApplicationQueriesSchemes

**Adicione `LSApplicationQueriesSchemes`** para permitir a chamada para Microsoft Authenticator se estiver instalado.
Observe que o esquema "msauthv3" é necessário ao compilar seu aplicativo com o Xcode 11 e posterior. 

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Autenticação orientada para Xamarin. Android

MSAL.NET ainda não dá suporte a agentes para Android.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adquirindo um token](scenario-mobile-acquire-token.md)
