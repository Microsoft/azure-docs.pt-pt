---
title: Começar com o controlo do mapa web do Microsoft Azure Maps
description: Descubra como adicionar mapas a aplicações web e móveis utilizando a biblioteca JavaScript do lado do cliente do Controlo de Mapas no Azure Maps. Veja como localizar mapas.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 19db4fb0d75369a0f272eef1180e86f47d45d284
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335335"
---
# <a name="use-the-azure-maps-map-control"></a>Utilizar o controlo de mapa do Azure Maps

A biblioteca JavaScript do lado do cliente do Controlo de Mapas permite-lhe renderizar mapas e incorporar a funcionalidade Azure Maps na sua aplicação web ou móvel.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o Controlo do Mapa numa página web, deve ter um dos seguintes pré-requisitos:

* [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account) e [obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição.

* Obtenha as suas credenciais Azure Ative (AAD) com [opções de autenticação.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)

## <a name="create-a-new-map-in-a-web-page"></a>Criar um novo mapa numa página web

Pode incorporar um mapa numa página web utilizando a biblioteca JavaScript do lado do cliente do Controlo de Mapas.

1. Crie um novo ficheiro HTML.

2. Carregue no Azure Maps Web SDK. Pode escolher uma de duas opções:

    * Utilize a versão CDN globalmente hospedada do Azure Maps Web SDK adicionando referências ao JavaScript e à folha de estilo no `<head>` elemento do ficheiro HTML:

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      ```

    * Carregue o código fonte SDK web Azure Maps localmente usando o pacote NPM [de controlo de mapas azure](https://www.npmjs.com/package/azure-maps-control) e acolhê-lo com a sua aplicação. Este pacote também inclui definições typeScript.

      > **npm instalar azure-maps-control**

    Em seguida, adicione referências à folha de estilo Azure Maps ao `<head>` elemento do ficheiro:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
     ```

    > [!NOTE]
    > As definições de escrita podem ser importadas para a sua aplicação adicionando o seguinte código:
    >
    > ```javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Para tornar o mapa de modo a preencher todo o corpo da página, adicione o `<style>` seguinte elemento ao `<head>` elemento.

   ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
   ```

4. No corpo da página, adicione um `<div>` elemento e dê-lhe um `id` dos meus **Mapas.**

   ```HTML
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
   ```

5. Agora, vamos inicializar o controlo do mapa. Para autenticar o controlo, você terá de possuir uma chave de subscrição Azure Maps ou usar credenciais Azure Ative Directory (AAD) com [opções de autenticação](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions).

    Se estiver a utilizar uma chave de subscrição para autenticação, copie e cole o seguinte elemento de script dentro do `<head>` elemento e abaixo do primeiro `<script>` elemento. Substitua `<Your Azure Maps Key>` a sua chave de subscrição primária do Azure Maps.

     ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            }
        });
    </script>
    ```

    Se estiver a utilizar o Azure Ative Directory (AAD) para autenticação, copie e cole o seguinte elemento de script dentro do `<head>` elemento e abaixo do primeiro `<script>` elemento.

      ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'aad',
                    clientId: '<Your AAD Client Id>',
                    aadAppId: '<Your AAD App Id>',
                    aadTenant: '<Your AAD Tenant Id>'
                }
            }
        });
    </script>
   ```

    Para obter mais informações sobre a autenticação com o Azure Maps, consulte o documento [Autenticação com Mapas Azure.](azure-maps-authentication.md) Além disso, uma lista de amostras que mostram como integrar o Azure Ative Directory (AAD) com a Azure Maps pode ser consultada [aqui.](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)

    >[!TIP]
    >Neste exemplo, passamos no `id` `<div>` mapa. Outra forma de fazer isso é passar no `HTMLElement` objeto passando `document.getElementById('myMap')` como o primeiro parâmetro.

6. Opcionalmente, poderá achar útil adicionar os `meta` seguintes elementos ao `head` elemento da página:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Juntando tudo, o seu ficheiro HTML deve parecer algo como a seguinte marcação:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>


        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            function InitMap()
            {
                var map = new atlas.Map('myMap', {
                    center: [-122.33, 47.6],
                    zoom: 12,
                    language: 'en-US',
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });
            }
        </script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

8. Abra o ficheiro no seu navegador web e veja o mapa renderizado. Deve parecer a imagem abaixo:

   ![Imagem de mapa mostrando resultado renderizado](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Localização do mapa

O Azure Maps fornece duas maneiras diferentes de definir a língua e a visão regional para o mapa renderizado. A primeira opção é adicionar esta informação ao espaço de nome global, o `atlas` que resultará em todas as instâncias de controlo de mapas na sua aplicação por defeito nestas definições. O seguinte define a língua para o francês ("fr-FR") e a visão regional para "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

A segunda opção é passar esta informação para as opções do mapa ao carregar o mapa assim:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!NOTE]
> É possível carregar várias instâncias de mapa na mesma página com diferentes configurações de linguagem e região. Além disso, estas definições podem ser atualizadas após as cargas do mapa utilizando a `setStyle` função do mapa.

Aqui está um exemplo de Azure Maps com a linguagem definida para "fr-FR" e a visão regional definida para "Auto".

![Imagem de mapa mostrando rótulos em francês](./media/how-to-use-map-control/websdk-localization.png)

Está aqui documentada [uma](supported-languages.md)lista completa das línguas apoiadas e dos pontos de vista regionais.

## <a name="azure-government-cloud-support"></a>Apoio à nuvem do Governo de Azure

O Azure Maps Web SDK suporta a nuvem do Governo Azure. Todos os URLs JavaScript e CSS utilizados para aceder ao Azure Maps Web SDK permanecem os mesmos. As seguintes tarefas terão de ser feitas para se ligar à versão em nuvem do Governo Azure da plataforma Azure Maps.

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

## <a name="javascript-frameworks"></a>Estruturas JavaScript

Se desenvolver usando uma estrutura JavaScript, um dos seguintes projetos de código aberto pode ser útil:

- [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps) - Angular 10 wrapper em torno de mapas Azure.
- [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) - Um componente Blazor Azure Maps.
- [Componente de reação do Azure Maps](https://github.com/WiredSolutions/react-azure-maps) - Um invólucro de reação para o controlo Azure Maps.
- [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) - Um componente Azure Maps para aplicação Vue.

## <a name="next-steps"></a>Passos seguintes

Saiba como criar e interagir com um mapa:

> [!div class="nextstepaction"]
> [Criar um mapa](map-create.md)

Saiba como modelar um mapa:

> [!div class="nextstepaction"]
> [Escolher um estilo de mapa](choose-map-style.md)

Para adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Criar um mapa](map-create.md)

> [!div class="nextstepaction"]
> [Amostras de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)

Para obter uma lista de amostras que mostram como integrar o Azure Ative Directory (AAD) com o Azure Maps, consulte:

> [!div class="nextstepaction"]
> [Amostras de autenticação AD AZure](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
