---
title: Solicite reclamações personalizadas (MSAL iOS/macOS) | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como solicitar reclamações personalizadas.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 08/26/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: a570dccad5f14cf9adf5ca2825d8a3b31ae60d3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85477197"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Como: Solicitar reclamações personalizadas usando MSAL para iOS e macOS

O OpenID Connect permite-lhe solicitar opcionalmente a devolução de reclamações individuais do Ponto Final do UserInfo e/ou no Token ID. Um pedido de reclamação é representado como um objeto JSON que contém uma lista de reclamações solicitadas. Consulte [o OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) para obter mais detalhes.

A Microsoft Authentication Library (MSAL) para iOS e macOS permite solicitar reclamações específicas em cenários de aquisição de token interativos e silenciosos. Fá-lo através do `claimsRequest` parâmetro.

Há vários cenários em que isto é necessário. Por exemplo:

- Solicitando reclamações fora do padrão definido para a sua aplicação.
- Solicitando combinações específicas das reclamações padrão que não podem ser especificadas usando âmbitos para a sua aplicação. Por exemplo, se um token de acesso for rejeitado por falta de reclamações, o pedido pode solicitar os pedidos em falta usando o MSAL.

> [!NOTE]
> A MSAL contorna a cache do token de acesso sempre que um pedido de reclamação é especificado. É importante apenas fornecer `claimsRequest` parâmetro quando são necessárias reclamações adicionais (em vez de fornecer sempre o mesmo `claimsRequest` parâmetro em cada chamada de API MSAL).

`claimsRequest` pode ser especificado `MSALSilentTokenParameters` `MSALInteractiveTokenParameters` em:

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest` pode ser construído a partir de uma representação NSString do pedido de Reclamações JSON. 

Objetivo C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Rápido:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



Pode igualmente ser modificado solicitando reclamações específicas adicionais:

Objetivo C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Rápido:

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest` devem então ser definidos nos parâmetros simbólicos e fornecidos a uma das APIs de aquisição de tokens DAM:

Objetivo C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Rápido:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [fluxos de autenticação e cenários de aplicação](authentication-flows-app-scenarios.md)
