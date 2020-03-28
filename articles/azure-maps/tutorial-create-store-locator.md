---
title: 'Tutorial: Criar uma aplicação de localização de loja utilizando o Azure Maps Microsoft Azure Maps'
description: Neste tutorial, você vai aprender a criar uma aplicação web de localização de loja usando o Microsoft Azure Maps web SDK.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 49b57b213a452d6c594bbc1ca537e68bd7a83864
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333847"
---
# <a name="tutorial-create-a-store-locator-by-using-azure-maps"></a>Tutorial: Criar um localizador de loja utilizando o Azure Maps

Este tutorial guia-o através do processo de criação de um simples localizador de loja utilizando o Azure Maps. Os localizadores de loja são comuns. Muitos dos conceitos que são utilizados neste tipo de aplicações aplicam-se a muitos outros tipos de aplicações. Oferecer um localizador de loja aos clientes é uma obrigação para a maioria das empresas que vendem diretamente aos consumidores. Neste tutorial, ficará a saber como:
    
> [!div class="checklist"]
> * Crie uma nova página web utilizando a API de Controlo de Mapas Azure.
> * Carregue os dados personalizados de um ficheiro e exiba-os num mapa.
> * Utilize o serviço de pesquisa do Azure Maps para encontrar um endereço ou introduzir uma consulta.
> * Obtenha a localização do utilizador do navegador e mostre-a no mapa.
> * Combine várias camadas para criar símbolos personalizados no mapa.  
> * Pontos de dados do cluster.  
> * Adicione controlos de zoom ao mapa.

<a id="Intro"></a>

Salte para o exemplo do [localizador da loja ao vivo](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) ou código [fonte](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator). 

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste tutorial, primeiro é necessário criar uma conta Azure Maps e obter a sua chave principal (chave de subscrição). Siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma subscrição de conta Azure Maps com o nível de preços S1 e siga os passos para obter a [chave](quick-demo-map-app.md#get-the-primary-key-for-your-account) principal para obter a chave principal para a sua conta. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps](how-to-manage-authentication.md).

## <a name="design"></a>Design

Antes de entrar no código, é uma boa ideia começar com um desenho. O seu localizador de loja pode ser tão simples ou complexo como quiser. Neste tutorial, criamos um simples localizador de loja. Incluímos algumas dicas ao longo do caminho para ajudá-lo a estender algumas funcionalidades se assim o desejar. Criamos um localizador de loja para uma empresa fictícia chamada Contoso Coffee. A figura que se segue mostra uma estrutura de arame do layout geral do localizador de loja que construímos neste tutorial:

<center>

![Arame de uma aplicação de localizador de loja para locais da Loja Contoso](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)</center>

Para maximizar a utilidade deste localizador de loja, incluímos um layout responsivo que se ajusta quando a largura do ecrã de um utilizador é inferior a 700 pixels de largura. Um layout responsivo facilita a utilização do localizador da loja num pequeno ecrã, como num dispositivo móvel. Aqui está uma moldura de arame de um layout de pequeno ecrã:  

<center>

![Wireframe da aplicação do localizador de loja de café Contoso num dispositivo móvel](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</center>

As molduras de arame mostram uma aplicação bastante simples. A aplicação tem uma caixa de pesquisa, uma lista de lojas próximas, e um mapa que tem alguns marcadores, como símbolos. E tem uma janela pop-up que exibe informações adicionais quando o utilizador seleciona um marcador. Com mais detalhes, aqui estão as características que construímos neste localizador de loja neste tutorial:

* Todas as localizações do ficheiro de dados delimitado por separadores importados são carregadas no mapa.
* O utilizador pode fazer pan e zoom no mapa, realizar uma pesquisa e selecionar o botão My Location GPS.
* O layout da página ajusta-se com base na largura do ecrã do dispositivo.  
* Um cabeçalho mostra o logotipo da loja.  
* O utilizador pode utilizar uma caixa de pesquisa e um botão de pesquisa para procurar um local, como um endereço, código postal ou cidade. 
* Um `keypress` evento adicionado à caixa de pesquisa desencadeia uma pesquisa se o utilizador premir O Enter. Esta funcionalidade é muitas vezes negligenciada, mas cria uma melhor experiência do utilizador.
* Quando o mapa se move, a distância até cada local a partir do centro do mapa é calculada. A lista de resultados é atualizada para exibir os locais mais próximos na parte superior do mapa.  
* Quando seleciona um resultado na lista de resultados, o mapa está centrado na localização selecionada e as informações sobre a localização aparecem numa janela pop-up.  
* A seleção de uma localização específica no mapa também despoleta uma janela pop-up.
* Quando o utilizador faz zoom, as localizações são agrupadas em clusters. Os aglomerados são representados por um círculo com um número dentro do círculo. Os clusters formam-se e separam-se à medida que o utilizador altera o nível de zoom.
* Selecionando um cluster zooms no mapa dois níveis e centros sobre a localização do cluster.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Criar o conjunto de dados de localização da loja

Antes de desenvolvermos uma aplicação de localização de loja, precisamos de criar um conjunto de dados das lojas que queremos exibir no mapa. Neste tutorial, usamos um conjunto de dados para um café fictício chamado Café Contoso. O conjunto de dados para este simples localizador de loja é gerido num livro do Excel. O conjunto de dados contém 10.213 cafés Contoso, distribuídos por nove países/regiões: Estados Unidos, Canadá, Reino Unido, França, Alemanha, Itália, Países Baixos, Dinamarca e Espanha. Aqui está uma imagem do que os dados parecem:

<center>

![Screenshot dos dados do localizador da loja em um livro excel](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)</center>

Pode [baixar o livro excel.](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data) 

Olhando para a imagem dos dados, podemos fazer as seguintes observações:
    
* As informações de localização são armazenadas utilizando as colunas **AddressLine**, **City**, **Município** (concelho), **AdminDivision** (estado/província), **Código Postal** (código postal) e **Colunas Country.**  
* As colunas **Latitude** e **Longitude** contêm as coordenadas para cada local de café Contoso Coffee. Se não tiver informações coordenadas, pode utilizar os serviços de pesquisa no Azure Maps para determinar as coordenadas de localização.
* Algumas colunas adicionais contêm metadados relacionados com os cafés: um número de telefone, colunas Boolean, e horários de abertura e fecho de loja em formato 24 horas. As colunas Boolean são para Wi-Fi e acessibilidade em cadeira de rodas. Pode criar as suas próprias colunas que contenham metadados mais relevantes para os seus dados de localização.

> [!Note]
> O Azure Maps dá dados na projeção esférica do Mercator "EPSG:3857" mas lê os dados do "EPSG:4325" que utilizam o datum WGS84. 

Existem muitas formas de expor o conjunto de dados à aplicação. Uma abordagem é colocar os dados numa base de dados e expor um serviço web que questiona os dados. Em seguida, pode enviar os resultados para o navegador do utilizador. Esta opção é ideal para grandes conjuntos de dados ou para conjuntos de dados que são atualizados frequentemente. No entanto, esta opção requer mais trabalho de desenvolvimento e tem um custo mais elevado. 

Outra abordagem é converter este conjunto de dados num ficheiro de texto plano que o navegador pode facilmente analisar. O ficheiro em si pode ser hospedado com o resto da aplicação. Esta opção mantém as coisas simples, mas é uma boa opção apenas para conjuntos de dados mais pequenos porque o utilizador descarrega todos os dados. Utilizamos o ficheiro de texto plano para este conjunto de dados porque o tamanho do ficheiro de dados é inferior a 1 MB.  

Para converter o livro num ficheiro de texto plano, guarde o livro como ficheiro delimitado por separadores. Cada coluna é delimitada por um caracteres de separador, o que torna as colunas fáceis de analisar no nosso código. Pode utilizar o formato de valor separado da vírce (CSV), mas essa opção requer mais lógica de análise. Qualquer campo que tenha uma vírem à volta seria embrulhado com aspas. Para exportar estes dados como um ficheiro delimitado por separadores no Excel, selecione **Save As**. Na lista **de reparação do tipo,** selecione **Texto (Separador delimitado)(*.txt)**. Nomeie o ficheiro *ContosoCoffee.txt*. 

<center>

![Screenshot do Save como caixa de diálogo tipo](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)</center>

Se abrir o ficheiro de texto no Bloco de Notas, parece semelhante ao seguinte figura:

<center>

![Screenshot de um ficheiro DoBloco que mostra um conjunto de dados delimitado por separador](./media/tutorial-create-store-locator/StoreDataTabFile.png)</center>


## <a name="set-up-the-project"></a>Configurar o projeto

Para criar o projeto, pode utilizar o [Visual Studio](https://visualstudio.microsoft.com) ou o editor de código à sua escolha. Na pasta do projeto, crie três ficheiros: *index.html,* *index.css*, e *index.js*. Estes ficheiros definem o layout, estilo e lógica para a aplicação. Crie uma pasta com *dados* nomeados e adicione *ContosoCoffee.txt* à pasta. Crie outra pasta com *imagens.* Utilizamos 10 imagens nesta aplicação para ícones, botões e marcadores no mapa. Pode [descarregar estas imagens.](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data) A pasta do projeto deve agora parecer a seguinte figura:

<center>

![Screenshot da pasta do projeto do localizador de loja simples](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)</center>

## <a name="create-the-user-interface"></a>Criar a interface de utilizador

Para criar a interface do utilizador, adicione código para *indexar.html:*

1. Adicione as `meta` seguintes `head` etiquetas ao *índice.html*. A `charset` etiqueta define o conjunto de caracteres (UTF-8). O valor `http-equiv` diz ao Internet Explorer e microsoft Edge para usar as versões mais recentes do navegador. E, a `meta` última etiqueta especifica um viewport que funciona bem para layouts responsivos.

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. Adicione referências aos ficheiros JavaScript e CSS de controlo web do Azure Maps:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

1. Adicione uma referência ao módulo Deserviços Azure Maps. O módulo é uma biblioteca JavaScript que envolve os serviços De REST do Azure Maps e os facilita a sua utilização no JavaScript. O módulo é útil para alimentar a funcionalidade de pesquisa.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

1. Adicione referências ao *index.js* e *index.css:*

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. No corpo do documento, `header` adicione uma etiqueta. Dentro `header` da etiqueta, adicione o logótipo e o nome da empresa.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. Adicione `main` uma etiqueta e crie um painel de pesquisa que tenha uma caixa de texto e um botão de pesquisa. Além `div` disso, adicione referências para o mapa, o painel de listas e o botão DE GPS my location.

    ```HTML
    <main>
        <div class="searchPanel">
            <div>
                <input id="searchTbx" type="search" placeholder="Find a store" />
                <button id="searchBtn" title="Search"></button>
            </div>
        </div>
        <div id="listPanel"></div>
        <div id="myMap"></div>
        <button id="myLocationBtn" title="My Location"></button>
    </main>
    ```

Quando terminar, *o index.html* deve parecer [este exemplo index.html ficheiro](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html).

O próximo passo é definir os estilos CSS. Os estilos CSS definem como os componentes da aplicação são definidos e a aparência da aplicação. Abra *o index.css* e adicione-lhe o seguinte código. O `@media` estilo define opções de estilo alternativas para usar quando a largura do ecrã é inferior a 700 pixels.  

   ```CSS
    html, body {
        padding: 0;
        margin: 0;
        font-family: Gotham, Helvetica, sans-serif;
        overflow-x: hidden;
    }

    header {
        width: calc(100vw - 10px);
        height: 30px;
        padding: 15px 0 20px 20px;
        font-size: 25px;
        font-style: italic;
        font-family: "Comic Sans MS", cursive, sans-serif;
        line-height: 30px;
        font-weight: bold;
        color: white;
        background-color: #007faa;
    }

    header span {
        vertical-align: middle;
    }

    header img {
        height: 30px;
        vertical-align: middle;
    }

    .searchPanel {
        position: relative;
        width: 350px;
    }

    .searchPanel div {
        padding: 20px;
    }

    .searchPanel input {
        width: calc(100% - 50px);
        font-size: 16px;
        border: 0;
        border-bottom: 1px solid #ccc;
    }

    #listPanel {
        position: absolute;
        top: 135px;
        left: 0px;
        width: 350px;
        height: calc(100vh - 135px);
        overflow-y: auto;
    }

    #myMap { 
        position: absolute;
        top: 65px;
        left: 350px;
        width: calc(100vw - 350px);
        height: calc(100vh - 65px);
    }

    .statusMessage {
        margin: 10px;
    }

    #myLocationBtn, #searchBtn {
        margin: 0;
        padding: 0;
        border: none;
        border-collapse: collapse;
        width: 32px;
        height: 32px; 
        text-align: center;
        cursor: pointer;
        line-height: 32px;
        background-repeat: no-repeat;
        background-size: 20px;
        background-position: center center;
        z-index: 200;
    }

    #myLocationBtn {
        position: absolute;
        top: 150px;
        right: 10px;
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16);
        background-color: white;
        background-image: url("images/GpsIcon.png");
    }

    #myLocationBtn:hover {
        background-image: url("images/GpsIcon-hover.png");
    }

    #searchBtn {
        background-color: transparent;
        background-image: url("images/SearchIcon.png");
    }

    #searchBtn:hover {
        background-image: url("images/SearchIcon-hover.png");
    }

    .listItem {
        height: 50px;
        padding: 20px;
        font-size: 14px;
    }

    .listItem:hover {
        cursor: pointer;
        background-color: #f1f1f1;
    }

    .listItem-title {
        color: #007faa;
        font-weight: bold;
    }

    .storePopup {
        min-width: 150px;
    }

    .storePopup .popupTitle {
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        padding: 8px;
        height: 30px;
        background-color: #007faa;
        color: white;
        font-weight: bold;
    }

    .storePopup .popupSubTitle {
        font-size: 10px;
        line-height: 12px;
    }

    .storePopup .popupContent {
        font-size: 11px;
        line-height: 18px;
        padding: 8px;
    }

    .storePopup img {
        vertical-align:middle;
        height: 12px;
        margin-right: 5px;
    }

    /* Adjust the layout of the page when the screen width is less than 700 pixels. */
    @media screen and (max-width: 700px) {
        .searchPanel {
            width: 100vw;
        }

        #listPanel {
            top: 385px;
            width: 100%;
            height: calc(100vh - 385px);
        }

        #myMap {
            width: 100vw;
            height: 250px;
            top: 135px;
            left: 0px;
        }

        #myLocationBtn {
            top: 220px;
        }
    }

    .mapCenterIcon {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: orange;
        border: 2px solid white;
        cursor: pointer;
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);
        animation: pulse 3s infinite;
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4);
        }

        70% {
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);
        }

        100% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0);
        }
    }
   ```

Executar a aplicação agora, verá o cabeçalho, caixa de pesquisa e botão de pesquisa. Mas o mapa não é visível porque ainda não foi carregado. Se tentar esponsar, nada acontece. Precisamos de configurar a lógica JavaScript, que é descrita na secção seguinte. Esta lógica acede a toda a funcionalidade do localizador da loja.

## <a name="wire-the-application-with-javascript"></a>Ligue a aplicação com JavaScript

Tudo está agora configurado na interface do utilizador. Ainda precisamos adicionar o JavaScript para carregar e analisar os dados e, em seguida, renderizar os dados no mapa. Para começar, abra *o index.js* e adicione-lhe código, conforme descrito nos seguintes passos.

1. Adicione opções globais para facilitar a atualização das configurações. Defina as variáveis para o mapa, janela pop-up, fonte de dados, camada de ícone e marcador HTML. Delineie o marcador HTML para indicar o centro de uma área de pesquisa. E, defina uma instância do cliente de serviço de pesquisa do Azure Maps.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. Adicione código ao *index.js*. O código seguinte inicializa o mapa. Adicionámos um [ouvinte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para esperar até que a página termine de carregar. Depois, ligámos eventos para monitorizar o carregamento do mapa e dar funcionalidade ao botão de pesquisa e ao botão de localização My.

   Quando o utilizador seleciona o botão de pesquisa ou escreve uma localização na caixa de pesquisa, em seguida, pressiona a entrada, inicia-se uma pesquisa confusa contra a consulta do utilizador. Passe numa série de valores iso 2 do país para a opção `countrySet` de limitar os resultados de pesquisa a esses países/regiões. Limitar os países/regiões à procura ajuda a aumentar a precisão dos resultados que são devolvidos. 
  
   Uma vez terminada a pesquisa, pegue o primeiro resultado e coloque a câmara do mapa sobre essa área. Quando o utilizador selecionar o botão My Location, recupere a localização do utilizador utilizando a API de Geolocalização HTML5. Esta API está incorporada no navegador. Então, centro o mapa sobre a sua localização.  

   > [!Tip]
   > Quando se usa janelas pop-up, o `Popup` melhor é criar uma única instância e reutilizar a instância atualizando o seu conteúdo e posição. Para `Popup`cada caso que adiciona ao seu código, vários elementos DOM são adicionados à página. Quanto mais elementos DOM existem numa página, mais coisas o navegador tem de acompanhar. Se houver muitos itens, o navegador pode tornar-se lento.

    ```JavaScript
    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps primary subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });

        //Create a pop-up window, but leave it closed so we can update it and display it later.
        popup = new atlas.Popup();

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 } // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

            //Add your post-map load functionality.

        });
    }

    //Create an array of country ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();

            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            }
        });
    }

    function setMapToUserLocation() {
        //Request the user's location.
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
            //If an error occurs when the API tries to access the user's position information, display an error message.
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert('User denied the request for geolocation.');
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert('Position information is unavailable.');
                    break;
                case error.TIMEOUT:
                    alert('The request to get user position timed out.');
                    break;
                case error.UNKNOWN_ERROR:
                    alert('An unknown error occurred.');
                    break;
            }
        });
    }

    //Initialize the application when the page is loaded.
    window.onload = initialize;
    ```

1. No ouvinte do `ready` mapa, adicione um controlo de zoom e um marcador HTML para exibir o centro de uma área de pesquisa.

    ```JavaScript
    //Add a zoom control to the map.
    map.controls.add(new atlas.control.ZoomControl(), {
        position: 'top-right'
    });

    //Add an HTML marker to the map to indicate the center to use for searching.
    centerMarker = new atlas.HtmlMarker({
        htmlContent: '<div class="mapCenterIcon"></div>',
        position: map.getCamera().center
    });

    map.markers.add(centerMarker);
    ```

1. No ouvinte do `ready` mapa, adicione uma fonte de dados. Em seguida, faça uma chamada para carregar e analisar o conjunto de dados. Ativar o agrupamento na fonte de dados. Agrupamento sinuoso nos grupos de fonte de dados que se sobrepõem a pontos num cluster. Os clusters separam-se em pontos individuais à medida que o utilizador faz zoom. Este comportamento proporciona uma melhor experiência do utilizador e melhora o desempenho.

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. Depois de carregar o conjunto de `ready` dados no ouvinte do mapa, defina um conjunto de camadas para renderizar os dados. Uma camada de bolha é usada para renderizar pontos de dados agrupados. Uma camada de símbolo é usada para renderizar o número de pontos em cada cluster acima da camada de bolha. Uma segunda camada de símbolo torna um ícone personalizado para localizações individuais no mapa.

   Adicione `mouseover` `mouseout` e eventos às camadas de bolha e ícone para alterar o cursor do rato quando o utilizador paira sobre um cluster ou ícone no mapa. Adicione `click` um evento à camada de bolha de cluster. Este `click` evento amplia no mapa dois níveis e centra o mapa sobre um cluster quando o utilizador seleciona qualquer cluster. Adicione `click` um evento à camada de ícone. Este `click` evento exibe uma janela pop-up que mostra os detalhes de um café quando um utilizador seleciona um ícone de localização individual. Adicione um evento ao mapa para monitorizar quando o mapa estiver terminado de movimento. Quando este evento disparar, atualize os itens no painel da lista.  

    ```JavaScript
    //Create a bubble layer to render clustered data points.
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, {
        radius: 12,
        color: '#007faa',
        strokeColor: 'white',
        strokeWidth: 2,
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property.
    });

    //Create a symbol layer to render the count of locations in a cluster.
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none' //Hide the icon image.
        },

        textOptions: {
            textField: ['get', 'point_count_abbreviated'],
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

    //Create a layer to render a coffee cup symbol above each bubble for an individual location.
    iconLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            //Pass in the ID of the custom icon that was loaded into the map resources.
            image: 'myCustomIcon',

            //Optionally, scale the size of the icon.
            font: ['SegoeUi-Bold'],

            //Anchor the center of the icon image to the coordinate.
            anchor: 'center',

            //Allow the icons to overlap.
            allowOverlap: true
        },

        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
    });

    map.layers.add(iconLayer);

    //When the mouse is over the cluster and icon layers, change the cursor to a pointer.
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'pointer';
    });

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'grab';
    });

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function(e) {
        map.setCamera({
            center: e.position,
            zoom: map.getCamera().zoom + 2
        });
    });

    //Add a click event to the icon layer and show the shape that was selected.
    map.events.add('click', iconLayer, function(e) {
        showPopup(e.shapes[0]);
    });

    //Add an event to monitor when the map is finished rendering the map after it has moved.
    map.events.add('render', function() {
        //Update the data in the list.
        updateListItems();
    });
    ```

1. Quando o conjunto de dados da cafetaria é carregado, deve primeiro ser descarregado. Em seguida, o ficheiro de texto deve ser dividido em linhas. A primeira linha contém a informação do cabeçalho. Para tornar o código mais fácil de seguir, analisamos o cabeçalho num objeto, que podemos usar para procurar o índice celular de cada propriedade. Depois da primeira linha, passe as linhas restantes e crie uma característica de ponto. Adicione a função de ponto à fonte de dados. Finalmente, atualize o painel da lista.

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

            //Parse the tab-delimited file data into GeoJSON features.
            var features = [];

            //Split the lines of the file.
            var lines = text.split('\n');

            //Grab the header row.
            var row = lines[0].split('\t');

            //Parse the header row and index each column to make the code for parsing each row easier to follow.
            var header = {};
            var numColumns = row.length;
            for (var i = 0; i < row.length; i++) {
                header[row[i]] = i;
            }

            //Skip the header row and then parse each row into a GeoJSON feature.
            for (var i = 1; i < lines.length; i++) {
                row = lines[i].split('\t');

                //Ensure that the row has the correct number of columns.
                if (row.length >= numColumns) {

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), {
                        AddressLine: row[header['AddressLine']],
                        City: row[header['City']],
                        Municipality: row[header['Municipality']],
                        AdminDivision: row[header['AdminDivision']],
                        Country: row[header['Country']],
                        PostCode: row[header['PostCode']],
                        Phone: row[header['Phone']],
                        StoreType: row[header['StoreType']],
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false,
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false,
                        Opens: parseInt(row[header['Opens']]),
                        Closes: parseInt(row[header['Closes']])
                    }));
                }
            }

            //Add the features to the data source.
            datasource.add(new atlas.data.FeatureCollection(features));

            //Initially, update the list items.
            updateListItems();
        });
    }
    ```

1. Quando o painel da lista é atualizado, a distância é calculada. Esta distância é do centro do mapa para todas as características do ponto na vista do mapa atual. As características são então classificadas à distância. O HTML é gerado para mostrar cada local no painel da lista.

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button.
        if (camera.zoom < maxClusterZoomLevel) {
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>';
        } else {
            //Update the location of the centerMarker property.
            centerMarker.setOptions({
                position: camera.center,
                visible: true
            });

            //List the ten closest locations in the side panel.
            var html = [], properties;

            /*
            Generating HTML for each item that looks like this:
            <div class="listItem" onclick="itemSelected('id')">
                <div class="listItem-title">1 Microsoft Way</div>
                Redmond, WA 98052<br />
                Open until 9:00 PM<br />
                0.7 miles away
            </div>
            */

            //Get all the shapes that have been rendered in the bubble layer. 
            var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

            //Create an index of the distances of each shape.
            var distances = {};

            data.forEach(function (shape) {
                if (shape instanceof atlas.Shape) {

                    //Calculate the distance from the center of the map to each shape and store in the index. Round to 2 decimals.
                    distances[shape.getId()] = Math.round(atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles') * 100) / 100;
                }
            });

            //Sort the data by distance.
            data.sort(function (x, y) {
                return distances[x.getId()] - distances[y.getId()];
            });

            data.forEach(function(shape) {
                properties = shape.getProperties();
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">',
                properties['AddressLine'],
                '</div>',
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode.
                getAddressLine2(properties),
                '<br />',

                //Convert the closing time to a format that is easier to read.
                getOpenTillTime(properties),
                '<br />',

                //Get the distance of the shape.
                distances[shape.getId()],
                ' miles away</div>');
            });

            listPanel.innerHTML = html.join('');

            //Scroll to the top of the list panel in case the user has scrolled down.
            listPanel.scrollTop = 0;
        }
    }

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string.
    function getOpenTillTime(properties) {
        var time = properties['Closes'];
        var t = time / 100;
        var sTime;

        if (time === 1200) {
            sTime = 'noon';
        } else if (time === 0 || time === 2400) {
            sTime = 'midnight';
        } else {
            sTime = Math.round(t) + ':';

            //Get the minutes.
            t = (t - Math.round(t)) * 100;

            if (t === 0) {
                sTime += '00';
            } else if (t < 10) {
                sTime += '0' + t;
            } else {
                sTime += Math.round(t);
            }

            if (time < 1200) {
                sTime += ' AM';
            } else {
                sTime += ' PM';
            }
        }

        return 'Open until ' + sTime;
    }

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode.
    function getAddressLine2(properties) {
        var html = [properties['City']];

        if (properties['Municipality']) {
            html.push(', ', properties['Municipality']);
        }

        if (properties['AdminDivision']) {
            html.push(', ', properties['AdminDivision']);
        }

        if (properties['PostCode']) {
            html.push(' ', properties['PostCode']);
        }

        return html.join('');
    }
    ```

1. Quando o utilizador seleciona um item no painel da lista, a forma a que o item está relacionado é recuperada a partir da fonte de dados. Uma janela pop-up é gerada que é baseada na informação da propriedade armazenada na forma. O mapa está centrado sobre a forma. Se o mapa tiver menos de 700 pixels de largura, a vista do mapa é compensada para que a janela pop-up seja visível.

    ```JavaScript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window.
    function itemSelected(id) {
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id);
        showPopup(shape);

        //Center the map over the shape on the map.
        var center = shape.getCoordinates();
        var offset;

        //If the map is less than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }

        map.setCamera({
            center: center,
            centerOffset: offset
        });
    }

    function showPopup(shape) {
        var properties = shape.getProperties();

        /* Generating HTML for the pop-up window that looks like this:

            <div class="storePopup">
                <div class="popupTitle">
                    3159 Tongass Avenue
                    <div class="popupSubTitle">Ketchikan, AK 99901</div>
                </div>
                <div class="popupContent">
                    Open until 22:00 PM<br/>
                    <img title="Phone Icon" src="images/PhoneIcon.png">
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a>
                    <br>Amenities:
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png">
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png">
                </div>
            </div>
        */

         //Calculate the distance from the center of the map to the shape in miles, round to 2 decimals.
        var distance = Math.round(atlas.math.getDistanceTo(map.getCamera().center, shape.getCoordinates(), 'miles') * 100)/100;

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Add the distance information.  
            '<br/>', distance,
            ' miles away',
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:',
            properties['Phone'],
            '">',  
            properties['Phone'],
            '</a>'
        );

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) {
            html.push('<br/>Amenities: ');

            if (properties['IsWiFiHotSpot']) {
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>');
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>');
            }
        }

        html.push('</div></div>');

        //Update the content and position of the pop-up window for the specified shape information.
        popup.setOptions({

            //Create a table from the properties in the feature.
            content:  html.join(''),
            position: shape.getCoordinates()
        });

        //Open the pop-up window.
        popup.open(map);
    }
    ```

Agora, tem um localizador de loja totalmente funcional. Num navegador web, abra o ficheiro *index.html* para o localizador da loja. Quando os clusters aparecem no mapa, pode procurar um local utilizando a caixa de pesquisa, selecionando o botão My Location, selecionando um cluster ou fazendo zoom no mapa para ver localizações individuais.

A primeira vez que um utilizador seleciona o botão My Location, o navegador apresenta um aviso de segurança que pede permissão para aceder à localização do utilizador. Se o utilizador concordar em partilhar a sua localização, o mapa faz zoom na localização do utilizador e os cafés próximos são mostrados. 

<center>

![Screenshot do pedido do navegador para aceder à localização do utilizador](./media/tutorial-create-store-locator/GeolocationApiWarning.png)</center>

Quando se aproxima o suficiente numa área que tem localizações de café, os clusters separam-se em locais individuais. Selecione um dos ícones do mapa ou selecione um item no painel lateral para ver uma janela pop-up. O pop-up mostra informações para o local selecionado.

<center>

![Screenshot do localizador de loja acabado](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)</center>

Se redimensionar a janela do navegador para menos de 700 pixels de largura ou abrir a aplicação num dispositivo móvel, o layout muda para ser mais adequado para ecrãs mais pequenos. 

<center>

![Screenshot da versão de pequeno ecrã do localizador da loja](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)</center>

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprende a criar um localizador de loja básico usando O Azure Maps. O localizador de loja que cria neste tutorial pode ter toda a funcionalidade de que necessita. Pode adicionar funcionalidades ao seu localizador de loja ou utilizar funcionalidades mais avançadas para uma experiência mais personalizada do utilizador: 

> [!div class="checklist"]
> * Ative [sugestões à medida que escreve](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI) na caixa de pesquisa.  
> * Adicione [suporte para várias línguas](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization). 
> * Permita ao utilizador [filtrar as localizações ao longo de um percurso](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route). 
> * Adicione a capacidade de [definir filtros](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property). 
> * Adicione suporte para especificar um valor de pesquisa inicial utilizando uma corda de consulta. Quando inclui esta opção no seu localizador de loja, os utilizadores podem marcar e partilhar pesquisas. Também fornece um método fácil para você passar pesquisas para esta página a partir de outra página.  
> * Implemente o seu localizador de loja como uma Aplicação Web do Serviço de [Aplicações Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html). 
> * Guarde os seus dados numa base de dados e procure locais próximos. Para saber mais, consulte os tipos de [dados espaciais do SQL Server](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-2017) e os [dados espaciais da Consulta para o vizinho mais próximo](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-2017).

> [!div class="nextstepaction"]
> [Ver código fonte completo](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)

> [!div class="nextstepaction"]
> [Ver amostra ao vivo](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)

Para saber mais sobre a cobertura e as capacidades do Azure Maps:

> [!div class="nextstepaction"]
> [Níveis de zoom e grelha de azulejos](zoom-levels-and-tile-grid.md)

Para ver mais exemplos de código e uma experiência interativa de programação:

> [!div class="nextstepaction"]
> [Como usar o controlo do mapa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-web-sdk.md)
