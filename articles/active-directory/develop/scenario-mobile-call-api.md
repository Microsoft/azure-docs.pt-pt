---
title: Aplicação móvel que chamadas de web APIs - chamar uma API web | Plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação móvel que chama o web APIs (chamar uma API web)
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
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44b6c203583a082228c2ba1f4c5f6fdb04d059be
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962379"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Aplicação móvel que chama a APIs - web chamar uma API web

Depois da aplicação tem um utilizador com sessão iniciada e recebido tokens, MSAL expõe várias partes de informações sobre o utilizador, o ambiente do utilizador e os tokens emitidos. A aplicação pode utilizar estes valores para chamar uma API web ou exibir uma mensagem de boas-vindas ao usuário.

Em primeiro lugar, vamos analisar o resultado MSAL. Em seguida, vamos ver como utilizar um token de acesso a partir da `AuthenticationResult` ou `result` para chamar uma API web protegida.

## <a name="msal-result"></a>Resultado MSAL
A MSAL fornece os seguintes valores: 

- `AccessToken`: Utilizada para chamar as APIs de web protegida numa solicitação de portador de HTTP.
- `IdToken`: Contém informações úteis sobre o utilizador com sessão iniciada, como o nome do utilizador, o inquilino principal e um identificador exclusivo para o armazenamento.
- `ExpiresOn`: A hora de expiração do token. A MSAL processa a atualização automática para aplicações.
- `TenantId`: O identificador do inquilino que o utilizador com sessão iniciado em com. Para os utilizadores convidados (Azure Active Directory B2B), este valor irá identificar o inquilino que o utilizador iniciou sessão com, não do usuário principal de inquilino.  
- `Scopes`: Os âmbitos que foram concedidos com o seu token. Os âmbitos concedidos podem ser um subconjunto dos âmbitos que pediu.

A MSAL também fornece uma abstração para um `Account`. Um `Account` representa a atual conta do utilizador com sessão iniciada.

- `HomeAccountIdentifier`: O identificador do inquilino principal do utilizador.
- `UserName`: O nome do usuário preferencial do usuário. Isto pode estar vazio para os utilizadores do Azure Active Directory B2C.
- `AccountIdentifier`: O identificador do utilizador com sessão iniciada. Este valor será o mesmo que o `HomeAccountIdentifier` valor na maioria dos casos, a menos que o utilizador é um convidado noutro inquilino.

## <a name="call-an-api"></a>Chamar uma API

Depois de ter o token de acesso, é fácil chamar uma API web. A aplicação irá utilizar o token para construir uma solicitação HTTP e, em seguida, execute o pedido.

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

### <a name="ios"></a>iOS

```swift
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)

        // Put access token in HTTP request.
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

            // Successfully got data from Graph.
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put access token in HTTP request.
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Tornar vários pedidos de API

Se precisa chamar a mesma API várias vezes, ou se tiver de chamar as APIs de vários, de ter em consideração o seguinte ao criar a sua aplicação:

- **Consentimento incremental**: Plataforma de identidade da Microsoft permite que as aplicações obter o consentimento do utilizador que são necessárias permissões, em vez de contas no início. Sempre que a aplicação está pronta para chamar uma API, deve pedir a apenas os âmbitos que precisa de utilizar.
- **Acesso condicional**: Em determinados cenários, poderá obter requisitos adicionais de acesso condicional ao fazer várias solicitações da API. Isto pode acontecer se a primeira solicitação tiver não existem políticas de acesso condicional aplicadas e a aplicação tenta aceder automaticamente uma nova API que precisa de acesso condicional. Para lidar com este cenário, certifique-se de que detetar erros dos pedidos silenciosas e estar preparado para fazer uma solicitação interativa.  Para obter mais informações, consulte [orientações para o acesso condicional](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-mobile-production.md)
