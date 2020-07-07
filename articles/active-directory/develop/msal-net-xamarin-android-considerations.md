---
title: Considerações do Xamarin Android (MSAL.NET) Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações para utilizar o Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).
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
ms.openlocfilehash: bb5950360734bc46923ef18424e3ad1ce275ad7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82652676"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>Considerações para usar Xamarin Android com MSAL.NET
Este artigo discute o que deve considerar quando utiliza o Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Definir a atividade dos pais

No Xamarin Android, defina a atividade dos pais para que o token regresse após a interação. Aqui está um exemplo de código:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

No MSAL 4.2 e mais tarde, também pode definir esta funcionalidade ao nível de `PublicClientApplication` . Para tal, utilize uma chamada:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Se utilizar [CurrentActivityPlugin,](https://github.com/jamesmontemagno/CurrentActivityPlugin)o seu `PublicClientApplication` código de construtor parece ser o seguinte exemplo.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Certifique-se de que o controlo regressa à MSAL 
Quando a parte interativa do fluxo de autenticação terminar, certifique-se de que o controlo volta para o MSAL. No Android, substitua o `OnActivityResult` método de `Activity` . Em seguida, chame o `SetAuthenticationContinuationEventArgs` método da `AuthenticationContinuationHelper` classe MSAL. 

Eis um exemplo:

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

Esta linha garante que o controlo regressa à MSAL no final da parte interativa do fluxo de autenticação.

## <a name="update-the-android-manifest"></a>Atualize o manifesto Android
O *ficheiroAndroidManifest.xml* deve conter os seguintes valores:

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
```
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
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

Em alternativa, [crie a atividade em código](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) em vez de editar manualmente *AndroidManifest.xml*. Para criar a atividade em código, primeiro crie uma classe que inclua o `Activity` atributo e o `IntentFilter` atributo. 

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

### <a name="xamarinforms-43x-manifest"></a>Xamarin.Formulários 4.3.X manifesto

Xamarin.Forms 4.3.x gera código que define o `package` atributo `com.companyname.{appName}` em *AndroidManifest.xml*. Se usar `DataScheme` como `msal{client_id}` , então é melhor alterar o valor para corresponder ao valor do `MainActivity.cs` espaço de nome.

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


## <a name="troubleshoot"></a>Resolução de problemas
Pode criar uma nova aplicação Xamarin.Forms e adicionar uma referência ao pacote NuGet MSAL.NET.
Mas pode ter problemas de construção se atualizar uma aplicação Xamarin.Forms existente para MSAL.NET pré-visualização 1.1.2 ou posterior.

Para resolver problemas, crie problemas:

- Atualize o pacote NuGet MSAL.NET existente para MSAL.NET pré-visualização 1.1.2 ou posterior.
- Verifique se Xamarin.Forms atualizados automaticamente para a versão 2.5.0.122203. Se necessário, atualize Xamarin.Forms para esta versão.
- Verifique se Xamarin.Android.Support.v4 atualizado automaticamente para a versão 25.4.0.2. Se necessário, atualize a versão 25.4.0.2.
- Certifique-se de que todos os pacotes Xamarin.Android.Suporte a versão 25.4.0.2.
- Limpe ou reconstrua a aplicação.
- No Visual Studio, tente definir o número máximo de construções paralelas para 1. Para isso, selecione **Opções**  >  **Projetos e Soluções**  >  **Construir e Executar**O número máximo de  >  **projetos paralelos constrói.**
- Se estiver a construir a partir da linha de comando e o seu comando `/m` utilizar, tente remover este elemento do comando.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Erro: O nome AutenticaçãoContinuationHelhelper não existe no contexto atual

Se um erro indicar que `AuthenticationContinuationHelper` não existe no contexto atual, o Visual Studio pode ter atualizado incorretamente o ficheiro Android.csproj*. Por vezes, o caminho do *\<HintPath>* ficheiro contém incorretamente *netstandard13* em vez de *monoandroid90*.

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
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UWP | Uma aplicação simples Xamarin.Forms que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e AD AD AZure através do ponto final Azure AD 2.0. A aplicação também mostra como aceder ao Microsoft Graph e mostra o token resultante. <br>![Topologia](media/msal-net-xamarin-android-considerations/topology.png) |
