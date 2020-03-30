---
title: Configurar keychain
titleSuffix: Microsoft identity platform
description: Aprenda a configurar o chaveiro para que a sua aplicação possa cache tokens no porta-chaves.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d94bf7ffe955c9ec9ee2a2e7f7c4dbaaa28df270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085858"
---
# <a name="configure-keychain"></a>Configurar keychain

Quando a [Microsoft Authentication Library para iOS e macOS](msal-overview.md) (MSAL) assina num utilizador, ou atualiza um token, tenta cache tokens no porta-chaves. Fichas de cache no porta-chaves permitem ao MSAL fornecer um único sinal silencioso (SSO) entre várias aplicações que são distribuídas pelo mesmo desenvolvedor da Apple. O SSO é conseguido através da funcionalidade dos grupos de acesso ao keychain. Para mais informações, consulte a documentação dos itens de [keychain](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)da Apple.

Este artigo abrange como configurar os direitos da aplicação para que a MSAL possa escrever tokens em cache para iOS e macOS keychain.

## <a name="default-keychain-access-group"></a>Grupo de acesso padrão à porta-chaves

### <a name="ios"></a>iOS

A MSAL no `com.microsoft.adalcache` iOS utiliza o grupo de acesso por defeito. Este é o grupo de acesso partilhado utilizado tanto pela MSAL como pela Azure AD Authentication Library (ADAL) SDKs e garante a melhor experiência de inscrição individual (SSO) entre várias aplicações da mesma editora.

No iOS, `com.microsoft.adalcache` adicione o grupo keychain ao direito da sua aplicação no XCode ao abrigo das **definições** > do Projeto**Capabilities** > **Keychain partilhando**

### <a name="macos"></a>macOS

O MSAL no `com.microsoft.identity.universalstorage` macOS utiliza o grupo de acesso por defeito.

Devido às limitações do porta-chaves macOS, o MSAL's `access group` não se traduz diretamente para o atributo do grupo de acesso à porta-chaves (ver [kSecAttrAccessGroup)](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)no macOS 10.14 e anterior. No entanto, comporta-se de forma semelhante do ponto de vista do SSO, garantindo que várias aplicações distribuídas pelo mesmo desenvolvedor da Apple podem ter SSO silencioso.

No macOS 10.15 em diante (macOS Catalina), o MSAL utiliza o atributo do grupo de acesso à cadeia de chaves para alcançar sSO silencioso, à semelhança do iOS.

## <a name="custom-keychain-access-group"></a>Grupo de acesso personalizado ao porta-chaves

Se quiser usar um grupo de acesso a um porta-chaves `MSALPublicClientApplicationConfig` diferente, `MSALPublicClientApplication`pode passar pelo seu grupo personalizado ao criar , assim:

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

## <a name="disable-keychain-sharing"></a>Desativar a partilha de porta-chaves

Se não quiser partilhar o estado SSO entre várias aplicações, ou utilizar qualquer grupo de acesso à cadeia de chaves, desative a partilha de porta-chaves passando o pacote de aplicações ID como o seu keychainGroup:

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

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Erro do punho -34018 (não conseguiu definir o item no porta-chaves)

Erro -34018 normalmente significa que o porta-chaves não foi configurado corretamente. Certifique-se de que o grupo de acesso ao porta-chaves que foi configurado em MSAL corresponde ao configurado nos direitos.

## <a name="ensure-your-application-is-properly-signed"></a>Certifique-se de que a sua candidatura está devidamente assinada

No macOS, as aplicações podem ser executadas sem serem assinadas pelo desenvolvedor. Embora a maior parte da funcionalidade da MSAL continue a funcionar, o SSO através do acesso à porta-chaves requer a assinatura da aplicação. Se estiver a experimentar várias solicitações de porta-chaves, certifique-se de que a assinatura da sua aplicação é válida.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os grupos de acesso ao keychain na [Apple's Sharing Access to Keychain Itens Entre uma coleção de apps.](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)
