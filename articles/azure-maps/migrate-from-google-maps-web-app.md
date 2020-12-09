---
title: Tutorial - Migrar uma aplicação web do Google Maps para o Microsoft Azure Maps
description: Tutorial sobre como migrar uma aplicação web do Google Maps para o Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: 4dee8de8f42b78ecdab9d9e15bb277d58fa8ba70
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905065"
---
# <a name="tutorial---migrate-a-web-app-from-google-maps"></a>Tutorial - Migrar uma aplicação web do Google Maps

A maioria das aplicações web, que utilizam o Google Maps, estão a usar o SDK JavaScript do Google Maps V3. O Azure Maps Web SDK é o SDK baseado em Azure adequado para migrar. O Azure Maps Web SDK permite personalizar mapas interativos com o seu próprio conteúdo e imagens. Pode executar a sua aplicação em aplicações web ou móveis. Este controlo utiliza o WebGL, permitindo-lhe compor grandes conjuntos de dados com elevado desempenho. Desenvolva-se com este SDK utilizando JavaScript ou TypeScript. Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Carregue um mapa
> * Localize um mapa
> * Adicione marcadores, polilinas e polígonos.
> * Mostrar informações numa janela popup ou info
> * Carregar e exibir dados KML e GeoJSON
> * Marcadores de cluster
> * Sobrepor uma camada de azulejo
> * Mostrar dados de tráfego
> * Adicione uma sobreposição de solo

Também vai aprender: 

> [!div class="checklist"]
> * Como realizar tarefas comuns de mapeamento usando o Azure Maps Web SDK
> * Melhores práticas para melhorar o desempenho e a experiência do utilizador
> * Dicas sobre como tornar a sua aplicação usando mais funcionalidades avançadas disponíveis no Azure Maps

Se migrar uma aplicação web existente, verifique se está a utilizar uma biblioteca de controlo de mapas de código aberto. Exemplos de biblioteca de controlo de mapas de código aberto são: Césio, Folheto e OpenLayers. Ainda pode migrar a sua aplicação, mesmo que utilize uma biblioteca de controlo de mapas de código aberto, e não queira utilizar o Azure Maps Web SDK. Nesse caso, ligue a sua aplicação aos serviços de azulejos Azure Maps[road tiles](/rest/api/maps/render/getmaptile) \| [(azulejos de telhas](/rest/api/maps/render/getmapimagerytile)de estrada). Os seguintes pontos detalham como usar o Azure Maps em algumas bibliotecas de controlo de mapas de código aberto comumente utilizadas.

- Cesium - Um controlo de mapa 3D para a web. [Amostra de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Documentação](https://cesiumjs.org/)
- Folheto – Controlo de mapa 2D leve para a web. [Amostra de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Documentação](https://leafletjs.com/)
- OpenLayers - Um controlo de mapa 2D para a web que suporta projeções. [Amostra de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Documentação](https://openlayers.org/)

Se desenvolver usando uma estrutura JavaScript, um dos seguintes projetos de código aberto pode ser útil:

- [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps) - Angular 10 wrapper em torno de mapas Azure.
- [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) - Um componente Blazor Azure Maps.
- [Componente de reação do Azure Maps](https://github.com/WiredSolutions/react-azure-maps) - Um invólucro de reação para o controlo Azure Maps.
- [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) - Um componente Azure Maps para aplicação Vue.

## <a name="prerequisites"></a>Pré-requisitos 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](how-to-manage-authentication.md)

## <a name="key-features-support"></a>Suporte de funcionalidades principais

A tabela lista as principais funcionalidades da API no Google Maps V3 JavaScript SDK e na funcionalidade API suportada no Azure Maps Web SDK.

| Funcionalidade do Google Maps     | Suporte SDK web Azure Maps |
|-------------------------|:--------------------------:|
| Marcadores                 | ✓                          |
| Agrupamento de marcadores       | ✓                          |
| Polígonos & Poligóis    | ✓                          |
| Camadas de dados             | ✓                          |
| Sobreposições de solo         | ✓                          |
| Mapas de calor               | ✓                          |
| Camadas de azulejos             | ✓                          |
| Camada KML               | ✓                          |
| Ferramentas de desenho           | ✓                          |
| Serviço geocoder        | ✓                          |
| Serviço de direções      | ✓                          |
| Serviço Matrix distância | ✓                          |
| Serviço de elevação       | ✓                          |

## <a name="notable-differences-in-the-web-sdks"></a>Diferenças notáveis nos SDKs web

Seguem-se algumas diferenças fundamentais entre os SDKs web do Google Maps e Azure Maps, para ter em conta:

- Além de fornecer um ponto final hospedado para aceder ao Azure Maps Web SDK, está disponível um pacote NPM. Incorporar o pacote Web SDK em aplicativos. Para mais informações, consulte esta [documentação.](how-to-use-map-control.md) Este pacote também inclui definições typeScript.
- Primeiro, é necessário criar uma instância da classe Map em Azure Maps. Aguarde que os mapas `ready` ou `load` evento disparem antes de interagir programaticamente com o mapa. Esta ordem garantirá que todos os recursos do mapa foram carregados e estão prontos para serem acedidos.
- Ambas as plataformas utilizam um sistema de inclinação semelhante para os mapas base. Os azulejos do Google Maps têm 256 pixels de dimensão; no entanto, os azulejos em Azure Maps são 512 pixels em dimensão. Para obter a mesma vista de mapa no Azure Maps que o Google Maps, subtrai o nível de zoom do Google Maps pelo número um no Azure Maps.
- As coordenadas no Google Maps são referidas como `latitude,longitude` , enquanto o Azure Maps utiliza `longitude,latitude` . O formato Azure Maps está alinhado com o padrão `[x, y]` , que é seguido pela maioria das plataformas GIS.
- As formas no Azure Maps Web SDK são baseadas no esquema geoJSON. As classes de ajudantes são expostas através do [ *atlas.data* namespace](/javascript/api/azure-maps-control/atlas.data). Há também o [*atlas. Classe de*](/javascript/api/azure-maps-control/atlas.shape) forma. Utilize esta classe para embrulhar objetos GeoJSON, para facilitar a atualização e manutenção dos dados de forma bindable.
- As coordenadas nos Mapas Azure são definidas como objetos de posição. Uma coordenada é especificada como um conjunto de números no formato `[longitude,latitude]` . Ou, é especificado usando novo atlas.data.Position (longitude, latitude).
    > [!TIP]
    > A classe Position tem um método de ajuda estático para importar coordenadas que estão em formato "latitude, longitude". O método [atlas.data.position.fromLatLng](/javascript/api/azure-maps-control/atlas.data.position) pode muitas vezes ser substituído pelo `new google.maps.LatLng` método no código do Google Maps.
- Em vez de especificar informações de estilo em cada formato que é adicionado ao mapa, o Azure Maps separa os estilos dos dados. Os dados são armazenados numa fonte de dados e estão ligados a camadas de renderização. O código Azure Maps utiliza fontes de dados para render os dados. Esta abordagem proporciona um benefício de desempenho melhorado. Além disso, muitas camadas suportam o estilo orientado por dados onde a lógica do negócio pode ser adicionada às opções de estilo de camada. Este suporte altera a forma como as formas individuais são renderizadas dentro de uma camada com base em propriedades definidas na forma.

## <a name="web-sdk-side-by-side-examples"></a>Exemplos web SDK lado a lado

Esta recolha tem amostras de código para cada plataforma, e cada amostra cobre uma caixa de uso comum. Destina-se a ajudá-lo a migrar a sua aplicação web do Google Maps V3 JavaScript SDK para o Azure Maps Web SDK. As amostras de código relacionadas com aplicações web são fornecidas no JavaScript. No entanto, o Azure Maps também fornece definições TypeScript como uma opção adicional através de um [módulo NPM.](how-to-use-map-control.md)

**Tópicos**

- [Carregue um mapa](#load-a-map)
- [Localização do mapa](#localizing-the-map)
- [Definição da vista do mapa](#setting-the-map-view)
- [Adicionar um marcador](#adding-a-marker)
- [Adicionar um marcador personalizado](#adding-a-custom-marker)
- [Adicionar uma polilina](#adding-a-polyline)
- [Adicionar um polígono](#adding-a-polygon)
- [Mostrar uma janela de informação](#display-an-info-window)
- [Importar um ficheiro GeoJSON](#import-a-geojson-file)- 
- [Agrupamento de marcadores](#marker-clustering)
- [Adicione um mapa de calor](#add-a-heat-map)
- [Sobrepor uma camada de azulejo](#overlay-a-tile-layer)
- [Mostrar dados de tráfego](#show-traffic-data)
- [Adicione uma sobreposição de solo](#add-a-ground-overlay)
- [Adicione dados KML ao mapa](#add-kml-data-to-the-map)

### <a name="load-a-map"></a>Carregue um mapa

Ambos os SDKs têm os mesmos passos para carregar um mapa:

- Adicione uma referência ao Map SDK.
- Adicione uma `div` etiqueta ao corpo da página, que funcionará como um espaço reservado para o mapa.
- Crie uma função JavaScript que é chamada quando a página estiver carregada.
- Crie um exemplo da respetiva classe de mapas.

**Algumas diferenças fundamentais**

- O Google Maps requer uma chave de conta a especificar na referência do script da API. As credenciais de autenticação para Azure Maps são especificadas como opções da classe de mapa. Esta credencial pode ser uma chave de subscrição ou informações do Azure Ative Directory.
- O Google Maps aceita uma função de retorno na referência de script da API, que é usada para chamar uma função de inicialização para carregar o mapa. Com o Azure Maps, o evento de carga da página deve ser usado.
- Ao fazer referência ao `div` elemento em que o mapa será renderizado, a classe em `Map` Azure Maps apenas requer o valor enquanto o `id` Google Maps requer um `HTMLElement` objeto.
- As coordenadas em Azure Maps são definidas como objetos de posição, que podem ser especificados como uma simples matriz de números no formato `[longitude, latitude]` .
- O nível de zoom no Azure Maps é um nível inferior ao nível de zoom no Google Maps. Esta discrepância deve-se à diferença nos tamanhos do sistema de inclinação das duas plataformas.
- O Azure Maps não adiciona quaisquer controlos de navegação à tela do mapa. Então, por padrão, um mapa não tem botões de zoom e botões de estilo de mapa. Mas, existem opções de controlo para adicionar um picker de estilo de mapa, botões de zoom, compass ou controlo de rotação, e um controlo de tom.
- Um manipulador de eventos é adicionado no Azure Maps para monitorizar o `ready` caso da instância do mapa. Este evento disparará quando o mapa terminar de carregar o contexto WebGL e todos os recursos necessários. Adicione qualquer código que pretenda executar depois de o mapa completar o carregamento, a este manipulador de eventos.

Os exemplos básicos abaixo usam o Google Maps para carregar um mapa centrado em Nova Iorque nas coordenadas. A longitude: -73.985, latitude: 40.747, e o mapa está ao nível de zoom de 12.

#### <a name="before-google-maps"></a>Antes: Google Maps

Exibir um Mapa do Google centrado e ampliado sobre um local.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Executar este código num browser apresentará um mapa que se parece com a seguinte imagem:

![Mapas simples do Google](media/migrate-google-maps-web-app/simple-google-map.png)

#### <a name="after-azure-maps"></a>Depois: Mapas Azure

Carregue um mapa com a mesma vista em Azure Maps juntamente com um controlo de estilo de mapa e botões de zoom.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Executar este código num browser apresentará um mapa que se parece com a seguinte imagem:

![Mapas simples do Azure](media/migrate-google-maps-web-app/simple-azure-maps.png)

Encontre documentação detalhada sobre como configurar e utilizar o controlo do mapa do Azure Maps numa aplicação web, clicando [aqui.](how-to-use-map-control.md)

> [!NOTE]
> Ao contrário do Google Maps, o Azure Maps não requer um centro inicial e um nível de zoom para carregar o mapa. Se esta informação não for fornecida ao carregar o mapa, os mapas do Azure tentarão determinar a cidade do utilizador. Vai centrar-se e ampliar o mapa.

**Recursos adicionais:**

- O Azure Maps também fornece controlos de navegação para rodar e lançar a vista do mapa, como documentado [aqui.](map-add-controls.md)

### <a name="localizing-the-map"></a>Localização do mapa

Se o seu público estiver espalhado por vários países/regiões ou falar línguas diferentes, a localização é importante.

#### <a name="before-google-maps"></a>Antes: Google Maps

Para localizar o Google Maps, adicione parâmetros de linguagem e região.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Aqui está um exemplo do Google Maps com o idioma definido para "fr-FR".

![Localização do Google Maps](media/migrate-google-maps-web-app/google-maps-localization.png)

#### <a name="after-azure-maps"></a>Depois: Mapas Azure

O Azure Maps fornece duas maneiras diferentes de definir a linguagem e a visão regional do mapa. A primeira opção é adicionar esta informação ao espaço de nome *do atlas* global. Resultará em todas as instâncias de controlo do mapa na sua aplicação por defeito nestas definições. O que se segue define a língua para o francês ("fr-FR") e a visão regional para "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

A segunda opção é passar esta informação para as opções do mapa ao carregar o mapa. Assim:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Com o Azure Maps, é possível carregar várias instâncias de mapas na mesma página com diferentes configurações de idioma e região. Também é possível atualizar estas definições no mapa depois de ter sido carregado. 

Encontre uma lista detalhada de [línguas apoiadas](supported-languages.md) no Azure Maps.

Aqui está um exemplo de Azure Maps com o idioma definido para "fr" e a região de utilizadores definida como "fr-FR".

![Localização Azure Maps](media/migrate-google-maps-web-app/azure-maps-localization.png)

### <a name="setting-the-map-view"></a>Definição da vista do mapa

Os mapas dinâmicos tanto no Azure como no Google Maps podem ser movidos programáticamente para novas localizações geográficas. Para tal, ligue para as funções apropriadas no JavaScript. Os exemplos mostram como fazer o mapa exibir imagens aéreas de satélite, centrar o mapa sobre uma localização e alterar o nível de zoom para 15 no Google Maps. São utilizadas as seguintes coordenadas de localização: longitude: -111.0225 e latitude: 35.0272.

> [!NOTE]
> O Google Maps utiliza azulejos com 256 pixels em dimensões, enquanto o Azure Maps utiliza um azulejo maior de 512 pixels. Assim, o Azure Maps requer menos pedidos de rede para carregar a mesma área de mapa que o Google Maps. Devido à forma como as pirâmides de azulejos funcionam nos controlos de mapas, é necessário subtrair o nível de zoom utilizado no Google Maps pelo número um ao utilizar o Azure Maps. Esta operação aritmética garante que azulejos maiores no Azure Maps tornam a mesma área de mapa que no Google Maps,

#### <a name="before-google-maps"></a>Antes: Google Maps

Mova o controlo do mapa do Google Maps usando o `setOptions` método. Este método permite especificar o centro do mapa e um nível de zoom.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

![Vista definida do Google Maps](media/migrate-google-maps-web-app/google-maps-set-view.png)

#### <a name="after-azure-maps"></a>Depois: Mapas Azure

Em Azure Maps, altere a posição do mapa usando o `setCamera` método e altere o estilo do mapa usando o `setStyle` método. As coordenadas em Azure Maps estão em formato "longitude, latitude", e o valor do nível de zoom é subtraído por um.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

![Vista definida Azure Maps](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)

**Recursos adicionais:**

- [Escolher um estilo de mapa](choose-map-style.md)
- [Estilos de mapas suportados](supported-map-styles.md)

### <a name="adding-a-marker"></a>Adicionar um marcador

No Azure Maps, existem várias formas de os dados de pontos poderem ser renderizados no mapa:

- **Marcadores HTML** – Faz pontos utilizando elementos DOM tradicionais. HTML Os marcadores suportam arrastar.
- **Camada de Símbolo** – Renderiza pontos com um ícone ou texto dentro do contexto WebGL.
- **Camada de Bolha** – Renderiza pontos como círculos no mapa. O raios dos círculos pode ser dimensionado com base em propriedades nos dados.

Rendere as camadas de símbolo e as camadas de bolha dentro do contexto WebGL. Ambas as camadas podem renderizar grandes conjuntos de pontos no mapa. Estas camadas requerem que os dados sejam armazenados numa fonte de dados. As fontes de dados e as camadas de renderização devem ser adicionadas ao mapa após o `ready` evento ter disparado. Os marcadores HTML são renderizados como elementos DOM dentro da página, e não utilizam uma fonte de dados. Quanto mais elementos DOM uma página tem, mais lenta a página se torna. Se renderizar mais de algumas centenas de pontos num mapa, é recomendado usar uma das camadas de renderização.

Vamos adicionar um marcador ao mapa com o número 10 sobreposto como uma etiqueta. Use longitude: -0.2 e latitude: 51.5.

#### <a name="before-google-maps"></a>Antes: Google Maps

Com o Google Maps, adicione marcadores ao mapa usando a `google.maps.Marker` classe e especifique o mapa como uma das opções.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

![Marcador do Google Maps](media/migrate-google-maps-web-app/google-maps-marker.png)

**Depois: Mapas Azure usando marcadores HTML**

Em Azure Maps, utilize marcadores HTML para mostrar um ponto no mapa. Os marcadores HTML são recomendados para apps que apenas precisam de apresentar um pequeno número de pontos no mapa. Para utilizar um marcador HTML, crie uma instância da `atlas.HtmlMarker` classe. Desa estale as opções de texto e posição e adicione o marcador ao mapa utilizando o `map.markers.add` método.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

![Marcador HTML Azure Maps](media/migrate-google-maps-web-app/azure-maps-html-marker.png)

**Depois: Mapas Azure usando uma camada de símbolo**

Para uma camada de símbolo, adicione os dados a uma fonte de dados. Anexe a fonte de dados à camada. Além disso, a fonte de dados e a camada devem ser adicionadas ao mapa após o `ready` evento ter disparado. Para tornar um valor de texto único acima de um símbolo, a informação de texto precisa de ser armazenada como uma propriedade do ponto de dados. A propriedade deve ser referenciada na `textField` opção da camada. Esta abordagem é um pouco mais trabalho do que usar marcadores HTML, mas melhor desempenho.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Camada de símbolo de Azure Maps](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)

**Recursos adicionais:**

- [Criar uma origem de dados](create-data-source-web-sdk.md)
- [Adicione uma camada de símbolo](map-add-pin.md)
- [Adicione uma camada de bolha](map-add-bubble-layer.md)
- [Dados de ponto do cluster](clustering-point-data-web-sdk.md)
- [Adicionar marcadores HTML](map-add-custom-html.md)
- [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-web-sdk.md)
- [Opções de ícone de camada de símbolo](/javascript/api/azure-maps-control/atlas.iconoptions)
- [Opção de texto de camada de símbolo](/javascript/api/azure-maps-control/atlas.textoptions)
- [Classe de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarker)
- [Opções de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-marker"></a>Adicionar um marcador personalizado

Pode utilizar imagens personalizadas para representar pontos num mapa. O mapa abaixo usa uma imagem personalizada para exibir um ponto no mapa. O ponto é mostrado na latitude: 51.5 e longitude: -0.2. A âncora compensa a posição do marcador, de modo que o ponto do ícone do pino de pressão se alinha com a posição correta no mapa.

<center>

![imagem de pushpin amarelo](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png</center>


#### <a name="before-google-maps"></a>Antes: Google Maps

Crie um marcador personalizado especificando um `Icon` objeto que contenha `url` a imagem. Especifique um `anchor` ponto para alinhar o ponto da imagem do pino com a coordenada no mapa. O valor de âncora no Google Maps é relativo ao canto superior esquerdo da imagem.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```


![Marcador personalizado do Google Maps](media/migrate-google-maps-web-app/google-maps-custom-marker.png)

**Depois: Mapas Azure usando marcadores HTML**

Para personalizar um marcador HTML, passe um HTML `string` ou `HTMLElement` à `htmlContent` opção do marcador. Utilize a `anchor` opção para especificar a posição relativa do marcador, em relação à coordenada de posição. Atribua um dos nove pontos de referência definidos à `anchor` opção. Os pontos definidos são: "centro", "superior", "inferior", "esquerdo", "direita", "superior-esquerdo", "superior-direita", "inferior-esquerdo", "inferior-direita". O conteúdo está ancorado no centro inferior do conteúdo html por predefinição. Para facilitar a migração do código do Google Maps, desempenhe-o `anchor` em "topo-de-esquerda", e depois use a `pixelOffset` opção com o mesmo offset utilizado no Google Maps. As compensações no Azure Maps movem-se na direção oposta das compensações no Google Maps. Então, multiplique as compensações por menos um.

> [!TIP]
> Adicione `pointer-events:none` como um estilo no conteúdo html para desativar o comportamento de arrasto padrão no Microsoft Edge, que irá exibir um ícone indesejado.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

![Marcador HTML personalizado do Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)

**Depois: Mapas Azure usando uma camada de símbolo**

As camadas de símbolo no Azure Maps também suportam imagens personalizadas. Primeiro, carregue a imagem nos recursos do mapa e atribua-a com uma identificação única. Referenciar a imagem na camada do símbolo. Utilize a `offset` opção para alinhar a imagem com o ponto correto no mapa. Utilize a `anchor` opção para especificar a posição relativa do símbolo, em relação às coordenadas de posição. Utilize um dos nove pontos de referência definidos. Estes pontos são: "centro", "superior", "inferior", "esquerdo", "direita", "superior-esquerdo", "superior-direita", "inferior-esquerdo", "inferior-direita". O conteúdo está ancorado no centro inferior do conteúdo html por predefinição. Para facilitar a migração do código do Google Maps, desempenhe-o `anchor` em "topo-de-esquerda", e depois use a `offset` opção com o mesmo offset utilizado no Google Maps. As compensações no Azure Maps movem-se na direção oposta das compensações no Google Maps. Então, multiplique as compensações por menos um.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps camada de símbolo de ícone personalizado](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</

> [!TIP]
> Para tornar os pontos personalizados avançados, utilize várias camadas de renderização juntas. Por exemplo, digamos que você quer ter múltiplos pinos que têm o mesmo ícone em diferentes círculos coloridos. Em vez de criar um monte de imagens para cada sobreposição de cores, adicione uma camada de símbolo em cima de uma camada de bolha. Tenha os pinos de pressão referenciar a mesma fonte de dados. Esta abordagem será mais eficiente do que criar e manter um conjunto de imagens diferentes.

**Recursos adicionais:**

- [Criar uma origem de dados](create-data-source-web-sdk.md)
- [Adicione uma camada de símbolo](map-add-pin.md)
- [Adicionar marcadores HTML](map-add-custom-html.md)
- [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-web-sdk.md)
- [Opções de ícone de camada de símbolo](/javascript/api/azure-maps-control/atlas.iconoptions)
- [Opção de texto de camada de símbolo](/javascript/api/azure-maps-control/atlas.textoptions)
- [Classe de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarker)
- [Opções de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Adicionar uma polilina

Utilize polilinas para representar uma linha ou caminho no mapa. Vamos criar um polilina tracejado no mapa.

#### <a name="before-google-maps"></a>Antes: Google Maps

A classe Polyline aceita um conjunto de opções. Passe uma série de coordenadas na `path` opção do polilina.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

![Poliline do Google Maps](media/migrate-google-maps-web-app/google-maps-polyline.png)

#### <a name="after-azure-maps"></a>Depois: Mapas Azure

Os polilinas são `LineString` chamados ou `MultiLineString` objetos. Estes objetos podem ser adicionados a uma fonte de dados e renderizados através de uma camada de linha. Adicione `LineString` a uma fonte de dados e, em seguida, adicione a fonte de dados a um para `LineLayer` renderizá-lo.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```
![Polilina Azure Maps](media/migrate-google-maps-web-app/azure-maps-polyline.png)

**Recursos adicionais:**

- [Adicione linhas ao mapa](map-add-line-layer.md)
- [Opções de camada de linha](/javascript/api/azure-maps-control/atlas.linelayeroptions)
- [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Adicionar um polígono

O Azure Maps e o Google Maps fornecem suporte semelhante para polígonos. Os polígonos são usados para representar uma área no mapa. Os exemplos a seguir mostram como criar um polígono que forma um triângulo baseado na coordenada central do mapa.

#### <a name="before-google-maps"></a>Antes: Google Maps

A classe Polygon aceita um conjunto de opções. Passe uma série de coordenadas para a `paths` opção do polígono.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

![Polígono do Google Maps](media/migrate-google-maps-web-app/google-maps-polygon.png)

#### <a name="after-azure-maps"></a>Depois: Mapas Azure

Adicione um `Polygon` ou um objeto a uma fonte de `MultiPolygon` dados. Torne o objeto no mapa usando camadas. Torne a área de um polígono usando uma camada de polígono. E, torne o contorno de um polígono usando uma camada de linha.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```
![Polígono Azure Maps](media/migrate-google-maps-web-app/azure-maps-polygon.png)

**Recursos adicionais:**

- [Adicione um polígono ao mapa](map-add-shape.md)
- [Adicione um círculo ao mapa](map-add-shape.md#add-a-circle-to-the-map)
- [Opções de camada de polígono](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
- [Opções de camada de linha](/javascript/api/azure-maps-control/atlas.linelayeroptions)
- [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Mostrar uma janela de informação

Informações adicionais para uma entidade podem ser exibidas no mapa como uma `google.maps.InfoWindow` classe no Google Maps. No Azure Maps, esta funcionalidade pode ser alcançada utilizando a `atlas.Popup` classe. Os próximos exemplos adicionam um marcador ao mapa. Quando o marcador é clicado, é apresentada uma janela de informação ou um pop-up.

#### <a name="before-google-maps"></a>Antes: Google Maps

Instantiizar uma janela de informação utilizando o `google.maps.InfoWindow` construtor.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```
![Popup do Google Maps](media/migrate-google-maps-web-app/google-maps-popup.png)

#### <a name="after-azure-maps"></a>Depois: Mapas Azure

Vamos usar o popup para mostrar informações adicionais sobre a localização. Passe um HTML `string` ou objeto para a `HTMLElement` `content` opção do popup. Se quiser, os popups podem ser exibidos independentemente de qualquer forma. Assim, os Popups requerem um `position` valor a ser especificado. Especifique o `position` valor. Para exibir um popup, ligue para o `open` método e passe o `map` popup em que o popup deve ser exibido.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```
![Popup Azure Maps](media/migrate-google-maps-web-app/azure-maps-popup.png)

> [!NOTE]
> Pode fazer a mesma coisa com um símbolo, bolha, linha ou camada de polígono, passando a camada escolhida para o código de evento dos mapas em vez de um marcador.

**Recursos adicionais:**

- [Adicionar um pop-up](map-add-popup.md)
- [Popup com Conteúdo de Mídia](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Popups em Formas](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Reutilizar Popup com múltiplos pinos](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Classe popup](/javascript/api/azure-maps-control/atlas.popup)
- [Opções popup](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="import-a-geojson-file"></a>Importar um ficheiro GeoJSON

O Google Maps suporta o carregamento e o estilo dinâmico dos dados da GeoJSON através da `google.maps.Data` classe. A funcionalidade desta classe alinha-se muito mais com o estilo de dados do Azure Maps. Mas há uma diferença fundamental. Com o Google Maps, especifica uma função de retorno. A lógica de negócio para estilizar cada recurso que processou individualmente no fio da UI. Mas no Azure Maps, as camadas suportam especificar expressões baseadas em dados como opções de styling. Estas expressões são processadas no tempo de renderização num fio separado. A abordagem Azure Maps melhora o desempenho da renderização. Esta vantagem é notada quando conjuntos de dados maiores precisam de ser renderizados rapidamente.

Os exemplos seguintes carregam um feed GeoJSON de todos os terramotos nos últimos sete dias do USGS. Os dados dos terramotos são feitos como círculos escalonado no mapa. A cor e escala de cada círculo baseia-se na magnitude de cada terramoto, que é armazenado na `"mag"` propriedade de cada recurso no conjunto de dados. Se a magnitude for maior ou igual a cinco, o círculo será vermelho. Se for maior ou igual a três, mas menos de cinco, o círculo será laranja. Se for menos de três, o círculo será verde. O raio de cada círculo será o exponencial da magnitude multiplicada por 0,1.

#### <a name="before-google-maps"></a>Antes: Google Maps

Especifique uma única função de retorno no `map.data.setStyle` método. Dentro da função de retorno, aplique a lógica de negócio a cada recurso. Carregue o alimento GeoJSON com o `map.data.loadGeoJson` método.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Google Maps GeoJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)

#### <a name="after-azure-maps"></a>Depois: Mapas Azure

GeoJSON é o tipo de dados base em Azure Maps. Importe-o numa fonte de dados utilizando o `datasource.importFromUrl` método. Use uma camada de bolha. A camada de bolha fornece funcionalidade para renderização de círculos em escala, com base nas propriedades das funcionalidades numa fonte de dados. Em vez de ter uma função de retorno, a lógica de negócio é convertida numa expressão e passada para as opções de estilo. Expressões definem como funciona a lógica do negócio. As expressões podem ser transmitidas para outro fio e avaliadas com os dados da funcionalidade. Várias fontes de dados e camadas podem ser adicionadas ao Azure Maps, cada um com lógica de negócio diferente. Esta funcionalidade permite que vários conjuntos de dados sejam renderizados no mapa de diferentes maneiras.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```



![Azure Maps GeoJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)

**Recursos adicionais:**

- [Adicione uma camada de símbolo](map-add-pin.md)
- [Adicione uma camada de bolha](map-add-bubble-layer.md)
- [Dados de ponto do cluster](clustering-point-data-web-sdk.md)
- [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Agrupamento de marcadores

Ao visualizar muitos pontos de dados no mapa, os pontos podem sobrepor-se uns aos outros. Sobreposição faz com que o mapa pareça desordenado, e o mapa torna-se difícil de ler e usar. Os dados de pontos de agrupamento são o processo de combinar pontos de dados que estão próximos uns dos outros e representá-los no mapa como um único ponto de dados agrupado. À medida que o utilizador faz zoom no mapa, os clusters separam-se nos seus pontos de dados individuais. Os dados do cluster apontam para melhorar a experiência do utilizador e o desempenho do mapa.

Nos exemplos seguintes, o código carrega um feed GeoJSON de dados de terramotos da semana passada e adiciona-o ao mapa. Os aglomerados são renderizados como círculos dimensionados e coloridos. A escala e a cor dos círculos dependem do número de pontos que contêm.

> [!NOTE]
> O Google Maps e o Azure Maps utilizam algoritmos de agrupamento ligeiramente diferentes. Como tal, por vezes, a distribuição de pontos nos clusters varia.

#### <a name="before-google-maps"></a>Antes: Google Maps

Utilize a biblioteca MarkerCluster para marcadores de agrupamento. Os ícones do cluster estão limitados a imagens, que têm os números de um a cinco como nome. Estão hospedados no mesmo diretório.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```



![Agrupamento do Google Maps](media/migrate-google-maps-web-app/google-maps-clustering.png)

#### <a name="after-azure-maps"></a>Depois: Mapas Azure

Adicione e gerencie os dados numa fonte de dados. Ligue fontes de dados e camadas e, em seguida, torne os dados. A `DataSource` aula em Azure Maps oferece várias opções de clustering.

- `cluster` – Informa a fonte de dados para o cluster de dados.
- `clusterRadius` - O raio dos pixels para os pontos de agrupamento juntos.
- `clusterMaxZoom` - O nível máximo de zoom em que ocorre o agrupamento. Se fizer zoom em mais do que este nível, todos os pontos são renderizados como símbolos.
- `clusterProperties` - Define propriedades personalizadas que são calculadas usando expressões contra todos os pontos dentro de cada cluster e adicionadas às propriedades de cada ponto de cluster.

Quando o agrupamento estiver ativado, a fonte de dados enviará pontos de dados agrupados e não agrupados para camadas para renderização. A fonte de dados é capaz de agrupar centenas de milhares de pontos de dados. Um ponto de dados agrupado tem as seguintes propriedades:

| Nome da propriedade             | Tipo    | Descrição   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Indica se a característica representa um cluster. |
| `cluster_id`              | string  | Um ID único para o cluster que pode ser usado com o `getClusterExpansionZoom` DataSource, `getClusterChildren` e `getClusterLeaves` métodos. |
| `point_count`             | número  | O número de pontos que o cluster contém.  |
| `point_count_abbreviated` | string  | Uma corda que abrevia o `point_count` valor se for longa. (por exemplo, 4.000 torna-se 4K)  |

A `DataSource` classe tem a seguinte função de ajudante para aceder a informações adicionais sobre um cluster utilizando o `cluster_id` .

| Método | Tipo de retorno | Descrição |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Prometa &lt; &lt; geometria de característica &lt; de matriz, qualquer &gt; \| forma&gt;&gt; | Recupera as crianças do aglomerado dado no próximo nível de zoom. Estas crianças podem ser uma combinação de formas e subclusters. Os subclusters serão funcionalidades com propriedades correspondentes a ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | &lt;Número de promessa&gt; | Calcula um nível de zoom no qual o cluster começará a expandir-se ou a separar-se. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Prometa &lt; &lt; geometria de característica &lt; de matriz, qualquer &gt; \| forma&gt;&gt; | Recupera todos os pontos num aglomerado. Defina o `limit` para devolver um subconjunto dos pontos e use a `offset` página para página através dos pontos. |

Ao renderizar dados agrupados no mapa, é geralmente melhor usar duas ou mais camadas. O exemplo a seguir utiliza três camadas. Uma camada de bolha para desenhar círculos coloridos escalonados com base no tamanho dos clusters. Uma camada de símbolo para tornar o tamanho do cluster como texto. E usa uma segunda camada de símbolo para tornar os pontos desagrupar. Há muitas outras formas de renderizar dados agrupados. Para obter mais informações, consulte a documentação de [dados do ponto cluster.](clustering-point-data-web-sdk.md)

Importe diretamente os dados da GeoJSON utilizando a `importDataFromUrl` função na classe, dentro do `DataSource` mapa do Azure Maps.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```



![Agrupamento de mapas Azure](media/migrate-google-maps-web-app/azure-maps-clustering.png)

**Recursos adicionais:**

- [Adicione uma camada de símbolo](map-add-pin.md)
- [Adicione uma camada de bolha](map-add-bubble-layer.md)
- [Dados de ponto do cluster](clustering-point-data-web-sdk.md)
- [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Adicione um mapa de calor

Os mapas de calor, também conhecidos como mapas de densidade de pontos, são um tipo de visualização de dados. São usados para representar a densidade de dados usando uma gama de cores. E são frequentemente usados para mostrar os dados "pontos quentes" num mapa. Os mapas de calor são uma ótima maneira de tornar grandes conjuntos de dados de pontos.

Os exemplos seguintes carregam um feed GeoJSON de todos os terramotos no mês passado, do USGS, e torna-os como um mapa de calor ponderado. A `"mag"` propriedade é usada como o peso.

#### <a name="before-google-maps"></a>Antes: Google Maps

Para criar um mapa de calor, carregue a biblioteca "visualização" adicionando `&libraries=visualization` ao URL de script API. A camada de mapa de calor no Google Maps não suporta diretamente os dados da GeoJSON. Primeiro, descarregue os dados e converta-os numa série de pontos de dados ponderados:

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```



![Mapa de calor do Google Maps](media/migrate-google-maps-web-app/google-maps-heatmap.png)

#### <a name="after-azure-maps"></a>Depois: Mapas Azure

Carregue os dados do GeoJSON numa fonte de dados e ligue a fonte de dados a uma camada de mapa de calor. A propriedade que será usada para o peso pode ser passada para a `weight` opção usando uma expressão. Importe diretamente os dados da GeoJSON para a Azure Maps utilizando a `importDataFromUrl` função na `DataSource` classe.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```



![Mapa de calor Azure Maps](media/migrate-google-maps-web-app/azure-maps-heatmap.png)

**Recursos adicionais:**

- [Adicionar uma camada de mapa térmico](map-add-heat-map-layer.md)
- [Classe de camada de mapa de calor](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
- [Opções de camada de mapa de calor](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
- [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Sobrepor uma camada de azulejo

As camadas de azulejos no Azure Maps são conhecidas como sobreposições de imagem no Google Maps. As camadas de azulejos permitem sobrepor-se a grandes imagens que foram divididas em imagens de azulejos menores, que se alinham com o sistema de inclinação de mapas. Esta abordagem é comumente usada para sobrepor grandes imagens ou grandes conjuntos de dados.

Os exemplos a seguir sobrepõem uma camada de azulejos de radar meteorológico do Iowa Environmental Mesonet da Universidade Estadual de Iowa.

#### <a name="before-google-maps"></a>Antes: Google Maps

No Google Maps, as camadas de azulejos podem ser criadas utilizando a `google.maps.ImageMapType` classe.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```



![Camada de azulejo do Google Maps](media/migrate-google-maps-web-app/google-maps-tile-layer.png)

#### <a name="after-azure-maps"></a>Depois: Mapas Azure

Adicione uma camada de azulejo ao mapa da mesma forma que qualquer outra camada. Utilize um URL formatado que tenha em x, y, suportes para lugares de zoom; `{x}`, `{y}` `{z}`  para dizer a camada onde aceder aos azulejos. As camadas de azulejos Azure Maps também `{quadkey}` `{bbox-epsg-3857}` suportam, e espaços `{subdomain}` reservados.

> [!TIP]
> Nas camadas Azure Maps podem ser facilmente renderizadas abaixo de outras camadas, incluindo camadas de mapas base. Muitas vezes é desejável tornar as camadas de azulejos abaixo dos rótulos dos mapas para que sejam fáceis de ler. O `map.layers.add` método toma um segundo parâmetro que é o id da camada em que inserir a nova camada abaixo. Para inserir uma camada de azulejos abaixo das etiquetas do mapa, utilize este código: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```



![Camada de azulejos Azure Maps](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)

> [!TIP]
> Os pedidos de azulejos podem ser capturados usando a `transformRequest` opção do mapa. Isto permitir-lhe-á modificar ou adicionar cabeçalhos ao pedido, se desejar.

**Recursos adicionais:**

- [Adicionar camadas de mosaico](map-add-tile-layer.md)
- [Classe de camada de azulejos](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
- [Opções de camada de azulejos](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Mostrar dados de tráfego

Os dados de tráfego podem ser sobrepostos tanto no Azure como no Google Maps.

#### <a name="before-google-maps"></a>Antes: Google Maps

Sobrepor dados de tráfego no mapa usando a camada de tráfego.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```



![Tráfego do Google Maps](media/migrate-google-maps-web-app/google-maps-traffic.png)

#### <a name="after-azure-maps"></a>Depois: Mapas Azure

O Azure Maps oferece várias opções diferentes para exibir tráfego. Exiba incidentes de tráfego, tais como encerramentos de estradas e acidentes como ícones no mapa. Sobrepor o fluxo de tráfego e as estradas codificadas a cores no mapa. As cores podem ser modificadas com base no limite de velocidade registado, em relação ao atraso normal esperado, ou atraso absoluto. Os dados de incidentes no Azure Maps atualizam cada minuto e os dados de fluxo atualizam-se a cada dois minutos.

Atribua os valores procurados para `setTraffic` opções.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```



![Tráfego Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic.png)

Se clicar num dos ícones de tráfego no Azure Maps, são apresentadas informações adicionais num pop-up.



![Incidente de tráfego de Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)

**Recursos adicionais:**

- [Mostrar tráfego no mapa](map-show-traffic.md)
- [Opções de sobreposição de tráfego](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Adicione uma sobreposição de solo

Tanto o Azure como o Google Maps suportam a sobreposição de imagens georreferenciadas no mapa. As imagens georreferenciadas movem-se e escalam à medida que se faz o painel e faz zoom no mapa. No Google Maps, as imagens georreferenciadas são conhecidas como sobreposições terrestres enquanto no Azure Maps são referidas como camadas de imagem. São ótimos para construir plantas de piso, sobrepor mapas antigos, ou imagens de um drone.

#### <a name="before-google-maps"></a>Antes: Google Maps

Especifique o URL para a imagem que pretende sobrepor e uma caixa de delimitação para ligar a imagem no mapa. Este exemplo sobrepõe-se a uma imagem de mapa de [Newark New Jersey de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Executar este código num browser apresentará um mapa que se parece com a seguinte imagem:

![Sobreposição de imagem do Google Maps](media/migrate-google-maps-web-app/google-maps-image-overlay.png)

#### <a name="after-azure-maps"></a>Depois: Mapas Azure

Use a `atlas.layer.ImageLayer` classe para sobrepor imagens georreferenciadas. Esta classe requer um URL para uma imagem e um conjunto de coordenadas para os quatro cantos da imagem. A imagem deve ser hospedada no mesmo domínio ou ter CORs ativados.

> [!TIP]
> Se tiver apenas informações de norte, sul, leste, oeste e rotação, e não tiver coordenadas para cada canto da imagem, pode usar o [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) método estático.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```



![Sobreposição de imagem de Azure Maps](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)

**Recursos adicionais:**

- [Sobrepor uma imagem](map-add-image-layer.md)
- [Classe de camada de imagem](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>Adicione dados KML ao mapa

Tanto o Azure como o Google Maps podem importar e renderizar dados de KML, KMZ e GeoRSS no mapa. O Azure Maps também suporta GPX, GML, ficheiros de CSV espaciais, GeoJSON, Texto Bem Conhecido (WKT), serviços de Web-Mapping (WMS), serviços de Web-Mapping de azulejos (WMTS) e Serviços de Funcionalidade Web (WFS). O Azure Maps lê os ficheiros localmente na memória e na maioria dos casos pode lidar com ficheiros KML muito maiores. 

#### <a name="before-google-maps"></a>Antes: Google Maps


```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(0, 0),
                zoom: 1
            });

             var layer = new google.maps.KmlLayer({
              url: 'https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml',
              map: map
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Executar este código num browser apresentará um mapa que se parece com a seguinte imagem:

![Google Maps KML](media/migrate-google-maps-web-app/google-maps-kml.png)

#### <a name="after-azure-maps"></a>Depois: Mapas Azure

No Azure Maps, o GeoJSON é o principal formato de dados utilizado na web SDK, os formatos de dados espaciais adicionais podem ser facilmente integrados na utilização do [módulo IO espacial.](/javascript/api/azure-maps-spatial-io/) Este módulo tem funções tanto para ler como escrever dados espaciais e também inclui uma camada de dados simples que pode facilmente renderizar dados de qualquer um destes formatos de dados espaciais. Para ler os dados num ficheiro de dados espaciais, passe num URL ou dados brutos como string ou blob para a `atlas.io.read` função. Isto irá devolver todos os dados analisados do ficheiro que podem ser adicionados ao mapa. O KML é um pouco mais complexo do que a maioria do formato de dados espaciais, pois inclui muito mais informações de estilo. A `SpatialDataLayer` classe suporta a renderização da maioria destes estilos, no entanto as imagens de ícones têm de ser carregadas no mapa antes de carregar os dados da funcionalidade, e as sobreposições do solo têm de ser adicionadas como camadas ao mapa separadamente. Ao carregar dados através de um URL, deve ser hospedado num ponto final ativado por CORs, ou um serviço de procuração deve ser transmitido como uma opção para a função de leitura. 

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
            
                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```


![Azure Maps KML](media/migrate-google-maps-web-app/azure-maps-kml.png)</center>


**Recursos adicionais:**

- [função atlas.io.read](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
- [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
- [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

## <a name="additional-code-samples"></a>Amostras de código adicionais

Seguem-se algumas amostras de código adicionais relacionadas com a migração do Google Maps:

- [Ferramentas de desenho](map-add-drawing-toolbar.md)
- [Mapa limite para dois dedos panning](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Limite o zoom da roda de pergaminho](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Criar um controlo de ecrã completo](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Serviços:**

- [Utilizando o módulo de serviços Azure Maps](how-to-use-services-module.md)
- [Procurar pontos de interesse](map-search-location.md)
- [Obtenha informações a partir de uma coordenada (geocódigo inverso)](map-get-information-from-coordinate.md)
- [Mostrar as direções de A para B](map-route.md)
- [Pesse autosuggest com JQuery UI](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Google Maps V3 para Azure Maps Web SDK mapeamento de classe SDK

O apêndice seguinte fornece uma referência cruzada das classes comumente usadas no Google Maps V3 e o equivalente Azure Maps Web SDK.

### <a name="core-classes"></a>Classes Principais

| Google Maps   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [atlas. Mapa](/javascript/api/azure-maps-control/atlas.map)  |
| `google.maps.InfoWindow` | [atlas. Popup](/javascript/api/azure-maps-control/atlas.popup)  |
| `google.maps.InfoWindowOptions` | [atlas. PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Position](/javascript/api/azure-maps-control/atlas.data.position)  |
| `google.maps.LatLngBounds` | [atlas.data.BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox) |
| `google.maps.MapOptions`  | [atlas. Opções de Câmara](/javascript/api/azure-maps-control/atlas.cameraoptions)<br/>[atlas. Opções de Acessos de Câmara](/javascript/api/azure-maps-control/atlas.cameraboundsoptions)<br/>[atlas. Opções de Serviço](/javascript/api/azure-maps-control/atlas.serviceoptions)<br/>[atlas. Opções de Estilo](/javascript/api/azure-maps-control/atlas.styleoptions)<br/>[atlas. Opções de Utilização de Utilizador](/javascript/api/azure-maps-control/atlas.userinteractionoptions) |
| `google.maps.Point`  | [atlas. Pixel](/javascript/api/azure-maps-control/atlas.pixel)   |

## <a name="overlay-classes"></a>Aulas de sobreposição

| Google Maps  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [atlas.HtmlMarker](/javascript/api/azure-maps-control/atlas.htmlmarker)<br/>[atlas.data.Point](/javascript/api/azure-maps-control/atlas.data.point)  |
| `google.maps.MarkerOptions`  | [atlas.HtmiMarkerOptions](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)<br/>[atlas.layer.SymbolLayer](/javascript/api/azure-maps-control/atlas.layer.symbollayer)<br/>[atlas. SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)<br/>[atlas. IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)<br/>[atlas. Opções de Texto](/javascript/api/azure-maps-control/atlas.textoptions)<br/>[atlas.layer.BubbleLayer](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)<br/>[atlas. BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions) |
| `google.maps.Polygon`  | [atlas.data.Polygon](/javascript/api/azure-maps-control/atlas.data.polygon)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)<br/> [atlas. PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)<br/> [atlas.layer.LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)<br/> [atlas. LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)|
| `google.maps.Polyline` | [atlas.data.LineString](/javascript/api/azure-maps-control/atlas.data.linestring)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)<br/>[atlas. LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) |
| `google.maps.Circle`  | Ver [Adicionar um círculo ao mapa](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [atlas. TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)         |
| `google.maps.ImageMapTypeOptions` | [atlas. Opções TileLayer](/javascript/api/azure-maps-control/atlas.tilelayeroptions) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)<br/>[atlas. Opções ImageLayer](/javascript/api/azure-maps-control/atlas.imagelayeroptions) |

## <a name="service-classes"></a>Aulas de Serviço

O Azure Maps Web SDK inclui um módulo de serviços, que pode ser carregado separadamente. Este módulo envolve os serviços Azure Maps REST com uma API web e pode ser utilizado em aplicações JavaScript, TypeScript e Node.js.

| Google Maps | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](/javascript/api/azure-maps-rest/atlas.service.searchurl)  |
| `google.maps.GeocoderRequest`  | [atlas. Opções de Endereços de Busca](/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions)<br/>[atlas. SearchAddressRevrseOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions)<br/>[atlas. SearchAddressReverseCrossStreetOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions)<br/>[atlas. SearchAddressStructuredOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions)<br/>[atlas. SearchAlongRouteOptions](/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions)<br/>[atlas. SearchFuzzyOptions](/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions)<br/>[atlas. SearchInsideGeometryOptions](/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions)<br/>[atlas. SearchNearbyOptions](/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions)<br/>[atlas. Opções SearchPOI](/javascript/api/azure-maps-rest/atlas.service.searchpoioptions)<br/>[atlas. Opções searchPOICategory](/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](/javascript/api/azure-maps-rest/atlas.service.routeurl)  |
| `google.maps.DirectionsRequest`  | [atlas. Calcular Digestões](/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](/javascript/api/azure-maps-rest/atlas.service.searchurl)  |

## <a name="libraries"></a>Bibliotecas

As bibliotecas adicionam funcionalidade adicional ao mapa. Muitas destas bibliotecas estão no núcleo SDK de Azure Maps. Aqui estão algumas classes equivalentes para usar no lugar destas bibliotecas do Google Maps

| Google Maps           | Azure Maps   |
|-----------------------|--------------|
| Biblioteca de desenho       | [Módulo de ferramentas de desenho](set-drawing-options.md) |
| Biblioteca de geometria      | [atlas.math](/javascript/api/azure-maps-control/atlas.math)   |
| Biblioteca de visualização | [Camada de mapa de calor](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Azure Maps Web SDK:

> [!div class="nextstepaction"]
> [Como usar o controlo do mapa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Como usar o módulo de ferramentas de desenho](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Como utilizar o módulo de serviços](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Como usar o módulo IO espacial](how-to-use-spatial-io-module.md)