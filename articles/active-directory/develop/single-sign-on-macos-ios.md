---
title: Configure SSO no macOS e iOS
titleSuffix: Microsoft identity platform
description: Saiba como configurar um único sinal no macOS e iOS.
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
ms.openlocfilehash: ecc55c0d41f552d2c29fe5c964a7c40ab9e382ba
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701387"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Como: Configure SSO no macOS e iOS

A Microsoft Authentication Library (MSAL) para macOS e iOS suporta o Single Sign-on (SSO) entre aplicações macOS/iOS e navegadores. Este artigo abrange os seguintes cenários SSO:

- [SSO Silencioso entre várias aplicações](#silent-sso-between-apps)

Este tipo de SSO funciona entre várias aplicações distribuídas pelo mesmo Apple Developer. Fornece SSO silencioso (isto é, o utilizador não é solicitado para credenciais) lendo tokens de atualização escritos por outras aplicações do porta-chaves, e trocando-os por tokens de acesso em silêncio.  

- [SSO através do corretor de autenticação](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Este fluxo não está disponível no macOS.

A Microsoft fornece aplicações, chamadas corretoras, que permitem o SSO entre aplicações de diferentes fornecedores, desde que o dispositivo móvel esteja registado no Azure Ative Directory (AAD). Este tipo de SSO requer a instalação de uma aplicação de corretor no dispositivo do utilizador.

- **SSO entre MSAL e Safari**

O SSO é conseguido através da classe [ASWebAuthenticationSession.](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) Utiliza o estado de inscrição existente a partir de outras aplicações e do navegador Safari. Não se limita a aplicações distribuídas pelo mesmo Apple Developer, mas requer alguma interação do utilizador.

Se utilizar a visão web predefinida na sua aplicação para iniciar sessão nos utilizadores, receberá SSO automático entre aplicações baseadas em MSAL e Safari. Para saber mais sobre as vistas web que o MSAL suporta, visite [Personalizar navegadores e WebViews](customize-webviews.md).

> [!IMPORTANT]
> Este tipo de SSO não está atualmente disponível no macOS. A MSAL no macOS apenas suporta o WKWebView que não tem suporte SSO com o Safari. 

- **SSO Silencioso entre aplicações MacOS/iOS da ADAL e MSAL**

O MSAL Objective-C apoia a migração e o SSO com aplicações baseadas no Objectivo-C da ADAL. As aplicações devem ser distribuídas pelo mesmo Apple Developer.

Consulte o [SSO entre aplicações ADAL e MSAL no macOS e iOS](sso-between-adal-msal-apps-macos-ios.md) para obter instruções para sso de aplicações cruzadas entre apps baseadas em ADAL e MSAL.

## <a name="silent-sso-between-apps"></a>SSO Silencioso entre apps

A MSAL apoia a partilha de SSO através de grupos de acesso à porta-chaves iOS.

Para ativar o SSO através das suas aplicações, terá de fazer os seguintes passos, que são explicados com mais detalhes abaixo:

1. Certifique-se de que todas as suas aplicações utilizam o mesmo ID de Cliente ou ID de Aplicação.
1. Certifique-se de que todas as suas aplicações partilham o mesmo certificado de assinatura da Apple para que possa partilhar porta-chaves.
1. Solicite o mesmo direito de porta-chaves para cada um dos seus pedidos.
1. Informe os SDKs da MSAL sobre o porta-chaves partilhado que quer que usemos se for diferente do padrão.

### <a name="use-the-same-client-id-and-application-id"></a>Use o mesmo ID do cliente e ID de aplicação

Para que a plataforma de identidade da Microsoft saiba quais as aplicações que podem partilhar fichas, essas aplicações precisam de partilhar o mesmo ID do Cliente ou ID de aplicação. Este é o identificador único que lhe foi fornecido quando registou a sua primeira aplicação no portal.

A forma como a plataforma de identidade da Microsoft diz às aplicações que utilizam o mesmo ID de aplicação à parte é através dos seus **URIs redirecionados**. Cada aplicação pode ter vários URIs Redirecionados registados no portal de embarque. Cada aplicativo em seu pacote terá um URI de redirecionamento diferente. Por exemplo:

App1 Redirecione URI: `msauth.com.contoso.mytestapp1://auth` App2 Redirecionar URI: `msauth.com.contoso.mytestapp2://auth` App3 Redirecionamento URI: `msauth.com.contoso.mytestapp3://auth`

> [!IMPORTANT]
> O formato de uris redirecionado deve ser compatível com o formato mSAL suporta, que está documentado nos requisitos de [formato MSAL Redirect URI](redirect-uris-ios.md#msal-redirect-uri-format-requirements).

### <a name="setup-keychain-sharing-between-applications"></a>Partilha de porta-chaves de configuração entre aplicações

Consulte o artigo capacidades [adicionais](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) da Apple para permitir a partilha de porta-chaves. O que é importante é que decida o que quer que o seu porta-chaves seja chamado e adicione essa capacidade a todas as suas aplicações que estarão envolvidas no SSO.

Quando tiver os direitos configurados corretamente, verá um ficheiro `entitlements.plist` no seu diretório de projeto que contém algo como este exemplo:

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

Assim que tiver o direito do porta-chaves ativado em cada uma das suas aplicações, e estiver pronto para utilizar o SSO, configure `MSALPublicClientApplication` com o seu grupo de acesso à porta-chaves como no seguinte exemplo:

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
> Quando partilha um porta-chaves nas suas aplicações, qualquer aplicação pode eliminar utilizadores ou até mesmo todas as fichas através da sua aplicação.
> Isto é particularmente impactante se tiver aplicações que dependem de fichas para fazer trabalho de fundo.
> Partilhar um porta-chaves significa que deve ter muito cuidado quando a sua aplicação utiliza as operações de remoção de sdk de identidade da Microsoft.

Já está! O SDK de identidade da Microsoft irá agora partilhar credenciais em todas as suas aplicações. A lista de conta também será partilhada em casos de candidatura.

## <a name="sso-through-authentication-broker-on-ios"></a>SSO através do corretor de autenticação no iOS

A MSAL fornece suporte para autenticação intermediada com o Microsoft Authenticator. O Microsoft Authenticator fornece SSO para dispositivos registados em AAD, e também ajuda a sua aplicação a seguir as políticas de Acesso Condicional.

Os seguintes passos são como ativa o SSO utilizando um corretor de autenticação para a sua aplicação:

1. Registe um formato Redirect URI compatível com um corretor para a aplicação na lista info.plist da sua aplicação. O formato Redirect URI compatível com o corretor é `msauth.<app.bundle.id>://auth`. Substitua '<app.bundle.id&gt'' com o id do pacote da sua aplicação. Por exemplo:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Adicione os seguintes esquemas à lista info.plist da sua aplicação em `LSApplicationQueriesSchemes`:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Adicione o seguinte ao seu ficheiro `AppDelegate.m` para lidar com as chamadas:

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
    
Se estiver a utilizar o **Xcode 11,** deverá colocar a chamada MSAL no ficheiro `SceneDelegate`.
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
    
## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [fluxos de autenticação e cenários](authentication-flows-app-scenarios.md) de aplicação
