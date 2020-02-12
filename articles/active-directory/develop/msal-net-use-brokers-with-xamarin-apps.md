---
title: Utilize corretores com xamarin iOS e Android / Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar aplicações xamarin iOS que podem utilizar o Autenticador Microsoft e a Microsoft Authentication Library para .NET (MSAL.NET). Aprenda também a migrar da Biblioteca de Autenticação AD Azure para .NET (ADAL.NET) para a Microsoft Authentication Library para .NET (MSAL.NET).
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
ms.openlocfilehash: 25b8aa9b5e80720e9543dafce7970404a62b7d1f
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132646"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Utilize o Autenticador microsoft ou o Portal da Empresa Intune nas aplicações Xamarin

No Android e iOS, corretores como o Microsoft Authenticator e o Portal da Empresa Intune microsoft específico para android permitem:

- **Único sinal (SSO)** : Os utilizadores não precisam de iniciar sessão em cada aplicação.
- **Identificação do dispositivo**: O corretor acede ao certificado do dispositivo. Este certificado é criado no dispositivo quando é ligado ao local de trabalho.
- **Verificação de identificação de aplicações**: Quando uma aplicação chama o corretor, passa o url de redirecionamento. O corretor verifica o URL.

Para ativar uma destas funcionalidades, utilize o parâmetro `WithBroker()` quando chamar o método `PublicClientApplicationBuilder.CreateApplication`. O parâmetro `.WithBroker()` é definido para verdade por defeito. 

Utilize também as instruções nas seguintes secções para configurar a autenticação intermediada para aplicações [iOS](#brokered-authentication-for-ios) ou aplicações [Android.](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Autenticação intermediada para iOS

Utilize os seguintes passos para permitir que a sua aplicação Xamarin.iOS fale com a aplicação [Microsoft Authenticator.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

### <a name="step-1-enable-broker-support"></a>Passo 1: Ativar suporte ao corretor
Deve ativar o suporte do corretor para casos individuais de `PublicClientApplication`. O suporte é desativado por defeito. Quando criar `PublicClientApplication` através `PublicClientApplicationBuilder`, utilize o parâmetro `WithBroker()` como mostra o seguinte exemplo. O parâmetro `WithBroker()` é definido para verdade por defeito.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Passo 2: Ativar o acesso ao porta-chaves

Para permitir o acesso ao porta-chaves, deve ter um grupo de acesso à porta-chaves para a sua aplicação. Pode utilizar a API `WithIosKeychainSecurityGroup()` para definir o seu grupo de acesso ao porta-chaves quando criar a sua aplicação:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Para mais informações, consulte [Enable keychain access](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Passo 3: Atualizar appDelegate para lidar com o backback
Quando a Microsoft Authentication Library for .NET (MSAL.NET) ligar para o corretor, o corretor volta a ligar para a sua aplicação através do método `OpenUrl` da classe `AppDelegate`. Como a MSAL aguarda a resposta do corretor, o seu pedido precisa de cooperar para chamar MSAL.NET de volta. Para permitir esta cooperação, atualize o ficheiro `AppDelegate.cs` para anular o seguinte método.

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

Este método é invocado sempre que a aplicação é iniciada. É usado como uma oportunidade para processar a resposta do corretor e completar o processo de autenticação que MSAL.NET iniciado.

### <a name="step-4-set-uiviewcontroller"></a>Passo 4: Definir uiviewcontroller()
Ainda no ficheiro `AppDelegate.cs`, tens de definir uma janela de objetos. Normalmente, para o xamarin iOS não é necessário definir a janela do objeto. Mas precisa de uma janela de objeto para enviar e receber respostas do corretor. 

Para configurar a janela do objeto: 
1. No ficheiro `AppDelegate.cs`, detetete `App.RootViewController` para um novo `UIViewController()`. Esta atribuição garante que a chamada para o corretor inclui `UIViewController`. Se esta definição for atribuída incorretamente, poderá obter este erro:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Na chamada `AcquireTokenInteractive`, use `.WithParentActivityOrWindow(App.RootViewController)` e, em seguida, passe a referência à janela do objeto que utilizará.

    Em `App.cs`:
    
    ```csharp
       public static object RootViewController { get; set; }
    ```
    
    Em `AppDelegate.cs`:
    
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    
    Na chamada `AcquireToken`:
    
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>Passo 5: Registar um esquema de URL
MSAL.NET usa URLs para invocar o corretor e depois devolver a resposta do corretor à sua aplicação. Para terminar a viagem de ida e volta, registe um esquema de URL para a sua aplicação no ficheiro `Info.plist`.

O nome `CFBundleURLSchemes` deve incluir `msauth.` como prefixo. Siga o prefixo com `CFBundleURLName`. 

No esquema de URL, `BundleId` identifica exclusivamente a aplicação: `$"msauth.(BundleId)"`. Portanto, se `BundleId` é `com.yourcompany.xforms`, então o sistema de URL é `msauth.com.yourcompany.xforms`.

> [!NOTE]
> Este esquema de URL torna-se parte do URI redirecionado que identifica exclusivamente a sua aplicação quando recebe a resposta do corretor.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Passo 6: Adicionar o identificador de corretor à secção LSApplicationQueriesSchemes
A MSAL utiliza `–canOpenURL:` para verificar se o corretor está instalado no dispositivo. No iOS 9, a Apple bloqueou os esquemas que uma aplicação pode consultar. 

Adicione `msauthv2` à secção `LSApplicationQueriesSchemes` do ficheiro `Info.plist`, como no seguinte exemplo:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Passo 7: Registe o seu URI redirecionado no portal de aplicações
Quando utiliza o corretor, o seu URI redirecionado tem um requisito extra. O uri redirecionamento _deve_ ter o seguinte formato:
```csharp
$"msauth.{BundleId}://auth"
```

Segue-se um exemplo: 

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
Note que o REdirectURI corresponde ao nome `CFBundleURLSchemes` que incluiu no ficheiro `Info.plist`.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Passo 8: Certifique-se de que o URI redirecionado está registado na sua aplicação

O redirecionamento URI precisa de ser registado no portal de registo de [aplicações](https://portal.azure.com) como um URI de redirecionamento válido para a sua aplicação. 

O portal de registo de aplicações fornece uma nova experiência para ajudá-lo a calcular a resposta intermediada URI a partir do id do pacote. 

Para calcular o redirecionamento uri:

1. No portal de registo da aplicação, escolha **Autenticação** > **Experimente a nova experiência**.

   ![Experimente a nova experiência de registo de aplicações](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Selecione **Adicionar uma plataforma**.

   ![Adicione uma plataforma](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Quando a lista de plataformas for suportada, selecione **iOS**.

   ![Configure iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Introduza o seu ID de pacote conforme solicitado e, em seguida, **selecione Configurar**.

   ![Insira o ID do pacote](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Quando terminar os passos, o URI redirecionado é calculado para si.

![Copiar redirecionar URI](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Autenticação intermediada para Android

MSAL.NET suporta apenas a plataforma Xamarin.iOS. Ainda não suporta corretores para a plataforma Xamarin.Android.

A biblioteca nativa MSAL Android já suporta a autenticação intermediada. Para mais informações, consulte [autenticação intermediada no Android](brokered-auth.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [considerações para utilizar](msal-net-uwp-considerations.md)a Plataforma Universal windows com MSAL.NET .
