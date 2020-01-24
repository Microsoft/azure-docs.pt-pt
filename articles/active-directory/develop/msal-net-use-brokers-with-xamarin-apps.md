---
title: Usar agentes com Xamarin, iOS, & Android | Azure
titleSuffix: Microsoft identity platform
description: Saiba como migrar aplicativos do Xamarin iOS que podem usar Microsoft Authenticator da biblioteca de autenticação do AD do Azure para .NET (ADAL.NET) para a biblioteca de autenticação da Microsoft para .NET (MSAL.NET)
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
ms.openlocfilehash: 839f62660096aaf3d7954acc45443f04d9ace77d
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695148"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Usar Microsoft Authenticator ou Microsoft Intune Portal da Empresa em aplicativos Xamarin

No Android e iOS, os agentes como Microsoft Authenticator ou Microsoft Intune Portal da Empresa habilitam (somente Android):

- SSO (logon único). Os usuários não precisarão entrar em cada aplicativo.
- Identificação do dispositivo. O agente acessa o certificado do dispositivo, que foi criado no dispositivo quando ele foi ingressado no local de trabalho.
- Verificação da identificação do aplicativo. Quando um aplicativo chama o agente, ele passa sua URL de redirecionamento e o agente o verifica.

Para habilitar um desses recursos, os desenvolvedores de aplicativos precisam usar o parâmetro `WithBroker()` ao chamarem o método `PublicClientApplicationBuilder.CreateApplication`. `.WithBroker()` é definido como true por padrão. Os desenvolvedores também precisam seguir as etapas aqui para aplicativos [Ios](#brokered-authentication-for-ios) ou [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Autenticação orientada para iOS

Siga estas etapas para habilitar seu aplicativo Xamarin. iOS para conversar com o aplicativo [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Etapa 1: habilitar o suporte do Broker
O suporte do Broker é habilitado por PublicClientApplication. Ele é desabilitado por padrão. Use o parâmetro `WithBroker()` (definido como true por padrão) ao criar o PublicClientApplication por meio do PublicClientApplicationBuilder.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Etapa 2: habilitar o acesso ao conjunto de chaves

Para habilitar o acesso ao conjunto de chaves, seu aplicativo deve ter um grupo de acesso de cadeia de chaves. Você pode usar a API `WithIosKeychainSecurityGroup()` para definir o grupo de acesso do conjunto de chaves ao criar seu aplicativo:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Para obter mais informações, consulte [habilitar o acesso](msal-net-xamarin-ios-considerations.md#enable-keychain-access)ao conjunto de chaves.

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Etapa 3: atualizar AppDelegate para manipular o retorno de chamada
Quando a MSAL.NET (biblioteca de autenticação da Microsoft para .NET) chama o agente, o agente, por sua vez, chama de volta para seu aplicativo por meio do método `OpenUrl` da classe `AppDelegate`. Como MSAL aguarda a resposta do agente, seu aplicativo precisa cooperar para chamar MSAL.NET de volta. Para habilitar essa cooperação, atualize o arquivo de `AppDelegate.cs` para substituir o método a seguir.

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

Esse método é invocado toda vez que o aplicativo é iniciado. Ele é usado como uma oportunidade de processar a resposta do agente e concluir o processo de autenticação iniciado pelo MSAL.NET.

### <a name="step-4-set-a-uiviewcontroller"></a>Etapa 4: definir um UIViewController ()
Ainda em `AppDelegate.cs`, você precisa definir uma janela de objeto. Normalmente, com o Xamarin iOS, você não precisa definir a janela de objeto. Para enviar e receber respostas do agente, você precisa de uma janela de objeto. 

Para fazer isso, você faz duas coisas. 
1. Em `AppDelegate.cs`, defina o `App.RootViewController` como um novo `UIViewController()`. Essa atribuição certifica-se de que há um UIViewController com a chamada para o agente. Se não estiver corretamente definido, poderá ter este erro: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Na chamada AcquireTokenInteractive, use o `.WithParentActivityOrWindow(App.RootViewController)` e passe a referência para a janela de objeto que você usará.

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
Na chamada de aquisição de token:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

### <a name="step-5-register-a-url-scheme"></a>Etapa 5: registrar um esquema de URL
O MSAL.NET usa URLs para invocar o agente e, em seguida, retornar a resposta do agente de volta para seu aplicativo. Para concluir a viagem de ida e volta, registre um esquema de URL para seu aplicativo no arquivo de `Info.plist`.

O nome do `CFBundleURLSchemes` deve incluir `msauth.` como um prefixo, seguido pelo seu `CFBundleURLName`.

`$"msauth.(BundleId)"`

**Por exemplo:**

`msauth.com.yourcompany.xforms`

> [!NOTE]
> Esse esquema de URL se torna parte do URI de redirecionamento que é usado para identificar exclusivamente seu aplicativo quando ele recebe a resposta do agente.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Etapa 6: Adicionar o identificador do agente à seção LSApplicationQueriesSchemes
O MSAL usa `–canOpenURL:` para verificar se o agente está instalado no dispositivo. No iOS 9, a Apple bloqueou o que os esquemas que um aplicativo pode consultar. 

Adicione `msauthv2` à seção `LSApplicationQueriesSchemes` do arquivo de `Info.plist`.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Etapa 7: registrar seu URI de redirecionamento no portal do aplicativo
O uso do agente adiciona um requisito extra no URI de redirecionamento. O URI de redirecionamento _deve_ ter o seguinte formato:
```csharp
$"msauth.{BundleId}://auth"
```
**Por exemplo:**
```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
Observe que o URI de redirecionamento corresponde ao nome de `CFBundleURLSchemes` que você incluiu no arquivo `Info.plist`.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Etapa 8: Verifique se o URI de redirecionamento está registrado com seu aplicativo

Esse URI de redirecionamento precisa ser registrado no portal de registro de aplicativo (https://portal.azure.com) como um URI de redirecionamento válido para seu aplicativo. 

O portal tem um novo portal de registro de aplicativo de experiência para ajudá-lo a calcular o URI de resposta orientado da ID do pacote.

1. No registro do aplicativo, escolha **autenticação** e selecione **experimentar a nova experiência**.

   ![Experimente a nova experiência de registro de aplicativo](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Selecione **Adicionar uma plataforma**.

   ![Adicionar uma plataforma](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Quando houver suporte para a lista de plataformas, selecione **Ios**.

   ![Configurar o iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Insira a ID do pacote conforme solicitado e, em seguida, selecione **Configurar**.

   ![Inserir ID do pacote](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

1. O URI de redirecionamento é calculado para você.

   ![Copiar URI de redirecionamento](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Autenticação orientada para Android

O MSAL.NET só dá suporte à plataforma Xamarin. iOS no momento. Ainda não há suporte para agentes para a plataforma Xamarin. Android.

A biblioteca nativa do MSAL Android já dá suporte a ela. Para obter detalhes, consulte [autenticação orientada no Android](brokered-auth.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [Considerações específicas de plataforma universal do Windows com o MSAL.net](msal-net-uwp-considerations.md).
