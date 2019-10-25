---
title: Usando URIs de redirecionamento com a biblioteca de autenticação da Microsoft
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as diferenças entre a biblioteca de autenticação da Microsoft para ObjectiveC (MSAL para iOS e macOS) e a biblioteca de autenticação do Azure AD para ObjectiveC (ADAL. ObjC) e como migrar entre eles.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: jak
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b78906a03eb9dc96fb58dea4ceabff953f2a3e4f
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803777"
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

App1 usa redirecionamento `msauth.com.contoso.app1://auth` App2 usa `msauth.com.contoso.app2://auth` uso de App3 `msauth.com.contoso.app1://auth`

### <a name="migrating-from-adal-to-msal"></a>Migrando do ADAL para o MSAL

Ao migrar o código que usou a ADAL (biblioteca de autenticação do Azure AD) para o MSAL, talvez você já tenha um URI de redirecionamento configurado para seu aplicativo. Você pode continuar usando o mesmo URI de redirecionamento, desde que seu aplicativo ADAL tenha sido configurado para dar suporte a cenários orientados e seu URI de redirecionamento atenda aos requisitos de formato de URI de redirecionamento MSAL.

## <a name="msal-redirect-uri-format-requirements"></a>Requisitos de formato de URI de redirecionamento MSAL

* O URI de redirecionamento MSAL deve estar no formato `<scheme>://host`

    Onde `<scheme>` é uma cadeia de caracteres exclusiva que identifica seu aplicativo. Ele se baseia principalmente no identificador de pacote do seu aplicativo para garantir a exclusividade. Por exemplo, se a ID do pacote do seu aplicativo for `com.contoso.myapp`, o URI de redirecionamento estaria no formato: `msauth.com.contoso.myapp://auth`.

    Se você estiver migrando do ADAL, seu URI de redirecionamento provavelmente terá esse formato: `<scheme>://[Your_Bundle_Id]`, em que `scheme` é uma cadeia de caracteres exclusiva. Esse formato continuará a funcionar quando você usar o MSAL.

* `<scheme>` deve ser registrado no info. plist do seu aplicativo em `CFBundleURLTypes > CFBundleURLSchemes`.  Neste exemplo, o info. plist foi aberto como código-fonte:

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
    
* Se você quiser usar links universais como um URI de redirecionamento, a `<scheme>` deverá ser `https` e não precisará ser declarada em `CFBundleURLSchemes`. Em vez disso, configure o aplicativo e o domínio por instruções da Apple em [links universais para desenvolvedores](https://developer.apple.com/ios/universal-links/) e chame o método de `handleMSALResponse:sourceApplication:` de `MSALPublicClientApplication` quando seu aplicativo for aberto por meio de um link universal.

## <a name="use-a-custom-redirect-uri"></a>Usar um URI de redirecionamento personalizado

Para usar um URI de redirecionamento personalizado, passe o parâmetro `redirectUri` para `MSALPublicClientApplicationConfig` e passe esse objeto para `MSALPublicClientApplication` ao inicializar o objeto. Se o URI de redirecionamento for inválido, o inicializador retornará `nil` e definirá o `redirectURIError`com informações adicionais.  Por exemplo:

Objective-C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift

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

Seu aplicativo deve chamar MSAL quando receber qualquer resposta por meio de esquemas de URL ou links universais. Chame o método de `handleMSALResponse:sourceApplication:` de `MSALPublicClientApplication` quando seu aplicativo for aberto. Aqui está um exemplo de esquemas personalizados:

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os [fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)
