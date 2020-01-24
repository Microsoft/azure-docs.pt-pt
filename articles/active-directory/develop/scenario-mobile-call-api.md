---
title: Ligue para uma Web API de uma aplicação móvel Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação móvel que chama APIs web (chamando uma API web)
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
ms.reviwer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 6b87809e29940b343a395ffb461c0829295fcd8a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702067"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Aplicação móvel que chama APIs web - ligue para uma Web API

Depois de a sua aplicação ter assinado num utilizador e recebido fichas, a MSAL expõe várias informações sobre o utilizador, o ambiente do utilizador e as fichas emitidas. A sua aplicação pode utilizar estes valores para chamar uma API web ou apresentar uma mensagem de boas-vindas ao utilizador.

Primeiro, vamos ver o resultado da MSAL. Depois vamos ver como usar um sinal de acesso do `AuthenticationResult` ou `result` para chamar uma API protegida.

## <a name="msal-result"></a>Resultado da MSAL
A MSAL fornece os seguintes valores: 

- `AccessToken`: Utilizado para chamar APIs web protegidos num pedido http Bearer.
- `IdToken`: Contém informações úteis sobre o utilizador inscrito, como o nome do utilizador, o inquilino da casa e um identificador único para armazenamento.
- `ExpiresOn`: O tempo de validade do símbolo. MSAL lida com a atualização automática para apps.
- `TenantId`: O identificador do inquilino com quem o utilizador assinou. Para os utilizadores convidados (Azure Ative Directory B2B), este valor identificará o inquilino com quem o utilizador assinou, e não o inquilino da casa do utilizador.  
- `Scopes`: Os âmbitos que foram concedidos com o seu símbolo. Os âmbitos concedidos podem ser um subconjunto dos âmbitos que solicitou.

A MSAL também fornece uma abstração para uma `Account`. Um `Account` representa a conta de assinatura do utilizador atual.

- `HomeAccountIdentifier`: O identificador do inquilino do utilizador.
- `UserName`: O nome de utilizador preferido do utilizador. Isto pode estar vazio para os utilizadores do Azure Ative Directory B2C.
- `AccountIdentifier`: O identificador do utilizador inscrito. Este valor será o mesmo que o valor `HomeAccountIdentifier` na maioria dos casos, a menos que o utilizador seja hóspede de outro inquilino.

## <a name="call-an-api"></a>Chame uma API

Depois de ter o sinal de acesso, é fácil chamar uma API web. A sua aplicação utilizará o símbolo para construir um pedido HTTP e, em seguida, executar o pedido.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>MSAL para iOS e macOS

Os métodos para adquirir tokens retornam um objeto `MSALResult`. `MSALResult` expõe uma propriedade `accessToken` que pode ser usada para chamar uma API da Web. O token de acesso deve ser adicionado ao cabeçalho de autorização HTTP, antes de fazer a chamada para acessar a API Web protegida.

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

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="making-several-api-requests"></a>Fazer vários pedidos de API

Se precisar de ligar várias vezes para a mesma API, ou se precisar de ligar para várias APIs, tenha em consideração o seguinte quando construir a sua aplicação:

- **Consentimento incremental**: A plataforma de identidade da Microsoft permite que as aplicações obtem o consentimento do utilizador, uma vez que são necessárias permissões, em vez de todas no início. Cada vez que a sua aplicação estiver pronta para chamar uma API, deve solicitar apenas os âmbitos que precisa de utilizar.
- **Acesso Condicional**: Em certos cenários, poderá obter requisitos adicionais de Acesso Condicional quando então então então então vários pedidos de API. Isto pode acontecer se o primeiro pedido não tiver políticas de Acesso Condicional aplicadas e a sua aplicação tentar aceder silenciosamente a uma nova API que requer acesso condicional. Para lidar com este cenário, certifique-se de que apanha erros de pedidos silenciosos e esteja preparado para fazer um pedido interativo.  Para saber mais, consulte [Orientação para Acesso Condicional](conditional-access-dev-guide.md).

## <a name="calling-several-apis-in-xamarin-or-uwp---incremental-consent-and-conditional-access"></a>Chamando várias APIs em Xamarin ou UWP - Consentimento incremental e acesso condicional

Se necessitar de ligar para várias APIs para o mesmo utilizador, uma vez adquirido um símbolo para um utilizador, pode evitar repetidamente pedir credenciais ao utilizador, ligando `AcquireTokenSilent` para obter um sinal.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Os casos em que a interação é necessária é quando:

- O usuário consentiu a primeira API, mas agora precisa consentir para obter mais escopos (consentimento incremental)
- A primeira API não exigia a autenticação de vários fatores, mas a próxima.

```csharp
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
> [Mover para produção](scenario-mobile-production.md)
