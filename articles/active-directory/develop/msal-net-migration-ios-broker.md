---
title: Migrar aplicações Xamarin com mediadores para o MSAL.NET
titleSuffix: Microsoft identity platform
description: Saiba como migrar aplicações xamarin iOS que usam o Microsoft Authenticator de ADAL.NET para MSAL.NET.
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
ms.openlocfilehash: b4eff5910ff5230902d497b55b2afbe6d605365a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89177436"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migrar aplicações iOS que usam o Microsoft Authenticator de ADAL.NET para MSAL.NET

Tem utilizado a Biblioteca de Autenticação do Diretório Ativo Azure para .NET (ADAL.NET) e o corretor iOS. Agora é hora de migrar para a [Microsoft Authentication Library](msal-overview.md) for .NET (MSAL.NET), que suporta o corretor no iOS a partir do lançamento 4.3.

Por onde deve começar? Este artigo ajuda-o a migrar a sua aplicação Xamarin iOS de ADAL para MSAL.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que já tem uma aplicação Xamarin iOS que está integrada com o corretor iOS. Se não o fizer, mude-se diretamente para MSAL.NET e inicie a implementação do corretor lá. Para obter informações sobre como invocar o corretor iOS em MSAL.NET com uma nova aplicação, consulte [esta documentação.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)

## <a name="background"></a>Fundo

### <a name="what-are-brokers"></a>O que são corretores?

Os corretores são aplicações fornecidas pela Microsoft para Android e iOS. (Consulte a aplicação [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) no iOS e Android e a aplicação Intune Company Portal no Android.)

Permitem:

- Início de sessão único.
- Identificação do dispositivo, que é exigida por algumas [políticas de acesso condicional](../conditional-access/overview.md). Para obter mais informações, consulte [a gestão do Dispositivo.](../conditional-access/concept-conditional-access-conditions.md#device-platforms)
- Verificação de identificação de aplicação, que também é necessária em alguns cenários empresariais. Para obter mais informações, consulte [a Gestão de Aplicações Móveis Intune (MAM)](/intune/mam-faq).

## <a name="migrate-from-adal-to-msal"></a>Migrar de ADAL para MSAL

### <a name="step-1-enable-the-broker"></a>Passo 1: Ativar o corretor

<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida MSAL:</td></tr>
<tr><td>
Em ADAL.NET, o suporte ao corretor foi ativado numa base de contexto por autenticação. É desativado por defeito. Tinha que definir uma

`useBroker` bandeira para verdade no `PlatformParameters` construtor para chamar o corretor:

```csharp
public PlatformParameters(
        UIViewController callerViewController,
        bool useBroker)
```
Além disso, no código específico da plataforma, neste exemplo, no renderizador de página para iOS, definir o `useBroker`
bandeira para verdade:
```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```

Em seguida, inclua os parâmetros na chamada simbólica de aquisição:
```csharp
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
Em MSAL.NET, o suporte ao corretor é ativado numa base de Aplicação por PublicClientApplication. É desativado por defeito. Para o habilitar, utilize o

`WithBroker()` parâmetro (definido como verdadeiro por padrão) a fim de chamar o corretor:

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
Na chamada simbólica:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Passo 2: Definir um UIViewController()
Em ADAL.NET, passou num UIViewController como parte de `PlatformParameters` . (Ver o exemplo no passo 1.) Em MSAL.NET, para dar mais flexibilidade aos desenvolvedores, é utilizada uma janela de objetos, mas não é necessária no uso regular do iOS. Para utilizar o corretor, desajei a janela do objeto para enviar e receber respostas do corretor.
<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida MSAL:</td></tr>
<tr><td>
Um UIViewController é passado para

`PlatformParameters` na plataforma específica para iOS.

```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>
Em MSAL.NET, você faz duas coisas para definir a janela do objeto para iOS:

1. Em `AppDelegate.cs` , definir para um novo `App.RootViewController` `UIViewController()` .
Esta atribuição garante que existe um UIViewController com a chamada para o corretor. Se não estiver corretamente definido, poderá obter este erro: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Na chamada AcquireTokenInteractive, use `.WithParentActivityOrWindow(App.RootViewController)` e passe na referência à janela do objeto que utilizará.

**Por exemplo:**

Em `App.cs`:
```csharp
   public static object RootViewController { get; set; }
```
Em `AppDelegate.cs`:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
Na chamada simbólica:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Passo 3: Atualizar AppDeegate para lidar com a chamada
Tanto a ADAL como a MSAL ligam para o corretor, e o corretor, por sua vez, volta a ligar para a sua aplicação através `OpenUrl` do método da `AppDelegate` classe. Para mais informações, consulte [esta documentação.](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback)

Não há mudanças aqui entre ADAL.NET e MSAL.NET.

### <a name="step-4-register-a-url-scheme"></a>Passo 4: Registar um esquema de URL
ADAL.NET e MSAL.NET usam URLs para invocar o corretor e devolver a resposta do corretor à app. Registe o esquema de URL no `Info.plist` ficheiro da sua aplicação da seguinte forma:

<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida MSAL:</td></tr>
<tr><td>
O esquema de URL é exclusivo da sua aplicação.
</td><td>
O

`CFBundleURLSchemes` nome deve incluir

`msauth.`

como um prefixo, seguido pelo seu `CFBundleURLName`

Por exemplo: `$"msauth.(BundleId")`

```csharp
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

> [!NOTE]
> Este esquema de URL torna-se parte do URI de redirecionamento que é usado para identificar exclusivamente a app quando recebe a resposta do corretor.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Passo 5: Adicione o identificador de corretor à secção LSApplicationQueriesSchemes

ADAL.NET e MSAL.NET utilizam `-canOpenURL:` para verificar se o corretor está instalado no dispositivo. Adicione o identificador correto para o corretor iOS à secção LSApplicationQueriesSchemes do ficheiro info.plist da seguinte forma:

<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida MSAL:</td></tr>
<tr><td>
Utiliza

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Utiliza

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-azure-portal"></a>Passo 6: Registe o seu URI de redirecionamento no portal Azure

ADAL.NET e MSAL.NET ambos adicionam um requisito extra no URI de redirecionamento quando este visa o corretor. Registe o URI de redirecionamento com a sua aplicação no portal Azure.
<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida MSAL:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Exemplo:

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Exemplo:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Para obter mais informações sobre como registar o URI de redirecionamento no portal Azure, consulte o [Passo 7: Adicione um URI redirecionado ao registo da sua aplicação](msal-net-use-brokers-with-xamarin-apps.md#step-7-add-a-redirect-uri-to-your-app-registration).

### <a name="step-7-set-the-entitlementsplist"></a>**Passo 7: Definir os Direitos.plist**

Permitir o acesso do chaveiro no ficheiro *Entitlements.plist:*

```xml
 <key>keychain-access-groups</key>
    <array>
      <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
    </array>
```

Para obter mais informações sobre o acesso ao chaveiro, consulte [Ativar o acesso ao chaveiro](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

## <a name="next-steps"></a>Passos seguintes

Conheça as [considerações específicas do Xamarin iOS com MSAL.NET](msal-net-xamarin-ios-considerations.md).