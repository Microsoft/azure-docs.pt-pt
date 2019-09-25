---
title: Aplicativo de desktop que chama APIs da Web (chamando uma API Web)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo de área de trabalho que chama APIs da Web (chamando uma API Web)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56d3d01e39adfeb6bf2ef5e7e7d595f49c90f5a5
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268283"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Aplicativo de desktop que chama APIs da Web – chamar uma API da Web

Agora que você tem um token, você pode chamar uma API Web protegida.

## <a name="calling-a-web-api-from-net"></a>Chamando uma API Web do .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-a-web-api-in-msal-for-ios-and-macos"></a>Chamando uma API Web no MSAL para iOS e macOS

Os métodos para adquirir tokens retornam um `MSALResult` objeto. `MSALResult`expõe uma `accessToken` propriedade que pode ser usada para chamar uma API da Web. O token de acesso deve ser adicionado ao cabeçalho de autorização HTTP, antes de fazer a chamada para acessar a API Web protegida.

Objective-C:

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

Swift

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Chamando várias APIs-consentimento incremental e acesso condicional

Se você precisar chamar várias APIs para o mesmo usuário, depois de obter um token para a primeira API, basta chamar `AcquireTokenSilent`e obterá um token para as outras APIs silenciosamente na maioria das vezes.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Os casos em que a interação é necessária é quando:

- O usuário consentiu a primeira API, mas agora precisa consentir para obter mais escopos (consentimento incremental)
- A primeira API não exigia a autenticação de vários fatores, mas a próxima.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-desktop-production.md)
