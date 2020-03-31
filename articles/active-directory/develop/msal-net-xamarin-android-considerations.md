---
title: Considerações do Xamarin Android (MSAL.NET) / Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações para usar o Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b55253d757f641979c6f72001803d7d38d9af3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132506"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>Considerações para usar Xamarin Android com MSAL.NET
Este artigo discute o que deve considerar quando utiliza o Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Definir a atividade dos pais

No Xamarin Android, delineie a atividade dos pais para que o token regresse após a interação. Aqui está um exemplo de código:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

No MSAL 4.2 e mais tarde, também pode `PublicClientApplication`definir esta funcionalidade ao nível de . Para tal, use uma chamada:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Se utilizar o [CurrentActivityPlugin,](https://github.com/jamesmontemagno/CurrentActivityPlugin)então o seu `PublicClientApplication` código de construtor parece ser o seguinte exemplo.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Certifique-se de que o controlo regressa à MSAL 
Quando a parte interativa do fluxo de autenticação terminar, certifique-se de que o controlo volta para o MSAL. No Android, sobrepor-se ao `OnActivityResult` método de `Activity`. Então `SetAuthenticationContinuationEventArgs` ligue para `AuthenticationContinuationHelper` o método da classe MSAL. 

Segue-se um exemplo:

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

Esta linha garante que o controlo retorna à MSAL no final da parte interativa do fluxo de autenticação.

## <a name="update-the-android-manifest"></a>Atualizar o manifesto Android
O ficheiro *AndroidManifest.xml* deve conter os seguintes valores:

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

Substitua o nome do pacote que registou no portal Azure pelo `android:host=` valor. Substitua o hash chave que registou `android:path=` no portal Azure pelo valor. O hash de assinatura *não* deve ser codificado por URL. Certifique-se de que`/`aparece no início do seu hash de assinatura um primeiro-dia.

Alternativamente, [crie a atividade em código](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) em vez de editar manualmente *AndroidManifest.xml*. Para criar a atividade em código, crie primeiro uma classe que inclua o `Activity` atributo e o `IntentFilter` atributo. 

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

### <a name="xamarinforms-43x-manifest"></a>Xamarin.Forms 4.3.X manifesto

Xamarin.Forms 4.3.x gera código que `package` define `com.companyname.{appName}` o atributo em *AndroidManifest.xml*. Se utilizar `DataScheme` `msal{client_id}`como , então talvez queira alterar o `MainActivity.cs` valor para corresponder ao valor do espaço de nome.

## <a name="use-the-embedded-web-view-optional"></a>Utilize a visão web incorporada (opcional)

Por padrão, MSAL.NET utiliza o navegador web do sistema. Este navegador permite-lhe obter um único sinal (SSO) utilizando aplicações web e outras aplicações. Em alguns casos raros, pode querer que o seu sistema use uma visão web incorporada. 

Este exemplo de código mostra como configurar uma visão web incorporada:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Para mais informações, consulte [Utilize os navegadores web para MSAL.NET](msal-net-web-browsers.md) e o navegador Do sistema Android [Xamarin.](msal-net-system-browser-android-considerations.md)


## <a name="troubleshoot"></a>Resolução de problemas
Pode criar uma nova aplicação Xamarin.Forms e adicionar uma referência ao pacote nuGet MSAL.NET.
Mas pode ter problemas de construção se atualizar uma aplicação Xamarin.Forms existente para MSAL.NET pré-visualização 1.1.2 ou mais tarde.

Para resolver problemas, constrói problemas:

- Atualize o pacote nuGet MSAL.NET existente para MSAL.NET pré-visualização 1.1.2 ou posterior.
- Verifique se xamarin.Forms é automaticamente atualizado para a versão 2.5.0.122203. Se necessário, atualize xamarin.Forms para esta versão.
- Verifique se Xamarin.Android.Support.v4 foi atualizado automaticamente para a versão 25.4.0.2. Se necessário, atualize para a versão 25.4.0.2.
- Certifique-se de que todos os pacotes Xamarin.Android.Suporte saem para a versão 25.4.0.2.
- Limpe ou reconstrua a aplicação.
- No Estúdio Visual, tente definir o número máximo de construções paralelas do projeto para 1. Para isso, selecione **Options** > **Projects and Solutions** > **Build and Run** > **O número máximo de projetos paralelos constrói.**
- Se estiver a construir a partir da `/m`linha de comando e o seu comando usar, tente remover este elemento do comando.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Erro: O nome AutenticaçãoContinuationHelper não existe no contexto atual

Se um erro `AuthenticationContinuationHelper` indicar que isso não existe no contexto atual, o Visual Studio pode ter atualizado incorretamente o ficheiro Android.csproj*. Por * \<vezes,* o caminho de ficheiro sinuosamente>HintPath contém *13 líquidos* em vez de *monoandroid90*.

Este exemplo contém um caminho de ficheiro correto:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte a amostra de uma [aplicação móvel Xamarin que utiliza](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)a plataforma de identidade da Microsoft . O quadro seguinte resume as informações relevantes no ficheiro README.

| Sample | Plataforma | Descrição |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UWP | Uma simples aplicação Xamarin.Forms que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e Azure AD através do ponto final do Azure AD 2.0. A aplicação também mostra como aceder ao Microsoft Graph e mostra o símbolo resultante. <br>![Topologia](media/msal-net-xamarin-android-considerations/topology.png) |
