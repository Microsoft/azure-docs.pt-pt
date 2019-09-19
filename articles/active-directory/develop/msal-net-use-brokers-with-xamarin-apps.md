---
title: Usar Microsoft Authenticator ou Microsoft Intune Portal da Empresa em aplicativos Xamarin iOS e Android | Azure
description: Saiba como migrar aplicativos do Xamarin iOS que podem usar Microsoft Authenticator da biblioteca de autenticação do AD do Azure para .NET (ADAL.NET) para a biblioteca de autenticação da Microsoft para .NET (MSAL.NET)
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
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: abac7b95ceed0a199531b7ba30cea5fc1c9cc1e0
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103977"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Usar Microsoft Authenticator ou Microsoft Intune Portal da Empresa em aplicativos Xamarin

No Android e iOS, os agentes como Microsoft Authenticator ou Microsoft Intune Portal da Empresa habilitam (somente Android):

- SSO (logon único). Os usuários não precisarão entrar em cada aplicativo.
- Identificação do dispositivo. O agente acessa o certificado do dispositivo, que foi criado no dispositivo quando ele foi ingressado no local de trabalho.
- Verificação da identificação do aplicativo. Quando um aplicativo chama o agente, ele passa sua URL de redirecionamento e o agente o verifica.

Para habilitar um desses recursos, os desenvolvedores de aplicativos precisam usar o `WithBroker()` parâmetro quando chamam o `PublicClientApplicationBuilder.CreateApplication` método. `.WithBroker()`é definido como true por padrão. Os desenvolvedores também precisam seguir as etapas aqui para aplicativos [Ios](#brokered-authentication-for-ios) ou [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Autenticação orientada para iOS

Siga estas etapas para habilitar seu aplicativo Xamarin. iOS para conversar com o aplicativo [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Passo 1: Habilitar suporte do Broker
O suporte do Broker é habilitado por PublicClientApplication. Ele é desabilitado por padrão. Use o `WithBroker()` parâmetro (definido como true por padrão) ao criar o PublicClientApplication por meio do PublicClientApplicationBuilder.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Passo 2: Atualizar AppDelegate para manipular o retorno de chamada
Quando a MSAL.net (biblioteca de autenticação da Microsoft para .net) chama o agente, o agente, por sua vez, chama de `OpenUrl` volta para seu `AppDelegate` aplicativo por meio do método da classe. Como MSAL aguarda a resposta do agente, seu aplicativo precisa cooperar para chamar MSAL.NET de volta. Para habilitar essa cooperação, atualize o `AppDelegate.cs` arquivo para substituir o método a seguir.

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

Esse método é invocado toda vez que o aplicativo é iniciado. Ele é usado como uma oportunidade de processar a resposta do agente e concluir o processo de autenticação iniciado pelo MSAL.NET.

### <a name="step-3-set-a-uiviewcontroller"></a>Passo 3: Definir um UIViewController ()
Ainda no `AppDelegate.cs`, você precisa definir uma janela de objeto. Normalmente, com o Xamarin iOS, você não precisa definir a janela de objeto. Para enviar e receber respostas do agente, você precisa de uma janela de objeto. 

Para fazer isso, você faz duas coisas. 
1. No `AppDelegate.cs`, defina o `App.RootViewController` para um novo `UIViewController()`. Essa atribuição certifica-se de que há um UIViewController com a chamada para o agente. Se não estiver definido corretamente, você poderá receber esse erro:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Na chamada AcquireTokenInteractive, use `.WithParentActivityOrWindow(App.RootViewController)` e passe a referência para a janela de objeto que você usará.

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

### <a name="step-4-register-a-url-scheme"></a>Passo 4: Registrar um esquema de URL
O MSAL.NET usa URLs para invocar o agente e, em seguida, retornar a resposta do agente de volta para seu aplicativo. Para concluir a viagem de ida e volta, registre um esquema de URL para `Info.plist` seu aplicativo no arquivo.

O `CFBundleURLSchemes` nome deve incluir `msauth.` como um prefixo, seguido pelo seu `CFBundleURLName`.

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

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Passo 5: Adicionar o identificador do agente à seção LSApplicationQueriesSchemes
O MSAL `–canOpenURL:` usa para verificar se o agente está instalado no dispositivo. No iOS 9, a Apple bloqueou o que os esquemas que um aplicativo pode consultar. 

Adicione `msauthv2` à`LSApplicationQueriesSchemes` seção do`Info.plist` arquivo.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirect-uri-in-the-application-portal"></a>Passo 6: Registrar o URI de redirecionamento no portal do aplicativo
O uso do agente adiciona um requisito extra no URI de redirecionamento. O URI de redirecionamento _deve_ ter o seguinte formato:
```CSharp
$"msauth.{BundleId}://auth"
```
**Por exemplo:**
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
Observe que o URI de redirecionamento corresponde ao `CFBundleURLSchemes` nome que você incluiu `Info.plist` no arquivo.

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Passo 7: Certifique-se de que o URI de redirecionamento esteja registrado com seu aplicativo

Esse URI de redirecionamento precisa ser registrado no portal de registro https://portal.azure.com) de aplicativo (como um URI de redirecionamento válido para seu aplicativo. 

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

O suporte do agente não está disponível para Android.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [Considerações específicas de plataforma universal do Windows com o MSAL.net](msal-net-uwp-considerations.md).