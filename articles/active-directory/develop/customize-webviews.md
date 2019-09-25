---
title: Personalizar navegadores e webviews | Plataforma de identidade da Microsoft
description: Saiba como personalizar a experiência do navegador usada pelo MSAL para iOS e macOS para conectar usuários
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
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
ms.openlocfilehash: c1b7417de8de6fb063de18fe670ef474a3b486d0
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269080"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Como: Personalizar navegadores e webviews para iOS/macOS

Um navegador da Web é necessário para a autenticação interativa. No iOS, a MSAL (biblioteca de autenticação da Microsoft) usa um navegador da Web do sistema por padrão (que pode aparecer na parte superior do seu aplicativo) para fazer a autenticação interativa para conectar usuários. O uso do navegador do sistema tem a vantagem significativa de compartilhar o estado de logon único (SSO) com outros aplicativos e com aplicativos Web.

Você pode alterar a experiência Personalizando a configuração para outras opções de exibição de conteúdo da Web, como:

Somente para iOS:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

Para iOS e macOS:

- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

O MSAL para macOS só `WKWebView`oferece suporte a.

## <a name="system-browsers"></a>Navegadores do sistema

Para IOS, `ASWebAuthenticationSession`, `SFAuthenticationSession`, e `SFSafariViewController` são considerados navegadores do sistema. Em geral, os navegadores do sistema compartilham cookies e outros dados do site com o aplicativo de navegador do Safari.

Por padrão, o MSAL detectará a versão do iOS dinamicamente e selecionará o navegador do sistema recomendado disponível nessa versão. No iOS 12, será `ASWebAuthenticationSession`. 

| Version | Browser |
|:-------------:|:-------------:|
| iOS 12 + | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

Os desenvolvedores também podem selecionar um navegador de sistema diferente para aplicativos MSAL:

- `SFAuthenticationSession`é a versão do iOS 11 `ASWebAuthenticationSession`do.
- `SFSafariViewController`é uma finalidade mais geral e fornece uma interface para navegar na Web e também pode ser usada para fins de logon. No iOS 9 e 10, os cookies e outros dados do site são compartilhados com o Safari, mas não no iOS 11 e posterior.

## <a name="in-app-browser"></a>Navegador no aplicativo

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) é um navegador no aplicativo que exibe o conteúdo da Web. Ele não compartilha cookies ou dados do site com outras instâncias do **WKWebView** ou com o navegador Safari. O WKWebView é um navegador de plataforma cruzada que está disponível para iOS e macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Implicações de compartilhamento de cookies e SSO (logon único)

O navegador que você usa afeta a experiência de SSO devido à forma como eles compartilham cookies. As tabelas a seguir resumem as experiências de SSO por navegador.

| Tecnologia    | Tipo de navegador  | disponibilidade do iOS | disponibilidade do macOS | Compartilha cookies e outros dados  | Disponibilidade do MSAL | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Sistema | iOS12 e up | macOS 10,15 e superior | Sim | somente iOS | instâncias do Safari
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Sistema | iOS11 e up | N/A | Sim | somente iOS |  instâncias do Safari
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Sistema | iOS11 e up | N/A | Não | somente iOS | Não * *
| **SFSafariViewController** | Sistema | iOS10 | N/A | Sim | somente iOS |  instâncias do Safari
| **WKWebView**  | No aplicativo | iOS8 e up | macOS 10,10 e superior | Não | iOS e macOS | Não * *

\* * Para que o SSO funcione, os tokens precisam ser compartilhados entre aplicativos. Isso requer um cache de token ou aplicativo de agente, como Microsoft Authenticator para iOS.

## <a name="change-the-default-browser-for-the-request"></a>Alterar o navegador padrão para a solicitação

Você pode usar um navegador no aplicativo ou um navegador de sistema específico, dependendo dos requisitos de sua experiência do seu UX, alterando a `MSALWebviewParameters`seguinte propriedade em:

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Alterar por solicitação interativa

Cada solicitação pode ser configurada para substituir o navegador padrão, `MSALInteractiveTokenParameters.webviewParameters.webviewType` alterando a propriedade antes de `acquireTokenWithParameters:completionBlock:` passá-la para a API.

Além disso, o MSAL dá suporte à `WKWebView` passagem de um `MSALInteractiveTokenParameters.webviewParameters.customWebView` personalizado, definindo a propriedade.

Por exemplo:

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithParentViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(parentViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

Se você usar um WebView personalizado, as notificações serão usadas para indicar o status do conteúdo da Web que está sendo exibido, como:

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>Opções

Todos os tipos de navegadores da Web com suporte do MSAL são declarados na [Enumeração MSALWebviewType](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
#if TARGET_OS_IPHONE
    // For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession
    // or SFAuthenticationSession).
    // For older versions, with AuthenticationSession not being available, uses
    // SafariViewController.
    MSALWebviewTypeDefault,
    
    // Use SFAuthenticationSession/ASWebAuthenticationSession
    MSALWebviewTypeAuthenticationSession,
    
    // Use SFSafariViewController for all versions.
    MSALWebviewTypeSafariViewController,
    
#endif
    // Use WKWebView
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os [fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)
