---
title: Considerações do Xamarin Android (MSAL.NET) / Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações específicas ao utilizar o Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: fd6dd6781b808bc454a402a55aac9d07a6fc23b0
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085831"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Xamarin Android considerações específicas com MSAL.NET
Este artigo discute considerações específicas ao utilizar o Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Definir a atividade dos pais

Em Xamarin.Android, você precisa definir a atividade dos pais para que o token volte assim que a interação tiver acontecido.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```
Também pode defini-lo ao nível de PublicClientApplication (em MSAL4.2+) através de um backback.

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Uma recomendação é usar o CurrentActivityPlugin [aqui](https://github.com/jamesmontemagno/CurrentActivityPlugin).  Em seguida, o seu código de construtor de PublicClientApplication seria assim:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```


## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Garantir que o controlo remonta à MSAL assim que a parte interativa do fluxo de autenticação terminar
No Android, é necessário anular o método `OnActivityResult` do `Activity` e chamar o método SetAuthenticationContinuationContinuationEventArgs da classe AutenticaçãoContinuationHelper MSAL.

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```
Esta linha garante que o controlo remonta à MSAL assim que a parte interativa do fluxo de autenticação terminou.

## <a name="update-the-android-manifest"></a>Atualizar o manifesto Android
A `AndroidManifest.xml` deve conter os seguintes valores:

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity"><intent-filter> <action android:name="android.intent.action.VIEW" /> <category android:name="android.intent.category.DEFAULT" /> <category android:name="android.intent.category.BROWSABLE" /> <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" /> </intent-filter></activity>
```
Substitute the package name you registered in the Azure portal for the `android:host=` value. Substitute the key hash you registered in the Azure portal for the `android:path=` value. The Signature Hash should **not** be URL encoded. Ensure that there is a leading `/` at the beginning of your Signature Hash.

Or, you can [create the activity in code](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) and not manually edit `AndroidManifest.xml`. For that, you must create a class that has the `Activity` and `IntentFilter` attribute. A class that represents the same values of the above xml would be:

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

### <a name="xamarinforms-43x-manifest"></a>XamarinForms 4.3.X manifesto

O código gerado por XamarinForms 4.3.x define o atributo `package` para `com.companyname.{appName}` no `AndroidManifest.xml`. É melhor alterar o valor para ser o mesmo que o espaço de nome `MainActivity.cs`, se utilizar o `DataScheme` como `msal{client_id}`.

## <a name="use-the-embedded-web-view-optional"></a>Utilize a visão web incorporada (opcional)

Por padrão, MSAL.NET utiliza o navegador web do sistema, o que lhe permite obter SSO com aplicações Web e outras aplicações. Em alguns casos raros, é melhor especificar que pretende utilizar a visão web incorporada. Para mais informações, consulte [MSAL.NET utiliza um navegador Web](msal-net-web-browsers.md) e navegador de sistema [Android.](msal-net-system-browser-android-considerations.md)

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Resolução de problemas
Se criar uma nova aplicação Xamarin.Forms e adicionar uma referência ao pacote MSAL.Net NuGet, isto funcionará apenas.
No entanto, se quiser atualizar uma aplicação Xamarin.Forms existente para MSAL.NET pré-visualização 1.1.2 ou mais tarde poderá experimentar problemas de construção.

Para resolver estes problemas, deve:
- Atualize o pacote NuGet MSAL.NET existente para MSAL.NET pré-visualização 1.1.2 ou posterior
- Verifique se o Xamarin.Forms foi automaticamente atualizado para a versão 2.5.0.122203 (se não for, atualização para esta versão)
- Verifique se Xamarin.Android.Support.v4 atualizado automaticamente para a versão 25.4.0.2 (se não, atualização para esta versão)
- Todos os pacotes Xamarin.Android.Support devem ser direcionados para a versão 25.4.0.2
- Limpeza/Reconstrução
- Tente definir o projeto paralelo max construído para 1 no Estúdio Visual (Opções-&>Projetos e Soluções->Build e Run-> Número máximo de projetos paralelos construídos)
- Em alternativa, se estiver a construir a partir da linha de comando, tente retirar/m do seu comando se estiver a usá-lo.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Erro: O nome 'AutenticaçãoContinuationHelper' não existe no contexto atual

Isto provavelmente porque o Visual Studio não atualizou corretamente o ficheiro Android.csproj*. Por vezes, o **\<HintPath>** o filepath contém incorretamente o netstandard13 em vez de **monoandroid90**.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Passos seguintes

Mais detalhes e amostras são fornecidos no parágrafo [de Considerações Específicas do Android](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) do ficheiro readme.md da amostra seguinte:

| Sample | Plataforma | Descrição |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Uma simples aplicação Xamarin Forms que mostra como usar o MSAL para autenticar a MSA e a AD Azure através do ponto final AADD v2.0, e aceder ao Microsoft Graph com o token resultante. <br>![Topologia](media/msal-net-xamarin-android-considerations/topology.png) |
