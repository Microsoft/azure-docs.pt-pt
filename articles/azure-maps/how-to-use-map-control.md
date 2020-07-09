---
title: Começar com o controlo do mapa web Microsoft Azure Maps
description: Saiba como utilizar a biblioteca JavaScript do controlo do mapa do Microsoft Azure Maps para tornar os mapas e incorporar a funcionalidade Azure Maps na sua aplicação web ou móvel.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6becb504671c1fa380207fda9d7d553fca8ceddf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335236"
---
# <a name="use-the-azure-maps-map-control"></a>Utilizar o controlo de mapa do Azure Maps

A biblioteca JavaScript do lado do cliente do Controlo de Mapas permite-lhe renderizar mapas e incorporar a funcionalidade Azure Maps na sua aplicação web ou móvel.

## <a name="create-a-new-map-in-a-web-page"></a>Criar um novo mapa numa página web

Pode incorporar um mapa numa página web utilizando a biblioteca JavaScript do lado do cliente do Controlo de Mapas.

1. Crie um novo ficheiro HTML.

2. Carregue no Azure Maps Web SDK. Pode escolher uma de duas opções;

    * Utilize a versão CDN globalmente hospedada do Azure Maps Web SDK adicionando referências ao JavaScript e à folha de estilo no `<head>` elemento do ficheiro HTML:

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * Carregue o código fonte SDK web Azure Maps localmente usando o pacote NPM [de controlo de mapas azure](https://www.npmjs.com/package/azure-maps-control) e acolhê-lo com a sua aplicação. Este pacote também inclui definições typeScript.

        > **npm instalar azure-maps-control**

       Em seguida, adicione referências à folha de estilos Azure Maps e referências de origem de script ao `<head>` elemento do ficheiro:

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > As definições de escrita podem ser importadas para a sua aplicação adicionando o seguinte código:
    >
    > ```Javascript
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
    <body>
        <div id="myMap"></div>
    </body>
   ```

5. Para inicializar o controlo do mapa, defina uma nova etiqueta de script no corpo html. Passe no `id` mapa `<div>` ou num `HTMLElement` (por exemplo,) `document.getElementById('myMap')` como o primeiro parâmetro ao criar um exemplo da `Map` classe. Utilize a sua própria chave de conta Azure Maps ou credenciais Azure Ative Directory (AAD) para autenticar o mapa utilizando [opções de autenticação.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions) 

   Se precisar de criar uma conta ou encontrar a sua chave, siga as instruções na [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) e obter a chave [principal](quick-demo-map-app.md#get-the-primary-key-for-your-account) . 

   A opção **linguística** especifica o idioma a ser utilizado para etiquetas e controlos de mapas. Para obter mais informações sobre línguas apoiadas, consulte [as línguas apoiadas.](supported-languages.md) Se estiver a utilizar uma chave de subscrição para autenticação, utilize o seguinte:

   ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

   Se estiver a utilizar o Azure Ative Directory (AAD) para autenticação, utilize o seguinte:

   ```HTML
    <script type="text/javascript">
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
        });
    </script>
   ```

   Uma lista de amostras que mostram como integrar o Azure Ative Directory (AAD) com o Azure Maps pode ser consultada [aqui.](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples) 
    
   Para mais informações, consulte o documento [Autenticação com Mapas Azure](azure-maps-authentication.md) e também as [amostras de autenticação AZure AD do Azure Maps.](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)

6. Opcionalmente, poderá encontrar os seguintes elementos de meta-tag na cabeça da sua página útil:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Juntar tudo o que o seu ficheiro HTML deve parecer algo como o seguinte código:

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
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
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

> [!Note]
> Com o Web SDK é possível carregar várias instâncias de mapa na mesma página com diferentes configurações de idioma e região. Além disso, estas definições podem ser atualizadas após as cargas do mapa utilizando a `setStyle` função do mapa. 

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
