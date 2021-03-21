---
title: Utilize o módulo Azure Maps Services | Microsoft Azure Maps
description: Conheça o módulo de serviços Azure Maps. Veja como carregar e usar esta biblioteca auxiliar para aceder aos serviços Azure Maps REST em aplicações web ou Node.js.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: devx-track-js
ms.openlocfilehash: 2e07b614e87ed5dad94cf9bc5994e78071187839
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008604"
---
# <a name="use-the-azure-maps-services-module"></a>Utilize o módulo de serviços Azure Maps

O Azure Maps Web SDK fornece um *módulo de serviços.* Este módulo é uma biblioteca auxiliar que facilita a utilização dos serviços Azure Maps REST em aplicações web ou Node.js utilizando JavaScript ou TypeScript.

## <a name="use-the-services-module-in-a-webpage"></a>Utilize o módulo de serviços numa página web

1. Crie um novo ficheiro HTML.
1. Carregue o módulo de serviços Azure Maps. Pode carregá-lo de duas maneiras:
    - Utilize a versão globalmente hospedada da Rede de Entrega de Conteúdos Azure do módulo de serviços Azure Maps. Adicione uma referência de script ao `<head>` elemento do ficheiro:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - Em alternativa, carregue o módulo de serviços para o código fonte SDK web Azure Maps localmente, utilizando o pacote npm de [repouso azure-maps-rest](https://www.npmjs.com/package/azure-maps-rest) e, em seguida, hospede-o com a sua aplicação. Este pacote também inclui definições typeScript. Utilize este comando:
    
        > **npm install azure-maps-rest**
    
        Em seguida, adicione uma referência de script ao `<head>` elemento do ficheiro:

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. Criar um gasoduto de autenticação. O gasoduto deve ser criado antes de poder inicializar um ponto final do cliente URL de serviço. Utilize a sua própria chave de conta Azure Maps ou credenciais Azure Ative Directory (Azure AD) para autenticar um cliente de serviço de pesquisa Azure Maps. Neste exemplo, será criado o cliente URL do serviço de pesquisa. 

    Se utilizar uma chave de subscrição para autenticação:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Se utilizar a Azure AD para autenticação:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
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

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
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
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Para mais informações, consulte [autenticação com mapas Azure.](azure-maps-authentication.md)

1. O seguinte código utiliza o recém-criado cliente URL do serviço de pesquisa Azure Maps para geocodificar um endereço: "1 Microsoft Way, Redmond, WA". O código utiliza a `searchAddress` função e apresenta os resultados como tabela no corpo da página.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
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

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    Aqui está a amostra completa do código de execução:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Utilização do Módulo de Serviços" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>Utilizando o Módulo de Serviços</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Apoio à nuvem do Governo de Azure

O Azure Maps Web SDK suporta a nuvem do Governo Azure. Todos os URLs JavaScript e CSS utilizados para aceder ao Azure Maps Web SDK permanecem os mesmos, no entanto, as seguintes tarefas terão de ser feitas para se ligarem à versão em nuvem do Governo Azure da plataforma Azure Maps.

Quando utilizar o controlo interativo do mapa, adicione a seguinte linha de código antes de criar uma instância da `Map` classe. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Certifique-se de que utiliza os detalhes de autenticação do Azure Maps a partir da plataforma cloud do Governo Azure ao autenticar o mapa e os serviços.

Ao utilizar o módulo de serviços, o domínio dos serviços tem de ser definido ao criar uma instância de um ponto final de URL API. Por exemplo, o seguinte código cria uma instância da `SearchURL` classe e aponta o domínio para a nuvem do Governo Azure.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Se aceder diretamente aos serviços Azure Maps REST, altere o domínio URL para `atlas.azure.us` . Por exemplo, se utilizar o serviço de API de pesquisa, altere o domínio URL de `https://atlas.microsoft.com/search/` `https://atlas.azure.us/search/` .

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [MapsURL](/javascript/api/azure-maps-rest/atlas.service.mapsurl)

> [!div class="nextstepaction"]
> [SearchURL](/javascript/api/azure-maps-rest/atlas.service.searchurl)

> [!div class="nextstepaction"]
> [RouteURL](/javascript/api/azure-maps-rest/atlas.service.routeurl)

> [!div class="nextstepaction"]
> [SubscriçãoKeyCredential](/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential)

> [!div class="nextstepaction"]
> [TokenCredential](/javascript/api/azure-maps-rest/atlas.service.tokencredential)

Para obter mais amostras de código que utilizem o módulo de serviços, consulte estes artigos:

> [!div class="nextstepaction"]
> [Mostrar os resultados da pesquisa no mapa](./map-search-location.md)

> [!div class="nextstepaction"]
> [Obter informações a partir de uma coordenada](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Mostrar as direções de A para B](./map-route.md)