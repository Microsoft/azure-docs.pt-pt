---
title: Considerações de Xamarin Android (biblioteca de autenticação da Microsoft para .NET) | Azure
description: Saiba mais sobre as considerações específicas ao utilizar o Xamarin Android com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 357c83cfd0ae3fed8b13419e72f50fcb90c04186
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550662"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Considerações sobre o Xamarin. Android-específicas com MSAL.NET
Este artigo aborda considerações específicas ao utilizar o Xamarin Android com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).

Este artigo é para MSAL.NET 3.x. Se estiver interessado em MSAL.NET 2.x, consulte [especificações de Xamarin Android em MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics-2x).

## <a name="set-the-parent-activity"></a>Defina a atividade principal

No xamarin. Android, terá de definir a atividade principal, para que o token obtém assim que a interação aconteceu.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Garantir que o controle tem a ver com MSAL uma vez a parte interativa das extremidades de fluxo de autenticação
No Android, terá de substituir a `OnActivityResult` método da `Activity` e chame o método SetAuthenticationContinuationEventArgs da classe AuthenticationContinuationHelper MSAL.

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
Essa linha garante que o controle tem a ver com MSAL assim que a parte interativa do fluxo de autenticação terminou.

## <a name="update-the-android-manifest"></a>Atualizar manifesto do Android
O `AndroidManifest.xml` deve conter os seguintes valores:
```csharp
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msal{client_id}" android:host="auth" />
         </intent-filter>
</activity>
```

## <a name="use-the-embedded-web-view-optional"></a>Utilize a vista web incorporadas (opcional)

Por predefinição MSAL.NET utiliza o browser de web do sistema, que permite-lhe obter SSO com aplicativos Web e outras aplicações. Em alguns casos raros, poderá especificar que pretende utilizar a vista web incorporadas. Para obter mais informações, consulte [MSAL.NET utiliza um navegador da Web](msal-net-web-browsers.md) e [browser de sistema Android](msal-net-system-browser-android-considerations.md).

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Resolução de problemas
Se cria uma nova aplicação xamarin. Forms e adicionar uma referência ao pacote MSAL.Net NuGet, isso só funcionará.
No entanto, se quiser atualizar uma aplicação xamarin. Forms existente MSAL.NET pré-visualizar 1.1.2 ou posterior podem ocorrer problemas de compilação.

Para resolver estes problemas, deve:
- Atualizar o pacote de MSAL.NET NuGet existente para a pré-visualização MSAL.NET 1.1.2 ou posterior
- Verifique que o xamarin. Forms atualizada automaticamente para a versão 2.5.0.122203 (caso contrário, a atualização para esta versão)
- Verifique que o Xamarin.Android.Support.v4 atualizada automaticamente para a versão 25.4.0.2 (caso contrário, a atualização para esta versão)
- Todos os pacotes de Xamarin.Android.Support devem destinar-se versão 25.4.0.2
- Limpar/reconstrução
- Tente definir o projeto paralelo máximo baseia-se como 1 no Visual Studio (Opções -> projetos e soluções -> criar e executar -> o número máximo de compilações de projetos paralela)
- Em alternativa, se estiver a criar a partir da linha de comandos, experimente remover /m do seu comando, se estiver a utilizar.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Erro: O nome 'AuthenticationContinuationHelper' não existe no contexto atual

Isso ocorre provavelmente porque o Visual Studio não atualizava corretamente o ficheiro de Android.csproj*. Por vezes, o  **\<HintPath >** filepath incorretamente contém netstandard13 em vez de **monoandroid90**.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Passos Seguintes

Obter mais detalhes e exemplos que são fornecidos na [considerações específicas Android](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) parágrafo de ficheiro de readme.md o exemplo seguinte:

| Exemplo | Plataforma | Descrição |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Uma aplicação Xamarin Forms simples que mostra como utilizar a MSAL para autenticar MSA e o Azure AD através do ponto de final de v2.0 AADD e acessar o Microsoft Graph com o token resultante. <br>![Topologia](media/msal-net-xamarin-android-considerations/topology.png) |