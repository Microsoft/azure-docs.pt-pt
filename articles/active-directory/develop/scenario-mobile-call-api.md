---
title: Aplicativo móvel que chama APIs da Web – chamando uma API da Web
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo móvel que chama APIs da Web (chamando uma API da Web)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e70b828219fc497fc07e2bc128eb480a532a176
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802561"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Aplicativo móvel que chama APIs Web – chamar uma API da Web

Depois que seu aplicativo tiver entrado em um usuário e receber tokens, o MSAL expõe várias informações sobre o usuário, o ambiente do usuário e os tokens emitidos. Seu aplicativo pode usar esses valores para chamar uma API da Web ou exibir uma mensagem de boas-vindas para o usuário.

Primeiro, veremos o resultado do MSAL. Em seguida, veremos como usar um token de acesso do `AuthenticationResult` ou `result` para chamar uma API Web protegida.

## <a name="msal-result"></a>Resultado do MSAL
MSAL fornece os seguintes valores: 

- `AccessToken`: usado para chamar APIs Web protegidas em uma solicitação de portador HTTP.
- `IdToken`: contém informações úteis sobre o usuário conectado, como o nome do usuário, o locatário inicial e um identificador exclusivo para armazenamento.
- `ExpiresOn`: o tempo de expiração do token. MSAL lida com a atualização automática para aplicativos.
- `TenantId`: o identificador do locatário com o qual o usuário se conectou. Para usuários convidados (Azure Active Directory B2B), esse valor identificará o locatário com o qual o usuário se conectou, não o locatário inicial do usuário.  
- `Scopes`: os escopos que foram concedidos com seu token. Os escopos concedidos podem ser um subconjunto dos escopos que você solicitou.

O MSAL também fornece uma abstração para um `Account`. Um `Account` representa a conta conectada do usuário atual.

- `HomeAccountIdentifier`: o identificador do locatário inicial do usuário.
- `UserName`: o nome de usuário preferencial do usuário. Isso pode estar vazio para Azure Active Directory B2C usuários.
- `AccountIdentifier`: o identificador do usuário conectado. Esse valor será o mesmo que o valor de `HomeAccountIdentifier` na maioria dos casos, a menos que o usuário seja um convidado em outro locatário.

## <a name="call-an-api"></a>Chamar uma API

Depois de ter o token de acesso, é fácil chamar uma API da Web. Seu aplicativo usará o token para construir uma solicitação HTTP e, em seguida, executará a solicitação.

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

## <a name="making-several-api-requests"></a>Fazendo várias solicitações de API

Se você precisar chamar a mesma API várias vezes ou se precisar chamar várias APIs, leve em consideração o seguinte ao compilar seu aplicativo:

- **Consentimento incremental**: a plataforma de identidade da Microsoft permite que os aplicativos obtenham o consentimento do usuário, pois as permissões são necessárias, em vez de tudo no início. Cada vez que seu aplicativo está pronto para chamar uma API, ele deve solicitar somente os escopos que ele precisa para usar.
- **Acesso condicional**: em determinados cenários, você pode obter requisitos de acesso condicional adicionais ao fazer várias solicitações de API. Isso pode acontecer se a primeira solicitação não tiver nenhuma política de acesso condicional aplicada e seu aplicativo tentar acessar silenciosamente uma nova API que requer acesso condicional. Para lidar com esse cenário, não se esqueça de detectar erros de solicitações silenciosas e estar preparado para fazer uma solicitação interativa.  Para saber mais, consulte as [diretrizes para acesso condicional](conditional-access-dev-guide.md).

## <a name="calling-several-apis-in-xamarin-or-uwp---incremental-consent-and-conditional-access"></a>Chamando várias APIs no Xamarin ou UWP-consentimento incremental e acesso condicional

Se precisar chamar várias APIs para o mesmo usuário, depois de adquirir um token para um usuário, você poderá evitar solicitar repetidamente as credenciais do usuário, chamando novamente `AcquireTokenSilent` para obter um token.

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
> [Mover para produção](scenario-mobile-production.md)
