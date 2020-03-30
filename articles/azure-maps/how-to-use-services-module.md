---
title: Utilize o módulo Serviços Azure Maps [ Serviços Azure Maps] Microsoft Azure Maps
description: Neste artigo, você aprenderá a utilizar os serviços Microsoft Azure Maps REST usando o módulo de serviços Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: e985fdda4638529e8ade2c700456d595ff355e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988741"
---
# <a name="use-the-azure-maps-services-module"></a>Utilize o módulo de serviços Azure Maps

O Azure Maps Web SDK fornece um módulo de *serviços.* Este módulo é uma biblioteca de ajudantes que facilita a utilização dos serviços Azure Maps REST nas aplicações web ou Node.js utilizando javaScript ou TypeScript.

## <a name="use-the-services-module-in-a-webpage"></a>Utilize o módulo de serviços numa página web

1. Crie um novo ficheiro HTML.
1. Carregue o módulo de serviços Azure Maps. Pode carregá-lo de duas maneiras:
    - Utilize a versão da Rede de Entrega de Conteúdos Azure, a nível global, do módulo de serviços Azure Maps. Adicione uma referência `<head>` de script ao elemento do ficheiro:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - Em alternativa, carregue o módulo de serviços para o código fonte Azure Maps Web SDK localmente, utilizando o pacote npm de [descanso de mapas azuis](https://www.npmjs.com/package/azure-maps-rest) e, em seguida, acolhê-lo com a sua aplicação. Este pacote também inclui definições de TypeScript. Utilize este comando:
    
        > **npm install azure-maps-rest**
    
        Em seguida, adicione uma `<head>` referência de script ao elemento do ficheiro:

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. Criar um oleoduto de autenticação. O gasoduto deve ser criado antes de poder inicializar um ponto final do cliente url de serviço. Utilize a sua própria chave de conta Azure Maps ou credenciais azure Ative Directory (Azure AD) para autenticar um cliente de serviço de pesquisa do Azure Maps. Neste exemplo, será criado o cliente URL do serviço de pesquisa. 

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

    Se utilizar o Azure AD para autenticação:

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

    Para mais informações, consulte [Autenticação com Mapas Azure](azure-maps-authentication.md).

1. O seguinte código utiliza o recém-criado cliente URL do serviço de pesquisa Do Iamaps para geocodificar um endereço: "1 Microsoft Way, Redmond, WA". O código `searchAddress` utiliza a função e exibe os resultados como uma tabela no corpo da página.

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

    Aqui está a amostra completa de código:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Utilização do Módulo de Serviços" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta Utilizando o Módulo<a href='https://codepen.io/azuremaps'>@azuremaps</a>de <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>Serviços</a> por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Apoio à nuvem do Governo de Azure

O Azure Maps Web SDK suporta a nuvem do Governo Azure. Todos os URLs JavaScript e CSS utilizados para aceder ao Azure Maps Web SDK permanecem os mesmos, no entanto, as seguintes tarefas terão de ser feitas para se ligarem à versão cloud do Governo Azure da plataforma Azure Maps.

Ao utilizar o controlo interativo do mapa, adicione a `Map` seguinte linha de código antes de criar uma instância da classe. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Certifique-se de utilizar um azure Maps detalhes de autenticação da plataforma cloud do Governo Azure ao autenticar o mapa e os serviços.

Ao utilizar o módulo de serviços, o domínio dos serviços tem de ser definido ao criar uma instância de um ponto final de URL DaPi. Por exemplo, o seguinte código `SearchURL` cria uma instância da classe e aponta o domínio para a nuvem do Governo Azure.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Se aceder diretamente aos serviços De REPOUSO Do `atlas.azure.us`Azure Maps, altere o domínio URL para . Por exemplo, se utilizar o serviço Depesquisa `https://atlas.microsoft.com/search/` API, altere o domínio URL de . `https://atlas.azure.us/search/`

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [AssinaturaKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-maps-typescript-latest)

Para obter mais amostras de código que utilizem o módulo de serviços, consulte estes artigos:

> [!div class="nextstepaction"]
> [Mostrar resultados de pesquisa no mapa](./map-search-location.md)

> [!div class="nextstepaction"]
> [Obter informações a partir de uma coordenada](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Mostrar as direções de A para B](./map-route.md)
