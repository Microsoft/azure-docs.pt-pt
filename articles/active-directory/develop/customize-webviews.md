---
title: Personalize os navegadores e WebViews (MSAL iOS/macOS)  Azure
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
ms.openlocfilehash: 759f61860c62bcb668db6844df28c52fa28eac80
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085902"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Como: Personalizar navegadores e WebViews para iOS/macOS

Um navegador web é necessário para autenticação interativa. No iOS, a Microsoft Authentication Library (MSAL) utiliza o navegador web do sistema por padrão (que pode aparecer em cima da sua aplicação) para fazer a autenticação interativa para assinar nos utilizadores. A utilização do navegador do sistema tem a vantagem de partilhar o estado Single Sign On (SSO) com outras aplicações e com aplicações web.

Pode alterar a experiência personalizando a configuração para outras opções para exibir conteúdos web, tais como:

Apenas para iOS:

- [Sessão de Autenticação ASWeb](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [Controlador SFSafariView](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

Para iOS e macOS:

- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

A MSAL para macOS apenas suporta `WKWebView`.

## <a name="system-browsers"></a>Navegadores do sistema

Para iOS, `ASWebAuthenticationSession`, `SFAuthenticationSession`e `SFSafariViewController` são considerados navegadores do sistema. Em geral, os navegadores do sistema partilham cookies e outros dados do site com a aplicação do navegador Safari.

Por padrão, o MSAL irá detetar dinamicamente a versão iOS e selecionar o navegador de sistema recomendado disponível nessa versão. No iOS 12+ será `ASWebAuthenticationSession`. 

| Versão | Browser |
|:-------------:|:-------------:|
| iOS 12+ | Sessão de Autenticação ASWeb |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | Controlador SFSafariView |

Os desenvolvedores também podem selecionar um navegador de sistema diferente para aplicações MSAL:

- `SFAuthenticationSession` é a versão iOS 11 do `ASWebAuthenticationSession`.
- `SFSafariViewController` é um propósito mais geral e fornece uma interface para navegar na web e pode ser usado para fins de login também. No iOS 9 e 10, os cookies e outros dados do site são partilhados com o Safari-- mas não no iOS 11 e mais tarde.

## <a name="in-app-browser"></a>Navegador in-app

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) é um navegador na aplicação que exibe conteúdo web. Não partilha cookies ou dados do site com outras instâncias **WKWebView,** ou com o navegador Safari. WKWebView é um navegador cross-platform que está disponível tanto para iOS como para macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Partilha de cookies e implicações de inscrição única (SSO)

O navegador que utiliza tem impactos na experiência SSO devido à forma como partilham cookies. As tabelas seguintes resumem as experiências SSO por navegador.

| Tecnologia    | Tipo de navegador  | disponibilidade do iOS | disponibilidade do macOS | Partilha cookies e outros dados  | Disponibilidade da MSAL | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [Sessão de Autenticação ASWeb](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Sistema | iOS12 e até | macOS 10.15 e até | Sim | iOS apenas | c/ Casos de Safari
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Sistema | iOS11 e até | N/D | Sim | iOS apenas |  c/ Casos de Safari
| [Controlador SFSafariView](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Sistema | iOS11 e até | N/D | Não | iOS apenas | Não*
| **Controlador SFSafariView** | Sistema | iOS10 | N/D | Sim | iOS apenas |  c/ Casos de Safari
| **WKWebView**  | In-app | iOS8 e até | macOS 10.10 e acima | Não | iOS e macOS | Não*

** Para que o SSO funcione, os tokens precisam de ser partilhados entre apps. Isto requer uma cache simbólica, ou aplicação de corretor, como o Microsoft Authenticator para iOS.

## <a name="change-the-default-browser-for-the-request"></a>Alterar o navegador predefinido para o pedido

Pode utilizar um navegador in-app, ou um navegador de sistema específico, dependendo dos seus requisitos UX, alterando a seguinte propriedade em `MSALWebviewParameters`:

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Alteração por pedido interativo

Cada pedido pode ser configurado para anular o navegador predefinido, alterando a propriedade `MSALInteractiveTokenParameters.webviewParameters.webviewType` antes de passá-la para a API `acquireTokenWithParameters:completionBlock:`.

Além disso, a MSAL suporta a passagem de um `WKWebView` personalizado, definindo a propriedade `MSALInteractiveTokenParameters.webviewParameters.customWebView`.

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

Saiba mais sobre [fluxos de autenticação e cenários](authentication-flows-app-scenarios.md) de aplicação
