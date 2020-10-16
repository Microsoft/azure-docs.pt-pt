---
title: Personalize navegadores & WebViews (MSAL iOS/macOS) Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como personalizar a experiência do navegador MSAL iOS/macOS para iniciar serção nos utilizadores.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: a8486ec87b5198231a33b1dab382ba457c8c8066
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85478132"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Como: Personalizar navegadores e WebViews para iOS/macOS

É necessário um navegador web para a autenticação interativa. No iOS e no macOS 10.15+, a Microsoft Authentication Library (MSAL) utiliza o navegador web do sistema por padrão (que pode aparecer em cima da sua app) para fazer autenticação interativa para iniciar sação nos utilizadores. A utilização do navegador do sistema tem a vantagem de partilhar o estado de Sign On (SSO) com outras aplicações e com aplicações web.

Pode alterar a experiência personalizando a configuração para outras opções para exibir conteúdo sonoro, tais como:

Apenas para iOS:

- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

Para iOS e macOS:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

O MSAL para o macOS só suporta `WKWebView` em versões de SISTEMA mais antigas. `ASWebAuthenticationSession` é suportado apenas no macOS 10.15 ou acima. 

## <a name="system-browsers"></a>Navegadores de sistema

Para o `ASWebAuthenticationSession` iOS, são `SFAuthenticationSession` `SFSafariViewController` considerados navegadores de sistema. Para o macOS, apenas `ASWebAuthenticationSession` está disponível. Em geral, os navegadores do sistema partilham cookies e outros dados do site com a aplicação do navegador Safari.

Por predefinição, o MSAL irá detetar dinamicamente a versão iOS e selecionar o navegador de sistema recomendado disponível nessa versão. No iOS 12+ será `ASWebAuthenticationSession` . 

### <a name="default-configuration-for-ios"></a>Configuração padrão para iOS

| Versão | Browser |
|:-------------:|:-------------:|
| iOS 12+ | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

### <a name="default-configuration-for-macos"></a>Configuração padrão para macOS

| Versão | Browser |
|:-------------:|:-------------:|
| macOS 10.15+ | ASWebAuthenticationSession |
| outras versões | WKWebView |

Os desenvolvedores também podem selecionar um navegador de sistema diferente para aplicações MSAL:

- `SFAuthenticationSession` é a versão iOS 11 de `ASWebAuthenticationSession` .
- `SFSafariViewController` é um propósito mais geral e fornece uma interface para navegar na web e pode ser usado também para fins de login. No iOS 9 e 10, os cookies e outros dados do site são partilhados com o Safari -- mas não no iOS 11 e posteriormente.

## <a name="in-app-browser"></a>Navegador in-app

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) é um navegador in-app que exibe conteúdo web. Não partilha cookies ou dados do site com outras instâncias **WKWebView,** ou com o navegador Safari. WKWebView é um navegador de plataforma cruzada que está disponível tanto para iOS como para macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Partilha de cookies e implicações de assinatura única (SSO)

O navegador que utiliza impacta a experiência SSO devido à forma como partilham cookies. As tabelas seguintes resumem as experiências SSO por navegador.

| Tecnologia    | Tipo de navegador  | disponibilidade do iOS | disponibilidade de macOS | Partilha cookies e outros dados  | Disponibilidade MSAL | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Sistema | iOS12 e até | macOS 10.15 e mais | Sim | iOS e macOS 10.15+ | c/ Instâncias do Safari
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Sistema | iOS11 e até | N/D | Sim | Apenas no iOS |  c/ Instâncias do Safari
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Sistema | iOS11 e até | N/D | Não | Apenas no iOS | Não**
| **SFSafariViewController** | Sistema | iOS10 | N/D | Sim | Apenas no iOS |  c/ Instâncias do Safari
| **WKWebView**  | In-app | iOS8 e até | macOS 10.10 e mais | Não | iOS e macOS | Não**

** Para que o SSO funcione, os tokens precisam de ser partilhados entre aplicações. Isto requer uma cache simbólica, ou aplicação de corretor, como o Microsoft Authenticator para iOS.

## <a name="change-the-default-browser-for-the-request"></a>Altere o navegador predefinido para o pedido

Você pode usar um navegador in-app, ou um navegador de sistema específico, dependendo dos seus requisitos UX, alterando a seguinte propriedade em `MSALWebviewParameters` :

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Alteração por pedido interativo

Cada pedido pode ser configurado para anular o navegador predefinido alterando a `MSALInteractiveTokenParameters.webviewParameters.webviewType` propriedade antes de passá-la para a `acquireTokenWithParameters:completionBlock:` API.

Além disso, a MSAL suporta passar num personalizado `WKWebView` definindo a `MSALInteractiveTokenParameters.webviewParameters.customWebView` propriedade.

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

Se utilizar uma visão web personalizada, são utilizadas notificações para indicar o estado do conteúdo da web que está a ser apresentado, tais como:

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

Todos os tipos de navegador web suportados pela MSAL são declarados no [MSALWebviewType enum](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

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

Saiba mais sobre [fluxos de autenticação e cenários de aplicação](authentication-flows-app-scenarios.md)
