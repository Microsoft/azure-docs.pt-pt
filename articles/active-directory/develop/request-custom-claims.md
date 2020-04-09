---
title: Solicitar reclamações personalizadas (MSAL iOS/macOS) / Azure
titleSuffix: Microsoft identity platform
description: Saiba como solicitar reclamações personalizadas.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 4974fe3b387683f662d7a7b4f3ccb4935153f07e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883101"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Como: Solicitar reclamações personalizadas utilizando MSAL para iOS e macOS

O OpenID Connect permite-lhe solicitar opcionalmente a devolução de reclamações individuais do Ponto final do UserInfo e/ou no Id Token. Um pedido de reclamações é representado como um objeto JSON que contém uma lista de reclamações solicitadas. Consulte [o OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) para obter mais detalhes.

A Microsoft Authentication Library (MSAL) para iOS e macOS permite solicitar reclamações específicas em cenários de aquisição interativos e silenciosos. Fá-lo através `claimsRequest` do parâmetro.

Há vários cenários em que isto é necessário. Por exemplo:

- Solicitando reclamações fora do padrão definido para a sua aplicação.
- Solicitando combinações específicas das alegações padrão que não podem ser especificadas utilizando âmbitos para a sua aplicação. Por exemplo, se um token de acesso for rejeitado devido a reclamações em falta, o pedido pode solicitar as reclamações em falta usando mSAL.

> [!NOTE]
> A MSAL ignora a cache de acesso a qualquer que seja especificado um pedido de reclamações. É importante apenas `claimsRequest` fornecer parâmetro quando são necessárias reclamações adicionais `claimsRequest` (em oposição a fornecer sempre o mesmo parâmetro em cada chamada da API MSAL).

`claimsRequest`pode ser especificado em `MSALSilentTokenParameters` e: `MSALInteractiveTokenParameters`

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
`MSALClaimsRequest`pode ser construído a partir de uma representação NSString do pedido de reclamações da JSON. 

Objetivo C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



Também pode ser modificado solicitando reclamações específicas adicionais:

Objetivo C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift:

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



`MSALClaimsRequest`devem então ser fixados nos parâmetros simbólicos e fornecidos a uma das aquisições de token sancionadas da MSAL:

Objetivo C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [fluxos de autenticação e cenários](authentication-flows-app-scenarios.md) de aplicação
