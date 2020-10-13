---
title: Migrar aplicativos Xamarin Android usando corretores para MSAL.NET
titleSuffix: Microsoft identity platform
description: Saiba como migrar aplicações para Android Xamarin que utilizam o Microsoft Authenticator ou o Portal da Empresa Intune de ADAL.NET para MSAL.NET.
author: aiwang
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2020
ms.author: aiwang
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 47902b29da1dfe20fb54d633a0559e6a337fd771
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89183693"
---
# <a name="migrate-android-applications-that-use-a-broker-from-adalnet-to-msalnet"></a>Migrar aplicações Android que usam um corretor de ADAL.NET para MSAL.NET

Se tem uma aplicação Xamarin Android que utiliza atualmente a Biblioteca de Autenticação do Diretório Ativo Azure para .NET (ADAL.NET) e um corretor de [autenticação,](brokered-auth.md)é hora de migrar para a Biblioteca de Autenticação do [Microsoft para .NET ](msal-overview.md) (MSAL.NET).

## <a name="prerequisites"></a>Pré-requisitos

* Uma aplicação Xamarin Android já integrada com um corretor ([Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) ou [Portal da Empresa Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)) e ADAL.NET que precisa de migrar para MSAL.NET.

## <a name="step-1-enable-the-broker"></a>Passo 1: Ativar o corretor

<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida MSAL:</td></tr>
<tr><td>
Em ADAL.NET, o suporte ao corretor é ativado numa base de contexto por autenticação.

Para chamar o corretor, tinha que definir um `useBroker` *verdadeiro* no `PlatformParameters` construtor:

```CSharp
public PlatformParameters(
        Activity callerActivity,
        bool useBroker)
```

No código de renderização de página específico da plataforma para Android, definiu a `useBroker` bandeira como verdadeira:

```CSharp
page.BrokerParameters = new PlatformParameters(
        this,
        true,
        PromptBehavior.SelectAccount);
```

Em seguida, inclua os parâmetros na chamada simbólica de aquisição:

```CSharp
AuthenticationResult result =
        await
            AuthContext.AcquireTokenAsync(
                Resource,
                ClientId,
                new Uri(RedirectURI),
                platformParameters)
                .ConfigureAwait(false);
```

</td><td>
Em MSAL.NET, o suporte ao corretor é ativado numa base de Aplicação por PublicClientApplication.

Utilize o `WithBroker()` parâmetro (que é definido como verdadeiro por padrão) para chamar o corretor:

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid)
                .Build();
```

Em seguida, na chamada AcquireToken:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="step-2-set-an-activity"></a>Passo 2: Definir uma Atividade

Em ADAL.NET, passou numa atividade (normalmente a MainActivity) como parte dos PlataformaParameters como mostrado no [Passo 1: Ativar o corretor](#step-1-enable-the-broker).

MSAL.NET também usa uma atividade, mas não é necessária no uso regular do Android sem um corretor. Para utilizar o corretor, desa estale a atividade para enviar e receber respostas do corretor.

<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida MSAL:</td></tr>
<tr><td>
A atividade é transmitida para os PlataformaParameters na plataforma específica android.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>

Em MSAL.NET, faça duas coisas para definir a atividade para Android:

1. Em `MainActivity.cs` , definir o para garantir que há uma atividade com a chamada para o `App.RootViewController` `MainActivity` corretor.

    Se não estiver corretamente definido, poderá obter este erro: `"Activity_required_for_android_broker":"Activity is null, so MSAL.NET cannot invoke the Android broker. See https://aka.ms/Brokered-Authentication-for-Android"`

1. Na chamada AcquireTokenInteractive, utilize o `.WithParentActivityOrWindow(App.RootViewController)` e passe na referência à atividade que irá utilizar. Este exemplo utilizará o MainActivity.

**Por exemplo:**

Em *App.cs:*

```CSharp
   public static object RootViewController { get; set; }
```

Em *MainActivity.cs:*

```CSharp
   LoadApplication(new App());
   App.RootViewController = this;
```

Na chamada AcquireToken:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre considerações específicas do Android ao utilizar MSAL.NET com Xamarin, consulte [os requisitos de Configuração e dicas de resolução de problemas para O Android Xamarin com MSAL.NET](msal-net-xamarin-android-considerations.md).