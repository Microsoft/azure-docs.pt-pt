---
title: Criar um localizador de arquivo com o Azure Maps | Documentos da Microsoft
description: Crie um localizador de arquivo com o Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 11/15/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: d8256f96a79969103b17047c4ebb55fb140eb0bc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121116"
---
# <a name="create-a-store-locator-by-using-azure-maps"></a>Criar um localizador de arquivo com o Azure Maps

Este tutorial orienta-o ao longo do processo de criação de um localizador de arquivo simples com o Azure Maps. Os localizadores Store são comuns. Muitos dos conceitos que são utilizados neste tipo de aplicação são aplicáveis a muitos outros tipos de aplicativos. Oferecer um localizador de loja para os clientes é essencial para a maioria das empresas que vendem diretamente aos consumidores. Neste tutorial, ficará a saber como:
    
> [!div class="checklist"]
> * Crie uma nova página Web com a API de controlo de mapas do Azure.
> * Carregar dados personalizados de um arquivo e apresentá-lo num mapa.
> * Utilize o serviço de pesquisa de mapas do Azure para localizar um endereço ou introduza uma consulta.
> * Obter a localização do utilizador a partir do browser e mostrá-lo no mapa.
> * Combine várias camadas para criar os símbolos personalizados no mapa.  
> * Pontos de dados do cluster.  
> * Adicione controles de zoom ao mapa.

<a id="Intro"></a>

Ir diretamente para o [exemplo de localizador de armazenamento em direto](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) ou [código-fonte](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator). 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste tutorial, tem primeiro de [criar a sua conta do Azure Maps](./tutorial-search-location.md#createaccount) e [obter a chave de subscrição para a sua conta](./tutorial-search-location.md#getkey).

## <a name="design"></a>Design

Antes de mergulhar no código, é uma boa idéia começar com um design. O localizador de arquivo pode ser tão simples ou complexo quanto quiser que ela seja. Neste tutorial, vamos criar um localizador de arquivo simples. Incluímos algumas dicas ao longo do caminho para o ajudar a estender algumas funcionalidades, se optar por. Vamos criar um localizador de arquivo para uma empresa fictícia denominada Contoso café. A figura seguinte mostra um modelo de arames do layout geral o localizador de loja que é criar neste tutorial:

<br/>
<center>

![Modelo de arames de um localizador de arquivo para localizações de café do café da Contoso](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)</center>

Para maximizar a utilidade deste localizador de arquivo, incluímos um esquema responsivo que ajusta quando a largura da tela de um utilizador é menor do que 700 pixels de largura. Um esquema reativo torna mais fácil de utilizar o localizador de arquivo num ecrã pequeno, como num dispositivo móvel. Este é um modelo de arames de um esquema de ecrã pequeno:  

<br/>
<center>

![Modelo de arames o café da Contoso armazenar localizador num dispositivo móvel](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</center>

Os wireframes mostram um aplicativo bem simples. O aplicativo tem uma caixa de pesquisa, uma lista das lojas próximas, um mapa que tem alguns marcadores (símbolos) e uma janela de pop-up que apresenta informações adicionais quando o usuário seleciona um marcador. Mais detalhadamente, aqui estão os recursos que criamos para este localizador de armazenamento neste tutorial:

* Todas as localizações a partir do ficheiro de dados importados delimitado por tabulação são carregadas no mapa.
* O utilizador pode Panorâmica e zoom do mapa, efetua uma pesquisa e selecione o botão de minha localização GPS.
* O layout da página ajusta-se com base na largura de ecrã do dispositivo.  
* Um cabeçalho mostra o logótipo da loja.  
* O utilizador pode utilizar uma caixa de pesquisa e um botão de pesquisa para procurar uma localização, como um endereço, código postal ou cidade. 
* A `keypress` evento adicionado à caixa de pesquisa aciona uma pesquisa, se o usuário pressionar Enter. Esta funcionalidade é muitas vezes negligenciada, mas cria uma melhor experiência de utilizador.
* Quando move o mapa, a distância para cada localização do centro do mapa é calculada. A lista de resultados é atualizada para apresentar as localizações mais próximas na parte superior do mapa.  
* Quando seleciona um resultado na lista de resultados, o mapa centra-se sobre a localização selecionada e informações sobre o local é apresentado numa janela de pop-up.  
* Também selecionar uma localização específica no mapa aciona uma janela de pop-up.
* Quando o usuário amplia, localizações são agrupadas em clusters. Clusters são representados por um círculo com um número no interior do círculo. Clusters de formulário e separado como o utilizador altera o nível de zoom.
* Selecionar um cluster aplica zoom sobre os dois níveis de mapa e centra-se sobre a localização do cluster.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Criar o conjunto de dados de localização de armazenamento

Antes de desenvolver um aplicativo de localizador de arquivo, é necessário criar um conjunto de dados de arquivos de que queremos exibir no mapa. Neste tutorial, utilizamos um conjunto de dados para um café fictícia chamada Contoso café. O conjunto de dados para este localizador de arquivo simples é gerenciado num livro do Excel. O conjunto de dados contém 10,213 localizações de café Contoso café espalhadas em nove países: Estados Unidos, Canadá, Reino Unido, França, Alemanha, Itália, Países Baixos, Dinamarca e Espanha. Eis uma captura de ecrã o que os dados é semelhante a:

<br/>
<center>

![Captura de ecrã dos dados de localizador de arquivo num livro do Excel](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)</center>

Pode [transferir o livro do Excel](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). 

Observando a captura de ecrã dos dados, podemos fazer as seguintes observações:
    
* Informações de localização são armazenadas utilizando o **AddressLine**, **Cidade**, **município** (condados) **AdminDivision** (estado/província) , **PostCode** (código postal), e **país** colunas.  
* O **Latitude** e **Longitude** colunas incluir as coordenadas para cada localização de café Contoso café. Se não tiver informações de coordenadas, pode utilizar os serviços de pesquisa no Azure Maps para determinar as coordenadas de localização.
* Algumas colunas adicionais contêm os metadados relacionados com os cafés: um número de telefone, booleanas colunas para acessibilidade de ponto de acesso e wheelchair Wi-Fi e arquivo abrir e fechar vezes no formato de 24 horas. Pode criar suas próprias colunas que contêm metadados que são mais relevante para os dados de localização.

> [!Note]
> Mapas do Azure processa os dados na projeção Mercator esférica "EPSG:3857" mas lê os dados em "EPSG:4325" que utilizam a datum WGS84. 

Existem várias maneiras de expor o conjunto de dados para a aplicação. Uma abordagem é carregar os dados para uma base de dados e expor um serviço web que consulta os dados e envia os resultados para o navegador do usuário. Esta opção é ideal para grandes conjuntos de dados ou para conjuntos de dados que são atualizados com frequência. No entanto, esta opção requer significativamente mais trabalho de desenvolvimento e tem um custo mais elevado. 

Outra abordagem é converter este conjunto de dados num arquivo de texto simples que o navegador pode analisar facilmente. O próprio ficheiro pode ser hospedado com o restante do aplicativo. Esta opção mantém as coisas simples, mas é uma boa opção apenas para conjuntos de dados menores porque o utilizador transfere todos os dados. Utilizamos o arquivo de texto simples para este conjunto de dados porque o tamanho do ficheiro de dados é inferior a 1 MB.  

Para converter a pasta de trabalho para um arquivo de texto simples, guarde o livro como um ficheiro delimitado por tabulação. Cada coluna é delimitada por um caractere de tabulação, que faz com que as colunas mais fácil de analisar no nosso código. Poderia usar o formato de valores separados por vírgulas (CSV), mas essa opção exige mais lógica de análise. Qualquer campo que tenha uma vírgula em torno dele poderia ser colado entre aspas. Para exportar esses dados como um arquivo delimitado por tabulação no Excel, selecione **guardar como**. Na **guardar como tipo** na lista pendente, selecione **texto (separador delimited)(*.txt)**. Nomeie o arquivo *ContosoCoffee.txt*. 

<br/>
<center>

![Captura de ecrã do guardar como caixa de diálogo do tipo](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)</center>

Se abrir o ficheiro de texto no bloco de notas, o que é semelhante a figura a seguir:

<br/>
<center>

![Captura de ecrã de um ficheiro do bloco de notas que mostra um conjunto de dados delimitado por tabulação](./media/tutorial-create-store-locator/StoreDataTabFile.png)</center>


## <a name="set-up-the-project"></a>Configurar o projeto

Para criar o projeto, pode usar [Visual Studio](https://visualstudio.microsoft.com) ou o editor de código da sua preferência. Na pasta do projeto, crie três arquivos: *Index. HTML*, *index.css*, e *Index*. Estes ficheiros definem o layout, o estilo e a lógica para a aplicação. Crie uma pasta denominada *dados* e adicione *ContosoCoffee.txt* para a pasta. Criar outra pasta com o nome *imagens*. Podemos utilizar imagens de dez neste aplicativo para marcadores no mapa, botões e ícones. Pode [baixar essas imagens](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). A pasta do projeto deverá agora ser semelhante a figura a seguir:

<br/>
<center>

![Captura de ecrã da pasta de projeto do localizador de Store simples](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)</center>

## <a name="create-the-user-interface"></a>Criar a interface do usuário

Para criar a interface do usuário, adicione código ao *Index*:

1. Adicione o seguinte `meta` com as etiquetas para o `head` dos *Index*. As etiquetas definem o conjunto de caracteres (UTF-8), informar ao Internet Explorer e o Microsoft Edge para utilizar as versões mais recentes do navegador e especificar uma janela viewport que funciona bem para layouts de capacidade de resposta.

    ```HTML
    <meta charset="utf-8" /> 
    <meta http-equiv="x-ua-compatible" content="IE=Edge" /> 
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    ```

1. Adicione referências ao controle da web do Azure Maps JavaScript e arquivos CSS:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" /> 
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script> 
    ```
    
1. Adicione uma referência para o módulo de serviços de mapas do Azure. O módulo é uma biblioteca de JavaScript que encapsula os serviços do Azure Maps REST e os torna fáceis de usar em JavaScript. O módulo é útil para capacitar a funcionalidade de pesquisa.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2"></script>
    ```
    
1. Adicionar referências aos *Index* e *index.css*:

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css" /> 
    <script src="index.js"></script>
    ```
    
1. No corpo do documento, adicione um `header` marca. Dentro do `header` etiqueta, adicione o nome de logotipo e da empresa.

    ```HTML
    <header> 
        <img src="images/Logo.png" /> 
        <span>Contoso Coffee</span> 
    </header>
    ```

1. Adicionar um `main` Etiquetar e criar um painel de pesquisa que tem um botão de pesquisa e a caixa de texto. Além disso, adicione `div` referências para o mapa, o painel de lista e o botão de minha localização GPS.

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

Quando tiver terminado, *Index. HTML* deve ser semelhante à [este ficheiro Index. HTML de exemplo](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html).

A próxima etapa é definir os estilos CSS. Estilos CSS definem como são apresentados os componentes da aplicação e a aparência do aplicativo. Open *index.css* e adicione o seguinte código ao mesmo. O `@media` estilo define as opções de estilos alternativas para utilizar quando a largura da tela é menor do que 700 pixels.  

```css
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

Se executar o aplicativo agora, verá o cabeçalho, a caixa de pesquisa e o botão de pesquisa, mas o mapa não é visível porque ainda não foi carregada. Se tentar fazer uma pesquisa, nada acontece. É necessário configurar a lógica de JavaScript que é descrita na secção seguinte para aceder a todas as funcionalidades do localizador de arquivo.

## <a name="wire-the-application-with-javascript"></a>Durante a transmissão do aplicativo com JavaScript

Neste momento, tudo está configurado na interface do usuário. Agora, precisamos de adicionar o JavaScript para carregar e analisar os dados e, em seguida, renderizar os dados no mapa. Para começar, abra *Index* e adicione o código, conforme descrito nos passos seguintes.

1. Adicione opções globais para facilitar as definições atualizar. Além disso, defina variáveis para o mapa, uma janela de pop-up, uma origem de dados, uma camada de ícone, um marcador HTML que exibe o Centro de uma área de pesquisa e uma instância do cliente do serviço de pesquisa de mapas do Azure.

    ```Javascript
    //The maximum zoom level to cluster data point data on the map. 
    var maxClusterZoomLevel = 11; 

    //The URL to the store location data. 
    var storeLocationDataUrl = 'data/ContosoCoffee.txt'; 

    //The URL to the icon image. 
    var iconImageUrl = 'images/CoffeeIcon.png'; 
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. Adicione código ao *Index*. O código a seguir inicializa o mapa, adiciona uma [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) que aguarda a conclusão da página de carregamento, conecta eventos a monitorizar o carregamento do mapa e alimenta o botão de pesquisa e o botão de localização de My. 

   Quando o utilizador seleciona o botão de pesquisa, ou quando o usuário pressiona Enter depois de introduzir uma localização na caixa de pesquisa, pesquisas difusas em relação a consulta do utilizador é iniciada. Passar uma matriz de país ISO 2 valores para o `countrySet` opção para limitar os resultados da pesquisa nesses países. Limitar os países para pesquisar ajuda a aumentar a precisão dos resultados retornados. 
  
   Quando a pesquisa estiver concluída, levar o primeiro resultado e defina a câmara de mapa através dessa área. Quando o utilizador seleciona o botão de localização My, utilize a API de localização geográfica do HTML5 que está incorporado no browser para obter a localização do utilizador e centrar o mapa ao longo da respetiva localização.  

   > [!Tip]
   > Quando usa janelas pop-up, é melhor criar um único `Popup` de instância e reutilizar a instância ao atualizar o seu conteúdo e a posição. Para cada `Popup`instância adicionar ao seu código, vários elementos de DOM são adicionados à página. São os mais elementos DOM numa página, quanto mais coisas que o navegador precisa controlar. Se existirem demasiados itens, o navegador pode tornar-se lento.

    ```Javascript
    function initialize() { 
        //Initialize a map instance. 
        map = new atlas.Map('myMap', { 
            center: [-90, 40], 
            zoom: 2,

            //Add your Azure Maps subscription key to the map SDK.
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
            retryOptions: { maxTries: 4 }, // Retry options
        });
    
        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in. 
        document.getElementById('searchBtn').onclick = performSearch; 

        //If the user presses Enter in the search box, perform a search. 
        document.getElementById('searchTbx').onkeyup = function (e) {
            if (e.keyCode === 13) { 
                performSearch(); 
            } 
        }; 

        //If the user selects the My Location button, use the Geolocation API to get the user's location. Center and zoom the map on that location. 
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation; 

        //Wait until the map resources are ready.
        map.events.add('ready', function () { 

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
        navigator.geolocation.getCurrentPosition(function (position) { 
            //Convert the Geolocation API position to a longitude and latitude position value that the map can interpret and center the map over it. 
            map.setCamera({ 
                center: [position.coords.longitude, position.coords.latitude], 
                zoom: maxClusterZoomLevel + 1 
            }); 
        }, function (error) { 
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

1. O mapa `load` serviço de escuta de eventos, adicione um controle de zoom e um marcador HTML para exibir o Centro de uma área de pesquisa.

    ```Javascript
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

1. O mapa `load` serviço de escuta de eventos, adicionar uma origem de dados. Em seguida, fazer uma chamada para carregar e analisar o conjunto de dados. Ative o clustering na origem de dados. Clustering nos dados em grupos de origem a sobreposição de pontos em conjunto num cluster. Os clusters separados para os pontos individuais que o utilizador amplia. Isso torna um experiência de usuário mais fluido e melhora o desempenho.

    ```Javascript
    //Create a data source, add it to the map, and then enable clustering. 
    datasource = new atlas.source.DataSource(null, { 
        cluster: true, 
        clusterMaxZoom: maxClusterZoomLevel - 1 
    }); 

    map.sources.add(datasource); 

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. Depois que carregar o conjunto de dados do mapa `load` serviço de escuta de eventos, definir um conjunto de camadas para processar os dados. Uma camada de bolha é utilizada para processar os pontos de dados em cluster. Uma camada de símbolo é usada para renderizar o número de pontos em cada cluster acima da camada de bolhas. Uma segunda camada de símbolo compõe um ícone personalizado para locais individuais no mapa. 

   Adicione `mouseover` e `mouseout` eventos para as camadas de bolhas e ícone para alterar o cursor do rato quando o utilizador passa através de um cluster ou o ícone no mapa. Adicionar um `click` eventos para a camada de bolhas do cluster. Isso `click` evento amplia o mapa em dois níveis e centros de mapa através de um cluster, quando o usuário seleciona qualquer cluster. Adicionar um `click` eventos para a camada de ícone. Isso `click` eventos exibe uma janela de pop-up que mostra os detalhes de um café, quando um usuário selecionar um ícone de localização individuais. Adicione um evento para o mapa para monitorizar quando o mapa de conclusão da mudança. Quando este evento é disparado, atualize os itens no painel de lista.  

    ```Javascript
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
            textField: '{point_count_abbreviated}', 
            size: 12, 
            font: ['StandardFont-Bold'], 
            offset: [0, 0.4], 
            color: 'white' 
        } 
    }); 

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]); 

    //Load a custom image icon into the map resources.     
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function () {            

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
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function () { 
        map.getCanvasContainer().style.cursor = 'pointer'; 
    }); 

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab). 
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function () { 
        map.getCanvasContainer().style.cursor = 'grab'; 
    }); 

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function (e) { 
        map.setCamera({ 
            center: e.position, 
            zoom: map.getCamera().zoom + 2 
        }); 
    }); 

    //Add a click event to the icon layer and show the shape that was selected. 
    map.events.add('click', iconLayer, function (e) { 
        showPopup(e.shapes[0]); 
    }); 

    //Add an event to monitor when the map is finished rendering the map after it has moved. 
    map.events.add('render', function () { 
        //Update the data in the list. 
        updateListItems(); 
    });
    ```

1. Quando o conjunto de dados de café é carregado, primeiro deve ser transferido. Em seguida, o arquivo de texto têm de ser dividido em linhas. A primeira linha contém as informações de cabeçalho. Para facilitar o código a seguir, vamos analisar o cabeçalho num objeto, o que podemos usar para pesquisar o índice de célula de cada propriedade. Após a primeira linha, percorrer as linhas restantes e criar um recurso de ponto. Adicione a funcionalidade de ponto para a origem de dados. Por fim, atualize o painel de lista.

    ```Javascript
    function loadStoreData() { 

    //Download the store location data. 
    fetch(storeLocationDataUrl)     
        .then(response => response.text()) 
        .then(function (text) { 

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

1. Quando o painel de lista é atualizado, a distância do centro do mapa a todas as funcionalidades de ponto na vista atual do mapa é calculada. As funcionalidades, em seguida, são ordenadas por distância. HTML é gerado para exibir cada localização no painel de lista.

    ```Javascript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>'; 

    function updateListItems() { 
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map. 
        var camera = map.getCamera(); 
        var listPanel = document.getElementById('listPanel'); 

        //Get all the shapes that have been rendered in the bubble layer.  
        var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]); 

        data.forEach(function (shape) { 
            if (shape instanceof atlas.Shape) { 
                //Calculate the distance from the center of the map to each shape, and then store the data in a distance property.  
                shape.distance = atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles'); 
            } 
        }); 

        //Sort the data by distance. 
        data.sort(function (x, y) { 
            return x.distance - y.distance; 
        }); 

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

            data.forEach(function (shape) { 
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

                //Route the distance to two decimal places.  
                (Math.round(shape.distance * 100) / 100), 
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

1. Quando o usuário seleciona um item no painel de lista, a forma para que o item está relacionado é obtida a partir da origem de dados. Uma janela de pop-up é gerada com base nas informações de propriedade armazenadas na forma. O mapa centra-se sobre a forma. Se o mapa for inferior a 700 pixels de largura, a vista do mapa é de deslocamento para a janela de pop-up está visível.

    ```Javascript
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

        var html = ['<div class="storePopup">']; 
        html.push('<div class="popupTitle">', 
            properties['AddressLine'], 
            '<div class="popupSubTitle">', 
            getAddressLine2(properties), 
            '</div></div><div class="popupContent">', 

            //Convert the closing time to a format that's easier to read. 
            getOpenTillTime(properties), 

            //Route the distance to two decimal places.  
            '<br/>', (Math.round(shape.distance * 100) / 100), 
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
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>') 
            } 

            if (properties['IsWheelchairAccessible']) { 
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>') 
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

Agora, tem um localizador de arquivo totalmente funcional. Num browser, abra a *Index* ficheiro para o localizador de arquivo. Quando os clusters são apresentados no mapa, pode procurar uma localização utilizando a caixa de pesquisa, ao selecionar o botão de localização My, ao selecionar um cluster ou quando der zoom no mapa para ver localizações individuais.

Na primeira vez que um utilizador seleciona o botão de localização meu, o browser apresenta um aviso de segurança que solicita permissão para aceder à localização do utilizador. Se o utilizador aceitar partilhar a sua localização, o mapa amplia na localização do utilizador e cafés próximos são mostrados. 

<br/>
<center>

![Captura de ecrã do browser do pedido para aceder a localização do utilizador](./media/tutorial-create-store-locator/GeolocationApiWarning.png)</center>

Quando aplica zoom no fechar suficiente numa área que possui locais de café, os clusters de separam em locais individuais. Selecione um dos ícones no mapa ou um item no painel do lado para ver uma janela de pop-up que mostra as informações para esse local.

<br/>
<center>

![Captura de ecrã do localizador de arquivo concluído](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)</center>

Se redimensiona a janela do browser para menos de 700 pixels de largura ou abrir a aplicação num dispositivo móvel, as alterações de esquema a ser melhores adequam para ecrãs mais pequenos. 

<br/>
<center>

![Captura de ecrã da versão ecrã pequeno do localizador de arquivo](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)</center>

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, saiba como criar um localizador de arquivo básico com o Azure Maps. O localizador de loja que vai criar neste tutorial, pode ter toda a funcionalidade de que precisa. Pode adicionar funcionalidades à sua localização de arquivo ou utilizar mais funcionalidades avançadas para uma experiência de usuário mais personalizada: 

> [!div class="checklist"]
> * Ativar [sugestões enquanto digita](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI) na caixa de pesquisa.  
> * Adicione [suporte para vários idiomas](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization). 
> * Permitir que o usuário [filtrar localizações ao longo de uma rota](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route). 
> * Adicionar a capacidade de [defina filtros](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property). 
> * Adicione suporte para especificar um valor inicial de pesquisa através de uma cadeia de consulta. Ao incluir esta opção na sua localização de arquivo, os usuários podem marcar e compartilhar pesquisas. Ele também fornece um método fácil para passe pesquisas para esta página a partir de outra página.  
> * Implementar o localizador de arquivo como um [aplicação de Web do serviço de aplicações do Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html). 
> * Store seus dados numa base de dados e procure locais próximos. Para obter mais informações, consulte a [descrição geral de tipos de dados geográficos do SQL Server](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-2017) e [consultar dados geográficos para o vizinho mais próximo](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-2017).

Pode aceder ao código de exemplo deste tutorial aqui:

> [Criar um localizador de arquivo com o Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)

[Veja o exemplo aqui ao vivo](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)

Para saber mais sobre a cobertura e as capacidades do Azure Maps:

> [!div class="nextstepaction"]
> [Zoom levels and tile grid ](zoom-levels-and-tile-grid.md) (Níveis de zoom e grelha de mosaico)

Para ver mais exemplos de código e uma experiência interativa de programação:

> [!div class="nextstepaction"]
> [How to use the map control](how-to-use-map-control.md) (Como utilizar o controlo de mapa)
