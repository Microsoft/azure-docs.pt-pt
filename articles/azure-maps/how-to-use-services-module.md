---
title: Utilizar o módulo de serviços - Azure Maps | Documentos da Microsoft
description: Saiba como utilizar o módulo de serviços do Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 335e114fc6a4afa83a6b82509148cf258a9e8347
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501155"
---
# <a name="using-the-azure-maps-services-module"></a>Utilizar o módulo de serviços de mapas do Azure

O SDK do Azure Maps Web fornece um módulo de serviços que é uma biblioteca de programa auxiliar que torna mais fácil de utilizar os serviços do Azure Maps REST na web ou aplicações de node. js com JavaScript ou TypeScript.

## <a name="using-the-services-module-in-a-web-page"></a>Utilizar o módulo de serviços numa página da web

1. Crie um novo ficheiro HTML.
2. Carregar o módulo de serviços de mapas do Azure. Isso pode ser feito usando uma das duas opções;

    a. Utilizar a versão CDN globalmente hospedada do módulo de serviços do Azure Maps adicionando uma referência de script para o <head> elemento do arquivo:
    
    ```html
    <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2"></script>
    ```
    
    b. Em alternativa, carregar o código de origem do SDK de Web de mapas do Azure localmente, utilizando o [do azure maps-repouso](https://www.npmjs.com/package/azure-maps-rest) NPM empacotar e hospedá-lo com a sua aplicação. Este pacote também inclui definições de TypeScript.
    
    > instalação de npm do azure-maps-rest
    
    Em seguida, adicione um script de referências para o `<head>` elemento do arquivo:
    
    ```html
    <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
    ```

3. Para inicializar um ponto de extremidade do cliente de URL do serviço, tem primeiro de criar um pipeline de autenticação. Utilize a sua própria chave de conta do Azure Maps ou as credenciais do Azure Active Directory (AAD) para autenticar o cliente do serviço de pesquisa. Neste exemplo, o cliente de URL do serviço de pesquisa será criado. Se utilizar uma chave de subscrição para a autenticação:

    ```javascript
    //Get an Azure Maps key at https://azure.com/maps
    var subscriptionKey = '<Your Azure Maps Key>';
    
    //Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);
    
    //Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```
    
    Se utilizar o Azure Active Directory (AAD) para autenticação:

    ```javascript
    // Enter your Azure Actiuve Directory client ID.
    var clientId = "<Your Azure Active Directory Client Id>";
    
    // Use TokenCredential with OAuth token (AAD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);
    
    // Create a repeating timeout that will renew the AAD token.
    // This timeout must be cleared once the TokenCredential object is no longer needed.
    // If the timeout is not cleared the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
        try {
            console.log("Renewing token");
            var token = await getAadToken();
            tokenCredential.token = token;
            tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
        } catch (error) {
            console.log("Caught error when renewing token");
            clearTimeout(tokenRenewalTimer);
            throw error;
        }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));
    
    // Use tokenCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
        retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
        //Use the logged in auth context to get a token.
        return new Promise((resolve, reject) => {
            //The resource should always be https://atlas.microsoft.com/.
            const resource = "https://atlas.microsoft.com/";
            authContext.acquireToken(resource, (error, token) => {
                if (error) {
                    reject(error);
                } else {
                    resolve(token);
                }
            });
        })
    }

    function getExpiration(jwtToken) {
        //Decode the JWT token to get the expiration timestamp.
        const json = atob(jwtToken.split(".")[1]);
        const decode = JSON.parse(json);

        //Return the milliseconds until the token needs renewed.
        //Reduce the time until renew by 5 minutes to avoid using an expired token.
        //The exp property is the timestamp of the expiration in seconds.
        const renewSkew = 300000;
        return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Para obter mais informações, consulte [autenticação com o Azure Maps](azure-maps-authentication.md).

4. O código seguinte utiliza o cliente de URL do serviço de pesquisa recentemente criado para geocode um endereço, "Microsoft 1 forma, Redmond, WA" usando o `searchAddress` de função e exibir os resultados como uma tabela no corpo da página. 

    ```javascript
    //Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa').then(response => {
      var html = [];
      
      //Display the total results.
      html.push('Total results: ', response.summary.numResults, '<br/><br/>');
     
      //Create a table of the results.
      html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');
      
      for(var i=0;i<response.results.length;i++){
        html.push('<tr><td>', (i+1), '.</td><td>', 
                  response.results[i].address.freeformAddress, 
                  '</td><td>', 
                  response.results[i].position.lat,
                  '</td><td>', 
                  response.results[i].position.lon,
                  '</td></tr>');
      }
      
      html.push('</table>');
      
      //Add the result HTML to the body of the page.
      document.body.innerHTML = html.join('');
    });
    ```

    Eis o código de exemplo totalmente em execução:

<iframe height="500" style="width: 100%;" scrolling="no" title="Utilizar o módulo de serviços" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>utilizando o módulo de serviços</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-iot-typescript-latest)

Veja os artigos seguintes para obter mais exemplos de código que utilizam o módulo de serviços:

> [!div class="nextstepaction"]
> [Mostrar resultados da pesquisa no mapa](./map-search-location.md)

> [!div class="nextstepaction"]
> [Obtenha informações a partir de uma coordenada](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Mostrar as direções da para B](./map-route.md)