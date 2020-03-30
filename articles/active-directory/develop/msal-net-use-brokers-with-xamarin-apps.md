---
title: Utilize corretores com xamarin iOS & Android / Azure
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
ms.openlocfilehash: 1a57173311278c5e3e0304aeb12d4d6999379eb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262792"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Utilize o Autenticador microsoft ou o Portal da Empresa Intune nas aplicações Xamarin

No Android e iOS, corretores como o Microsoft Authenticator e o Portal da Empresa Intune microsoft específico para android permitem:

- **Único sinal (SSO)**: Os utilizadores não precisam de iniciar sessão em cada aplicação.
- **Identificação do dispositivo**: O corretor acede ao certificado do dispositivo. Este certificado é criado no dispositivo quando é ligado ao local de trabalho.
- **Verificação de identificação de aplicações**: Quando uma aplicação chama o corretor, passa o url de redirecionamento. O corretor verifica o URL.

Para ativar uma destas `WithBroker()` funcionalidades, utilize `PublicClientApplicationBuilder.CreateApplication` o parâmetro quando ligar para o método. O `.WithBroker()` parâmetro é definido para verdade por defeito. 

Utilize também as instruções nas seguintes secções para configurar a autenticação intermediada para aplicações [iOS](#brokered-authentication-for-ios) ou aplicações [Android.](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Autenticação intermediada para iOS

Utilize os seguintes passos para permitir que a sua aplicação Xamarin.iOS fale com a aplicação [Microsoft Authenticator.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

### <a name="step-1-enable-broker-support"></a>Passo 1: Ativar suporte ao corretor
Deve ativar o suporte do `PublicClientApplication`corretor para casos individuais de . O suporte é desativado por defeito. Quando `PublicClientApplication` `PublicClientApplicationBuilder`criar, utilize `WithBroker()` o parâmetro como mostra o seguinte exemplo. O `WithBroker()` parâmetro é definido para verdade por defeito.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Passo 2: Ativar o acesso ao porta-chaves

Para permitir o acesso ao porta-chaves, deve ter um grupo de acesso à porta-chaves para a sua aplicação. Pode utilizar `WithIosKeychainSecurityGroup()` a API para definir o seu grupo de acesso ao porta-chaves quando criar a sua aplicação:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Para mais informações, consulte [Enable keychain access](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Passo 3: Atualizar appDelegate para lidar com o backback
Quando a Microsoft Authentication Library for .NET (MSAL.NET) ligar para `OpenUrl` o corretor, o corretor volta a ligar para a sua aplicação através do método da `AppDelegate` classe. Como a MSAL aguarda a resposta do corretor, o seu pedido precisa de cooperar para chamar MSAL.NET de volta. Para permitir esta cooperação, atualize o `AppDelegate.cs` ficheiro para anular o seguinte método.

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
Ainda no `AppDelegate.cs` ficheiro, tens de definir uma janela de objetos. Normalmente, para o xamarin iOS não é necessário definir a janela do objeto. Mas precisa de uma janela de objeto para enviar e receber respostas do corretor. 

Para configurar a janela do objeto: 
1. No `AppDelegate.cs` ficheiro, `App.RootViewController` definido para `UIViewController()`um novo . Esta atribuição garante que a chamada `UIViewController`para o corretor inclui . Se esta definição for atribuída incorretamente, poderá obter este erro:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Na `AcquireTokenInteractive` chamada, `.WithParentActivityOrWindow(App.RootViewController)` utilize e, em seguida, passe a referência à janela do objeto que utilizará.

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
MSAL.NET usa URLs para invocar o corretor e depois devolver a resposta do corretor à sua aplicação. Para completar a viagem de ida e `Info.plist` volta, registe um esquema de URL para a sua aplicação no ficheiro.

O `CFBundleURLSchemes` nome `msauth.` deve incluir como prefixo. Siga o prefixo com `CFBundleURLName`. 

No esquema DEURL, `BundleId` identifica exclusivamente `$"msauth.(BundleId)"`a aplicação: . Então, `BundleId` `com.yourcompany.xforms`se é, então o esquema de URL é `msauth.com.yourcompany.xforms`.

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

Adicione `msauthv2` à `LSApplicationQueriesSchemes` secção `Info.plist` do ficheiro, como no seguinte exemplo:

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

Note que o URI `CFBundleURLSchemes` redirecionamento `Info.plist` corresponde ao nome que incluiu no ficheiro.

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

### <a name="step-1-enable-broker-support"></a>Passo 1: Ativar suporte ao corretor

O suporte do corretor é ativado numa base por PublicClientApplication. É desativado por defeito. Utilize `WithBroker()` o parâmetro (definido para ser verdadeiro `IPublicClientApplication` por `PublicClientApplicationBuilder`defeito) ao criar o .

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Passo 2: Atualizar appDelegate para lidar com o backback

Quando MSAL.NET chama o corretor, o corretor irá, por sua vez, voltar a ligar para a sua aplicação com o método OnActivityResult(). Uma vez que a MSAL aguardará a resposta do corretor, a sua aplicação precisa de encaminhar o resultado para MSAL.NET.
Isto pode ser conseguido encaminhando `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` o resultado para o método OnActivityResult() como mostrado abaixo

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Este método é invocado sempre que a aplicação de corretor é lançada e é usada como uma oportunidade para processar a resposta do corretor e completar o processo de autenticação iniciado por MSAL.NET.

### <a name="step-3-set-an-activity"></a>Passo 3: Definir uma atividade

Para que a autenticação intermediada funcione, terá de definir uma atividade para que a MSAL possa enviar e receber a resposta do corretor.

Para isso, terá de fornecer a atividade (normalmente a `WithParentActivityOrWindow(object parent)` MainActivity) como objeto-mãe. 

**Por exemplo:**

Na chamada Acquire Token:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>Passo 4: Registe o seu RedirectUri no portal de aplicações

A MSAL usa URLs para invocar o corretor e depois voltar à sua aplicação. Para completar essa viagem de ida e volta, precisa de registar um esquema de URL para a sua aplicação. Este Redirect URI precisa de ser registado no portal de registo de aplicações Azure AD como um URI de redirecionamento válido para a sua aplicação.


O URI redirecionado necessário para a sua aplicação depende do certificado utilizado para assinar o APK.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

A última parte do `hgbUYHVBYUTvuvT&Y6tr554365466=`URI, é a assinatura com a que a APK é assinada, base64 codificada.
No entanto, durante a fase de desenvolvimento da sua aplicação utilizando o Visual Studio, se estiver a depurar o seu código sem assinar o apk com um certificado específico, o Visual Studio assinará o apk para si para fins de depuração, dando à APK uma assinatura única para o máquina em que é construído. Assim, sempre que construir a sua aplicação numa máquina diferente, terá de atualizar o URI redirecionado no código da aplicação e o registo da aplicação no portal Azure para autenticar com a MSAL. 

Durante a depuração, pode encontrar uma exceção MSAL (ou mensagem de registo) indicando que o URI redirecionado fornecido está incorreto. **Esta exceção também lhe fornecerá o URI redirecionado que deve utilizar** com a máquina atual em que está a depurar. Pode utilizar este URI redirecionado para continuar a desenvolver-se por enquanto.

Assim que estiver pronto para finalizar o seu código, certifique-se de atualizar o URI redirecionado no código e no registo da aplicação no portal Azure para utilizar a assinatura do certificado com o qual irá assinar a APK.

Na prática, isto significa que você tem que registar um URI redirecionado para cada membro da equipa, além de um URI redirecionado para a versão de produção assinada da APK.

Também pode calcular esta assinatura por si mesmo, semelhante à forma como a MSAL o faz: 

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

Também tem a opção de adquirir a assinatura para o seu pacote utilizando o teclado com os seguintes comandos:

Para janelas:`keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Para Mac:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [considerações para utilizar](msal-net-uwp-considerations.md)a Plataforma Universal windows com MSAL.NET .
