---
title: Use corretores com Xamarin iOS & Android Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar aplicações xamarin iOS que podem utilizar a Microsoft Authenticator e a Microsoft Authentication Library para .NET (MSAL.NET). Aprenda também a migrar da Biblioteca de Autenticação AD Azure para .NET (ADAL.NET) para a Microsoft Authentication Library para .NET (MSAL.NET).
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8e19677adf5fe0f64ad9e1c845f516f81ad89512
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166064"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Utilize o Microsoft Authenticator ou o Portal da Empresa Intune em aplicações Xamarin

No Android e iOS, corretores como o Microsoft Authenticator e o Portal da Empresa Microsoft Intune específico para Android permitem:

- **Único sinal de sso :** Os utilizadores não precisam de iniciar sômposições em cada aplicação.
- **Identificação do dispositivo**: O corretor acede ao certificado do dispositivo. Este certificado é criado no dispositivo quando é ligado ao local de trabalho.
- **Verificação de identificação de aplicação**: Quando uma aplicação chama o corretor, passa o seu URL de redirecionamento. O corretor verifica a URL.

Para ativar uma destas funcionalidades, utilize o `WithBroker()` parâmetro quando ligar para o `PublicClientApplicationBuilder.CreateApplication` método. O `.WithBroker()` parâmetro é definido para verdadeiro por defeito. 

Utilize também as instruções nas seguintes secções para configurar a autenticação intermediada para aplicações [iOS](#brokered-authentication-for-ios) ou aplicações [Android.](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Autenticação intermediada para iOS

Utilize os seguintes passos para permitir que a sua aplicação Xamarin.iOS fale com a aplicação [Microsoft Authenticator.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

### <a name="step-1-enable-broker-support"></a>Passo 1: Permitir o suporte do corretor
Deve permitir o suporte do corretor para instâncias individuais de `PublicClientApplication` . O suporte é desativado por predefinição. Quando criar `PublicClientApplication` `PublicClientApplicationBuilder` através , utilize o parâmetro como mostra `WithBroker()` o exemplo a seguir. O `WithBroker()` parâmetro é definido para verdadeiro por defeito.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Passo 2: Permitir o acesso ao chaveiro

Para permitir o acesso ao chaveiro, tem de ter um grupo de acesso ao chaveiro para a sua aplicação. Pode utilizar a `WithIosKeychainSecurityGroup()` API para definir o seu grupo de acesso ao chaveiro quando criar a sua aplicação:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Para obter mais informações, consulte [ativar o acesso ao chaveiro.](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Passo 3: Atualizar AppDeegate para lidar com a chamada
Quando a Microsoft Authentication Library for .NET (MSAL.NET) ligar para o corretor, o corretor volta a ligar para a sua aplicação através `OpenUrl` do método da `AppDelegate` classe. Como a MSAL aguarda a resposta do corretor, a sua aplicação precisa de cooperar para voltar a ligar MSAL.NET. Para permitir esta cooperação, atualize o `AppDelegate.cs` ficheiro para anular o seguinte método.

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

Este método é invocado sempre que a aplicação é iniciada. É usada como uma oportunidade para processar a resposta do corretor e completar o processo de autenticação que MSAL.NET iniciado.

### <a name="step-4-set-uiviewcontroller"></a>Passo 4: Definir UIViewController()
Ainda no `AppDelegate.cs` ficheiro, tens de definir uma janela de objetos. Normalmente, para xamarin iOS não é necessário definir a janela do objeto. Mas precisa de uma janela de objeto para enviar e receber respostas do corretor. 

Para configurar a janela do objeto: 
1. No `AppDelegate.cs` ficheiro, definido `App.RootViewController` para um novo `UIViewController()` . Esta atribuição garante que a chamada para o corretor inclui `UIViewController` . Se esta definição for atribuída incorretamente, poderá obter este erro:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Na `AcquireTokenInteractive` chamada, use `.WithParentActivityOrWindow(App.RootViewController)` e passe na referência à janela do objeto que utilizará.

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

### <a name="step-5-register-a-url-scheme"></a>Passo 5: Registar um esquema de URL
MSAL.NET usa URLs para invocar o corretor e, em seguida, devolver a resposta do corretor à sua app. Para completar a viagem de ida e volta, registe um esquema de URL para a sua aplicação no `Info.plist` ficheiro.

O `CFBundleURLSchemes` nome deve incluir como `msauth.` prefixo. Siga o prefixo com `CFBundleURLName` . 

No esquema de URL, `BundleId` identifica exclusivamente a aplicação: `$"msauth.(BundleId)"` . Então, se `BundleId` `com.yourcompany.xforms` for, então o esquema de URL é `msauth.com.yourcompany.xforms` .

> [!NOTE]
> Este esquema de URL torna-se parte do URI de redirecionamento que identifica exclusivamente a sua app quando recebe a resposta do corretor.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Passo 6: Adicione o identificador de corretor à secção LSApplicationQueriesSchemes

A MSAL utiliza `–canOpenURL:` para verificar se o corretor está instalado no dispositivo. No iOS 9, a Apple bloqueou os esquemas que uma aplicação pode consultar. 

Adicione `msauthv2` à secção do `LSApplicationQueriesSchemes` `Info.plist` ficheiro, como no seguinte exemplo:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Passo 7: Registe o seu URI de redirecionamento no portal de aplicações

Quando utiliza o corretor, o seu URI de redirecionamento tem um requisito extra. O URI de redirecionamento _deve_ ter o seguinte formato:

```csharp
$"msauth.{BundleId}://auth"
```

Eis um exemplo:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

Note que o URI de redirecionamento corresponde ao `CFBundleURLSchemes` nome que incluiu no `Info.plist` ficheiro.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Passo 8: Certifique-se de que o URI de redirecionamento está registado na sua app

O URI de redirecionamento precisa de ser registado no portal de registo de [aplicações](https://portal.azure.com) como um URI de redirecionamento válido para a sua aplicação. 

O portal de registo de aplicações fornece uma nova experiência para ajudá-lo a calcular a resposta intermediada URI a partir do iD do pacote. 

Para calcular o URI de redirecionamento:

1. No portal de registo de aplicações, escolha **Autenticação**  >  **Experimente a nova experiência.**

   ![Experimente a nova experiência de registo de aplicações](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. **Selecione Adicionar uma plataforma**.

   ![Adicionar uma plataforma](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Quando a lista de plataformas for suportada, selecione **iOS**.

   ![Configure iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Insira o seu iD do pacote como solicitado e, em seguida, selecione **Configure**.

   ![Insira o iD do pacote](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Quando terminar os passos, o URI de redirecionamento é calculado para si.

![Copy redirecionar URI](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Autenticação intermediada para Android

### <a name="step-1-enable-broker-support"></a>Passo 1: Permitir o suporte do corretor

O suporte ao corretor é ativado numa base de Aplicação por PublicClientApplication. É desativado por defeito. Utilize o `WithBroker()` parâmetro (definido para verdadeiro por padrão) ao criar o `IPublicClientApplication` através do `PublicClientApplicationBuilder` .

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Passo 2: Atualizar AppDeegate para lidar com a chamada

Quando MSAL.NET ligar para o corretor, o corretor, por sua vez, ligará de volta para a sua aplicação com o método OnActivityResult(). Uma vez que a MSAL aguardará a resposta do corretor, a sua aplicação precisa de encaminhar o resultado para MSAL.NET.
Isto pode ser conseguido através do encaminhamento do resultado para o `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` método OnActivityResult,como mostrado abaixo

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Este método é invocado sempre que a aplicação do corretor é lançada e é usado como uma oportunidade para processar a resposta do corretor e completar o processo de autenticação iniciado por MSAL.NET.

### <a name="step-3-set-an-activity"></a>Passo 3: Definir uma Atividade

Para a autenticação intermediada funcionar, terá de definir uma atividade para que a MSAL possa enviar e receber a resposta do corretor.

Para isso, terá de fornecer a atividade (normalmente a MainActivity) ao `WithParentActivityOrWindow(object parent)` objeto principal. 

**Por exemplo:**

Na chamada De Aquisição Token:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>Passo 4: Registe o seu RedirectUri no portal da aplicação

A MSAL usa URLs para invocar o corretor e, em seguida, voltar à sua aplicação. Para completar essa viagem de ida e volta, precisa de registar um esquema de URL para a sua aplicação. Este URI de redirecionamento precisa de ser registado no portal de registo de aplicações AZure AD como um URI de redirecionamento válido para a sua aplicação.


O URI de redirecionamento necessário para a sua aplicação depende do certificado utilizado para assinar a APK.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

A última parte do URI, `hgbUYHVBYUTvuvT&Y6tr554365466=` é a assinatura com a qual a APK é assinada, base64 codificada.
No entanto, durante a fase de desenvolvimento da sua aplicação utilizando o Visual Studio, se estiver a depurar o seu código sem assinar o apk com um certificado específico, o Visual Studio assinará o apk para si para fins de depuração, dando à APK uma assinatura única para a máquina em que foi construída. Assim, cada vez que construir a sua app numa máquina diferente, terá de atualizar o URI de redirecionamento no código da aplicação e o registo da aplicação no portal Azure para autenticar com o MSAL. 

Durante a depuragem, pode encontrar uma exceção MSAL (ou mensagem de registo) indicando que o URI de redireccionamento fornecido está incorreto. **Esta exceção também lhe fornecerá o URI de redirecionamento que deve utilizar** com a máquina atual em que está a depurar. Você pode usar este URI redirecionado para continuar a desenvolver-se por enquanto.

Assim que estiver pronto para finalizar o seu código, certifique-se de atualizar o URI de redirecionamento no código e no registo da aplicação no portal Azure para utilizar a assinatura do certificado com o qual irá assinar o APK.

Na prática, isto significa que tem de registar um URI de redirecionamento para cada membro da equipa, além de um URI de redirecionamento para a versão assinada pela produção da APK.

Você também pode calcular esta assinatura, semelhante à forma como a MSAL o faz: 

```CSharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

Tem também a opção de adquirir a assinatura do seu pacote utilizando o porta-chaves com os seguintes comandos:

Para janelas:`keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Para Mac:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>Passos seguintes

Saiba mais [sobre considerações para utilizar a Plataforma Universal do Windows com MSAL.NET](msal-net-uwp-considerations.md).
