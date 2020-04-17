---
title: Personalize os navegadores & WebViews (MSAL iOS/macOS) [ Azure
titleSuffix: Microsoft identity platform
description: Saiba como personalizar a experiência do navegador MSAL iOS/macOS para iniciar sessão nos utilizadores.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 3b4362e4c5e69efddfbc99ef0f98ad3c5966165c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450884"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Como: Personalizar navegadores e WebViews para iOS/macOS

Um navegador web é necessário para autenticação interativa. No iOS e no macOS 10.15+, a Microsoft Authentication Library (MSAL) utiliza o navegador web do sistema por padrão (que pode aparecer em cima da sua app) para fazer a autenticação interativa para assinar nos utilizadores. A utilização do navegador do sistema tem a vantagem de partilhar o estado Single Sign On (SSO) com outras aplicações e com aplicações web.

Pode alterar a experiência personalizando a configuração para outras opções para exibir conteúdos web, tais como:

Apenas para iOS:

- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [Controlador SFSafariView](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

Para iOS e macOS:

- [Sessão de Autenticação ASWeb](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MSAL para macOS `WKWebView` apenas suporta em versões oS mais antigas. `ASWebAuthenticationSession`é suportado apenas no macOS 10.15 ou superior. 

## <a name="system-browsers"></a>Navegadores do sistema

Para `ASWebAuthenticationSession`iOS, `SFAuthenticationSession`e `SFSafariViewController` são considerados navegadores de sistema. Para macOS, `ASWebAuthenticationSession` apenas está disponível. Em geral, os navegadores do sistema partilham cookies e outros dados do site com a aplicação do navegador Safari.

Por padrão, o MSAL irá detetar dinamicamente a versão iOS e selecionar o navegador de sistema recomendado disponível nessa versão. No iOS 12+ `ASWebAuthenticationSession`será. 

### <a name="default-configuration-for-ios"></a>Configuração padrão para iOS

| Versão | Browser |
|:-------------:|:-------------:|
| iOS 12+ | Sessão de Autenticação ASWeb |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | Controlador SFSafariView |

### <a name="default-configuration-for-macos"></a>Configuração padrão para macOS

| Versão | Browser |
|:-------------:|:-------------:|
| macOS 10.15+ | Sessão de Autenticação ASWeb |
| outras versões | WKWebView |

Os desenvolvedores também podem selecionar um navegador de sistema diferente para aplicações MSAL:

- `SFAuthenticationSession`é a versão iOS `ASWebAuthenticationSession`11 de .
- `SFSafariViewController`é um propósito mais geral e fornece uma interface para navegar na web e pode ser usado para fins de login também. No iOS 9 e 10, os cookies e outros dados do site são partilhados com o Safari-- mas não no iOS 11 e mais tarde.

## <a name="in-app-browser"></a>Navegador in-app

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) é um navegador na aplicação que exibe conteúdo web. Não partilha cookies ou dados do site com outras instâncias **WKWebView,** ou com o navegador Safari. WKWebView é um navegador cross-platform que está disponível tanto para iOS como para macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Partilha de cookies e implicações de inscrição única (SSO)

O navegador que utiliza tem impactos na experiência SSO devido à forma como partilham cookies. As tabelas seguintes resumem as experiências SSO por navegador.

| Tecnologia    | Tipo de navegador  | disponibilidade do iOS | disponibilidade do macOS | Partilha cookies e outros dados  | Disponibilidade da MSAL | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [Sessão de Autenticação ASWeb](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Sistema | iOS12 e até | macOS 10.15 e até | Sim | iOS e macOS 10.15+ | c/ Casos de Safari
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Sistema | iOS11 e até | N/D | Sim | Apenas no iOS |  c/ Casos de Safari
| [Controlador SFSafariView](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Sistema | iOS11 e até | N/D | Não | Apenas no iOS | Não*
| **Controlador SFSafariView** | Sistema | iOS10 | N/D | Sim | Apenas no iOS |  c/ Casos de Safari
| **WKWebView**  | In-app | iOS8 e até | macOS 10.10 e acima | Não | iOS e macOS | Não*

** Para que o SSO funcione, os tokens precisam de ser partilhados entre apps. Isto requer uma cache simbólica, ou aplicação de corretor, como o Microsoft Authenticator para iOS.

## <a name="change-the-default-browser-for-the-request"></a>Alterar o navegador predefinido para o pedido

Pode utilizar um navegador in-app, ou um navegador de sistema específico, dependendo `MSALWebviewParameters`dos seus requisitos UX, alterando a seguinte propriedade em:

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Alteração por pedido interativo

Cada pedido pode ser configurado para anular `MSALInteractiveTokenParameters.webviewParameters.webviewType` o navegador predefinido, alterando a propriedade antes de passá-la para a `acquireTokenWithParameters:completionBlock:` API.

Além disso, a MSAL `WKWebView` suporta a `MSALInteractiveTokenParameters.webviewParameters.customWebView` passagem de um costume, definindo a propriedade.

Por exemplo:

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(authPresentationViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

Se utilizar uma webview personalizada, as notificações são utilizadas para indicar o estado do conteúdo web que está a ser apresentado, tais como:

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

Todos os tipos de navegador web suportados mSAL são declarados no [Enum MSALWebviewType](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
    /**
     For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession or SFAuthenticationSession).
     For older versions, with AuthenticationSession not being available, uses SafariViewController.
     For macOS 10.15 and above uses ASWebAuthenticationSession
     For older macOS versions uses WKWebView
     */
    MSALWebviewTypeDefault,
    
    /** Use ASWebAuthenticationSession where available.
     On older iOS versions uses SFAuthenticationSession
     Doesn't allow any other webview type, so if either of these are not present, fails the request*/
    MSALWebviewTypeAuthenticationSession,
    
#if TARGET_OS_IPHONE
    
    /** Use SFSafariViewController for all versions. */
    MSALWebviewTypeSafariViewController,
    
#endif
    /** Use WKWebView */
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [fluxos de autenticação e cenários](authentication-flows-app-scenarios.md) de aplicação
