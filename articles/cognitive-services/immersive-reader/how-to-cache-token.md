---
title: Colocar o token de autenticação em cache
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como cache o token de autenticação.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 01/14/2020
ms.author: metang
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: e79ae3914e32038e2823fb37e3eee658c95e0003
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608772"
---
# <a name="how-to-cache-the-authentication-token"></a>Como cache o símbolo de autenticação

Este artigo demonstra como cache o token de autenticação de forma a melhorar o desempenho da sua aplicação.

## <a name="using-aspnet"></a>Utilização de ASP.NET

Importe o pacote **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet, que é usado para adquirir um token. Em seguida, utilize o seguinte código para adquirir um `AuthenticationResult` , utilizando os valores de autenticação que obteve quando [criou o recurso Leitor Imersivo](./how-to-create-immersive-reader.md).

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

O `AuthenticationResult` objeto tem uma propriedade que é o símbolo real que você `AccessToken` usará ao lançar o Leitor Imersivo usando o SDK. Também tem uma `ExpiresOn` propriedade que denota quando o token expirará. Antes de lançar o Leitor Imersivo, pode verificar se o token expirou e adquirir um novo token apenas se tiver expirado.

## <a name="using-nodejs"></a>Usando Node.JS

Adicione o pacote de [**pedido**](https://www.npmjs.com/package/request) npm ao seu projeto. Utilize o seguinte código para adquirir um token, utilizando os valores de autenticação que obteve quando [criou o recurso Leitor Imersivo](./how-to-create-immersive-reader.md).

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

A `expires_on` propriedade é a data e hora em que o token expira, expresso como o número de segundos desde 1 de janeiro de 1970 UTC. Use este valor para determinar se o seu token expirou antes de tentar adquirir um novo.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Passos seguintes

* Explore a [referência SDK do leitor imersivo](./reference.md)