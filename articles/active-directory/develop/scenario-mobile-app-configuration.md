---
title: Configure aplicações móveis que chamem APIs web | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar o código da sua aplicação móvel para chamar uma API web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6f13d789cd63bb568bb8940ce614ebdb2dbcdb83
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199753"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Configure uma aplicação móvel que chama APIs web

Depois de criar a sua aplicação, aprenderá a configurar o código utilizando os parâmetros de registo da aplicação. As aplicações móveis apresentam algumas complexidades relacionadas com a adaptação ao seu quadro de criação.

## <a name="microsoft-libraries-supporting-mobile-apps"></a>Bibliotecas da Microsoft que suportam aplicações móveis

As seguintes bibliotecas da Microsoft suportam aplicações móveis:

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="instantiate-the-application"></a>Instantaneamente a aplicação

### <a name="android"></a>Android

As aplicações móveis usam a `PublicClientApplication` classe. Eis como instantaneamente:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

As aplicações móveis no iOS precisam de instantaneamente a `MSALPublicClientApplication` aula. Para instantaneaizar a aula, utilize o seguinte código.

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[As propriedades adicionais do MSALPublicClientAppig](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) podem sobrepor-se à autoridade padrão, especificar um URI de redirecionamento ou alterar o comportamento do caching token MSAL.

### <a name="xamarin-or-uwp"></a>Xamarin ou UWP

Esta secção explica como instantaneamente a aplicação para aplicações Xamarin.iOS, Xamarin.Android e UWP.

#### <a name="instantiate-the-application"></a>Instantaneamente a aplicação

Em Xamarin ou UWP, a forma mais simples de instantaneaizar a aplicação é utilizando o seguinte código. Neste código, `ClientId` encontra-se o GUID da sua aplicação registada.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Métodos adicionais `With<Parameter>` definem o progenitor da UI, substituem a autoridade predefinida, especificam um nome e versão do cliente para telemetria, especificam um URI de redirecionamento e especificam a fábrica HTTP para utilizar. A fábrica HTTP pode ser utilizada, por exemplo, para manusear proxies e especificar telemetria e registo.

As secções seguintes fornecem mais informações sobre a instantânea aplicação.

##### <a name="specify-the-parent-ui-window-or-activity"></a>Especificar a UI, janela ou atividade dos pais

No Android, passe a atividade dos pais antes de fazer a autenticação interativa. No iOS, quando se usa um corretor, o `ViewController` passe. Da mesma forma na UWP, talvez queiras passar pela janela dos pais. Passa-se quando se adquire o símbolo. Mas quando estiver a criar a app, também pode especificar uma chamada como um delegado que `UIParent` retorna.

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

No Android, recomendamos que [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin) utilize. O código de construtor resultante `PublicClientApplication` parece este exemplo:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>Encontre mais parâmetros de construção de aplicativos

Para obter uma lista de todos os métodos `PublicClientApplicationBuilder` disponíveis, consulte a lista [métodos.](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)

Para uma descrição de todas as opções `PublicClientApplicationOptions` expostas, consulte a [documentação de referência](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions).

## <a name="tasks-for-xamarin-ios"></a>Tarefas para Xamarin iOS

Se utilizar MSAL.NET no Xamarin iOS, faça as seguintes tarefas.

* [Sobrepor e implementar a `OpenUrl` função em `AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Ativar grupos de chavechain](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Permitir a partilha de cache token](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Permitir o acesso ao chaveiro](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Para mais informações, consulte [as considerações do Xamarin iOS](msal-net-xamarin-ios-considerations.md).

## <a name="tasks-for-msal-for-ios-and-macos"></a>Tarefas para MSAL para iOS e macOS

Estas tarefas são necessárias quando utiliza o MSAL para iOS e macOS:

* [Implementar o `openURL` retorno](#brokered-authentication-for-msal-for-ios-and-macos)
* [Ativar grupos de acesso ao chaveiro](howto-v2-keychain-objc.md)
* [Personalizar browsers e WebViews](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Tarefas para Xamarin.Android

Se utilizar Xamarin.Android, faça as seguintes tarefas:

- [Certifique-se de que o controlo volta para a MSAL após o fim da parte interativa do fluxo de autenticação](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Atualize o manifesto Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest-for-system-webview-support)
- [Utilize a vista web incorporada (opcional)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Resolução de problemas, se necessário](msal-net-xamarin-android-considerations.md#troubleshooting)

Para mais informações, consulte [as considerações de Xamarin.Android.](msal-net-xamarin-android-considerations.md)

Para obter considerações sobre os navegadores no Android, consulte [considerações específicas de Xamarin.Android com MSAL.NET](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>Tarefas para uWP

Na UWP, podes usar redes corporativas. As secções seguintes explicam as tarefas que deve completar no cenário corporativo.

Para obter mais informações, consulte [considerações específicas da UWP com MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configure-the-application-to-use-the-broker"></a>Configure a aplicação para usar o corretor

No Android e iOS, os corretores permitem:

- **Único sinal de sinalização (SSO)**: Pode utilizar SSO para dispositivos registados no Azure Ative Directory (Azure AD). Quando utiliza sSO, os seus utilizadores não precisam de iniciar sômposições em cada aplicação.
- **Identificação do dispositivo**: Esta definição permite políticas de acesso condicional relacionadas com dispositivos AD Azure. O processo de autenticação utiliza o certificado do dispositivo que foi criado quando o dispositivo foi associado ao local de trabalho.
- **Verificação de identificação de aplicação**: Quando uma aplicação chama o corretor, passa o seu URL de redirecionamento. Então o corretor verifica.

### <a name="enable-the-broker-on-xamarin"></a>Ativar o corretor em Xamarin

Para ativar o corretor em Xamarin, utilize o `WithBroker()` parâmetro quando ligar para o `PublicClientApplicationBuilder.CreateApplication` método. Por defeito, `.WithBroker()` está definido como verdadeiro.

Para ativar a autenticação intermediada para Xamarin.iOS, siga os passos na [secção Xamarin.iOS](#enable-brokered-authentication-for-xamarin-ios) neste artigo.

### <a name="enable-the-broker-for-msal-for-android"></a>Ativar o corretor para MSAL para Android

Para obter informações sobre como ativar um corretor no Android, consulte [a autenticação mediada no Android.](msal-android-single-sign-on.md)

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Ativar o corretor para MSAL para iOS e macOS

A autenticação intermediada é ativada por padrão para cenários Azure AD em MSAL para iOS e macOS.

As seguintes secções fornecem instruções para configurar o seu pedido de suporte de autenticação intermediado para o MSAL para Xamarin.iOS ou MSAL para iOS e macOS. Nos dois conjuntos de instruções, alguns dos passos diferem.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Ativar a autenticação intermediada para Xamarin iOS

Siga os passos nesta secção para permitir que a sua aplicação Xamarin.iOS fale com a aplicação [Microsoft Authenticator.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

#### <a name="step-1-enable-broker-support"></a>Passo 1: Permitir o suporte do corretor

O suporte do corretor é desativado por padrão. Permite-se uma `PublicClientApplication` aula individual. Utilize o `WithBroker()` parâmetro quando criar a classe através `PublicClientApplication` `PublicClientApplicationBuilder` . O `WithBroker()` parâmetro é definido para verdadeiro por defeito.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Passo 2: Atualizar AppDeegate para lidar com a chamada

Quando MSAL.NET chama o corretor, o corretor volta a ligar para a sua aplicação. Volta a ligar usando o `AppDelegate.OpenUrl` método. Como a MSAL aguarda a resposta do corretor, a sua aplicação precisa de cooperar para voltar a ligar MSAL.NET. Configura este comportamento atualizando o `AppDelegate.cs` ficheiro para anular o método, como mostra o seguinte código.

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

Para xamarin iOS, normalmente não é necessário definir uma janela de objeto. Mas neste caso deve defini-lo para que possa enviar e receber respostas de um corretor. Para definir uma janela de objeto, `AppDelegate.cs` in, você definir um `ViewController` .

Para definir a janela do objeto, siga estes passos:

1. Em `AppDelegate.cs` , definir o para um novo `App.RootViewController` `UIViewController()` . Esta definição garante que a chamada para o corretor inclui `UIViewController` . Se não estiver corretamente definido, poderá obter este erro:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. Na `AcquireTokenInteractive` chamada, `.WithParentActivityOrWindow(App.RootViewController)` use. Passe na referência à janela do objeto que vai usar. Eis um exemplo:

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

MSAL.NET usa URLs para invocar o corretor e, em seguida, devolver a resposta do corretor à sua aplicação. Para terminar a viagem de ida e volta, registe o esquema de URL da sua aplicação no `Info.plist` ficheiro.

Para registar o esquema de URL da sua aplicação, siga estes passos:

1. Prefixo `CFBundleURLSchemes` com `msauth` .
1. Adicione `CFBundleURLName` ao fim. Siga este padrão:

   `$"msauth.(BundleId)"`

   Aqui, `BundleId` identifica exclusivamente o seu dispositivo. Por exemplo, se `BundleId` `yourcompany.xforms` for, o seu esquema de URL é `msauth.com.yourcompany.xforms` .

  
      Este esquema de URL passará a fazer parte do URI de redirecionamento que identifica exclusivamente a sua app quando recebe a resposta do corretor.

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

#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>Passo 5: Adicione à secção LSApplicationQueriesSchemes

A MSAL utiliza `–canOpenURL:` para verificar se o corretor está instalado no dispositivo. No iOS 9, a Apple bloqueou os esquemas que uma aplicação pode consultar.

Adicione `msauthv2` à secção do `LSApplicationQueriesSchemes` `Info.plist` ficheiro, como no seguinte exemplo de código:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Autenticação intermediada para MSAL para iOS e macOS

A autenticação intermediada é ativada por padrão para cenários Azure AD.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Passo 1: Atualizar AppDeegate para lidar com a chamada

Quando o MSAL para iOS e macOS liga para o corretor, o corretor volta para a sua aplicação utilizando o `openURL` método. Como a MSAL aguarda a resposta do corretor, a sua aplicação precisa de cooperar para chamar a MSAL. Configurar esta capacidade atualizando o `AppDelegate.m` ficheiro para anular o método, como mostram os seguintes exemplos de código.

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

Se tiver adotado `UISceneDelegate` no iOS 13 ou mais tarde, em seguida, coloque a chamada MSAL no `scene:openURLContexts:` de `UISceneDelegate` 20. O MSAL `handleMSALResponse:sourceApplication:` deve ser chamado apenas uma vez para cada URL.

Para mais informações, consulte a documentação da [Apple.](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)

#### <a name="step-2-register-a-url-scheme"></a>Passo 2: Registar um esquema de URL

MSAL para iOS e macOS usa URLs para invocar o corretor e, em seguida, devolver a resposta do corretor à sua app. Para terminar a viagem de ida e volta, registe um esquema de URL para a sua aplicação no `Info.plist` ficheiro.

Para registar um esquema para a sua aplicação:

1. Prefixar o seu esquema de URL personalizado com `msauth` .

1. Adicione o seu identificador de pacotes ao fim do seu esquema. Siga este padrão:

   `$"msauth.(BundleId)"`

   Aqui, `BundleId` identifica exclusivamente o seu dispositivo. Por exemplo, se `BundleId` `yourcompany.xforms` for, o seu esquema de URL é `msauth.com.yourcompany.xforms` .

    Este esquema de URL passará a fazer parte do URI de redirecionamento que identifica exclusivamente a sua app quando recebe a resposta do corretor. Certifique-se de que o URI de redirecionamento no formato `msauth.(BundleId)://auth` está registado para a sua aplicação no portal [Azure](https://portal.azure.com).

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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>Passo 3: Adicionar LSApplicationQueriesSchemes

Adicione `LSApplicationQueriesSchemes` para permitir chamadas para a aplicação Microsoft Authenticator, se for instalada.

> [!NOTE]
> O `msauthv3` esquema é necessário quando a sua aplicação é compilada utilizando o Xcode 11 e mais tarde.

Aqui está um exemplo de como `LSApplicationQueriesSchemes` adicionar:

```XML
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Autenticação intermediada para Xamarin.Android

Para obter informações sobre como ativar um corretor no Android, consulte [a autenticação mediada em Xamarin.Android](msal-net-use-brokers-with-xamarin-apps.md#brokered-authentication-for-android).

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [Adquirindo um símbolo](scenario-mobile-acquire-token.md).
