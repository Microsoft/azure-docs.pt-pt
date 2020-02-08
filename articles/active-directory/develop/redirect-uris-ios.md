---
title: Usar URIs de redirecionamento com MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as diferenças entre a biblioteca de autenticação da Microsoft para ObjectiveC (MSAL para iOS e macOS) e a biblioteca de autenticação do Azure AD para ObjectiveC (ADAL. ObjC) e como migrar entre eles.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: c36c6b1b1b08de6d2db9a7f7f9ebd3b162c02383
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085637"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Usando URIs de redirecionamento com a biblioteca de autenticação da Microsoft para iOS e macOS

Quando um usuário é autenticado, o Azure Active Directory (Azure AD) envia o token para o aplicativo usando o URI de redirecionamento registrado com o aplicativo do Azure AD.

A MSAL (biblioteca de autenticação da Microsoft) requer que o URI de redirecionamento seja registrado com o aplicativo do Azure AD em um formato específico. O MSAL usará um URI de redirecionamento padrão, se você não especificar um. O formato é `msauth.[Your_Bundle_Id]://auth`.

O formato de URI de redirecionamento padrão funciona para a maioria dos aplicativos e cenários, incluindo autenticação orientada e exibição da Web do sistema. Use o formato padrão sempre que possível.

No entanto, talvez seja necessário alterar o URI de redirecionamento para cenários avançados, conforme descrito abaixo.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Cenários que exigem um URI de redirecionamento diferente

### <a name="cross-app-single-sign-on-sso"></a>SSO (logon único) entre aplicativos

Para que a plataforma de identidade da Microsoft Compartilhe tokens entre aplicativos, cada aplicativo precisa ter a mesma ID de cliente ou ID do aplicativo. Esse é o identificador exclusivo fornecido quando você registrou seu aplicativo no portal (não a ID do pacote de aplicativos que você registra por aplicativo com a Apple).

Os URIs de redirecionamento precisam ser diferentes para cada aplicativo iOS. Isso permite que o serviço de identidade da Microsoft identifique exclusivamente aplicativos diferentes que compartilham uma ID de aplicativo. Cada aplicativo pode ter vários URIs de redirecionamento registrados no portal do Azure. Cada aplicativo em seu pacote terá um URI de redirecionamento diferente. Por exemplo:

Dado o seguinte registro de aplicativo no portal do Azure:

    Client ID: ABCDE-12345 (this is a single client ID)
    RedirectUris: msauth.com.contoso.app1://auth, msauth.com.contoso.app2://auth, msauth.com.contoso.app3://auth

App1 usa redirecionamento `msauth.com.contoso.app1://auth` App2 usa `msauth.com.contoso.app2://auth` App3 usa `msauth.com.contoso.app1://auth`

### <a name="migrating-from-adal-to-msal"></a>Migrando do ADAL para o MSAL

Ao migrar o código que usou a ADAL (biblioteca de autenticação do Azure AD) para o MSAL, talvez você já tenha um URI de redirecionamento configurado para seu aplicativo. Você pode continuar usando o mesmo URI de redirecionamento, desde que seu aplicativo ADAL tenha sido configurado para dar suporte a cenários orientados e seu URI de redirecionamento atenda aos requisitos de formato de URI de redirecionamento MSAL.

## <a name="msal-redirect-uri-format-requirements"></a>Requisitos de formato de URI de redirecionamento MSAL

* O redirecionamento mSAL URI deve estar na forma `<scheme>://host`

    Onde `<scheme>` é uma cadeia única que identifica a sua aplicação. Ele se baseia principalmente no identificador de pacote do seu aplicativo para garantir a exclusividade. Por exemplo, se o Bundle ID da sua aplicação for `com.contoso.myapp`, o seu URI redirecionado estaria na forma: `msauth.com.contoso.myapp://auth`.

    Se estiver a migrar da ADAL, o seu URI redirecionado terá provavelmente este formato: `<scheme>://[Your_Bundle_Id]`, onde `scheme` é uma cadeia única. Esse formato continuará a funcionar quando você usar o MSAL.

* `<scheme>` devem ser registados na lista info.plist da sua aplicação em `CFBundleURLTypes > CFBundleURLSchemes`.  Neste exemplo, o info. plist foi aberto como código-fonte:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>msauth.[BUNDLE_ID]</string>
            </array>
        </dict>
    </array>
    ```
    

MSAL verificará se o URI de redirecionamento se registra corretamente e retorna um erro se não for.
    
* Se quiser utilizar links universais como um URI redirecionado, o `<scheme>` deve ser `https` e não precisa de ser declarado em `CFBundleURLSchemes`. Em vez disso, configure a aplicação e o domínio de acordo com as instruções da Apple na [Universal Links for Developers](https://developer.apple.com/ios/universal-links/) e ligue para o método `handleMSALResponse:sourceApplication:` de `MSALPublicClientApplication` quando a sua aplicação for aberta através de um link universal.

## <a name="use-a-custom-redirect-uri"></a>Usar um URI de redirecionamento personalizado

Para utilizar um URI de redirecionamento personalizado, passe o parâmetro `redirectUri` para `MSALPublicClientApplicationConfig` e passe esse objeto para `MSALPublicClientApplication` quando rubricar o objeto. Se o URI redireccionador for inválido, o inicializador devolverá `nil` e definirá o `redirectURIError`com informações adicionais.  Por exemplo:

Objetivo C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>Manipular o evento de URL aberta

Seu aplicativo deve chamar MSAL quando receber qualquer resposta por meio de esquemas de URL ou links universais. Ligue para o método `handleMSALResponse:sourceApplication:` de `MSALPublicClientApplication` quando a sua aplicação for aberta. Aqui está um exemplo de esquemas personalizados:

Objetivo C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [fluxos de autenticação e cenários](authentication-flows-app-scenarios.md) de aplicação
