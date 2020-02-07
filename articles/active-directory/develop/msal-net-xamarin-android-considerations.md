---
title: Considerações sobre o Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações específicas ao usar o Xamarin Android com a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 54df91d38541fbe17a28c9ae083ae0e7d0c9d88d
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063667"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Considerações específicas do Xamarin Android com MSAL.NET
Este artigo discute considerações específicas ao usar o Xamarin Android com a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).

## <a name="set-the-parent-activity"></a>Definir a atividade pai

No Xamarin. Android, você precisa definir a atividade pai para que o token seja retornado depois que a interação acontecer.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```
Você também pode definir isso no nível de PublicClientApplication (no MSAL 4.2 +) por meio de um retorno de chamada.

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Uma recomendação é usar o CurrentActivityPlugin [aqui](https://github.com/jamesmontemagno/CurrentActivityPlugin).  Em seguida, o código do PublicClientApplication Builder ficaria assim:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```


## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Garantindo que o controle volte para MSAL depois que a parte interativa do fluxo de autenticação for encerrada
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
Essa linha garante que o controle volte para MSAL depois que a parte interativa do fluxo de autenticação for encerrada.

## <a name="update-the-android-manifest"></a>Atualizar o manifesto do Android
A `AndroidManifest.xml` deve conter os seguintes valores:
```xml
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msauth"
              android:host="Enter_the_Package_Name"
              android:path="/Enter_the_Signature_Hash"/>
         </intent-filter>
</activity>
```
Substitua o nome do pacote registado no portal Azure pelo valor `android:host=`. Substitua o hash chave que registou no portal Azure pelo valor `android:path=`. O Hash Signature **não** deve ser codificado por URL. Certifique-se de que há uma `/` líder no início do seu Signature Hash.

Ou, pode [criar a atividade em código](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) e não editar manualmente `AndroidManifest.xml`. Para isso, é preciso criar uma classe que tenha o `Activity` e `IntentFilter` atributo. Uma classe que representa os mesmos valores do XML acima seria:

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

### <a name="xamarinforms-43x-manifest"></a>Manifesto XamarinForms 4.3. X

O código gerado por XamarinForms 4.3.x define o atributo `package` para `com.companyname.{appName}` no `AndroidManifest.xml`. É melhor alterar o valor para ser o mesmo que o espaço de nome `MainActivity.cs`, se utilizar o `DataScheme` como `msal{client_id}`.

## <a name="use-the-embedded-web-view-optional"></a>Usar a exibição da Web inserida (opcional)

Por padrão, o MSAL.NET usa o navegador da Web do sistema, que permite que você obtenha o SSO com aplicativos Web e outros aplicativos. Em alguns casos raros, talvez você queira especificar que deseja usar a exibição da Web inserida. Para mais informações, consulte [MSAL.NET utiliza um navegador Web](msal-net-web-browsers.md) e navegador de sistema [Android.](msal-net-system-browser-android-considerations.md)

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Resolução de problemas
Se você criar um novo aplicativo Xamarin. Forms e adicionar uma referência ao pacote NuGet do MSAL.Net, isso só funcionará.
No entanto, se você quiser atualizar um aplicativo Xamarin. Forms existente para o MSAL.NET Preview 1.1.2 ou posterior, você poderá enfrentar problemas de compilação.

Para solucionar esses problemas, você deve:
- Atualizar o pacote NuGet do MSAL.NET existente para o MSAL.NET Preview 1.1.2 ou posterior
- Verifique se o Xamarin. Forms foi atualizado automaticamente para a versão 2.5.0.122203 (caso contrário, atualize para esta versão)
- Verifique se o Xamarin. Android. support. v4 foi atualizado automaticamente para a versão 25.4.0.2 (caso contrário, atualize para esta versão)
- Todos os pacotes Xamarin. Android. support devem ter como destino a versão 25.4.0.2
- Limpar/recompilar
- Tente definir as compilações de projetos paralelos máximos como 1 no Visual Studio (opções-> projetos e soluções-> Build e Run-> número máximo de compilações de projetos paralelos)
- Como alternativa, se você estiver criando a partir da linha de comando, tente remover/m do comando se estiver usando-a.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Erro: o nome ' AuthenticationContinuationHelper ' não existe no contexto atual

Isso é provável porque o Visual Studio não atualizou corretamente o arquivo Android. csproj *. Por vezes, o **\<HintPath>** o filepath contém incorretamente o netstandard13 em vez de **monoandroid90**.

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
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Um aplicativo Xamarin Forms simples mostrando como usar o MSAL para autenticar o MSA e o Azure AD por meio do ponto de extremidade do adicione v 2.0 e acessar o Microsoft Graph com o token resultante. <br>![Topologia](media/msal-net-xamarin-android-considerations/topology.png) |
