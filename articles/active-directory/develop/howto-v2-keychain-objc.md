---
title: Configurar keychain
titleSuffix: Microsoft identity platform
description: Saiba como configurar o conjunto de chaves para que seu aplicativo possa armazenar em cache tokens no conjunto de chaves.
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: oldalton
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b4599549e15d6ebe4d0bd04f96c89df86b0c0cd
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917510"
---
# <a name="configure-keychain"></a>Configurar keychain

Quando a [biblioteca de autenticação da Microsoft para IOS e MacOS](msal-overview.md) (MSAL) assina um usuário ou atualiza um token, ele tenta armazenar em cache tokens no conjunto de chaves. Os tokens de cache no conjunto de chaves permitem que o MSAL forneça SSO (logon único) silencioso entre vários aplicativos que são distribuídos pelo mesmo desenvolvedor da Apple. O SSO é obtido por meio da funcionalidade de grupos de acesso do conjunto de chaves. Para obter mais informações, consulte a [documentação de itens](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)do conjunto de chaves da Apple.

Este artigo aborda como configurar os direitos do aplicativo para que o MSAL possa gravar tokens em cache no conjunto de chaves do iOS e macOS.

## <a name="default-keychain-access-group"></a>Grupo de acesso padrão do conjunto de chaves

### <a name="ios"></a>iOS

O MSAL no iOS usa o grupo de acesso `com.microsoft.adalcache` por padrão. Esse é o grupo de acesso compartilhado usado pelos SDKs do MSAL e da ADAL (biblioteca de autenticação do Azure AD) e garante a melhor experiência de SSO (logon único) entre vários aplicativos do mesmo editor.

No iOS, adicione o grupo de conjunto de chaves `com.microsoft.adalcache` ao direito do seu aplicativo no XCode em **configurações do projeto** > **recursos** > compartilhamento de conjunto de **chaves**

### <a name="macos"></a>macOS

O MSAL no macOS usa `com.microsoft.identity.universalstorage` grupo de acesso por padrão.

Devido a limitações do conjunto de chaves do macOS, o `access group` da MSAL não é convertido diretamente no atributo do grupo de acesso do chaveiro (consulte [kSecAttrAccessGroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)) no MacOS 10,14 e versões anteriores. No entanto, ele se comporta da mesma forma de uma perspectiva de SSO, garantindo que vários aplicativos distribuídos pelo mesmo desenvolvedor da Apple possam ter SSO silencioso.

No macOS 10,15 em diante (macOS Catalina), o MSAL usa o atributo de grupo de acesso de conjunto de chaves para obter o SSO silencioso, da mesma forma que o iOS.

## <a name="custom-keychain-access-group"></a>Grupo de acesso personalizado do conjunto de chaves

Se você quiser usar um grupo de acesso de conjunto de chaves diferente, poderá passar seu grupo personalizado ao criar `MSALPublicClientApplicationConfig` antes de criar `MSALPublicClientApplication`, desta forma:

# <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

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

# <a name="swifttabswift"></a>[Swift](#tab/swift)

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

## <a name="disable-keychain-sharing"></a>Desabilitar compartilhamento de conjunto de chaves

Se você não quiser compartilhar o estado de SSO entre vários aplicativos ou usar qualquer grupo de acesso de conjunto de chaves, desabilite o compartilhamento de conjunto de chaves, passando a ID do pacote de aplicativos como seu grupo de chaves:

# <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Identificador-erro 34018 (falha ao definir item no conjunto de chaves)

Erro-34018 normalmente significa que o conjunto de chaves não foi configurado corretamente. Verifique se o grupo de acesso do conjunto de chaves que foi configurado em MSAL corresponde ao configurado em direitos.

## <a name="ensure-your-application-is-properly-signed"></a>Verifique se seu aplicativo está assinado corretamente

No macOS, os aplicativos podem ser executados sem serem assinados pelo desenvolvedor. Embora a maioria das funcionalidades do MSAL continue a funcionar, o SSO por meio do acesso ao conjunto de chaves exige que o aplicativo seja assinado. Se você estiver enfrentando vários prompts de conjunto de chaves, verifique se a assinatura do aplicativo é válida.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os grupos de acesso do conjunto de chaves no artigo [compartilhamento de acesso do grupo de chaves entre conjuntos de aplicativos](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) .
