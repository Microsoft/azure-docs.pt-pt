---
title: Utilize corretores com Xamarin iOS & | Android Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar aplicações xamarin iOS que podem utilizar o Autenticador microsoft e a Biblioteca de Autenticação do Microsoft para .NET (MSAL.NET). Aprenda também a migrar da Biblioteca de Autenticação AD Azure para .NET (ADAL.NET) para a Biblioteca de Autenticação do Microsoft para .NET (MSAL.NET).
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
ms.openlocfilehash: 226e94510709b37a7e6b1aae90a7e0ec5b4222b9
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199583"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Utilize o Microsoft Authenticator ou o Portal da Empresa Intune em aplicações Xamarin

No Android e iOS, corretores como o Microsoft Authenticator e o Portal da Empresa Microsoft Intune específico para Android permitem:

- **Único sinal de sso :** Os utilizadores não precisam de iniciar sômposições em cada aplicação.
- **Identificação do dispositivo**: O corretor acede ao certificado do dispositivo. Este certificado é criado no dispositivo quando é ligado ao local de trabalho.
- **Verificação de identificação de aplicação**: Quando uma aplicação chama o corretor, passa o seu URL de redirecionamento. O corretor verifica a URL.

Para ativar uma destas funcionalidades, utilize o `WithBroker()` parâmetro quando ligar para o `PublicClientApplicationBuilder.CreateApplication` método. O `.WithBroker()` parâmetro é definido para verdadeiro por defeito.

A configuração da autenticação intermediada na Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET) varia por plataforma:

* [aplicações iOS](#brokered-authentication-for-ios)
* [Aplicações Android](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Autenticação intermediada para iOS

Utilize os seguintes passos para permitir que a sua aplicação Xamarin.iOS comunique com a aplicação [Microsoft Authenticator.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) Se está a direcionar o iOS 13, considere ler sobre a [mudança de API da Apple.](./msal-net-xamarin-ios-considerations.md)

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

Quando MSAL.NET chama o corretor, o corretor volta para a sua aplicação através `OpenUrl` do método da `AppDelegate` classe. Como a MSAL aguarda a resposta do corretor, a sua aplicação precisa de cooperar para voltar a ligar MSAL.NET. Para permitir esta cooperação, atualize o ficheiro *.cs AppDeegate* para anular o seguinte método.

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

Ainda no ficheiro *.cs AppDeegate,* desfise uma janela de objeto. Normalmente, não precisa de definir a janela do objeto para o IOS de Xamarin, mas precisa de uma janela de objeto para enviar e receber respostas do corretor.

Para configurar a janela do objeto:

1. No ficheiro *.cs AppDeegate,* definido `App.RootViewController` para um novo `UIViewController()` . Esta atribuição garante que a chamada para o corretor inclui `UIViewController` . Se esta definição for atribuída incorretamente, poderá obter este erro:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Na `AcquireTokenInteractive` chamada, use `.WithParentActivityOrWindow(App.RootViewController)` e passe na referência à janela do objeto que utilizará.

    Na *App.cs:*

    ```csharp
       public static object RootViewController { get; set; }
    ```

    No *AppDeegate.cs:*

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

MSAL.NET usa URLs para invocar o corretor e, em seguida, devolver a resposta do corretor à sua app. Para completar a viagem de ida e volta, registe um esquema de URL para a sua aplicação no ficheiro *Info.plist.*

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

Adicione `msauthv2` à secção do ficheiro `LSApplicationQueriesSchemes` *Info.plist,* como no seguinte exemplo:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-add-a-redirect-uri-to-your-app-registration"></a>Passo 7: Adicione um URI redirecionado ao registo da sua aplicação

Quando utiliza o corretor, o seu URI de redirecionamento tem um requisito extra. O URI de redirecionamento _deve_ ter o seguinte formato:

```csharp
$"msauth.{BundleId}://auth"
```

Eis um exemplo:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth";
```

Note que o URI de redirecionamento corresponde ao `CFBundleURLSchemes` nome que incluiu no ficheiro *Info.plist.*

Adicione o URI redirecionado ao registo da aplicação no [portal Azure](https://portal.azure.com). Para gerar um URI de redirecionamento devidamente formatado, utilize **os registos da App** no portal Azure para gerar o URI de redirecionamento intermediado a partir do ID do pacote.

**Para gerar o URI de redirecionamento:**

1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Selecione registos de Aplicações **de Diretório Ativo Azure**  >   > sua aplicação registada
1. **Selecione autenticação**  >  **Adicione uma plataforma**  >  **iOS / macOS**
1. Introduza o seu iD do pacote e, em seguida, **selecione Configure**.

    Copie o URI de redirecionamento gerado que aparece na caixa de texto **Redirect URI** para inclusão no seu código:

    :::image type="content" source="media/msal-net-use-brokers-with-xamarin-apps/portal-01-ios-platform-settings.png" alt-text="Configurações da plataforma iOS com URI de redirecionamento gerado no portal Azure":::
1. Selecione **Feito** para completar a geração do URI de redirecionamento.

## <a name="brokered-authentication-for-android"></a>Autenticação intermediada para Android

### <a name="step-1-enable-broker-support"></a>Passo 1: Permitir o suporte do corretor

O suporte do corretor é ativado por `PublicClientApplication` base. É desativado por defeito. Utilize o `WithBroker()` parâmetro (definido para verdadeiro por padrão) ao criar o `IPublicClientApplication` através do `PublicClientApplicationBuilder` .

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) // See step #4
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Passo 2: Atualizar AppDeegate para lidar com a chamada

Quando MSAL.NET ligar para o corretor, o corretor, por sua vez, ligará de volta para a sua aplicação com o `OnActivityResult()` método. Uma vez que a MSAL aguardará a resposta do corretor, a sua aplicação precisa de encaminhar o resultado para MSAL.NET.

Encaminhe o resultado para o `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` método, sobrepôs o `OnActivityResult()` método como mostrado aqui:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Este método é invocado sempre que a aplicação do corretor é lançada, e é usado como uma oportunidade para processar a resposta do corretor e completar o processo de autenticação iniciado por MSAL.NET.

### <a name="step-3-set-an-activity"></a>Passo 3: Definir uma Atividade

Para ativar a autenticação intermediada, detenva uma atividade para que a MSAL possa enviar e receber a resposta de e para o corretor. Para tal, forneça a atividade (normalmente `MainActivity` a) ao `WithParentActivityOrWindow(object parent)` objeto-mãe.

Por exemplo, na chamada `AcquireTokenInteractive()` para:

```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-add-a-redirect-uri-to-your-app-registration"></a>Passo 4: Adicione um URI redirecionado ao registo da sua aplicação

A MSAL utiliza URLs para invocar o corretor e, em seguida, voltar à sua aplicação. Para completar essa viagem de ida e volta, registe um **URI de redirecionamento** para a sua aplicação utilizando o [portal Azure.](https://portal.azure.com)

O formato do URI de redirecionamento para a sua aplicação depende do certificado utilizado para assinar a APK. Por exemplo:

```
msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

A última parte do URI, `hgbUYHVBYUTvuvT&Y6tr554365466=` é a versão codificada base64 da assinatura com a qual a APK é assinada. Ao desenvolver a sua aplicação no Visual Studio, se estiver a depurar o seu código sem assinar o APK com um certificado específico, o Visual Studio assina o APK para si para fins de depuração. Quando o Visual Studio assina o APK para si desta forma, dá-lhe uma assinatura única para a máquina em que é construída. Assim, cada vez que construir a sua app numa máquina diferente, terá de atualizar o URI de redirecionamento no código da aplicação e o registo da aplicação no portal Azure para autenticar com o MSAL.

Durante a depuragem, pode encontrar uma exceção MSAL (ou mensagem de registo) indicando que o URI de redireccionamento fornecido está incorreto. **A exceção ou mensagem de registo também indica o URI de redirecionamento que deve utilizar** com a máquina atual em que está a depurar. Pode utilizar o URI de redirecionamento fornecido para continuar a desenvolver a sua app desde que atualize o URI redirecionado em código e adicione o URI de redirecionamento fornecido ao registo da app no portal Azure.

Assim que estiver pronto para finalizar o seu código, atualize o URI de redirecionamento no código e o registo da aplicação no portal Azure para utilizar a assinatura do certificado com o qual assina a APK.

Na prática, isto significa que deve considerar adicionar um URI redirecionado para cada membro da sua equipa de desenvolvimento, *além* de um URI de redirecionamento para a versão assinada pela produção da APK.

Você mesmo pode calcular a assinatura, semelhante à forma como a MSAL o faz:

```csharp
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

Também tem a opção de adquirir a assinatura do seu pacote utilizando **o porta-chaves** com os seguintes comandos:

* Windows:
    ```console
    keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
    ````
* macOS:
    ```console
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ````

### <a name="step-5-optional-fall-back-to-the-system-browser"></a>Passo 5 (opcional): Recue para o navegador do sistema

Se o MSAL estiver configurado para utilizar o corretor, mas o corretor não estiver instalado, o MSAL voltará a utilizar uma visualização web (um browser). O MSAL tentará autenticar usando o navegador do sistema padrão no dispositivo, o que falha porque o URI de redirecionamento está configurado para o corretor e o navegador do sistema não sabe como usá-lo para navegar de volta para o MSAL. Para evitar a falha, pode configurar um *filtro de intenção* com o reorientar o URI do corretor utilizado no passo 4.

Modifique o manifesto da sua aplicação para adicionar o filtro de intenção:

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="Package Name"
                    android:path="/Package Signature"/>
```

Por exemplo, se tiver um URI de `msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=` redirecionamento, o seu manifesto deve parecer o seguinte corte XML.

É necessário o corte para `/` a frente ( ) em frente à assinatura no valor `android:path` . 

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="com.microsoft.xforms.testApp"
                    android:path="/hgbUYHVBYUTvuvT&Y6tr554365466="/>
```

Para obter mais informações sobre a configuração da sua aplicação para o navegador do sistema e suporte ao Android 11, consulte [Update the Android manifest para suporte ao navegador do sistema.](msal-net-xamarin-android-considerations.md#update-the-android-manifest-for-system-webview-support)

Como alternativa, pode configurar a MSAL para voltar ao navegador incorporado, que não depende de um URI de redirecionamento:

```csharp
.WithUseEmbeddedWebUi(true)
```

## <a name="troubleshooting-tips-for-android-brokered-authentication"></a>Dicas de resolução de problemas para autenticação intermediada por Android

Aqui ficam algumas dicas para evitar problemas quando implementa a autenticação intermediada no Android:

- **Redirecionamento URI** - Adicione um URI redirecionado ao seu registo de inscrição no [portal Azure](https://portal.azure.com/). Um URI de redirecionamento em falta ou incorreto é uma questão comum encontrada pelos desenvolvedores.
- **Versão broker** - Instale a versão mínima exigida das aplicações do corretor. Qualquer uma destas duas aplicações pode ser utilizada para autenticação intermediada no Android.
  - [Portal da Empresa Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) (versão 5.0.4689.0 ou superior)
  - [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) (versão 6.2001.0140 ou superior).
- **Precedência do corretor** - A MSAL comunica com o *primeiro corretor instalado* no dispositivo quando vários corretores são instalados.

    Exemplo: Se instalar primeiro o Microsoft Authenticator e depois instalar o Portal da Empresa Intune, a autenticação intermediada *só* acontecerá no Autenticador microsoft.
- **Registos** - Se encontrar um problema com a autenticação intermediada, a visualização dos registos do corretor poderá ajudá-lo a diagnosticar a causa.
  - Obtenha registos autenticadores da Microsoft:

    1. Selecione o botão do menu no canto superior direito da aplicação.
    1. Selecione **Enviar Feedback**  >  **Com Problemas?**
    1. Em **O que está a tentar fazer?**
    1. Para enviar os registos, selecione a seta no canto superior direito da aplicação.

    Depois de enviar os registos, uma caixa de diálogo mostra o ID do incidente. Grave a identificação do incidente, e inclua-o quando pedir assistência.

  - Obtenha registos do Portal da Empresa Intune:

    1. Selecione o botão do menu no canto superior esquerdo da aplicação.
    1. Selecione **Suporte**  >  **de e-mail de ajuda**.
    1. Para enviar os registos, selecione **'Apenas' 'Registares'** de upload .

    Depois de enviar os registos, uma caixa de diálogo mostra o ID do incidente. Grave a identificação do incidente, e inclua-o quando pedir assistência.

## <a name="next-steps"></a>Passos seguintes

Saiba mais [sobre considerações para utilizar a Plataforma Universal do Windows com MSAL.NET](msal-net-uwp-considerations.md).
