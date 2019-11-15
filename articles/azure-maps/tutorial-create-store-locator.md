---
title: 'Tutorial: criar um localizador de repositório usando mapas do Azure'
description: 'Tutorial: criar um localizador de repositório usando mapas do Azure.'
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 52deb1cf872176b69975d550dd89d870b34d9bf0
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74107081"
---
# <a name="tutorial-create-a-store-locator-by-using-azure-maps"></a>Tutorial: criar um localizador de repositório usando mapas do Azure

Este tutorial orienta você pelo processo de criação de um localizador de repositório simples usando mapas do Azure. Os localizadores de repositório são comuns. Muitos dos conceitos que são usados nesse tipo de aplicativo são aplicáveis a muitos outros tipos de aplicativos. A oferta de um localizador de loja para os clientes é um necessário para a maioria das empresas que vendem diretamente aos consumidores. Neste tutorial, ficará a saber como:
    
> [!div class="checklist"]
> * Crie uma nova página da Web usando a API de Controle de Mapeamento do Azure.
> * Carregar dados personalizados de um arquivo e exibi-los em um mapa.
> * Use o serviço de pesquisa do Azure Maps para localizar um endereço ou inserir uma consulta.
> * Obtenha o local do usuário no navegador e mostre-o no mapa.
> * Combine várias camadas para criar símbolos personalizados no mapa.  
> * Pontos de dados do cluster.  
> * Adicione controles de zoom ao mapa.

<a id="Intro"></a>

Vá direto para o exemplo ou [código-fonte](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)do [localizador de Live Store](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) . 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste tutorial, primeiro você precisa [criar sua conta do Azure Maps](./tutorial-search-location.md#createaccount) e seguir as etapas em [obter chave primária](./tutorial-search-location.md#getkey) para obter a chave de assinatura primária para sua conta.

## <a name="design"></a>Design

Antes de ir para o código, é uma boa ideia começar com um design. O localizador de armazenamento pode ser tão simples ou complexo quanto você desejar. Neste tutorial, criamos um localizador de repositório simples. Incluímos algumas dicas ao longo do caminho para ajudá-lo a estender algumas funcionalidades se você optar por. Criamos um localizador de repositório para uma empresa fictícia chamada contoso Coffee. A figura a seguir mostra um wireframe do layout geral do localizador de loja que criamos neste tutorial:

<br/>
<center>

![wireframe de um localizador de loja para locais de cafeteria de café da Contoso](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)</center>

Para maximizar a utilidade desse localizador de loja, incluímos um layout responsivo que se ajusta quando a largura da tela de um usuário é menor que 700 pixels de largura. Um layout responsivo facilita o uso do localizador de armazenamento em uma tela pequena, como em um dispositivo móvel. Aqui está um wireframe de um layout de tela pequena:  

<br/>
<center>

![wireframe do localizador da loja da Contoso Coffee em um dispositivo móvel](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</center>

Os wireframes mostram um aplicativo razoavelmente simples. O aplicativo tem uma caixa de pesquisa, uma lista de lojas próximas, um mapa que tem alguns marcadores (símbolos) e uma janela pop-up que exibe informações adicionais quando o usuário seleciona um marcador. Mais detalhadamente, aqui estão os recursos que criamos neste localizador de armazenamento neste tutorial:

* Todos os locais do arquivo de dados não limitado por tabulação importado são carregados no mapa.
* O usuário pode deslocar e aplicar zoom no mapa, executar uma pesquisa e selecionar o botão meu local de GPS.
* O layout da página se ajusta com base na largura da tela do dispositivo.  
* Um cabeçalho mostra o logotipo da loja.  
* O usuário pode usar uma caixa de pesquisa e um botão Pesquisar para procurar um local, como um endereço, código postal ou cidade. 
* Um evento `keypress` adicionado à caixa de pesquisa disparará uma pesquisa se o usuário pressionar Enter. Essa funcionalidade geralmente é ignorada, mas cria uma melhor experiência do usuário.
* Quando o mapa é movido, a distância de cada local do centro do mapa é calculada. A lista de resultados é atualizada para exibir os locais mais próximos na parte superior do mapa.  
* Quando você seleciona um resultado na lista de resultados, o mapa é centralizado sobre o local selecionado e as informações sobre o local são exibidas em uma janela pop-up.  
* A seleção de um local específico no mapa também dispara uma janela pop-up.
* Quando o usuário se reduz, os locais são agrupados em clusters. Os clusters são representados por um círculo com um número dentro do círculo. Os clusters formam e são separados conforme o usuário altera o nível de zoom.
* A seleção de um cluster amplia o mapeamento de dois níveis e centraliza o local do cluster.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Criar o conjunto de armazenamento do local do repositório

Antes de desenvolvermos um aplicativo de localizador de repositório, precisamos criar um conjunto de um dos armazenamentos que desejamos exibir no mapa. Neste tutorial, usamos um conjunto de um DataSet para uma cafeteria fictícia chamada contoso Coffee. O conjunto de armazenamento para esse localizador de repositório simples é gerenciado em uma pasta de trabalho do Excel. O conjunto de conjuntos contém 10.213 locais de loja de café da Contoso distribuídos em nove países/regiões: o Estados Unidos, o Canadá, o Reino Unido, a França, a Alemanha, a Itália, os Países Baixos, a Dinamarca e a Espanha. Aqui está uma captura de tela da aparência dos dados:

<br/>
<center>

![captura de tela dos dados do localizador da loja em uma pasta de trabalho do Excel](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)</center>

Você pode [baixar a pasta de trabalho do Excel](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). 

Observando a captura de tela dos dados, podemos fazer as seguintes observações:
    
* As informações de local são armazenadas usando as colunas **Addressing**, **City**, **Municipal** (County), **AdminDivision** (estado/província), **PostCode** (CEP) e **país** .  
* As colunas de **latitude** e **longitude** contêm as coordenadas de cada local da loja de café da contoso. Se você não tiver informações de coordenadas, poderá usar os serviços de pesquisa no Azure Maps para determinar as coordenadas de local.
* Algumas colunas adicionais contêm metadados relacionados às cafeterias: um número de telefone, as colunas booleanas para a acessibilidade Wi-Fi e o acesso a rodas, além de armazenar horários de abertura e fechamento no formato de 24 horas. Você pode criar suas próprias colunas que contêm metadados que são mais relevantes para seus dados de localização.

> [!Note]
> O Azure Maps renderiza dados na projeção de Mercator esférica "EPSG: 3857", mas lê dados em "EPSG: 4325" que usam a referência de WGS84. 

Há várias maneiras de expor o conjunto de aplicativos ao aplicativo. Uma abordagem é carregar os dados em um banco de dado e expor um serviço Web que consulta os dados e envia os resultados para o navegador do usuário. Essa opção é ideal para grandes conjuntos de altos ou para conjuntos de de de valores que são atualizados com frequência. No entanto, essa opção requer muito mais trabalho de desenvolvimento e tem um custo mais alto. 

Outra abordagem é converter esse conjunto de DataSet em um arquivo de texto simples que o navegador possa facilmente analisar. O próprio arquivo pode ser hospedado com o restante do aplicativo. Essa opção mantém as coisas simples, mas é uma boa opção apenas para conjuntos de dados menores, pois o usuário baixa todos eles. Usamos o arquivo de texto simples para este conjunto de dados porque o tamanho do arquivo é menor que 1 MB.  

Para converter a pasta de trabalho em um arquivo de texto simples, salve a pasta de trabalho como um arquivo delimitado por tabulação. Cada coluna é delimitada por um caractere de tabulação, o que torna as colunas fáceis de analisar em nosso código. Você pode usar o formato CSV (valores separados por vírgula), mas essa opção requer mais lógica de análise. Qualquer campo que tenha uma vírgula em torno dele seria quebrado entre aspas. Para exportar esses dados como um arquivo delimitado por tabulação no Excel, selecione **salvar como**. Na lista suspensa **salvar como tipo** , selecione **texto (delimitado por tabulação) (*. txt)** . Nomeie o arquivo *ContosoCoffee. txt*. 

<br/>
<center>

![captura de tela da caixa de diálogo Salvar como tipo](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)</center>

Se você abrir o arquivo de texto no bloco de notas, ele será semelhante à seguinte figura:

<br/>
<center>

![captura de tela de um arquivo do bloco de notas que mostra um conjunto de DataSet delimitado por tabulação](./media/tutorial-create-store-locator/StoreDataTabFile.png)</center>


## <a name="set-up-the-project"></a>Configurar o projeto

Para criar o projeto, você pode usar o [Visual Studio](https://visualstudio.microsoft.com) ou o editor de código de sua escolha. Na pasta do projeto, crie três arquivos: *index. html*, *index. css*e *index. js*. Esses arquivos definem o layout, o estilo e a lógica para o aplicativo. Crie uma pasta denominada *dados* e adicione *ContosoCoffee. txt* à pasta. Crie outra pasta chamada *imagens*. Usamos dez imagens neste aplicativo para ícones, botões e marcadores no mapa. Você pode [baixar essas imagens](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). A pasta do projeto agora deve ser parecida com a seguinte figura:

<br/>
<center>

![captura de tela da pasta de projeto do localizador de repositório simples](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)</center>

## <a name="create-the-user-interface"></a>Criar a interface do usuário

Para criar a interface do usuário, adicione o código a *index. html*:

1. Adicione as seguintes marcas de `meta` à `head` de *index. html*. As marcas definem o conjunto de caracteres (UTF-8), informe ao Internet Explorer e ao Microsoft Edge para usar as versões mais recentes do navegador e especifique um visor que funcione bem para layouts responsivos.

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. Adicione referências aos arquivos JavaScript e CSS do controle da Web do Azure Maps:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

1. Adicione uma referência ao módulo de serviços do Azure Maps. O módulo é uma biblioteca JavaScript que encapsula os serviços REST do Azure Maps e torna-os fáceis de usar em JavaScript. O módulo é útil para ligar a funcionalidade de pesquisa.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

1. Adicione referências a *index. js* e *index. css*:

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. No corpo do documento, adicione uma marca de `header`. Dentro da marca de `header`, adicione o logotipo e o nome da empresa.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. Adicione uma marca de `main` e crie um painel de pesquisa que tenha uma caixa de texto e um botão de pesquisa. Além disso, adicione referências de `div` para o mapa, o painel de lista e o botão de GPS de meu local.

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

Quando tiver terminado, o *index. html* deverá ser semelhante [a este arquivo index. html de exemplo](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html).

A próxima etapa é definir os estilos de CSS. Os estilos CSS definem como os componentes do aplicativo são dispostos e a aparência do aplicativo. Abra *index. css* e adicione o código a seguir a ele. O estilo `@media` define as opções de estilo alternativo a serem usadas quando a largura da tela for menor que 700 pixels.  

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

Se você executar o aplicativo agora, verá o cabeçalho, a caixa de pesquisa e o botão Pesquisar, mas o mapa não será visível porque ainda não foi carregado. Se você tentar fazer uma pesquisa, nada acontecerá. Precisamos configurar a lógica JavaScript descrita na próxima seção para acessar toda a funcionalidade do localizador de loja.

## <a name="wire-the-application-with-javascript"></a>Conectar o aplicativo ao JavaScript

Neste ponto, tudo está configurado na interface do usuário. Agora, precisamos adicionar o JavaScript para carregar e analisar os dados e, em seguida, renderizar os dados no mapa. Para começar, abra *index. js* e adicione código a ele, conforme descrito nas etapas a seguir.

1. Adicione opções globais para facilitar a atualização das configurações. Além disso, defina variáveis para o mapa, uma janela pop-up, uma fonte de dados, uma camada de ícone, um marcador HTML que exibe o centro de uma área de pesquisa e uma instância do cliente do serviço de pesquisa do Azure Maps.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. Adicione código a *index. js*. O código a seguir inicializa o mapa, adiciona um [ouvinte de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) que aguarda até que a página termine de ser carregada, conecta eventos para monitorar o carregamento do mapa e ativa o botão de pesquisa e o botão meu local.

   Quando o usuário seleciona o botão de pesquisa ou quando o usuário pressiona ENTER depois de inserir um local na caixa de pesquisa, uma pesquisa difusa em relação à consulta do usuário é iniciada. Transmita uma matriz de valores de país ISO 2 para a opção `countrySet` para limitar os resultados da pesquisa a esses países/regiões. Limitar os países/regiões a serem pesquisados ajuda a aumentar a precisão dos resultados retornados. 
  
   Quando a pesquisa for concluída, pegue o primeiro resultado e defina a câmera do mapa nessa área. Quando o usuário seleciona o botão meu local, use a API de localização geográfica do HTML5 que está incorporada ao navegador para recuperar o local do usuário e centralizar o mapa em seu local.  

   > [!Tip]
   > Quando você usa janelas pop-up, é melhor criar uma única instância de `Popup` e reutilizar a instância atualizando seu conteúdo e posição. Para cada instância de `Popup`que você adiciona ao seu código, vários elementos DOM são adicionados à página. Quanto mais elementos DOM houver em uma página, mais coisas o navegador precisa acompanhar. Se houver muitos itens, o navegador poderá ficar lento.

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

1. No ouvinte de eventos de `ready` do mapa, adicione um controle de zoom e um marcador HTML para exibir o centro de uma área de pesquisa.

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

1. No ouvinte de eventos de `ready` do mapa, adicione uma fonte de dados. Em seguida, faça uma chamada para carregar e analisar o conjunto de os. Habilite o clustering na fonte de dados. O clustering nos grupos de fontes de dados sobrepõem pontos juntos em um cluster. Os clusters são separados em pontos individuais à medida que o usuário se amplia. Isso torna uma experiência de usuário mais fluida e melhora o desempenho.

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

1. Depois de carregar o conjunto de dados no ouvinte de evento de `ready` do mapa, defina um conjunto de camadas para renderizar os dados. Uma camada de bolha é usada para renderizar pontos de dados clusterizados. Uma camada de símbolo é usada para renderizar o número de pontos em cada cluster acima da camada de bolha. Uma segunda camada de símbolo renderiza um ícone personalizado para locais individuais no mapa.

   Adicione `mouseover` e `mouseout` eventos às camadas de bolha e ícone para alterar o cursor do mouse quando o usuário focaliza um cluster ou ícone no mapa. Adicione um evento `click` à camada de bolha do cluster. Esse `click` evento amplia o mapa em dois níveis e centraliza o mapa em um cluster quando o usuário seleciona qualquer cluster. Adicione um evento de `click` à camada de ícone. Esse `click` evento exibe uma janela pop-up que mostra os detalhes de uma cafeteria quando um usuário seleciona um ícone de local individual. Adicione um evento ao mapa a ser monitorado quando o mapa terminar de ser movido. Quando esse evento for acionado, atualize os itens no painel de lista.  

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

1. Quando o conjunto de uma loja de café é carregado, primeiro ele deve ser baixado. Em seguida, o arquivo de texto deve ser dividido em linhas. A primeira linha contém as informações de cabeçalho. Para facilitar o acompanhamento do código, analisamos o cabeçalho em um objeto, que podemos usar para pesquisar o índice de célula de cada propriedade. Após a primeira linha, execute o loop pelas linhas restantes e crie um recurso de ponto. Adicione o recurso de ponto à fonte de dados. Por fim, atualize o painel de lista.

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

1. Quando o painel de lista é atualizado, a distância do centro do mapa para todos os recursos de ponto no modo de exibição de mapa atual é calculada. Os recursos são então classificados por distância. O HTML é gerado para exibir cada local no painel de lista.

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

1. Quando o usuário seleciona um item no painel de lista, a forma à qual o item está relacionado é recuperada da fonte de dados. Uma janela pop-up é gerada com base nas informações de propriedade armazenadas na forma. O mapa é centralizado na forma. Se o mapa tiver menos de 700 pixels de largura, a exibição do mapa será deslocada para que a janela pop-up fique visível.

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

Agora, você tem um localizador de repositório totalmente funcional. Em um navegador da Web, abra o arquivo *index. html* do localizador da loja. Quando os clusters aparecem no mapa, você pode pesquisar um local usando a caixa de pesquisa, selecionando o botão meu local, selecionando um cluster ou ampliando o mapa para ver locais individuais.

Na primeira vez que um usuário seleciona o botão meu local, o navegador exibe um aviso de segurança que solicita permissão para acessar o local do usuário. Se o usuário concordar em compartilhar seu local, o mapa ampliará o local do usuário e as lanchonetes próximas serão mostradas. 

<br/>
<center>

![captura de tela da solicitação do navegador para acessar o local do usuário](./media/tutorial-create-store-locator/GeolocationApiWarning.png)</center>

Ao ampliar o suficiente em uma área que tem locais de cafeterias, os clusters são separados em locais individuais. Selecione um dos ícones no mapa ou selecione um item no painel lateral para ver uma janela pop-up que mostra informações para esse local.

<br/>
<center>

![captura de tela do localizador de loja concluído](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)</center>

Se você redimensionar a janela do navegador para menos de 700 pixels de largura ou abrir o aplicativo em um dispositivo móvel, o layout será alterado para ser mais adequado para telas menores. 

<br/>
<center>

![captura de tela da versão do localizador da loja](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)</center>

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprenderá a criar um localizador de armazenamento básico usando o Azure Maps. O localizador de repositório criado neste tutorial pode ter toda a funcionalidade necessária. Você pode adicionar recursos ao seu localizador de armazenamento ou usar recursos mais avançados para uma experiência de usuário personalizada: 

> [!div class="checklist"]
> * Habilite [sugestões conforme você digita](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI) na caixa de pesquisa.  
> * Adicione [suporte para vários idiomas](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization). 
> * Permitir que o usuário [filtre os locais ao longo de uma rota](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route). 
> * Adicione a capacidade de [definir filtros](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property). 
> * Adicione suporte para especificar um valor de pesquisa inicial usando uma cadeia de caracteres de consulta. Quando você inclui essa opção em seu localizador de armazenamento, os usuários podem marcar e compartilhar pesquisas. Ele também fornece um método fácil para você passar pesquisas para esta página de outra página.  
> * Implante o localizador de repositório como um [aplicativo Web de serviço Azure app](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html). 
> * Armazene seus dados em um banco de dado e pesquise locais próximos. Para saber mais, confira a [visão geral dos tipos de dados espaciais SQL Server](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-2017) e [dados espaciais de consulta para o vizinho mais próximo](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-2017).

> [!div class="nextstepaction"]
> [Exibir código-fonte completo](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)

> [!div class="nextstepaction"]
> [Exibir exemplo ao vivo](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)

Para saber mais sobre a cobertura e as capacidades do Azure Maps:

> [!div class="nextstepaction"]
> [Zoom levels and tile grid ](zoom-levels-and-tile-grid.md) (Níveis de zoom e grelha de mosaico)

Para ver mais exemplos de código e uma experiência interativa de programação:

> [!div class="nextstepaction"]
> [How to use the map control](how-to-use-map-control.md) (Como utilizar o controlo de mapa)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)
