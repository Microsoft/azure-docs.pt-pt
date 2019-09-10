---
title: Migrando aplicativos do Xamarin iOS usando o Microsoft Authenticator de ADAL.NET para MSAL.NET | Azure
description: Saiba como migrar aplicativos do Xamarin iOS usando o Microsoft Authenticator da biblioteca de autenticação do AD do Azure para .NET (ADAL.NET) para a biblioteca de autenticação da Microsoft para .NET (MSAL.NET)
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
ms.openlocfilehash: 609aeeada78985466cb7c254cab32864e5d84b0e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875656"
---
# <a name="migrating-ios-applications-using-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migrando aplicativos iOS usando o Microsoft Authenticator do ADAL.NET para o MSAL.NET

Você esteve usando o ADAL.NET e o agente do iOS, e é hora de migrar para a [biblioteca de autenticação da Microsoft](msal-overview.md)MSAL.net, que oferece suporte ao agente no Ios da versão 4,3 em diante. 

Por onde começar? Este artigo o ajudará a migrar seu aplicativo do Xamarin iOS do ADAL para o MSAL.

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que você já tenha um aplicativo do Xamarin iOS que está integrado ao agente do iOS. Caso contrário, seria melhor mover diretamente para MSAL.NET e começar a implementação do agente. Consulte [esta documentação](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications) para obter detalhes sobre como invocar o agente do iOS no MSAL.NET com um novo aplicativo.

## <a name="background"></a>Segundo plano

### <a name="what-are-brokers"></a>O que são agentes?

Os agentes são aplicativos, fornecidos pela Microsoft, no Android e no iOS ([Microsoft Authenticator](https://www.microsoft.com/account/authenticator) no Ios e no android, portal da empresa do Intune no Android). 

Eles permitem:

- Logon único,
- Identificação do dispositivo, que é exigido por algumas [políticas de acesso condicional](../conditional-access/overview.md) (consulte [Gerenciamento de dispositivo](../conditional-access/conditions.md#device-platforms))
- Verificação de identificação de aplicativo, também necessária em alguns cenários empresariais (consulte por exemplo [, gerenciamento de aplicativo móvel do Intune ou Mam](https://docs.microsoft.com/intune/mam-faq))

## <a name="migrate-from-adal-to-msal"></a>Migrar do ADAL para o MSAL

### <a name="step-1-enable-the-broker"></a>Passo 1: Habilitar o agente

<table>
<tr><td>Código de ADAL atual:</td><td>Equivalente de MSAL:</td></tr>
<tr><td>
No ADAL.NET, o suporte do Broker foi habilitado em uma base de contexto por autenticação, ele é desabilitado por padrão. Você precisava definir um `useBroker` sinalizador como true no construtor para chamar o `PlatformParameters` Broker:

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Além disso, no código específico da plataforma, neste exemplo, no renderizador de página para iOS, defina o`useBroker` 
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
No MSAL.NET, o suporte do Broker é habilitado em uma base de aplicativo cliente por público. Está desativado por predefinição. Para habilitá-lo, use: 

`WithBroker()`(definido como true por padrão) para chamar o Broker:

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

### <a name="step-2-set-a-uiviewcontroller"></a>Passo 2: Definir um UIViewController ()
No ADAL.NET, você passou no UIViewController como parte do Platformparameters (Veja o exemplo na etapa 1). No entanto, no MSAL.NET, para dar ao desenvolvedor mais flexibilidade, uma janela de objeto é usada, mas não é necessária no uso normal do iOS. No entanto, para usar o agente, você precisará definir a janela de objeto para enviar e receber respostas do agente. 
<table>
<tr><td>Código de ADAL atual:</td><td>Equivalente de MSAL:</td></tr>
<tr><td>
O UIViewController é passado para o PlatformParamters na plataforma específica do iOS.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
No MSAL.NET, você precisará fazer duas coisas para definir a janela de objeto para iOS:

1) No `AppDelegate.cs`, defina o `App.RootViewController` para um novo `UIViewController()`. Essa atribuição garantirá que haja um UIViewController com a chamada para o agente. Se não estiver definido corretamente, você poderá receber esse erro:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) Na chamada AcquireTokenInteractive, use o`.WithParentActivityOrWindow(App.RootViewController)`
e passe a referência para a janela de objeto you'will use.

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

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Passo 3: Atualizar AppDelegate para manipular o retorno de chamada
A Adal e a MSAL chamarão o agente, e o Broker, por sua vez, chamará de volta para `OpenUrl` seu aplicativo por `AppDelegate` meio do método da classe. Mais informações disponíveis [aqui](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS/_edit#step-two-update-appdelegate-to-handle-the-callback)

: heavy_check_mark: não há**nenhuma alteração aqui entre Adal.net e MSAL.net**

### <a name="step-4-register-a-url-scheme"></a>Passo 4: Registrar um esquema de URL
ADAL.NET e MSAL.NET usam URLs para invocar o agente e retornar a resposta do agente de volta para o aplicativo. Registre o esquema de URL no `Info.plist` arquivo para seu aplicativo da seguinte maneira:

<table>
<tr><td>Código de ADAL atual:</td><td>Equivalente de MSAL:</td></tr>
<tr><td>
O esquema de URL é exclusivo para seu aplicativo.
</td><td>
O 

`CFBundleURLSchemes`o nome deve incluir 

`msauth.`

como um prefixo, seguido pelo seu`CFBundleURLName`

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
>  Esse esquema de URL se tornará parte do RedirectUri usado para identificar exclusivamente o aplicativo ao receber a resposta do agente

</table>

### <a name="step-5-lsapplicationqueriesschemes"></a>Passo 5: LSApplicationQueriesSchemes

Adal.net e MSAL.NET usam `-canOpenURL:` para verificar se o agente está instalado no dispositivo. Adicione o identificador correto para o agente do iOS à seção LSApplicationQueriesSchemes do arquivo info. plist da seguinte maneira: 
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

### <a name="step-6-register-you-redirecturi-in-the-portal"></a>Passo 6: Registre você RedirectUri no portal

ADAL.NET e MSAL.NET adicionam um requisito extra no redirectUri ao direcionar o agente. Registre o URI de redirecionamento com seu aplicativo no Portal.
<table>
<tr><td>Código de ADAL atual:</td><td>Equivalente de MSAL:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`exemplo`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

exemplo:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Para obter mais informações sobre como registrar o redirectUri no portal, consulte [aproveitando o agente em aplicativos Xamarin. Ios](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app) para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [Considerações específicas do Xamarin Ios com o MSAL.net](msal-net-xamarin-ios-considerations.md). 
