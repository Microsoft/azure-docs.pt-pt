---
title: Configurar keychain
titleSuffix: Microsoft identity platform
description: Aprenda a configurar o chaveiro para que a sua aplicação possa cache tokens no chaveiro.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 06e197a6e445c7dc1179be696318905f2132ee36
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85477741"
---
# <a name="configure-keychain"></a>Configurar keychain

Quando a Biblioteca de Autenticação da [Microsoft para iOS e macOS](msal-overview.md) (MSAL) assina num utilizador, ou atualiza um token, tenta cache tokens no chaveiro. Caching tokens no chaveiro permite que a MSAL forneça um sign-on single silencioso (SSO) entre várias aplicações que são distribuídas pelo mesmo desenvolvedor da Apple. O SSO é alcançado através da funcionalidade dos grupos de acesso ao chaveiro. Para mais informações, consulte a [documentação de Itens keychain](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)da Apple.

Este artigo abrange como configurar os direitos das aplicações para que a MSAL possa escrever fichas em cache para iOS e keychain macOS.

## <a name="default-keychain-access-group"></a>Grupo de acesso de chaveiro predefinido

### <a name="ios"></a>iOS

O MSAL no iOS utiliza o `com.microsoft.adalcache` grupo de acesso por predefinição. Este é o grupo de acesso partilhado utilizado tanto pela MSAL como pela Azure AD Authentication Library (ADAL) SDKs e garante a melhor experiência de sso (SSO) entre várias aplicações da mesma editora.

No iOS, adicione o `com.microsoft.adalcache` grupo keychain ao direito da sua aplicação no XCode em **definições de projeto**  >  **Capabilities**  >  **Keychain**

### <a name="macos"></a>macOS

O MSAL no macOS utiliza `com.microsoft.identity.universalstorage` o grupo de acesso por padrão.

Devido às limitações do keychain do macOS, `access group` a MSAL's não se traduz diretamente no atributo do grupo de acesso ao chaveiro (ver [kSecAttrAccessGroup)](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)no macOS 10.14 e mais cedo. No entanto, comporta-se da mesma forma do ponto de vista SSO, garantindo que várias aplicações distribuídas pelo mesmo desenvolvedor da Apple podem ter SSO silencioso.

No macOS 10.15 em diante (macOS Catalina), o MSAL utiliza o atributo do grupo de acesso ao chaveiro para obter SSO silencioso, à semelhança do iOS.

## <a name="custom-keychain-access-group"></a>Grupo de acesso personalizado do chaveiro

Se quiser utilizar um grupo de acesso a chaveiros diferente, pode passar o seu grupo personalizado ao `MSALPublicClientApplicationConfig` `MSALPublicClientApplication` criar, como este:

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>Desativar a partilha do chaveiro

Se não quiser partilhar o estado SSO entre várias aplicações ou utilizar qualquer grupo de acesso a chaveiros, desative a partilha do chaveiro, passando o ID do pacote de aplicações como o seu KeychainGroup:

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Erro de pega -34018 (não definiu o item no chaveiro)

Erro -34018 normalmente significa que o porta-chaves não foi configurado corretamente. Certifique-se de que o grupo de acesso do chaveiro que foi configurado no MSAL corresponde ao configurado nos direitos.

## <a name="ensure-your-application-is-properly-signed"></a>Certifique-se de que a sua candidatura está devidamente assinada

No macOS, as aplicações podem ser executadas sem serem assinadas pelo desenvolvedor. Embora a maioria da funcionalidade da MSAL continue a funcionar, o SSO através do acesso ao chaveiro requer a assinatura da aplicação. Se estiver a experimentar várias indicações de chaveiro, certifique-se de que a assinatura da sua aplicação é válida.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os grupos de acesso ao chaveiro no [Apple's Sharing Access to Keychain Items Entre uma Coleção de Apps](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) artigo.
