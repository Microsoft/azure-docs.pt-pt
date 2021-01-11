---
title: Utilize URIs de redirecionamento com MSAL (iOS/macOS) Rio Azure
titleSuffix: Microsoft identity platform
description: Conheça as diferenças entre a Biblioteca de Autenticação da Microsoft para o ObjectiveC (MSAL para iOS e macOS) e a Azure AD Authentication Library for ObjectiveC (ADAL). ObjC) e como migrar entre eles.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: 2c728854846a3add1f5b41cb318d7dc5fd86e742
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064203"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Utilização de URIs de redirecionamento com a biblioteca de autenticação da Microsoft para iOS e macOS

Quando um utilizador autentica, o Azure Ative Directory (Azure AD) envia o símbolo para a aplicação utilizando o URI de redirecionamento registado com a aplicação AZure AD.

A biblioteca microsoft authentication (MSAL) exige que o URI de redirecionamento seja registado na aplicação AD Azure num formato específico. A MSAL utiliza um URI de redirecionamento padrão, se não especificar um. O formato é `msauth.[Your_Bundle_Id]://auth`.

O formato URI de redirecionamento padrão funciona para a maioria das aplicações e cenários, incluindo autenticação intermediada e visualização web do sistema. Utilize o formato predefinido sempre que possível.

No entanto, poderá ter de alterar o URI de redirecionamento para cenários avançados, como descrito abaixo.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Cenários que requerem um URI de redirecionamento diferente

### <a name="cross-app-single-sign-on-sso"></a>Sinal único de aplicação cruzada em (SSO)

Para que a plataforma Microsoft Identity partilhe fichas através de aplicações, cada aplicação precisa de ter o mesmo ID ou ID de aplicação do cliente. Este é o identificador único fornecido quando registou a sua aplicação no portal (não o ID do pacote de aplicações que regista por app com a Apple).

Os URIs de redirecionamento precisam de ser diferentes para cada aplicação iOS. Isto permite ao serviço de identidade da Microsoft identificar exclusivamente diferentes aplicações que partilham um ID de aplicação. Cada aplicação pode ter URIs de redirecionamento múltiplo registado no portal Azure. Cada aplicação na sua suite terá um URI de redirecionamento diferente. Por exemplo:

Tendo em conta o seguinte registo de candidatura no portal Azure:

* ID do cliente: `ABCDE-12345` (este é um ID de cliente único)
* RedirectUris: `msauth.com.contoso.app1://auth` `msauth.com.contoso.app2://auth` , `msauth.com.contoso.app3://auth`

A App1 usa redirecionamento `msauth.com.contoso.app1://auth` .\
App2 usa `msauth.com.contoso.app2://auth` .\
A App3 `msauth.com.contoso.app1://auth` utiliza.

### <a name="migrating-from-adal-to-msal"></a>Migração de ADAL para MSAL

Ao migrar o código que utilizou a Biblioteca de Autenticação AD Azure (ADAL) para a MSAL, pode já ter um URI de redirecionamento configurado para a sua aplicação. Pode continuar a utilizar o mesmo URI de redirecionamento desde que a sua aplicação ADAL tenha sido configurada para suportar cenários intermediados e o seu URI de redirecionamento satisfaz os requisitos do formato URI de redirecionamento MSAL.

## <a name="msal-redirect-uri-format-requirements"></a>MSAL redireciona requisitos do formato URI

* O URI redirecionado MSAL deve estar na forma `<scheme>://host`

    Onde `<scheme>` está uma corda única que identifica a sua aplicação. Baseia-se principalmente no Identificador de Pacotes da sua aplicação para garantir a singularidade. Por exemplo, se o Bundle ID da sua aplicação `com.contoso.myapp` for, o seu URI de redirecionamento estaria no formulário: `msauth.com.contoso.myapp://auth` .

    Se estiver a migrar da ADAL, o seu URI de redirecionamento provavelmente terá este formato: `<scheme>://[Your_Bundle_Id]` , onde está uma corda `scheme` única. Este formato continuará a funcionar quando utilizar o MSAL.

* `<scheme>` deve estar registado na info.plist da sua aplicação em `CFBundleURLTypes > CFBundleURLSchemes` .  Neste exemplo, info.plist foi aberto como código fonte:

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

A MSAL verificará se o seu redireccionamento URI regista corretamente e devolve um erro se não estiver.
    
* Se quiser usar links universais como URI redirecionado, o `<scheme>` deve ser e não precisa de ser declarado em `https` `CFBundleURLSchemes` . Em vez disso, configuure a aplicação e o domínio de acordo com as instruções da Apple na [Universal Links for Developers](https://developer.apple.com/ios/universal-links/) e ligue para o método de quando a sua `handleMSALResponse:sourceApplication:` aplicação é aberta através de um link `MSALPublicClientApplication` universal.

## <a name="use-a-custom-redirect-uri"></a>Use um URI de redirecionamento personalizado

Para utilizar um URI de redirecionamento personalizado, passe o `redirectUri` parâmetro para e passe esse objeto para quando `MSALPublicClientApplicationConfig` `MSALPublicClientApplication` rubricar o objeto. Se o URI de redirecionamento for inválido, o inicializador devolverá `nil` e definirá o `redirectURIError` com informações adicionais.  Por exemplo:

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

Rápido:

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



## <a name="handle-the-url-opened-event"></a>Lidar com o evento aberto URL

A sua aplicação deve ligar para a MSAL quando receber qualquer resposta através de esquemas de URL ou links universais. Ligue para o `handleMSALResponse:sourceApplication:` método de quando a sua `MSALPublicClientApplication` aplicação é aberta. Aqui está um exemplo para esquemas personalizados:

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

Rápido:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [fluxos de autenticação e cenários de aplicação](authentication-flows-app-scenarios.md)
