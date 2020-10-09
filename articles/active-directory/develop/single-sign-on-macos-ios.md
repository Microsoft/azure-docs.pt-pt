---
title: Configure SSO em macOS e iOS
titleSuffix: Microsoft identity platform
description: Saiba como configurar um único sinal (SSO) no macOS e iOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 25389348476552298ddb947ccb59acb8b3d5bc57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80881253"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Como: Configurar SSO em macOS e iOS

A Microsoft Authentication Library (MSAL) para macOS e iOS suporta o Sign-on Único (SSO) entre aplicações para macOS/iOS e navegadores. Este artigo abrange os seguintes cenários SSO:

- [SSO silencioso entre várias aplicações](#silent-sso-between-apps)

Este tipo de SSO funciona entre várias aplicações distribuídas pelo mesmo Apple Developer. Fornece SSO silencioso (isto é, o utilizador não é solicitado para credenciais) lendo fichas de atualização escritas por outras aplicações do chaveiro, e trocando-as por tokens de acesso silenciosamente.  

- [SSO através de corretor de autenticação](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Este fluxo não está disponível no macOS.

A Microsoft fornece aplicações, chamadas corretores, que permitem sSO entre aplicações de diferentes fornecedores, desde que o dispositivo móvel esteja registado no Azure Ative Directory (AAD). Este tipo de SSO requer a instalação de uma aplicação de corretor no dispositivo do utilizador.

- **SSO entre MSAL e Safari**

O SSO é alcançado através da classe [ASWebAuthenticationSession.](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) Utiliza o estado de inscrição existente a partir de outras aplicações e do navegador Safari. Não se limita a aplicações distribuídas pelo mesmo Apple Developer, mas requer alguma interação com o utilizador.

Se utilizar a vista web padrão na sua aplicação para iniciar sismo nos utilizadores, obterá SSO automático entre aplicações baseadas em MSAL e Safari. Para saber mais sobre as vistas web que o MSAL suporta, visite [Personalize navegadores e WebViews.](customize-webviews.md)

> [!IMPORTANT]
> Este tipo de SSO não está atualmente disponível no macOS. A MSAL no macOS suporta apenas o WKWebView que não tem suporte SSO com o Safari. 

- **SSO silencioso entre aplicações ADAL e MSAL macOS/iOS**

O MSAL Objective-C apoia a migração e o SSO com aplicações baseadas em Objetivos ADAL. As aplicações devem ser distribuídas pelo mesmo Apple Developer.

Consulte [as aplicações SSO entre as aplicações ADAL e MSAL no macOS e iOS](sso-between-adal-msal-apps-macos-ios.md) para obter instruções para aplicações cross-app SSO entre aplicações baseadas em ADAL e MSAL.

## <a name="silent-sso-between-apps"></a>SSO silencioso entre apps

A MSAL suporta a partilha de SSO através de grupos de acesso a chaveiros iOS.

Para ativar o SSO nas suas aplicações, terá de fazer os seguintes passos, que são explicados com mais detalhes abaixo:

1. Certifique-se de que todas as suas aplicações utilizam o mesmo ID do Cliente ou ID de aplicação.
1. Certifique-se de que todas as suas aplicações partilham o mesmo certificado de assinatura da Apple para que possa partilhar porta-chaves.
1. Solicite o mesmo direito a cada uma das suas candidaturas.
1. Informe os MSAL SDKs sobre o chaveiro partilhado que quer que usemos se for diferente do padrão.

### <a name="use-the-same-client-id-and-application-id"></a>Use o mesmo ID do Cliente e ID de aplicação

Para que a plataforma de identidade da Microsoft saiba quais as aplicações que podem partilhar fichas, essas aplicações precisam de partilhar o mesmo ID do Cliente ou ID de aplicação. Este é o identificador único que lhe foi fornecido quando registou a sua primeira aplicação no portal.

A forma como a plataforma de identidade da Microsoft diz às aplicações que utilizam o mesmo ID de aplicação à parte é através dos seus **URIs de redirecionamento.** Cada aplicação pode ter várias URIs de redirecionamento registadas no portal de embarque. Cada aplicação na sua suite terá um URI de redirecionamento diferente. Por exemplo:

App1 Redirecionar URI: `msauth.com.contoso.mytestapp1://auth`  
App2 Redirecionar URI: `msauth.com.contoso.mytestapp2://auth`  
App3 Redirecionamento URI: `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> O formato de redireccionamento uris deve ser compatível com o formato suportes MSAL, que está documentado nos requisitos do [formato MSAL Redirect URI](redirect-uris-ios.md#msal-redirect-uri-format-requirements).

### <a name="setup-keychain-sharing-between-applications"></a>Partilha de chaveiros de configuração entre aplicações

Consulte o artigo de [adicionar capacidades](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) da Apple para permitir a partilha do chaveiro. O que é importante é que decida o que quer que o seu chaveiro seja chamado e adicione essa capacidade a todas as suas aplicações que estarão envolvidas no SSO.

Quando tiver os direitos configurado corretamente, verá um `entitlements.plist` ficheiro no seu diretório de projeto que contém algo como este exemplo:

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

#### <a name="add-a-new-keychain-group"></a>Adicione um novo grupo de chaveiros

Adicione um novo grupo de chaveiros ao seu projeto **Capabilities**. O grupo dos porta-chaves deve ser:
* `com.microsoft.adalcache` no iOS 
* `com.microsoft.identity.universalstorage` no macOS.

![exemplo de porta-chaves](media/single-sign-on-macos-ios/keychain-example.png)

Para obter mais informações, consulte [os grupos keychain](howto-v2-keychain-objc.md).

## <a name="configure-the-application-object"></a>Configure o objeto de aplicação

Uma vez que tenha o direito de chaveiro ativo em cada uma das suas aplicações, e está pronto para usar SSO, configuure-se com o seu grupo de `MSALPublicClientApplication` acesso ao chaveiro como no seguinte exemplo:

Objetivo C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Rápido:

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
> Quando partilha um chaveiro em todas as suas aplicações, qualquer aplicação pode eliminar utilizadores ou até mesmo todos os tokens em toda a sua aplicação.
> Isto é particularmente impactante se tiver aplicações que dependem de fichas para fazer trabalhos de fundo.
> Partilhar um porta-chaves significa que deve ter muito cuidado quando a sua aplicação utiliza operações de remoção de identidade da Microsoft.

Já está. A SDK de identidade da Microsoft irá agora partilhar credenciais em todas as suas aplicações. A lista de contas também será partilhada em todas as instâncias de aplicação.

## <a name="sso-through-authentication-broker-on-ios"></a>SSO através de corretor de autenticação no iOS

A MSAL fornece suporte para autenticação intermediada com o Microsoft Authenticator. O Microsoft Authenticator fornece SSO para dispositivos registados AAD e também ajuda a sua aplicação a seguir as políticas de Acesso Condicional.

Os seguintes passos são como ativar o SSO usando um corretor de autenticação para a sua aplicação:

1. Registe um formato URI de redirecionamento compatível com corretor para a aplicação na info.plist da sua aplicação. O formato URI de redirecionamento compatível com corretor é `msauth.<app.bundle.id>://auth` . Substitua o '<app.bundle.id>'' pelo ID do feixe da sua aplicação. Por exemplo:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Adicione os seguintes esquemas à info.plist da sua aplicação em `LSApplicationQueriesSchemes` :

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Adicione o seguinte ao seu `AppDelegate.m` ficheiro para lidar com chamadas:

    Objetivo C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Rápido:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Se estiver a utilizar o Xcode 11,** deverá colocar a chamada MSAL no `SceneDelegate` ficheiro.
Se apoiar tanto o UISceneDeeDeegate como o UIApplicationDeegate para compatibilidade com iOS mais antigo, o callback MSAL teria de ser colocado em ambos os ficheiros.

Objetivo C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Rápido:

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

Saiba mais sobre [fluxos de autenticação e cenários de aplicação](authentication-flows-app-scenarios.md)