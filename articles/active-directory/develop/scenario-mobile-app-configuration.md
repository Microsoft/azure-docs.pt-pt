---
title: Configure aplicações móveis que chamam APIs web / Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação móvel que chama APIs web (configuração de código da aplicação)
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
ms.openlocfilehash: fc25f13d0b0b8a264dcd47a5fdebb0533e93fb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132495"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Configure uma aplicação móvel que chama APIs web

Depois de criar a sua aplicação, aprenderá a configurar o código utilizando os parâmetros de registo da aplicação. As aplicações móveis apresentam algumas complexidades relacionadas com a adaptação ao seu quadro de criação.

## <a name="find-msal-support-for-mobile-apps"></a>Encontre suporte mSAL para aplicações móveis

Os seguintes tipos da Microsoft Authentication Library (MSAL) suportam aplicações móveis.

MSAL | Descrição
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Usado para desenvolver aplicações portáteis. MSAL.NET suporta as seguintes plataformas para a construção de uma aplicação móvel: Universal Windows Platform (UWP), Xamarin.iOS e Xamarin.Android.
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Usado para desenvolver aplicações nativas iOS utilizando Objective-C ou Swift.
![MSAL. Android](media/sample-v2-code/logo_android.png) <br/> MSAL. Android | Usado para desenvolver aplicações android nativas em Java para Android.

## <a name="instantiate-the-application"></a>Instantânea a aplicação

### <a name="android"></a>Android

Aplicações móveis `PublicClientApplication` usam a classe. Eis como instantaneamente:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

As aplicações móveis no iOS precisam de instantaneamente a `MSALPublicClientApplication` classe. Para instantaneamente a aula, utilize o seguinte código. 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[Propriedades adicionais mSALPublicClientApplicationConfig](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) podem sobrepor-se à autoridade padrão, especificar um URI redirecionado ou alterar o comportamento do caching de token MSAL. 

### <a name="xamarin-or-uwp"></a>Xamarin ou UWP

Esta secção explica como instantaneamente a aplicação para aplicações Xamarin.iOS, Xamarin.Android e UWP.

#### <a name="instantiate-the-application"></a>Instantânea a aplicação

Em Xamarin ou UWP, a forma mais simples de instantaneamente a aplicação é utilizando o seguinte código. Neste código, `ClientId` está o GUID da sua aplicação registada.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Métodos adicionais `With<Parameter>` estabelecem o progenitor da UI, sobrepõem-se à autoridade padrão, especificam um nome e versão do cliente para telemetria, especificam um URI redirecionado e especificam a fábrica HTTP a utilizar. A fábrica HTTP pode ser utilizada, por exemplo, para manusear proxies e especificar telemetria e exploração madeireira. 

As seguintes secções fornecem mais informações sobre a instantânea da aplicação.

##### <a name="specify-the-parent-ui-window-or-activity"></a>Especificar a UI, janela ou atividade dos pais

No Android, é necessário passar a atividade dos pais antes de fazer a autenticação interativa. No iOS, quando se usa um corretor, `ViewController`é preciso passar. Da mesma forma na UWP, talvez queiras passar pela janela dos pais. Passe-o quando adquire o símbolo. Mas quando está a criar a aplicação, também pode especificar `UIParent`um callback como um delegado que retorna .

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

No Android, recomendamos [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)que utilize . O `PublicClientApplication` código de construtor resultante parece com este exemplo:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>Encontre mais parâmetros de construção de aplicativos

Para obter uma lista de `PublicClientApplicationBuilder`todos os métodos disponíveis, consulte a [lista de Métodos](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).

Para uma descrição de todas `PublicClientApplicationOptions`as opções expostas, consulte a documentação de [referência.](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)

## <a name="tasks-for-xamarin-ios"></a>Tarefas para Xamarin iOS

Se utilizar MSAL.NET no xamarin iOS, faça as seguintes tarefas.

* [Anular e implementar `OpenUrl` a função em`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Ativar grupos de porta-chaves](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Permitir a partilha de cache token](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Ativar o acesso ao porta-chaves](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Para mais informações, consulte [as considerações do Xamarin iOS.](msal-net-xamarin-ios-considerations.md)

## <a name="tasks-for-msal-for-ios-and-macos"></a>Tarefas para MSAL para iOS e macOS

Estas tarefas são necessárias quando utiliza MSAL para iOS e macOS:

* [Implementar `openURL` o backback](#brokered-authentication-for-msal-for-ios-and-macos)
* [Ativar grupos de acesso a porta-chaves](howto-v2-keychain-objc.md)
* [Personalizar browsers e WebViews](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Tarefas para Xamarin.Android

Se utilizar Xamarin.Android, faça as seguintes tarefas:

- [Assegurar que o controlo remonta à MSAL após o fim da parte interativa do fluxo de autenticação](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Atualizar o manifesto Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Utilize a visão web incorporada (opcional)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Resolução de problemas, se necessário](msal-net-xamarin-android-considerations.md#troubleshoot)

Para mais informações, consulte [as considerações xamarin.Android](msal-net-xamarin-android-considerations.md).

Para obter considerações sobre os navegadores no Android, consulte [considerações específicas de Xamarin.Android com MSAL.NET](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>Tarefas para a UWP

Na UWP, pode suster redes corporativas. As seguintes secções explicam as tarefas que deve completar no cenário corporativo.

Para mais informações, consulte [considerações específicas da UWP com MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configure-the-application-to-use-the-broker"></a>Configure a aplicação para usar o corretor

No Android e iOS, os corretores permitem:

- **Entrada única (SSO)**: Pode utilizar o SSO para dispositivos registados no Azure Ative Directory (Azure AD). Quando utiliza o SSO, os seus utilizadores não precisam de iniciar sessão em cada aplicação.
- **Identificação do dispositivo**: Esta definição permite políticas de acesso condicional que estão relacionadas com dispositivos AD Azure. O processo de autenticação utiliza o certificado do dispositivo que foi criado quando o dispositivo foi aderido ao local de trabalho.
- **Verificação de identificação de aplicações**: Quando uma aplicação chama o corretor, passa o url de redirecionamento. Então o corretor verifica..

### <a name="enable-the-broker-on-xamarin"></a>Ativar o corretor em Xamarin

Para ativar o corretor em `WithBroker()` Xamarin, utilize `PublicClientApplicationBuilder.CreateApplication` o parâmetro quando ligar para o método. Por defeito, `.WithBroker()` está definido para verdade. 

Para permitir a autenticação intermediada para Xamarin.iOS, siga os passos na [secção Xamarin.iOS](#enable-brokered-authentication-for-xamarin-ios) neste artigo.

### <a name="enable-the-broker-for-msal-for-android"></a>Ativar o corretor de MSAL para Android

Para obter informações sobre a ativação de um corretor no Android, consulte a [autenticação intermediada no Android](brokered-auth.md). 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Ativar o corretor para MSAL para iOS e macOS

A autenticação intermediada é ativada por predefinição para cenários de AD Azure em MSAL para iOS e macOS. 

As seguintes secções fornecem instruções para configurar a sua aplicação de suporte de autenticação intermediado para MSAL para Xamarin.iOS ou MSAL para iOS e macOS. Nos dois conjuntos de instruções, alguns dos passos diferem.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Ativar a autenticação intermediada para xamarin iOS

Siga os passos nesta secção para permitir que a sua aplicação Xamarin.iOS fale com a aplicação [Microsoft Authenticator.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

#### <a name="step-1-enable-broker-support"></a>Passo 1: Ativar suporte ao corretor

O suporte do corretor é desativado por defeito. Permite-o para `PublicClientApplication` uma classe individual. Utilize `WithBroker()` o parâmetro quando `PublicClientApplication` criar `PublicClientApplicationBuilder`a classe através de . O `WithBroker()` parâmetro é definido para verdade por defeito.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Passo 2: Atualizar appDelegate para lidar com o backback

Quando MSAL.NET chama o corretor, o corretor liga de volta para a sua aplicação. Volta a chamar `AppDelegate.OpenUrl` de volta usando o método. Como a MSAL aguarda a resposta do corretor, o seu pedido precisa de cooperar para chamar MSAL.NET de volta. Configura este comportamento atualizando `AppDelegate.cs` o ficheiro para anular o método, como mostra o seguinte código.

```csharp
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

Este método é invocado sempre que a aplicação é lançada. É uma oportunidade para processar a resposta do corretor e completar o processo de autenticação que MSAL.NET iniciado.

#### <a name="step-3-set-a-uiviewcontroller"></a>Passo 3: Definir um UIViewController()

Para o xamarin iOS, normalmente não é necessário definir uma janela de objetos. Mas neste caso deve defini-lo para que possa enviar e receber respostas de um corretor. Para definir uma janela `AppDelegate.cs`de objeto, dentro, você definir um `ViewController`.

Para definir a janela do objeto, siga estes passos:

1. Dentro, `AppDelegate.cs`coloque `App.RootViewController` o `UIViewController()`novo . Esta definição garante que a `UIViewController`chamada para o corretor inclui . Se não estiver corretamente definido, poderá ter este erro:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. Na `AcquireTokenInteractive` chamada, `.WithParentActivityOrWindow(App.RootViewController)`use. Passe a referência à janela do objeto que vai usar. Segue-se um exemplo:

    Em `App.cs`:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    Em `AppDelegate.cs`:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    Na `AcquireToken` chamada:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```
    
#### <a name="step-4-register-a-url-scheme"></a>Passo 4: Registar um esquema de URL

MSAL.NET usa URLs para invocar o corretor e depois devolver a resposta do corretor à sua aplicação. Para terminar a viagem de ida e `Info.plist` volta, registe o sistema de URL da sua aplicação no ficheiro. 

Para registar o sistema de URL da sua aplicação, siga estes passos:

1. Prefixo `CFBundleURLSchemes` `msauth`com . 
1. Adicione `CFBundleURLName` ao fim. Siga este padrão: 

   `$"msauth.(BundleId)"`

   Aqui, `BundleId` identifica exclusivamente o seu dispositivo. Por exemplo, `BundleId` `yourcompany.xforms`se for, `msauth.com.yourcompany.xforms`o seu esquema de URL é .
    
   > [!NOTE]
   > Este esquema de URL passará a fazer parte do URI redirecionado que identifica exclusivamente a sua aplicação quando recebe a resposta do corretor.
    
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
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>Passo 5: Adicionar à secção LSApplicationQueriesSchemes

A MSAL usa `–canOpenURL:` para verificar se o corretor está instalado no dispositivo. No iOS 9, a Apple bloqueou os esquemas que uma aplicação pode consultar.

Adicione `msauthv2` à `LSApplicationQueriesSchemes` secção `Info.plist` do ficheiro, como no seguinte exemplo de código:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Autenticação intermediada para MSAL para iOS e macOS

A autenticação intermediada é ativada por predefinição para cenários da AD Azure.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Passo 1: Atualizar appDelegate para lidar com o backback

Quando o MSAL para iOS e macOS chama o corretor, `openURL` o corretor volta a ligar para a sua aplicação utilizando o método. Como a MSAL aguarda a resposta do corretor, o seu pedido precisa de cooperar para rechamar a MSAL. Configurar esta capacidade atualizando o `AppDelegate.m` ficheiro para anular o método, como mostram os seguintes exemplos de código.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

> [!NOTE]
> Se adotou `UISceneDelegate` no iOS 13 ou mais tarde, `scene:openURLContexts:` então coloque a chamada MSAL no em vez disso. `UISceneDelegate` A MSAL `handleMSALResponse:sourceApplication:` deve ser chamada apenas uma vez para cada URL.
>
> Para mais informações, consulte a [documentação](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)da Apple .

#### <a name="step-2-register-a-url-scheme"></a>Passo 2: Registar um esquema de URL

MSAL para iOS e macOS usa URLs para invocar o corretor e, em seguida, devolver a resposta do corretor à sua app. Para terminar a viagem de ida e `Info.plist` volta, registe um esquema de URL para a sua aplicação no ficheiro.

Para registar um esquema para a sua aplicação: 

1. Prefixe o seu `msauth`esquema de URL personalizado com . 

1. Adicione o seu identificador de pacote ao fim do seu esquema. Siga este padrão: 

   `$"msauth.(BundleId)"`

   Aqui, `BundleId` identifica exclusivamente o seu dispositivo. Por exemplo, `BundleId` `yourcompany.xforms`se for, `msauth.com.yourcompany.xforms`o seu esquema de URL é .
  
   > [!NOTE]
   > Este esquema de URL passará a fazer parte do URI redirecionado que identifica exclusivamente a sua aplicação quando recebe a resposta do corretor. Certifique-se de que o `msauth.(BundleId)://auth` URI redirecionado no formato está registado para a sua aplicação no [portal Azure](https://portal.azure.com).
  
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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>Passo 3: Adicionar Esquemas de LSApplicationQueries

Adicione `LSApplicationQueriesSchemes` para permitir chamadas para a aplicação Microsoft Authenticator, se estiver instalada.

> [!NOTE]
> O `msauthv3` esquema é necessário quando a sua aplicação é compilada utilizando o Xcode 11 e mais tarde. 

Aqui está um exemplo de `LSApplicationQueriesSchemes`como adicionar:

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Autenticação intermediada para Xamarin.Android

MSAL.NET não suporta corretores para Android.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adquirir um símbolo](scenario-mobile-acquire-token.md)
