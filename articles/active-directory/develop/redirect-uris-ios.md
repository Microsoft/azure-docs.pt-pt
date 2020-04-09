---
title: Utilize URIs redirecionamento com MSAL (iOS/macOS) [ Azure
titleSuffix: Microsoft identity platform
description: Conheça as diferenças entre a Microsoft Authentication Library for ObjectiveC (MSAL for iOS e macOS) e a Azure AD Authentication Library for ObjectiveC (ADAL). ObjC) e como migrar entre eles.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: 1291563a39e3cf3acd4b343302be8b150bf794ca
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883513"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Utilização de URIs redirecionados com a biblioteca de autenticação da Microsoft para iOS e macOS

Quando um utilizador autentica, o Azure Ative Directory (Azure AD) envia o símbolo para a app utilizando o URI redirecionado registado na aplicação Azure AD.

A biblioteca de autenticação da Microsoft (MSAL) exige que o URI redirecionado seja registado na aplicação Azure AD num formato específico. A MSAL utiliza um URI de redirecionamento predefinido, se não especificar um. O formato é `msauth.[Your_Bundle_Id]://auth`.

O formato DE REdirecionamento padrão URI funciona para a maioria das aplicações e cenários, incluindo autenticação intermediada e visualização web do sistema. Utilize o formato predefinido sempre que possível.

No entanto, poderá ter de alterar o URI redirecionado para cenários avançados, como descrito abaixo.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Cenários que requerem um URI redirecionado diferente

### <a name="cross-app-single-sign-on-sso"></a>Sinal único de aplicação cruzada (SSO)

Para que a plataforma Microsoft Identity partilhe tokens em aplicações, cada aplicação precisa de ter o mesmo ID de cliente ou ID de aplicação. Este é o identificador único fornecido quando registou a sua aplicação no portal (não o pacote de aplicações ID que regista por app com a Apple).

Os URIs redirecionados têm de ser diferentes para cada aplicação iOS. Isto permite que o serviço de identidade da Microsoft identifique exclusivamente diferentes aplicações que partilham um ID de aplicação. Cada aplicação pode ter vários URIs redirecionados registados no portal Azure. Cada aplicação na sua suite terá um URI de redirecionamento diferente. Por exemplo:

Dado o seguinte registo de candidatura no portal Azure:

    Client ID: ABCDE-12345 (this is a single client ID)
    RedirectUris: msauth.com.contoso.app1://auth, msauth.com.contoso.app2://auth, msauth.com.contoso.app3://auth

App1 usa `msauth.com.contoso.app1://auth` app2 `msauth.com.contoso.app2://auth` redirecionado usa utilizações app3`msauth.com.contoso.app1://auth`

### <a name="migrating-from-adal-to-msal"></a>Migração da ADAL para a MSAL

Ao migrar o código que usou a Biblioteca de Autenticação AD Azure (ADAL) para a MSAL, pode já ter um URI redirecionado configurado para a sua aplicação. Pode continuar a utilizar o mesmo URI redirecionado desde que a sua aplicação ADAL tenha sido configurada para suportar cenários intermediados e o seu REdirect URI satisfaz os requisitos de formato URI redirecionamento da MSAL.

## <a name="msal-redirect-uri-format-requirements"></a>Requisitos de formato URI redirecionamento MSAL

* O redirecionamento mSAL URI deve estar na forma`<scheme>://host`

    Onde `<scheme>` está uma corda única que identifica a sua aplicação. Baseia-se principalmente no Identificador de Pacoteda sua aplicação para garantir a singularidade. Por exemplo, se o Bundle `com.contoso.myapp`ID da sua aplicação for `msauth.com.contoso.myapp://auth`, o seu URI redirecionado estaria no formulário: .

    Se estiver a migrar da ADAL, o seu URI `<scheme>://[Your_Bundle_Id]`redirecionado terá provavelmente este formato: onde `scheme` é uma corda única. Este formato continuará a funcionar quando utilizar o MSAL.

* `<scheme>`deve ser registado na lista info.plist da sua aplicação em `CFBundleURLTypes > CFBundleURLSchemes`.  Neste exemplo, info.plist foi aberto como código fonte:

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
    

A MSAL verificará se o seu URI redireciona corretamente e devolverá um erro se não for.
    
* Se quiser utilizar links universais como uri `<scheme>` redirecionar, o deve `https` ser `CFBundleURLSchemes`e não precisa de ser declarado em . Em vez disso, configure a aplicação e o domínio de `handleMSALResponse:sourceApplication:` acordo `MSALPublicClientApplication` com as instruções da Apple na Universal Links [for Developers](https://developer.apple.com/ios/universal-links/) e ligue para o método de quando a sua aplicação é aberta através de um link universal.

## <a name="use-a-custom-redirect-uri"></a>Use um URI de redirecionamento personalizado

Para utilizar um URI de `redirectUri` redirecionamento personalizado, passe o parâmetro e `MSALPublicClientApplicationConfig` passe esse objeto para `MSALPublicClientApplication` quando rubricar o objeto. Se o URI redireccionador for inválido, o inicializador devolverá `nil` e definirá o `redirectURIError`com informações adicionais.  Por exemplo:

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



## <a name="handle-the-url-opened-event"></a>Manuseie o evento aberto url

A sua aplicação deve ligar para a MSAL quando receber qualquer resposta através de esquemas de URL ou links universais. Ligue `handleMSALResponse:sourceApplication:` para `MSALPublicClientApplication` o método de quando a sua aplicação for aberta. Aqui está um exemplo para esquemas personalizados:

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
