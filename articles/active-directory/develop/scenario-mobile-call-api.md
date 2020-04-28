---
title: Ligue para uma Web API de uma aplicação móvel Azure
titleSuffix: Microsoft identity platform
description: Aprenda a construir uma aplicação móvel que chama APIs web. (Ligue para uma Web API.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 28f57c5657ce2f8537a654a7f67ed4481fab2c91
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882697"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Ligue para uma Web API de uma aplicação móvel

Depois de a sua aplicação assinar num utilizador e receber fichas, a Microsoft Authentication Library (MSAL) expõe informações sobre o utilizador, o ambiente do utilizador e os tokens emitidos. A sua aplicação pode utilizar estes valores para chamar uma API web ou apresentar uma mensagem de boas-vindas ao utilizador.

Neste artigo, vamos primeiro olhar para o resultado da MSAL. Então vamos ver como usar um sinal `AuthenticationResult` `result` de acesso de ou chamar uma API protegida.

## <a name="msal-result"></a>Resultado da MSAL
A MSAL fornece os seguintes valores: 

- `AccessToken`chamadas APIs web protegidas num pedido de portador http.
- `IdToken`contém informações úteis sobre o utilizador inscrito. Estas informações incluem o nome do utilizador, o inquilino da casa, e um identificador único para armazenamento.
- `ExpiresOn`é o tempo de validade do símbolo. A MSAL trata da atualização automática de uma aplicação.
- `TenantId`é o identificador do inquilino onde o utilizador assinou. Para os utilizadores convidados do Azure Ative Directory (Azure AD) B2B, este valor identifica o inquilino onde o utilizador inscreveu. O valor não identifica o inquilino da casa do utilizador.  
- `Scopes`indica os âmbitos que foram concedidos com o seu símbolo. Os âmbitos concedidos podem ser um subconjunto dos âmbitos que solicitou.

A MSAL também fornece uma `Account` abstração por um valor. Um `Account` valor representa a conta de assinatura do utilizador atual:

- `HomeAccountIdentifier`identifica o inquilino da casa do utilizador.
- `UserName`é o nome de utilizador preferido do utilizador. Este valor pode estar vazio para os utilizadores do Azure AD B2C.
- `AccountIdentifier`identifica o utilizador inscrito. Na maioria dos casos, este `HomeAccountIdentifier` valor é o mesmo que o valor, a menos que o utilizador seja hóspede de outro inquilino.

## <a name="call-an-api"></a>Chame uma API

Depois de ter o sinal de acesso, pode chamar uma API web. A sua aplicação utilizará o símbolo para construir um pedido HTTP e, em seguida, executar o pedido.

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

Os métodos para adquirir `MSALResult` fichas devolvem um objeto. `MSALResult`expõe uma `accessToken` propriedade. Pode usar `accessToken` para chamar uma API web. Adicione esta propriedade ao cabeçalho de autorização http antes de ligar para aceder à API da web protegida.

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

## <a name="make-several-api-requests"></a>Faça vários pedidos de API

Se precisar de ligar várias vezes para a mesma API, ou se precisar de ligar para várias APIs, considere os seguintes assuntos quando construir a sua aplicação:

- **Consentimento incremental**: A plataforma de identidade da Microsoft permite que as aplicações obtem o consentimento do utilizador quando são necessárias permissões e não todas no início. Cada vez que a sua aplicação estiver pronta para chamar uma API, deve solicitar apenas os âmbitos de que necessita.

- **Acesso condicional**: Quando efaz vários pedidos de API, em certos cenários poderá ter de cumprir requisitos adicionais de acesso condicional. Os requisitos podem aumentar desta forma se o primeiro pedido não tiver políticas de acesso condicional e a sua aplicação tentar aceder silenciosamente a uma nova API que requer acesso condicional. Para lidar com este problema, certifique-se de que apanha erros de pedidos silenciosos e esteja preparado para fazer um pedido interativo.  Para mais informações, consulte [Orientação para acesso condicional](../azuread-dev/conditional-access-dev-guide.md).

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Ligue para várias APIs usando o consentimento incremental e o acesso condicional

Se necessitar de ligar para várias APIs para o mesmo utilizador, depois de adquirir um símbolo para `AcquireTokenSilent` o utilizador, pode evitar repetidamente pedir credenciais ao utilizador, ligando posteriormente para obter um sinal:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

A interação é necessária quando:

- O utilizador consentiu na primeira API, mas agora precisa de consentir mais âmbitos. Neste caso, usa o consentimento incremental.
- A primeira API não requer autenticação de vários fatores, mas a próxima API requer.

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
