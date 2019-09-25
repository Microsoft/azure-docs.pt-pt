---
title: Configurar o SSO no macOS e iOS | Plataforma de identidade da Microsoft
description: Saiba como configurar o SSO (logon único) no macOS e no iOS.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a407b57a380d059703383b02e37decb8761786f4
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268937"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Como: Configurar o SSO no macOS e no iOS

A MSAL (biblioteca de autenticação da Microsoft) para macOS e iOS dá suporte ao SSO (logon único) entre aplicativos e navegadores macOS/iOS. Este artigo aborda os seguintes cenários de SSO:

- [SSO silencioso entre vários aplicativos](#silent-sso-between-apps)

Esse tipo de SSO funciona entre vários aplicativos distribuídos pelo mesmo desenvolvedor da Apple. Ele fornece SSO silencioso (ou seja, o usuário não é solicitado a fornecer credenciais) lendo tokens de atualização gravados por outros aplicativos do conjunto de chaves e trocando-os para tokens de acesso silenciosamente.  

- [SSO por meio do agente de autenticação](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Esse fluxo não está disponível no macOS.

A Microsoft fornece aplicativos, chamados de agentes, que habilitam o SSO entre aplicativos de diferentes fornecedores, desde que o dispositivo móvel seja registrado com o Azure Active Directory (AAD). Esse tipo de SSO requer que um aplicativo de agente seja instalado no dispositivo do usuário.

- **SSO entre MSAL e Safari**

O SSO é obtido por meio da classe [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) . Ele usa o estado de entrada existente de outros aplicativos e do navegador Safari. Ele não está limitado a aplicativos distribuídos pelo mesmo desenvolvedor da Apple, mas requer alguma interação do usuário.

Se você usar o modo de exibição da Web padrão em seu aplicativo para conectar usuários, obterá SSO automático entre aplicativos baseados em MSAL e Safari. Para saber mais sobre as exibições da Web que o MSAL dá suporte, visite [Personalizar navegadores e Webviews](customize-webviews.md).

> [!IMPORTANT]
> Esse tipo de SSO não está disponível no macOS no momento. O MSAL no macOS só dá suporte a WKWebView que não tem suporte a SSO com o Safari. 

- **SSO silencioso entre aplicativos ADAL e MSAL macOS/iOS**

O MSAL Objective-C dá suporte à migração e ao SSO com aplicativos baseados em Objective-C de ADAL. Os aplicativos devem ser distribuídos pelo mesmo desenvolvedor da Apple.

Consulte [SSO entre aplicativos Adal e MSAL no MacOS e Ios](sso-between-adal-msal-apps-macos-ios.md) para obter instruções para SSO entre aplicativos entre o Adal e os aplicativos baseados em MSAL.

## <a name="silent-sso-between-apps"></a>SSO silencioso entre aplicativos

O MSAL dá suporte ao compartilhamento de SSO por meio de grupos de acesso do iOS.

Para habilitar o SSO em seus aplicativos, você precisará executar as etapas a seguir, que são explicadas em mais detalhes abaixo:

1. Verifique se todos os seus aplicativos usam a mesma ID de cliente ou ID do aplicativo.
1. Verifique se todos os seus aplicativos compartilham o mesmo certificado de autenticação da Apple para que você possa compartilhar os conjuntos de chaves.
1. Solicite o mesmo direito de conjunto de chaves para cada um dos seus aplicativos.
1. Informe os SDKs do MSAL sobre o conjunto de chaves compartilhado que você deseja que possamos usar se ele for diferente do padrão.

### <a name="use-the-same-client-id-and-application-id"></a>Usar a mesma ID do cliente e ID do aplicativo

Para que a plataforma de identidade da Microsoft saiba quais aplicativos podem compartilhar tokens, esses aplicativos precisam compartilhar a mesma ID do cliente ou ID do aplicativo. Esse é o identificador exclusivo que foi fornecido a você quando você registrou seu primeiro aplicativo no Portal.

A maneira como a plataforma Microsoft Identity informa aos aplicativos que usam a mesma identificação de aplicativo, é por seus **URIs de redirecionamento**. Cada aplicativo pode ter vários URIs de redirecionamento registrados no portal de integração. Cada aplicativo em seu pacote terá um URI de redirecionamento diferente. Por exemplo:

URI de redirecionamento APP1: `msauth.com.contoso.mytestapp1://auth`URI de redirecionamento App2: `msauth.com.contoso.mytestapp2://auth`URI de redirecionamento App3:`msauth.com.contoso.mytestapp3://auth`

> [!IMPORTANT]
> O formato dos URIs de redirecionamento deve ser compatível com o formato que o MSAL dá suporte, que está documentado em [requisitos de formato de URI de redirecionamento MSAL](redirect-uris-ios.md#msal-redirect-uri-format-requirements).

### <a name="setup-keychain-sharing-between-applications"></a>Configurar o compartilhamento de conjunto de chaves entre aplicativos

Consulte o artigo [recursos de adição](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) da Apple para habilitar o compartilhamento de conjunto de chaves. O que é importante é que você decida o que deseja que seu conjunto de chaves seja chamado e adicione esse recurso a todos os seus aplicativos que estarão envolvidos no SSO.

Quando os direitos estiverem configurados corretamente, você verá um `entitlements.plist` arquivo no diretório do projeto que contém algo como este exemplo:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Depois que você tiver o direito do conjunto de chaves habilitado em cada um dos seus aplicativos, e estiver pronto para usar o SSO `MSALPublicClientApplication` , configure com o grupo de acesso do conjunto de chaves como no exemplo a seguir:

Objective-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"
        
do {
    let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



> [!WARNING]
> Quando você compartilha um conjunto de chaves em seus aplicativos, qualquer aplicativo pode excluir usuários ou até mesmo todos os tokens em seu aplicativo.
> Isso é particularmente afetado se você tiver aplicativos que dependem de tokens para fazer trabalho em segundo plano.
> Compartilhar um conjunto de chaves significa que você deve ser muito cuidadoso quando seu aplicativo usa operações de remoção do SDK do Microsoft Identity.

Já está! O SDK do Microsoft Identity agora compartilhará credenciais em todos os seus aplicativos. A lista de contas também será compartilhada entre instâncias do aplicativo.

## <a name="sso-through-authentication-broker-on-ios"></a>SSO por meio do agente de autenticação no iOS

O MSAL fornece suporte para autenticação orientada com Microsoft Authenticator. Microsoft Authenticator fornece SSO para dispositivos registrados no AAD e também ajuda seu aplicativo a seguir políticas de acesso condicional.

As etapas a seguir são como habilitar o SSO usando um agente de autenticação para seu aplicativo:

1. Registre um formato de URI de redirecionamento compatível com o agente para o aplicativo no info. plist do seu aplicativo. O formato do URI de redirecionamento compatível com o agente é `msauth.<app.bundle.id>://auth`. Substitua ' < app. Bundle. ID > ' ' pela ID do pacote do seu aplicativo. Por exemplo:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Adicione os seguintes esquemas ao info. plist do seu aplicativo `LSApplicationQueriesSchemes`em:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Adicione o seguinte ao seu `AppDelegate.m` arquivo para manipular retornos de chamada:

    Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Se você estiver usando o Xcode 11**, deverá posicionar o retorno de `SceneDelegate` chamada MSAL no arquivo em vez disso.
Se você oferecer suporte a UISceneDelegate e UIApplicationDelegate para compatibilidade com o iOS mais antigo, o retorno de chamada do MSAL precisaria ser colocado em ambos os arquivos.

Objective-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```
    
## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os [fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)
