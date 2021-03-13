---
title: Configuração de código Android Xamarin e resolução de problemas (MSAL.NET) | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações para utilizar o Xamarin Android com a Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 11642480ac817b50d102e396b8ab5e200948a615
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199552"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Requisitos de configuração e dicas de resolução de problemas para Xamarin Android com MSAL.NET

Existem várias alterações de configuração que é obrigado a fazer no seu código ao utilizar o Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET). As seguintes secções descrevem as modificações necessárias, seguidas por uma secção [de resolução de problemas](#troubleshooting) para ajudá-lo a evitar algumas das questões mais comuns.

## <a name="set-the-parent-activity"></a>Definir a atividade dos pais

No Xamarin Android, defina a atividade dos pais para que o token regresse após a interação:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

Em MSAL.NET 4.2 e mais tarde, também pode definir esta funcionalidade ao nível do [PublicClientApplication][PublicClientApplication]. Para tal, utilize uma chamada:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Se utilizar [o CurrentActivityPlugin,](https://github.com/jamesmontemagno/CurrentActivityPlugin)o seu [`PublicClientApplication`][PublicClientApplication] código de construtor deve ser semelhante a este corte de código:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Certifique-se de que o controlo regressa à MSAL

Quando a parte interativa do fluxo de autenticação terminar, volte o controlo à MSAL, sobrevoando o [`Activity`][Activity] .[`OnActivityResult()`][OnActivityResult] método.

Na sua substituição, ligue para o MSAL. NET's `AuthenticationContinuationHelper` .`SetAuthenticationContinuationEventArgs()` método para devolver o controlo à MSAL no final da parte interativa do fluxo de autenticação.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest-for-system-webview-support"></a>Atualize o manifesto Android para suporte ao System WebView 

Para suportar o System WebView, o ficheiro *AndroidManifest.xml* deve conter os seguintes valores:

```xml
<activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="msal{Client Id}" android:host="auth" />
  </intent-filter>
</activity>
```

O `android:scheme` valor é criado a partir do URI de redirecionamento que está configurado no portal de aplicações. Por exemplo, se o seu URI de redirecionamento `msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842://auth` for, a `android:scheme` entrada no manifesto seria semelhante a este exemplo:

```xml
<data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
```

Em alternativa, [crie a atividade em código](/xamarin/android/platform/android-manifest#the-basics) em vez de editar manualmente *AndroidManifest.xml*. Para criar a atividade em código, primeiro crie uma classe que inclua o `Activity` atributo e o `IntentFilter` atributo.

Aqui está um exemplo de uma classe que representa os valores do ficheiro XML:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="use-system-webview-in-brokered-authentication"></a>Utilizar o System WebView na autenticação intermediada

Para utilizar o System WebView como um recuo para a autenticação interativa quando configurar a sua aplicação para utilizar a autenticação intermediada e o dispositivo não tiver um corretor instalado, permita ao MSAL capturar a resposta de autenticação utilizando o URI de redirecionamento do corretor. A MSAL tentará autenticar utilizando o WebView do sistema predefinido no dispositivo quando detetar que o corretor não está disponível. Utilizando este padrão, falhará porque o URI de redirecionamento está configurado para usar um corretor, e o System WebView não sabe como usá-lo para voltar ao MSAL. Para resolver isto, crie um _filtro de intenção_ utilizando o corretor redirecionando o URI que configuraste anteriormente. Adicione o filtro de intenção modificando o manifesto da sua aplicação como este exemplo:

```xml
<!--Intent filter to capture System WebView or Authenticator calling back to our app after sign-in-->
<activity
      android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data android:scheme="msauth"
              android:host="Enter_the_Package_Name"
              android:path="/Enter_the_Signature_Hash" />
    </intent-filter>
</activity>
```

Substitua o nome do pacote que registou no portal Azure pelo `android:host=` valor. Substitua o haxixe chave que registou no portal Azure pelo `android:path=` valor. O haxixe de assinatura *não* deve ser codificado url. Certifique-se de que um corte dianteiro de referência `/` aparece no início do seu haxixe de assinatura.

### <a name="xamarinforms-43x-manifest"></a>Xamarin.Formulários 4.3.x manifesto

Xamarin.Forms 4.3.x gera código que define o `package` atributo `com.companyname.{appName}` em *AndroidManifest.xml*. Se usar `DataScheme` como `msal{client_id}` , então é melhor alterar o valor para corresponder ao valor do `MainActivity.cs` espaço de nome.

## <a name="android-11-support"></a>Suporte Android 11

Para utilizar o navegador do sistema e autenticação intermediada no Android 11, é necessário primeiro declarar estes pacotes, para que estes sejam visíveis para a aplicação. As aplicações que visam o Android 10 (API 29) e anteriormente podem consultar o SO para uma lista de pacotes que estão disponíveis no dispositivo a qualquer momento. Para suportar privacidade e segurança, o Android 11 reduz a visibilidade do pacote para uma lista padrão de pacotes de SO e os pacotes especificados no ficheiro *AndroidManifest.xml* da aplicação. 

Para permitir a autenticação da aplicação utilizando o navegador do sistema e o corretor, adicione a seguinte secção a *AndroidManifest.xml:*

```xml
<!-- Required for API Level 30 to make sure the app can detect browsers and other apps where communication is needed.-->
<!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
<queries>
  <package android:name="com.azure.authenticator" />
  <package android:name="{Package Name}" />
  <package android:name="com.microsoft.windowsintune.companyportal" />
  <!-- Required for API Level 30 to make sure the app detect browsers
      (that don't support custom tabs) -->
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="https" />
  </intent>
  <!-- Required for API Level 30 to make sure the app can detect browsers that support custom tabs -->
  <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
  <intent>
    <action android:name="android.support.customtabs.action.CustomTabsService" />
  </intent>
</queries>
``` 

`{Package Name}`Substitua-a pelo nome do pacote de aplicação. 

O seu manifesto atualizado, que agora inclui suporte para o navegador do sistema e autenticação intermediada, deve ser semelhante a este exemplo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.XamarinDev">
    <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="30" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <application android:theme="@android:style/Theme.NoTitleBar">
        <activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msauth" android:host="com.companyname.XamarinDev" android:path="/Fc4l/5I4mMvLnF+l+XopDuQ2gEM=" />
            </intent-filter>
        </activity>
    </application>
    <!-- Required for API Level 30 to make sure we can detect browsers and other apps we want to
     be able to talk to.-->
    <!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
    <queries>
        <package android:name="com.azure.authenticator" />
        <package android:name="com.companyname.xamarindev" />
        <package android:name="com.microsoft.windowsintune.companyportal" />
        <!-- Required for API Level 30 to make sure we can detect browsers
        (that don't support custom tabs) -->
        <intent>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="https" />
        </intent>
        <!-- Required for API Level 30 to make sure we can detect browsers that support custom tabs -->
        <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
        <intent>
            <action android:name="android.support.customtabs.action.CustomTabsService" />
        </intent>
    </queries>
</manifest>
```

## <a name="use-the-embedded-web-view-optional"></a>Utilize a vista web incorporada (opcional)

Por padrão, MSAL.NET utiliza o navegador web do sistema. Este navegador permite-lhe obter um único sign-on (SSO) utilizando aplicações web e outras aplicações. Em alguns casos raros, pode querer que o seu sistema utilize uma vista web incorporada.

Este exemplo de código mostra como configurar uma visão web incorporada:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Para obter mais informações, consulte [os navegadores web para obter](msal-net-web-browsers.md) considerações de MSAL.NET e [Xamarin Android.](msal-net-system-browser-android-considerations.md)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="general-tips"></a>Dicas gerais

- Atualize o pacote NuGet MSAL.NET existente para a [versão mais recente do MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/).
- Verifique se Xamarin.Forms está na versão mais recente.
- Verifique se Xamarin.Android.Support.v4 está na versão mais recente.
- Certifique-se de que todos os pacotes Xamarin.Android.Support visam a versão mais recente.
- Limpe ou reconstrua a aplicação.
- No Visual Studio, tente definir o número máximo de construções paralelas para **1**. Para isso, selecione **Opções**  >  **Projetos e Soluções**  >  **Construir e Executar** O número máximo de  >  **projetos paralelos constrói.**
- Se estiver a construir a partir da linha de comando e o seu comando `/m` utilizar, tente remover este elemento do comando.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Erro: O nome AutenticaçãoContinuationHelhelper não existe no contexto atual

Se um erro indicar que `AuthenticationContinuationHelper` não existe no contexto atual, o Visual Studio pode ter atualizado incorretamente o ficheiro *Android.csproj. \** Por vezes, o caminho do ficheiro no `<HintPath>` elemento contém incorretamente `netstandard13` em vez de `monoandroid90` .

Este exemplo contém um caminho de ficheiro correto:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte a amostra de uma [aplicação móvel Xamarin que utiliza a plataforma de identidade da Microsoft.](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) A tabela seguinte resume as informações relevantes no ficheiro README.

| Sample | Plataforma | Description |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UWP | Uma aplicação simples Xamarin.Forms que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e AD AD AZure através do ponto final Azure AD 2.0. A aplicação também mostra como aceder ao Microsoft Graph e mostra o token resultante. <br>![Diagrama de fluxo de autenticação](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
