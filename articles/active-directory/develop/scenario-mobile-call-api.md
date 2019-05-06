---
title: Aplicação móvel que chamadas de web APIs - chamar uma API web | Plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação móvel que chama as APIs da Web (chamar uma API Web)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd65b9f97c373c55a3486e06e83fca7cf824cad
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075119"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Aplicação móvel que chama a APIs - web chamar uma API web

Assim que a sua aplicação tem um utilizador com sessão iniciada e recebido tokens, MSAL expõe várias partes de informações sobre o utilizador, o seu ambiente e os tokens emitidos. A aplicação pode utilizar estes valores para chamar uma API web ou exibir uma mensagem de boas-vindas a um utilizador.

Em primeiro lugar, vamos explorar o resultado MSAL, em seguida, como utilizar um token de acesso a partir da `AuthenticationResult` ou `result` para chamar uma API web protegida.

## <a name="msal-result"></a>Resultado MSAL

- `AccessToken`: Utilizada para chamar as APIs de web protegida numa solicitação de portador de HTTP.
- `IdToken`: Contém afirmações úteis sobre o utilizador com sessão iniciada como seu nome, o inquilino principal e o identificador exclusivo para o armazenamento.
- `ExpiresOn`: a hora de expiração do token. A MSAL processa a atualização automática para aplicações.
- `TenantId`: O identificador do inquilino do utilizador utilizado para iniciar sessão. Para os utilizadores convidados (Azure AD B2B), este será o inquilino, o utilizador iniciou sessão com, não respetivo inquilino principal.  
- `Scopes`: os âmbitos que foram concedidos com o seu token. Isto pode ser um subconjunto de que foi solicitada.

Além disso, a MSAL também fornece uma abstração para um `Account`. Uma conta representa o utilizador atual tem sessão iniciada na conta.

- `HomeAccountIdentifier`: O identificador do inquilino principal do utilizador.
- `UserName`: O nome do usuário preferencial. Isto pode estar vazio para utilizadores do Azure AD B2C.
- `AccountIdentifier`: O identificador do utilizador com sessão iniciada. Este será o mesmo que o `HomeAccountIdentifier` na maioria dos casos, a menos que o utilizador é um convidado noutro inquilino.

## <a name="calling-an-api"></a>Chamar uma API

Assim que tiver pronto o token de acesso, é simples chamar uma API web. A aplicação irá levar isso token, construir uma solicitação HTTP e executá-lo.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called graph, process data and send to UI 
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put Access Token in HTTP request 
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

### <a name="ios"></a>iOS

```swift
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)

        // Put Access token in HTTP Request
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in
            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }
            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            // Successfully got data from Graph
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put Access token in HTTP request 
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Tornar vários pedidos de API

Se precisa chamar a mesma API várias vezes ou várias APIs, existem considerações adicionais ao criar a sua aplicação:

- ***Consentimento incremental***: Plataforma de identidade da Microsoft permite que as aplicações obter o consentimento do utilizador como permissão são necessárias em vez de todas as iniciais. Sempre que a aplicação está pronta para chamar uma API, deve pedir a apenas os âmbitos que tenciona utilizar.
- ***Acesso condicional***: Em determinados cenários, pode obter requisitos adicionais de acesso condicional ao fazer várias solicitações da API. Para lidar com este cenário, certifique-se de que detetar erros dos pedidos silenciosas e estar preparado para fazer uma solicitação interativa. Isto pode acontecer se a primeira solicitação tiver não existem políticas de acesso condicional aplicadas e a aplicação tenta aceder automaticamente uma nova API que precisa de acesso condicional. Para obter mais informações, consulte [orientações para o acesso condicional](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-mobile-production.md)
