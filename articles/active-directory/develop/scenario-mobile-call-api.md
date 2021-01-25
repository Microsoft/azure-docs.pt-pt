---
title: Ligue para uma API web a partir de uma aplicação móvel | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação móvel que chame APIs web. (Ligue para uma API web.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 601eefa7957972daa2d538e6ba0d9b7a5311c79d
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756484"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Ligue para uma API web a partir de uma aplicação móvel

Depois de a sua aplicação assinar num utilizador e receber fichas, a Microsoft Authentication Library (MSAL) expõe informações sobre o utilizador, o ambiente do utilizador e as fichas emitidas. A sua aplicação pode utilizar estes valores para chamar uma API web ou apresentar uma mensagem de boas-vindas ao utilizador.

Neste artigo, vamos primeiro olhar para o resultado do MSAL. Então vamos ver como usar um token de acesso de `AuthenticationResult` ou para chamar uma `result` API web protegida.

## <a name="msal-result"></a>Resultado da MSAL
A MSAL fornece os seguintes valores: 

- `AccessToken` chama APIs web protegidos num pedido do portador HTTP.
- `IdToken` contém informações úteis sobre o utilizador inscrito. Esta informação inclui o nome do utilizador, o inquilino da casa, e um identificador único para armazenamento.
- `ExpiresOn` é o tempo de expiração do símbolo. A MSAL trata da atualização automática de uma aplicação.
- `TenantId` é o identificador do inquilino onde o utilizador inscreveu. Para os utilizadores convidados em Azure Ative Directory (Azure AD) B2B, este valor identifica o inquilino onde o utilizador se inscreveu. O valor não identifica o inquilino da casa do utilizador.  
- `Scopes` indica os âmbitos que foram concedidos com o seu símbolo. Os âmbitos concedidos podem ser um subconjunto dos âmbitos que pediu.

O MSAL também fornece uma abstração para um `Account` valor. Um `Account` valor representa a conta de assinatura do utilizador atual:

- `HomeAccountIdentifier` identifica o inquilino da casa do utilizador.
- `UserName` é o nome de utilizador preferido do utilizador. Este valor pode estar vazio para os utilizadores do Azure AD B2C.
- `AccountIdentifier` identifica o utilizador inscrito. Na maioria dos casos, este valor é o mesmo que o `HomeAccountIdentifier` valor, a menos que o utilizador seja hóspede em outro inquilino.

## <a name="call-an-api"></a>Chame uma API

Depois de ter o token de acesso, pode ligar para uma API web. A sua aplicação utilizará o token para construir um pedido HTTP e, em seguida, executar o pedido.

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

Os métodos para adquirir fichas devolvem um `MSALResult` objeto. `MSALResult` expõe uma `accessToken` propriedade. Pode usar `accessToken` para chamar uma API web. Adicione esta propriedade ao cabeçalho de autorização HTTP antes de ligar para aceder à API web protegida.

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

- **Consentimento incremental**: A plataforma de identidade da Microsoft permite que as aplicações obtenham o consentimento do utilizador quando são necessárias permissões e não todas no início. Cada vez que a sua aplicação estiver pronta para chamar uma API, deve solicitar apenas os âmbitos de que necessita.

- **Acesso condicional**: Quando es faz vários pedidos de API, em certos cenários poderá ter de cumprir requisitos adicionais de acesso condicional. Os requisitos podem aumentar desta forma se o primeiro pedido não tiver políticas de acesso condicional e a sua aplicação tentar aceder silenciosamente a uma nova API que requer acesso condicional. Para resolver este problema, certifique-se de que apanha erros de pedidos silenciosos e esteja preparado para fazer um pedido interativo.  Para obter mais informações, consulte [Orientação para acesso condicional.](../azuread-dev/conditional-access-dev-guide.md)

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Ligue para várias APIs usando consentimento incremental e acesso condicional

Se precisar de ligar para várias APIs para o mesmo utilizador, depois de adquirir um token para o utilizador, pode evitar pedir repetidamente ao utilizador credenciais, ligando posteriormente `AcquireTokenSilent` para obter um token:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

É necessária interação quando:

- O utilizador consentiu na primeira API mas agora precisa de consentir por mais âmbitos. Neste caso, usa o consentimento incremental.
- A primeira API não requer [autenticação multi-factor,](../authentication/concept-mfa-howitworks.md)mas a próxima API sim.

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

## <a name="next-steps"></a>Próximos passos

Passe para o próximo artigo neste cenário, [passe para a produção](scenario-mobile-production.md).
