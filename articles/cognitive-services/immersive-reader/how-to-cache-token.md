---
title: Armazenar o token de autenticação em cache
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como armazenar em cache o token de autenticação.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946168"
---
# <a name="how-to-cache-the-authentication-token"></a>Como armazenar em cache o token de autenticação

Este artigo demonstra como armazenar em cache o token de autenticação a fim de melhorar o desempenho do seu aplicativo.

## <a name="using-aspnet"></a>Usando ASP.NET

Importe o pacote NuGet **Microsoft. IdentityModel. clients. ActiveDirectory** , que é usado para adquirir um token. Em seguida, use o código a seguir para adquirir um `AuthenticationResult`, usando os valores de autenticação que você obteve ao [criar o recurso de leitura de imersão](./how-to-create-immersive-reader.md).

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

O objeto `AuthenticationResult` tem uma propriedade `AccessToken`, que é o token real que você usará ao iniciar o leitor de imersão usando o SDK. Ele também tem uma propriedade `ExpiresOn`, que denota quando o token expirará. Antes de iniciar o leitor de imersão, você pode verificar se o token expirou e adquirir um novo token somente se ele tiver expirado.

## <a name="using-nodejs"></a>Usando o Node. JS

Adicione o pacote [**Request**](https://www.npmjs.com/package/request) NPM ao seu projeto. Use o código a seguir para adquirir um token, usando os valores de autenticação que você obteve ao [criar o recurso de leitor de imersão](./how-to-create-immersive-reader.md).

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

A propriedade `expires_on` é a data e a hora em que o token expira, expresso como o número de segundos desde 1º de janeiro de 1970 UTC. Use esse valor para determinar se o token expirou antes de tentar adquirir um novo.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Passos seguintes

* Explore a [referência do SDK do leitor de imersão](./reference.md)