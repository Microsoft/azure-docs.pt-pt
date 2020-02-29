---
title: Começar com o controlo do mapa web / Microsoft Azure Maps
description: Saiba como utilizar a biblioteca JavaScript do lado do cliente do Microsoft Azure Maps para renderizar mapas e incorporar a funcionalidade Do Azure Maps na sua aplicação web ou móvel.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9bcb0fd26710b5f44ca9e3e3715c40cb32b3c40d
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913945"
---
# <a name="use-the-azure-maps-map-control"></a>Utilize o controlo do mapa do Mapa do Mapa do Mapas Azure

A biblioteca JavaScript do lado do cliente do Map Control permite-lhe renderizar mapas e incorporar a funcionalidade Do Azure Maps na sua aplicação web ou móvel.

## <a name="create-a-new-map-in-a-web-page"></a>Crie um novo mapa numa página web

Pode incorporar um mapa numa página web utilizando a biblioteca JavaScript do lado do cliente do Map Control.

1. Crie um novo ficheiro HTML.

2. Carregue no Azure Maps Web SDK. Pode escolher uma de duas opções;

   1. Utilize a versão CDN globalmente hospedada do Azure Maps Web SDK adicionando os pontos finais url à folha de estilo e referências de script no elemento `<head>` do ficheiro:

      ```HTML
       <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
       <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      ```

   1. Carregue o código fonte Azure Maps Web SDK localmente utilizando o pacote NPM [de controlo de mapas azuis](https://www.npmjs.com/package/azure-maps-control) e apresente-o com a sua aplicação. Este pacote também inclui definições de TypeScript.

      > **npm instalar azure-maps-control**

   Em seguida, adicione referências à folha de estilo do Azure Maps e referências de origem de script ao elemento `<head>` do ficheiro:

      ```HTML
       <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
       <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
      ```

    > [!Note]
    > As definições de tipografia podem ser importadas para a sua aplicação adicionando o seguinte código:
    >
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Para renderizar o mapa de modo a preencher todo o corpo da página, adicione o seguinte elemento `<style>` ao elemento `<head>`.

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

4. No corpo da página, adicione um elemento `<div>` e dê-lhe um `id` do **myMap**.

   ```HTML
    <body>
        <div id="myMap"></div>
    </body>
   ```

5. Para inicializar o controlo do mapa, defina uma nova etiqueta de script no corpo html. Passe no `id` do mapa `<div>` ou um `HTMLElement` (por exemplo, `document.getElementById('myMap')`) como o primeiro parâmetro ao criar uma instância da classe `Map`. Utilize a sua própria chave de conta Azure Maps ou credenciais de Diretório Ativo Azure (AAD) para autenticar o mapa utilizando opções de [autenticação](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). 

   Se precisar de criar uma conta ou encontrar a sua chave, siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) e obter a chave [primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) . 

   A opção **linguística** especifica o idioma a utilizar para etiquetas e controlos de mapas. Para mais informações sobre línguas apoiadas, consulte [as línguas apoiadas.](supported-languages.md) Se estiver a utilizar uma chave de subscrição para autenticação, utilize o seguinte:

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

   Se estiver a utilizar o Diretório Ativo Azure (AAD) para autenticação, utilize o seguinte:

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

   Uma lista de amostras que mostram como integrar o Azure Ative Directory (AAD) com o Azure Maps pode ser consultada [aqui](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples). 
    
   Para mais informações, consulte a Autenticação com o documento [Azure Maps](azure-maps-authentication.md) e também as amostras de [autenticação aditiva Azure Maps Azure](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

6. Opcionalmente, pode encontrar a adição dos seguintes elementos de meta tag à cabeça da sua página útil:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Juntar tudo o que o seu ficheiro HTML deve parecer algo parecido com o seguinte código:

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

O Azure Maps fornece duas formas diferentes de definir a linguagem e a vista regional para o mapa renderizado. A primeira opção é adicionar esta informação ao espaço de nome solado global `atlas`, o que resultará em todas as instâncias de controlo de mapas na sua aplicação que não se adequem a estas definições. O seguinte define a língua para francês ("fr-FR") e a visão regional para "Auto":

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
> Com o Web SDK é possível carregar várias instâncias de mapas na mesma página com diferentes configurações de idioma e região. Além disso, estas definições podem ser atualizadas após as cargas do mapa utilizando a função `setStyle` do mapa. 

Aqui está um exemplo de Azure Maps com a linguagem definida para "fr-FR" e a vista regional definida para "Auto".

![Imagem de mapa mostrando rótulos em francês](./media/how-to-use-map-control/websdk-localization.png)

Uma lista completa de línguas apoiadas e pontos de vista regionais é documentada [aqui.](supported-languages.md)

## <a name="azure-government-cloud-support"></a>Apoio à nuvem do Governo de Azure

O Azure Maps Web SDK suporta a nuvem do Governo Azure. Todos os URLs JavaScript e CSS usados para aceder ao Azure Maps Web SDK permanecem os mesmos. As seguintes tarefas terão de ser feitas para se ligarà versão cloud do Governo Azure da plataforma Azure Maps.

Ao utilizar o controlo interativo do mapa, adicione a seguinte linha de código antes de criar uma instância da classe `Map`. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Certifique-se de que utiliza detalhes de autenticação do Azure Maps da plataforma cloud do Governo Azure ao autenticar o mapa e os serviços.

Ao utilizar o módulo de serviços, o domínio dos serviços tem de ser definido ao criar uma instância de um ponto final de URL DaPi. Por exemplo, o seguinte código cria uma instância da classe `SearchURL` e aponta o domínio para a nuvem do Governo Azure.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Se aceder diretamente aos serviços De REPOUSO Do Azure Maps, altere o domínio URL para `atlas.azure.us`. Por exemplo, se utilizar o serviço Depesquisa API, mude o domínio URL de `https://atlas.microsoft.com/search/` para `https://atlas.azure.us/search/`.

## <a name="next-steps"></a>Passos seguintes

Aprenda a criar e interagir com um mapa:

> [!div class="nextstepaction"]
> [Criar um mapa](map-create.md)

Saiba como modelar um mapa:

> [!div class="nextstepaction"]
> [Escolha um estilo de mapa](choose-map-style.md)

Para adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Criar um mapa](map-create.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)

Para obter uma lista de amostras que mostrem como integrar o Azure Ative Directory (AAD) com o Azure Maps, consulte:

> [!div class="nextstepaction"]
> [Amostras de autenticação da AD Azure](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
