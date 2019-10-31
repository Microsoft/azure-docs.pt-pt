---
title: Migrar aplicativos Xamarin iOS que usam Microsoft Authenticator de ADAL.NET para MSAL.NET
titleSuffix: Microsoft identity platform
description: Saiba como migrar aplicativos do Xamarin iOS que usam Microsoft Authenticator da biblioteca de autenticação do AD do Azure para .NET (ADAL.NET) para a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c64f9f371424eddc7295b6ec40bda1ebdaaafd5
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175586"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migrar aplicativos iOS que usam Microsoft Authenticator de ADAL.NET para MSAL.NET

Você esteve usando o Azure Active Directory ADAL.NET (biblioteca de autenticação para .NET) e o agente do iOS. Agora é hora de migrar para a MSAL.NET ( [biblioteca de autenticação da Microsoft](msal-overview.md) para .net), que dá suporte ao agente no Ios da versão 4,3 em diante. 

Onde você deve começar? Este artigo ajuda você a migrar seu aplicativo Xamarin iOS da ADAL para MSAL.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já tenha um aplicativo Xamarin iOS integrado ao agente do iOS. Se você não fizer isso, mova diretamente para MSAL.NET e comece a implementação do agente. Para obter informações sobre como invocar o agente do iOS no MSAL.NET com um novo aplicativo, consulte [esta documentação](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications).

## <a name="background"></a>Segundo plano

### <a name="what-are-brokers"></a>O que são agentes?

Os agentes são aplicativos fornecidos pela Microsoft no Android e no iOS. (Consulte o aplicativo [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) no Ios e Android e o aplicativo portal da empresa do Intune no Android.) 

Eles permitem:

- Logon único.
- Identificação do dispositivo, que é exigido por algumas [políticas de acesso condicional](../conditional-access/overview.md). Para obter mais informações, consulte [Gerenciamento de dispositivos](../conditional-access/conditions.md#device-platforms).
- Verificação de identificação do aplicativo, que também é necessária em alguns cenários empresariais. Para obter mais informações, consulte [Gerenciamento de aplicativo móvel (MAM) do Intune](https://docs.microsoft.com/intune/mam-faq).

## <a name="migrate-from-adal-to-msal"></a>Migrar do ADAL para o MSAL

### <a name="step-1-enable-the-broker"></a>Etapa 1: habilitar o agente

<table>
<tr><td>Código de ADAL atual:</td><td>Equivalente de MSAL:</td></tr>
<tr><td>
No ADAL.NET, o suporte do Broker foi habilitado em uma base de contexto por autenticação. Ele é desabilitado por padrão. Você precisava definir um 

`useBroker` sinalizador como true no Construtor `PlatformParameters` para chamar o Broker:

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Além disso, no código específico da plataforma, neste exemplo, no renderizador de página para iOS, defina o `useBroker` 
sinalizar para verdadeiro:
```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Em seguida, inclua os parâmetros na chamada de token de aquisição:
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
No MSAL.NET, o suporte do Broker é habilitado em uma base por PublicClientApplication. Ele é desabilitado por padrão. Para habilitá-lo, use o 

`WithBroker()` parâmetro (definido como true por padrão) para chamar o agente:

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
Na chamada de aquisição de token:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Etapa 2: definir um UIViewController ()
No ADAL.NET, você passou em um UIViewController como parte do `PlatformParameters`. (Consulte o exemplo na etapa 1.) No MSAL.NET, para dar aos desenvolvedores mais flexibilidade, uma janela de objeto é usada, mas não é necessária no uso normal do iOS. Para usar o agente, defina a janela de objeto para enviar e receber respostas do agente. 
<table>
<tr><td>Código de ADAL atual:</td><td>Equivalente de MSAL:</td></tr>
<tr><td>
Um UIViewController é passado para 

`PlatformParameters` na plataforma específica do iOS.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
No MSAL.NET, você faz duas coisas para definir a janela de objeto para iOS:

1. Em `AppDelegate.cs`, defina `App.RootViewController` como um novo `UIViewController()`. Essa atribuição garante que há um UIViewController com a chamada para o agente. Se não estiver definido corretamente, você poderá receber esse erro: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Na chamada AcquireTokenInteractive, use `.WithParentActivityOrWindow(App.RootViewController)`e passe a referência para a janela de objeto que você usará.

**Por exemplo:**

Em `App.cs`:
```CSharp
   public static object RootViewController { get; set; }
```
Em `AppDelegate.cs`:
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
Na chamada de aquisição de token:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Etapa 3: atualizar AppDelegate para manipular o retorno de chamada
A ADAL e a MSAL chamam o agente, e o agente, por sua vez, chama de volta para seu aplicativo por meio do método `OpenUrl` da classe `AppDelegate`. Para obter mais informações, consulte [esta documentação](msal-net-use-brokers-with-xamarin-apps.md#step-2-update-appdelegate-to-handle-the-callback).

Não há nenhuma alteração aqui entre ADAL.NET e MSAL.NET.

### <a name="step-4-register-a-url-scheme"></a>Etapa 4: registrar um esquema de URL
ADAL.NET e MSAL.NET usam URLs para invocar o agente e retornar a resposta do agente de volta para o aplicativo. Registre o esquema de URL no arquivo de `Info.plist` para seu aplicativo da seguinte maneira:

<table>
<tr><td>Código de ADAL atual:</td><td>Equivalente de MSAL:</td></tr>
<tr><td>
O esquema de URL é exclusivo para seu aplicativo.
</td><td>
O 

o nome do `CFBundleURLSchemes` deve incluir 

`msauth.`

como um prefixo, seguido pelo seu `CFBundleURLName`

Por exemplo: `$"msauth.(BundleId")`

```CSharp
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
> Esse esquema de URL se torna parte do URI de redirecionamento que é usado para identificar exclusivamente o aplicativo quando ele recebe a resposta do agente.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Etapa 5: Adicionar o identificador do agente à seção LSApplicationQueriesSchemes

ADAL.NET e MSAL.NET usam `-canOpenURL:` para verificar se o agente está instalado no dispositivo. Adicione o identificador correto para o agente do iOS à seção LSApplicationQueriesSchemes do arquivo info. plist da seguinte maneira:

<table>
<tr><td>Código de ADAL atual:</td><td>Equivalente de MSAL:</td></tr>
<tr><td>
Utilizações 

`msauth`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Utilizações 

`msauthv2`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>Etapa 6: registrar seu URI de redirecionamento no portal

ADAL.NET e MSAL.NET adicionam um requisito extra no URI de redirecionamento quando ele se destina ao agente. Registre o URI de redirecionamento com seu aplicativo no Portal.
<table>
<tr><td>Código de ADAL atual:</td><td>Equivalente de MSAL:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Exemplo: 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Exemplo:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Para obter mais informações sobre como registrar o URI de redirecionamento no portal, consulte [aproveitar o agente em aplicativos Xamarin. Ios](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [Considerações específicas do Xamarin Ios com o MSAL.net](msal-net-xamarin-ios-considerations.md). 
