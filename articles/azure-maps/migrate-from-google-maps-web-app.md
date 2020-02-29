---
title: 'Tutorial: Migrar uma aplicação web do Google Maps Microsoft Azure Maps'
description: Como migrar uma aplicação web do Google Maps para o Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: bdbf2a975cbdc3d06745b9375c1e6f8e751ddfd6
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914099"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Migrar uma aplicação web do Google Maps

A maioria das aplicações web, que utilizam o Google Maps, estão a usar o Google Maps V3 JavaScript SDK. O Azure Maps Web SDK é o SDK baseado em Azure para migrar. O Azure Maps Web SDK permite personalizar mapas interativos com o seu próprio conteúdo e imagens. Pode executar a sua aplicação tanto na web como nas aplicações móveis. Este controlo utiliza o WebGL, permitindo-lhe compor grandes conjuntos de dados com elevado desempenho. Desenvolva-se com este SDK utilizando javaScript ou TypeScript.

Se migrar uma aplicação web existente, verifique se está a utilizar uma biblioteca de controlo de mapas de código aberto. Exemplos de biblioteca de controlo de mapas de código aberto são: Cesium, Folheto e OpenLayers. Ainda pode migrar a sua aplicação, mesmo que utilize uma biblioteca de controlo de mapas de código aberto, e não quer utilizar o Azure Maps Web SDK. Neste caso, ligue a sua aplicação aos serviços de azulejos Do Azure Maps[(telhas \|](https://docs.microsoft.com/rest/api/maps/render/getmaptile) [azulejos por satélite).](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) Os seguintes pontos detalham sobre como usar o Azure Maps em algumas bibliotecas de controlo de mapas de código aberto comumente utilizadas.

- Césio - Um controlo de mapas 3D para a web. [Amostra de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Documentação](https://cesiumjs.org/)
- Folheto – Controlo de mapas leve 2D para a web. [Amostra de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Documentação](https://leafletjs.com/)
- OpenLayers - Um controlo de mapa 2D para a web que suporta projeções. [Amostra de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Documentação](https://openlayers.org/)

## <a name="key-features-support"></a>Suporte de funcionalidades chave

A tabela lista as principais funcionalidades da API no Google Maps V3 JavaScript SDK e na funcionalidade API suportada no Azure Maps Web SDK.

| Recurso do Google Maps     | Suporte Web SDK do Azure Maps |
|-------------------------|:--------------------------:|
| Marcadores                 | ✓                          |
| Agrupamento de marcadores       | ✓                          |
| Polilines e Polígonos    | ✓                          |
| Camadas de dados             | ✓                          |
| Sobreposições terrestres         | ✓                          |
| Mapas de calor               | ✓                          |
| Camadas de azulejos             | ✓                          |
| Camada KML               | ✓                          |
| Ferramentas de desenho           | ✓                          |
| Serviço de Geocoder        | ✓                          |
| Serviço de direções      | ✓                          |
| Serviço de Matriz de Distância | ✓                          |
| Serviço de elevação       | Planeado                    |

## <a name="notable-differences-in-the-web-sdks"></a>Diferenças notáveis nos SDKs web

Seguem-se algumas diferenças fundamentais entre o Google Maps e o Azure Maps Web SDKs, para estar atento:

- Além de fornecer um ponto final hospedado para aceder ao Azure Maps Web SDK, está disponível um pacote NPM. Incorporar o pacote Web SDK em aplicações. Para mais informações, consulte esta [documentação.](how-to-use-map-control.md) Este pacote também inclui definições de TypeScript.
- Primeiro é necessário criar uma instância da classe Map no Azure Maps. Aguarde que os mapas `ready` ou `load` evento disparem antes de interagirem programáticamente com o mapa. Esta ordem garantirá que todos os recursos do mapa foram carregados e estão prontos para serem acedidos.
- Ambas as plataformas usam um sistema de inclinação semelhante para os mapas base. Os azulejos do Google Maps têm 256 pixels de dimensão; no entanto, os azulejos em Azure Maps têm 512 pixels de dimensão. Para obter a mesma vista de mapa no Azure Maps que o Google Maps, subtraio o nível de zoom do Google Maps pelo número um no Azure Maps.
- As coordenadas no Google Maps são referidas como "latitude, longitude", enquanto o Azure Maps usa "longitude, latitude". O formato Azure Maps está alinhado com o padrão `[x, y]`, que é seguido pela maioria das plataformas GIS.
- As formas no Azure Maps Web SDK baseiam-se no esquema GeoJSON. As classes auxiliares são expostas através do espaço de nome [ *atlas.data* ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest). Há também o [*atlas. Classe*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) de forma. Utilize esta classe para embrulhar objetos GeoJSON, para facilitar a atualização e manutenção dos dados.
- As coordenadas em Mapas Azure são definidas como objetos de posição. Uma coordenada é especificada como um conjunto de números no formato `[longitude,latitude]`. Ou, é especificado usando novo atlas.data.Position (longitude, latitude).
    > [!TIP]
    > A classe Posição tem um método de ajuda estática para importar coordenadas que estão em formato de "latitude, longitude". O [método atlas.data.Position.fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) pode muitas vezes ser substituído pelo método `new google.maps.LatLng` no código Google Maps.
- Em vez de especificar informações de estilo em cada forma que é adicionada ao mapa, o Azure Maps separa os estilos dos dados. Os dados são armazenados em fontes de dados e estão ligados a camadas de renderização. O código Azure Maps utiliza fontes de dados para renderizar os dados. Esta abordagem proporciona um maior benefício de desempenho. Além disso, muitas camadas suportam o estilo baseado em dados onde a lógica do negócio pode ser adicionada às opções de estilo de camada. Este suporte altera a forma como as formas individuais são renderizadas dentro de uma camada com base em propriedades definidas na forma.

## <a name="web-sdk-side-by-side-examples"></a>Exemplos web SDK lado a lado

Esta recolha tem amostras de código para cada plataforma, e cada amostra cobre um caso de uso comum. Destina-se a ajudá-lo a migrar a sua aplicação web do Google Maps V3 JavaScript SDK para o Azure Maps Web SDK. As amostras de código relacionadas com aplicações web são fornecidas no JavaScript. No entanto, o Azure Maps também fornece definições de TypeScript como uma opção adicional através de um [módulo NPM](how-to-use-map-control.md).

### <a name="load-a-map"></a>Carregar um mapa

Ambos os SDKs têm os mesmos passos para carregar um mapa:

- Adicione uma referência ao Mapa SDK.
- Adicione uma etiqueta `div` ao corpo da página, que funcionará como um espaço reservado para o mapa.
- Crie uma função JavaScript que é chamada quando a página tiver carregado.
- Crie uma instância da respetiva classe de mapas.

**Algumas diferenças fundamentais**

- O Google Maps requer uma chave de conta a ser especificada na referência do script da API. As credenciais de autenticação para o Azure Maps são especificadas como opções da classe do mapa. Esta credencial pode ser uma chave de subscrição ou informações do Diretório Ativo Azure.
- O Google Maps aceita uma função de callback na referência do script da API, que é usada para chamar uma função de inicialização para carregar o mapa. Com o Azure Maps, o evento de onload da página deve ser utilizado.
- Ao referir o elemento `div` em que o mapa será renderizado, a classe `Map` no Azure Maps apenas requer o valor `id` enquanto o Google Maps requer um objeto `HTMLElement`.
- As coordenadas em Mapas Azure são definidas como objetos de posição, que podem ser especificados como uma simples matriz de números no formato `[longitude, latitude]`.
- O nível de zoom no Azure Maps é um nível inferior ao nível de zoom no Google Maps. Esta discrepância deve-se ao facto de a diferença nos tamanhos do sistema de tiling das duas plataformas.
- O Azure Maps não adiciona controlos de navegação à tela do mapa. Assim, por padrão, um mapa não tem botões de zoom e botões de estilo de mapa. Mas, existem opções de controlo para adicionar um picker de estilo de mapa, botões de zoom, controlo de bússola ou rotação, e um controlo de pitch.
- Um manipulador de eventos é adicionado no Azure Maps para monitorizar o evento `ready` da instância do mapa. Este evento irá disparar quando o mapa terminar de carregar o contexto WebGL e todos os recursos necessários. Adicione qualquer código que queira executar depois de o mapa completar o carregamento, a este manipulador de eventos.

Os exemplos básicos abaixo usam o Google Maps para carregar um mapa centrado em Nova Iorque em coordenadas. A longitude: -73.985, latitude: 40.747, e o mapa está no nível de zoom de 12.

**Antes: Google Maps**

Exiba um Mapa do Google centrado e zoomed sobre um local.

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

<center>

![](media/migrate-google-maps-web-app/simple-google-map.png)</center> simples do Google Maps

**Depois: Mapas Azure**

Carregue um mapa com a mesma vista no Azure Maps juntamente com um controlo de estilo de mapa e botões de zoom.

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

<center>

![](media/migrate-google-maps-web-app/simple-azure-maps.png)</center> simples de mapas azure

Encontre documentação detalhada sobre como configurar e utilizar o controlo de mapas do Azure Maps numa aplicação web, clicando [aqui](how-to-use-map-control.md).

> [!NOTE]
> Ao contrário do Google Maps, o Azure Maps não requer um centro inicial e um nível de zoom para carregar o mapa. Se esta informação não for fornecida ao carregar o mapa, os mapas do Azure tentarão determinar a cidade do utilizador. Vai centrar-se e ampliar o mapa.

**Recursos adicionais:**

- O Azure Maps também fornece controlos de navegação para rotação e arremesso da vista do mapa, conforme documentado [aqui](map-add-controls.md).

### <a name="localizing-the-map"></a>Localização do mapa

Se o seu público está espalhado por vários países ou fala línguas diferentes, a localização é importante.

**Antes: Google Maps**

Para localizar o Google Maps, adicione parâmetros de linguagem e região.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Aqui está um exemplo do Google Maps com o idioma definido para "fr-FR".

<center>

![](media/migrate-google-maps-web-app/google-maps-localization.png)</center> de localização do Google Maps

**Depois: Mapas Azure**

O Azure Maps fornece duas formas diferentes de definir a linguagem e a visão regional do mapa. A primeira opção é adicionar esta informação ao espaço *de* nome soque global. Resultará em todas as instâncias de controlo de mapas na sua aplicação, falhando nestas definições. O seguinte define a língua para francês ("fr-FR") e a visão regional para "auto":

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
> Com o Azure Maps, é possível carregar várias instâncias de mapas na mesma página com diferentes configurações linguísticas e regiões. Também é possível atualizar estas definições no mapa depois de ter carregado. 

Encontre uma lista detalhada de [línguas apoiadas](supported-languages.md) no Azure Maps.

Aqui está um exemplo de Azure Maps com a linguagem definida para "fr" e a região utilizadora definida para "fr-FR".

<center>

![Azure Maps](media/migrate-google-maps-web-app/azure-maps-localization.png)</center> de localização

### <a name="setting-the-map-view"></a>Definindo a vista do mapa

Mapas dinâmicos tanto no Azure como no Google Maps podem ser transferidos programáticamente para novos locais geográficos. Para isso, ligue para as funções apropriadas no JavaScript. Os exemplos mostram como fazer o mapa exibir imagens aéreas de satélite, centrar o mapa sobre um local, e alterar o nível de zoom para 15 no Google Maps. São utilizadas as seguintes coordenadas de localização: longitude: -111.0225 e latitude: 35.0272.

> [!NOTE]
> O Google Maps utiliza azulejos com 256 pixels em dimensões, enquanto o Azure Maps utiliza um azulejo de 512 pixels maior. Assim, o Azure Maps requer menos pedidos de rede para carregar a mesma área de mapas que o Google Maps. Devido à forma como as pirâmides de azulejos funcionam nos controlos de mapas, é necessário subtrair o nível de zoom utilizado no Google Maps pelo número um ao utilizar o Azure Maps. Esta operação aritmética garante que os azulejos maiores no Azure Maps tornem essa mesma área de mapa como no Google Maps,

**Antes: Google Maps**

Mova o controlo do mapa do Google Maps utilizando o método `setOptions`. Este método permite especificar o centro do mapa e um nível de zoom.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![google Maps definir vista](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**Depois: Mapas Azure**

No Azure Maps, altere a posição do mapa utilizando o método `setCamera` e altere o estilo do mapa utilizando o método `setStyle`. As coordenadas no Azure Maps estão em formato de "longitude, latitude", e o valor de zoom é subtraído por um.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![Azure Maps definir vista](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**Recursos adicionais:**

- [Escolha um estilo de mapa](choose-map-style.md)
- [Estilos de mapa suportados](supported-map-styles.md)

### <a name="adding-a-marker"></a>Adicionar um marcador

No Azure Maps, existem várias formas de os dados de pontos poderem ser renderizados no mapa:

- **Marcadores HTML** – Renderiza pontos utilizando elementos tradicionais do DOM. Os marcadores HTML suportam o arrastamento.
- **Camada de Símbolo** - Renderiza pontos com um ícone ou texto dentro do contexto WebGL.
- **Camada de Bolha** – Renderiza pontos como círculos no mapa. O rádio dos círculos pode ser dimensionado com base nas propriedades dos dados.

Render camadas de símbolo e camadas de bolha dentro do contexto WebGL. Ambas as camadas podem render grandes conjuntos de pontos no mapa. Estas camadas requerem que os dados sejam armazenados numa fonte de dados. As fontes de dados e as camadas de renderização devem ser adicionadas ao mapa após o `ready` evento ter disparado. Os marcadores HTML são renderizados como elementos DOM dentro da página, e não usam uma fonte de dados. Quanto mais elementos DOM uma página tem, mais lenta se torna a página. Se renderizar mais de algumas centenas de pontos num mapa, é aconselhável utilizar uma das camadas de renderização.

Vamos adicionar um marcador ao mapa com o número 10 sobreposto como rótulo. Use longitude: -0.2 e latitude: 51.5.

**Antes: Google Maps**

Com o Google Maps, adicione marcadores ao mapa usando a classe `google.maps.Marker` e especifique o mapa como uma das opções.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![](media/migrate-google-maps-web-app/google-maps-marker.png)</center> de marcadores do Google Maps

**Depois: Mapas Azure utilizando marcadores HTML**

Nos Mapas Azure, utilize marcadores HTML para exibir um ponto no mapa. Os marcadores HTML são recomendados para aplicações que apenas precisam de exibir um pequeno número de pontos no mapa. Para utilizar um marcador HTML, crie uma instância da classe `atlas.HtmlMarker`. Delineie as opções de texto e posição e adicione o marcador ao mapa utilizando o método `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps HTML marcador](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**Depois: Mapas Azure usando uma camada de símbolo**

Para uma camada de símbolo, adicione os dados a uma fonte de dados. Fixe a fonte de dados à camada. Além disso, a fonte de dados e a camada devem ser adicionadas ao mapa após o evento `ready` ter disparado. Para tornar um valor de texto único acima de um símbolo, a informação de texto precisa de ser armazenada como propriedade do ponto de dados. A propriedade deve ser referenciada na opção `textField` da camada. Esta abordagem é um pouco mais de trabalho do que usar marcadores HTML, mas é um melhor desempenho.

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

<center>

![](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center> de camada de símbolo do Azure Maps

**Recursos adicionais:**

- [Criar uma fonte de dados](create-data-source-web-sdk.md)
- [Adicione uma camada de símbolo](map-add-pin.md)
- [Adicione uma camada de bolha](map-add-bubble-layer.md)
- [Dados do ponto de cluster](clustering-point-data-web-sdk.md)
- [Adicionar marcadores HTML](map-add-custom-html.md)
- [Utilize expressões de estilo baseadas em dados](data-driven-style-expressions-web-sdk.md)
- [Opções de ícone de camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Opção de texto de camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Classe de marcador HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Opções de marcador HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Adicionar um marcador personalizado

Pode utilizar imagens personalizadas para representar pontos num mapa. O mapa abaixo usa uma imagem personalizada para exibir um ponto no mapa. O ponto é exibido na latitude: 51.5 e longitude: -0.2. A âncora compensa a posição do marcador, de modo que o ponto do ícone do pino se alinha com a posição correta no mapa.

<center>

![imagem de pino amarelo](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

**Antes: Google Maps**

Crie um marcador personalizado especificando um objeto `Icon` que contenha o `url` à imagem. Especifique um ponto `anchor` para alinhar o ponto da imagem do pino com a coordenada no mapa. O valor de âncora no Google Maps é relativo ao canto superior esquerdo da imagem.

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

<center>

![](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center> de marcador personalizado do Google Maps

**Depois: Mapas Azure utilizando marcadores HTML**

Para personalizar um marcador HTML, passe uma `string` HTML ou `HTMLElement` para a `htmlContent` opção do marcador. Utilize a opção `anchor` para especificar a posição relativa do marcador, em relação à coordenada de posição. Atribuir um dos nove pontos de referência definidos à opção `anchor`. Os pontos definidos são: "centro", "top", "bottom", "left", "right", "top-left", "top-right", "bottom-left", "bottom-right". O conteúdo está ancorado ao centro inferior do conteúdo html por padrão. Para facilitar a migração do código do Google Maps, desloque a `anchor` para "top-left", e depois use a opção `pixelOffset` com a mesma contrapartida utilizada no Google Maps. As compensações no Azure Maps movem-se na direção oposta das compensações no Google Maps. Então, multiplique as compensações por menos um.

> [!TIP]
> Adicione `pointer-events:none` como um estilo no conteúdo html para desativar o comportamento de arrasto padrão no Microsoft Edge, que apresentará um ícone indesejado.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps marca-](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center> personalizada de marcador HTML

**Depois: Mapas Azure usando uma camada de símbolo**

As camadas de símbolo no Azure Maps também suportam imagens personalizadas. Primeiro, carregue a imagem para os recursos do mapa e atribua-a com um ID único. Referência à imagem na camada de símbolo. Utilize a opção `offset` para alinhar a imagem ao ponto correto no mapa. Utilize a opção `anchor` para especificar a posição relativa do símbolo, em relação às coordenadas de posição. Utilize um dos nove pontos de referência definidos. Esses pontos são: "centro", "top", "bottom", "esquerda", "direita", "top-left", "top-right", "top-right", "bottom-left", "bottom-right". O conteúdo está ancorado ao centro inferior do conteúdo html por padrão. Para facilitar a migração do código do Google Maps, desloque a `anchor` para "top-left", e depois use a opção `offset` com a mesma contrapartida utilizada no Google Maps. As compensações no Azure Maps movem-se na direção oposta das compensações no Google Maps. Então, multiplique as compensações por menos um.

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

<center>

![Azure Maps ícone de ícone personalizado](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> Para renderizar pontos personalizados avançados, use várias camadas de renderização juntas. Por exemplo, digamos que quer ter vários pinos que têm o mesmo ícone em diferentes círculos coloridos. Em vez de criar um monte de imagens para cada sobreposição de cores, adicione uma camada de símbolo em cima de uma camada de bolha. Os pinos de pressão referem a mesma fonte de dados. Esta abordagem será mais eficiente do que criar e manter um monte de imagens diferentes.

**Recursos adicionais:**

- [Criar uma fonte de dados](create-data-source-web-sdk.md)
- [Adicione uma camada de símbolo](map-add-pin.md)
- [Adicionar marcadores HTML](map-add-custom-html.md)
- [Utilize expressões de estilo baseadas em dados](data-driven-style-expressions-web-sdk.md)
- [Opções de ícone de camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Opção de texto de camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Classe de marcador HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Opções de marcador HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Adicionar uma polilinha

Use polilines para representar uma linha ou caminho no mapa. Vamos criar uma polilinha tracejada no mapa.

**Antes: Google Maps**

A classe Polyline aceita um conjunto de opções. Passe uma série de coordenadas na opção `path` da polilinha.

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

<center>

![](media/migrate-google-maps-web-app/google-maps-polyline.png)</center> poliline do Google Maps

**Depois: Mapas Azure**

As polífilas são chamadas de objetos `LineString` ou `MultiLineString`. Estes objetos podem ser adicionados a uma fonte de dados e renderizados utilizando uma camada de linha. Adicione `LineString` a uma fonte de dados e, em seguida, adicione a fonte de dados a um `LineLayer` para torná-lo.

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

<center>

![Azure Maps](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center> poliline

**Recursos adicionais:**

- [Adicione linhas ao mapa](map-add-line-layer.md)
- [Opções de camada de linha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Utilize expressões de estilo baseadas em dados](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Adicionar um polígono

O Azure Maps e o Google Maps fornecem suporte semelhante para polígonos. Os polígonos são usados para representar uma área no mapa. Os exemplos seguintes mostram como criar um polígono que forma um triângulo baseado na coordenada central do mapa.

**Antes: Google Maps**

A classe Polygon aceita um conjunto de opções. Passe uma série de coordenadas para a opção `paths` do polígono.

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

<center>

![](media/migrate-google-maps-web-app/google-maps-polygon.png)</center> de polígono do Google Maps

**Depois: Mapas Azure**

Adicione uma `Polygon` ou um `MultiPolygon` objetos a uma fonte de dados. Rendero o objeto no mapa usando camadas. Renderizar a área de um polígono usando uma camada de polígono. E, renderizar o contorno de um polígono usando uma camada de linha.

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

<center>

![Azure Maps](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**Recursos adicionais:**

- [Adicione um polígono ao mapa](map-add-shape.md)
- [Adicione um círculo ao mapa](map-add-shape.md#add-a-circle-to-the-map)
- [Opções de camada de polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Opções de camada de linha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Utilize expressões de estilo baseadas em dados](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Exibir uma janela de informação

Informações adicionais para uma entidade podem ser exibidas no mapa como uma `google.maps.InfoWindow` classe no Google Maps. No Azure Maps, esta funcionalidade pode ser conseguida utilizando a classe `atlas.Popup`. Os próximos exemplos adicionam um marcador ao mapa. Quando o marcador é clicado, é apresentada uma janela de informação ou um pop-up.

**Antes: Google Maps**

Instantifique uma janela de informação usando o construtor `google.maps.InfoWindow`.

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

<center>

![](media/migrate-google-maps-web-app/google-maps-popup.png)</center> popup do Google Maps

**Depois: Mapas Azure**

Vamos usar popup para mostrar informações adicionais sobre a localização. Passe um `string` HTML ou `HTMLElement` oposição à opção `content` do popup. Se quiser, os pop-ups podem ser exibidos independentemente de qualquer forma. Assim, os popups requerem um valor `position` a especificar. Especifique o valor `position`. Para exibir um popup, ligue para o método `open` e passe o `map` em que o popup deve ser exibido.

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

<center>

![Azure Maps](media/migrate-google-maps-web-app/azure-maps-popup.png)</center> popup

> [!NOTE]
> Pode fazer a mesma coisa com um símbolo, bolha, linha ou camada de polígono, passando a camada escolhida para o código de evento dos mapas em vez de um marcador.

**Recursos adicionais:**

- [Adicione um popup](map-add-popup.md)
- [Popup com Conteúdo mediático](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Popups em Formas](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Reutilizar popup com pinos múltiplos](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Aula pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Opções popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>Importar um ficheiro GeoJSON

O Google Maps suporta o carregamento e o modelar dinamicamente os dados geoJSON através da classe `google.maps.Data`. A funcionalidade desta classe alinha-se muito mais com o estilo baseado em dados do Azure Maps. Mas há uma diferença fundamental. Com o Google Maps, especifica uma função de callback. A lógica de negócio para estilizar cada característica que processou individualmente no fio UI. Mas no Azure Maps, as camadas suportam especificar expressões baseadas em dados como opções de estilo. Estas expressões são processadas no momento de renderização num fio separado. A abordagem Azure Maps melhora o desempenho da renderização. Esta vantagem é notada quando conjuntos de dados maiores precisam de ser prestados rapidamente.

Os seguintes exemplos carregam um feed GeoJSON de todos os terramotos nos últimos sete dias do USGS. Os dados dos terramotos são tornados círculos escalados no mapa. A cor e a escala de cada círculo baseiam-se na magnitude de cada terramoto, que é armazenado na propriedade `"mag"` de cada característica no conjunto de dados. Se a magnitude for maior ou igual a cinco, o círculo será vermelho. Se for maior ou igual a três, mas menos de cinco, o círculo será laranja. Se for menos de três, o círculo será verde. O raio de cada círculo será o exponencial da magnitude multiplicada por 0,1.

**Antes: Google Maps**

Especifique uma única função de chamada no método `map.data.setStyle`. Dentro da função de callback, aplique a lógica do negócio em cada recurso. Carregue o feed GeoJSON com o método `map.data.loadGeoJson`.

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

<center>

![google Maps GeoJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**Depois: Mapas Azure**

GeoJSON é o tipo de dados base no Azure Maps. Importá-lo para uma fonte de dados utilizando o método `datasource.importFromUrl`. Use uma camada de bolha. A camada de bolha fornece funcionalidade para renderização de círculos escalados, com base nas propriedades das funcionalidades numa fonte de dados. Em vez de ter uma função de callback, a lógica do negócio é convertida em uma expressão e transmitida para as opções de estilo. As expressões definem como funciona a lógica do negócio. As expressões podem ser passadas para outro fio e avaliadas com os dados da funcionalidade. Várias fontes de dados e camadas podem ser adicionadas ao Azure Maps, cada um com uma lógica de negócio diferente. Esta funcionalidade permite que vários conjuntos de dados sejam renderizados no mapa de diferentes maneiras.

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

<center>

![Azure Maps GeoJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**Recursos adicionais:**

- [Adicione uma camada de símbolo](map-add-pin.md)
- [Adicione uma camada de bolha](map-add-bubble-layer.md)
- [Dados do ponto de cluster](clustering-point-data-web-sdk.md)
- [Utilize expressões de estilo baseadas em dados](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Agrupamento de marcadores

Ao visualizar muitos pontos de dados no mapa, os pontos podem sobrepor-se uns aos outros. Sobreposição faz o mapa parecer desordenado, e o mapa torna-se difícil de ler e usar. Os dados dos pontos de agrupamento são o processo de combinar pontos de dados que se aproximam uns dos outros e representá-los no mapa como um único ponto de dados agrupado. À medida que o utilizador faz zoom no mapa, os clusters separam-se nos seus pontos de dados individuais. Os dados do cluster apontam para melhorar a experiência do utilizador e o desempenho do mapa.

Nos exemplos seguintes, o código carrega um feed GeoJSON de dados de terramotos da semana passada e adiciona-os ao mapa. Os aglomerados são renderizados como círculos dimensionados e coloridos. A escala e a cor dos círculos dependem do número de pontos que contêm.

> [!NOTE]
> O Google Maps e o Azure Maps utilizam algoritmos de clustering ligeiramente diferentes. Como tal, às vezes a distribuição de pontos nos clusters varia.

**Antes: Google Maps**

Utilize a biblioteca MarkerCluster para marcadores de cluster. Os ícones do cluster limitam-se a imagens, que têm os números de um a cinco como nome. Estão hospedados no mesmo diretório.

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

<center>

![](media/migrate-google-maps-web-app/google-maps-clustering.png)</center> de agrupamento do Google Maps

**Depois: Mapas Azure**

Adicione e gere ncisem dados numa fonte de dados. Conecte as fontes e camadas de dados e, em seguida, torne os dados. A classe `DataSource` em Azure Maps fornece várias opções de agrupamento.

- `cluster` – Informa a fonte de dados aos dados do ponto de cluster.
- `clusterRadius` - O raio em píxeis para pontos de cluster juntos.
- `clusterMaxZoom` - O nível máximo de zoom em que ocorre o agrupamento. Se fizer um zoom em mais do que este nível, todos os pontos são renderizados como símbolos.
- `clusterProperties` - Define propriedades personalizadas que são calculadas usando expressões contra todos os pontos dentro de cada cluster e adicionadas às propriedades de cada ponto de cluster.

Quando o agrupamento estiver ativado, a fonte de dados enviará pontos de dados agrupados e não agrupados para camadas para renderização. A fonte de dados é capaz de agrupar centenas de milhares de pontos de dados. Um ponto de dados agrupado tem as seguintes propriedades:

| Nome da propriedade             | Tipo    | Descrição   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Indica se a funcionalidade representa um cluster. |
| `cluster_id`              | string  | Um ID único para o cluster que pode ser usado com os métodos DataSource `getClusterExpansionZoom`, `getClusterChildren`e `getClusterLeaves`. |
| `point_count`             | número  | O número de pontos que o cluster contém.  |
| `point_count_abbreviated` | string  | Uma corda que abrevia o valor `point_count` se for longa. (por exemplo, 4.000 torna-se 4K)  |

A classe `DataSource` tem a seguinte função de ajudante para aceder a informações adicionais sobre um cluster que utiliza o `cluster_id`.

| Método | Tipo de devolução | Descrição |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Prometa&lt;&lt;Característica&lt;Geometria, qualquer&gt; \|&gt;&gt; | Recupera as crianças do aglomerado dado no próximo nível de zoom. Estas crianças podem ser uma combinação de formas e subaglomerados. Os subclusters serão funcionalidades com propriedades correspondentes ao ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Prometa&lt;número&gt; | Calcula um nível de zoom no qual o cluster começará a expandir-se ou a separar-se. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Prometa&lt;&lt;Característica&lt;Geometria, qualquer&gt; \|&gt;&gt; | Recupera todos os pontos num aglomerado. Delineie o `limit` para devolver um subconjunto dos pontos e utilize o `offset` para página rindo através dos pontos. |

Ao renderizar dados agrupados no mapa, é muitas vezes melhor usar duas ou mais camadas. O exemplo que se segue utiliza três camadas. Uma camada de bolha para desenhar círculos coloridos escalados com base no tamanho dos aglomerados. Uma camada de símbolo para tornar o tamanho do cluster como texto. E usa uma segunda camada de símbolo para renderizar os pontos não agrupados. Há muitas outras formas de renderizar dados agrupados. Para mais informações, consulte a documentação de dados do [Ponto de Cluster.](clustering-point-data-web-sdk.md)

Importar diretamente os dados geoJSON utilizando a função `importDataFromUrl` na classe `DataSource`, dentro do mapa do Azure Maps.

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

<center>

![](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center> de agrupamento do Azure Maps

**Recursos adicionais:**

- [Adicione uma camada de símbolo](map-add-pin.md)
- [Adicione uma camada de bolha](map-add-bubble-layer.md)
- [Dados do ponto de cluster](clustering-point-data-web-sdk.md)
- [Utilize expressões de estilo baseadas em dados](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Adicione um mapa de calor

Os mapas de calor, também conhecidos como mapas de densidade de pontos, são um tipo de visualização de dados. São usados para representar a densidade de dados usando uma gama de cores. E são frequentemente usados para mostrar os dados "pontos quentes" num mapa. Os mapas de calor são uma ótima maneira de renderizar grandes conjuntos de dados de pontos.

Os exemplos seguintes carregam um feed GeoJSON de todos os terramotos durante o mês passado, a partir do USGS, e torna-os como um mapa de calor ponderado. A propriedade `"mag"` é usada como peso.

**Antes: Google Maps**

Para criar um mapa de calor, carregue a biblioteca de "visualização" adicionando `&libraries=visualization` ao URL do script API. A camada de mapa de calor no Google Maps não suporta diretamente os dados da GeoJSON. Primeiro, descarregue os dados e converta-os numa série de pontos de dados ponderados:

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

<center>

![](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center> do mapa de calor do Google Maps

**Depois: Mapas Azure**

Carregue os dados da GeoJSON numa fonte de dados e ligue a fonte de dados a uma camada de mapa de calor. A propriedade que será usada para o peso pode ser passada para a opção `weight` usando uma expressão. Importar diretamente os dados da GeoJSON para o Azure Maps utilizando a função `importDataFromUrl` na classe `DataSource`.

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

<center>

![mapa de calor do Azure Maps](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**Recursos adicionais:**

- [Adicione uma camada de mapa de calor](map-add-heat-map-layer.md)
- [Classe de camada de mapa de calor](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Opções de camada de mapa de calor](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Utilize expressões de estilo baseadas em dados](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Sobreponha uma camada de azulejos

As camadas de azulejos no Azure Maps são conhecidas como sobreposições de imagem no Google Maps. As camadas de azulejos permitem-lhe sobrepor grandes imagens que foram divididas em imagens de azulejos menores, que se alinham com o sistema de azulejos dos mapas. Esta abordagem é comumente usada para sobrepor grandes imagens ou grandes conjuntos de dados.

Os seguintes exemplos sobrepõem uma camada de azulejos de radar meteorológico do Iowa Environmental Mesonet da Universidade Estadual de Iowa.

**Antes: Google Maps**

No Google Maps, as camadas de azulejos podem ser criadas usando a classe `google.maps.ImageMapType`.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

![](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center> de camada de azulejos do Google Maps

**Depois: Mapas Azure**

Adicione uma camada de azulejos ao mapa da mesma forma que qualquer outra camada. Utilize um URL formatado que tenha em x, y, espaços reservados de zoom; `{x}`, `{y}`, `{z}` dizer à camada onde aceder aos azulejos. As camadas de azulejos Azure Maps também suportam `{quadkey}`, `{bbox-epsg-3857}`e `{subdomain}` espaços reservados.

> [!TIP]
> Em Azure Maps as camadas podem ser facilmente renderizadas abaixo de outras camadas, incluindo camadas de mapas base. Muitas vezes é desejável tornar camadas de azulejos abaixo dos rótulos do mapa para que sejam fáceis de ler. O método `map.layers.add` leva um segundo parâmetro que é o id da camada em que inserir a nova camada abaixo. Para inserir uma camada de azulejos abaixo das etiquetas do mapa, use este código: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center> de camada de azulejos Do Azure Maps

> [!TIP]
> Os pedidos de azulejos podem ser capturados usando a opção `transformRequest` do mapa. Isto permitir-lhe-á modificar ou adicionar cabeçalhos ao pedido, se desejar.

**Recursos adicionais:**

- [Adicione camadas de azulejos](map-add-tile-layer.md)
- [Classe de camada de azulejo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Opções de camada de azulejos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Mostrar trânsito

Os dados de tráfego podem ser sobrepostos tanto no Azure como no Google Maps.

**Antes: Google Maps**

Sobreponha os dados de tráfego no mapa utilizando a camada de tráfego.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![](media/migrate-google-maps-web-app/google-maps-traffic.png)</center> de tráfego do Google Maps

**Depois: Mapas Azure**

O Azure Maps oferece várias opções diferentes para exibir o tráfego. Exiba incidentes de trânsito, tais como encerramentos de estradas e acidentes como ícones no mapa. Sobreponha o fluxo de tráfego e as estradas codificadas por cores no mapa. As cores podem ser modificadas com base no limite de velocidade registado, em relação ao atraso normal esperado, ou atraso absoluto. Os dados de incidentes no Azure Maps atualizam-se a cada minuto e os dados de fluxo atualizam a cada dois minutos.

Atribuir os valores procurados para `setTraffic` opções.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center> de trânsito

Se clicar num dos ícones de tráfego no Azure Maps, informações adicionais são exibidas num popup.

<center>

![Azure Maps incidente de trânsito](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**Recursos adicionais:**

- [Mostrar tráfego no mapa](map-show-traffic.md)
- [Opções de sobreposição de tráfego](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Adicione uma sobreposição de terreno

Tanto o Azure como o Google Maps suportam sobrepor imagens georeferenciadas no mapa. As imagens georeferenciadas movem-se e escalam à medida que se pan e zoom do mapa. No Google Maps, as imagens georeferenciadas são conhecidas como sobreposições terrestres enquanto no Azure Maps são referidas como camadas de imagem. São ótimos para construir planos de piso, sobrepor mapas antigos, ou imagens de um drone.

**Antes: Google Maps**

Especifique o URL para a imagem que pretende sobrepor e uma caixa de limitação para ligar a imagem no mapa. Este exemplo sobrepõe uma imagem do mapa de [Newark New Jersey de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa.

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

<center>

![imagem do Google Maps sobrepõe](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**Depois: Mapas Azure**

Use a classe `atlas.layer.ImageLayer` para sobrepor imagens georeferenciadas. Esta aula requer um URL para uma imagem e um conjunto de coordenadas para os quatro cantos da imagem. A imagem deve ser alojada no mesmo domínio ou ter CORs ativados.

> [!TIP]
> Se tiver apenas informação de norte, sul, leste, oeste e rotação, e não tiver coordenadas para cada canto da imagem, pode utilizar o método estático [`atlas.layer.ImageLayer.getCoordinatesFromEdges`.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-)

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

<center>

![imagem do Azure Maps sobrepõe](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**Recursos adicionais:**

- [Sobreponha uma imagem](map-add-image-layer.md)
- [Classe de camada de imagem](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="add-kml-to-the-map"></a>Adicione KML ao mapa

Tanto o Azure como o Google Maps podem importar e render dados KML, KMZ e GeoRSS no mapa. O Azure Maps também suporta ficheiros GPX, GML, CSV espaciais, GeoJSON, Texto Bem Conhecido (WKT), Serviços de Mapeamento Web (WMS), Serviços de Azulejos de Mapeamento Web (WMTS) e Serviços de Recurso Web (WFS). O Azure Maps lê os ficheiros localmente na memória e, na maioria dos casos, consegue lidar com ficheiros KML muito maiores. 

**Antes: Google Maps**


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

<center>

![imagem do Google Maps sobrepõe](media/migrate-google-maps-web-app/google-maps-kml.png)</center>

**Depois: Mapas Azure**

No Azure Maps, o GeoJSON é o principal formato de dados utilizado no SDK web, formatos de dados espaciais adicionais podem ser facilmente integrados na utilização do [módulo IO espacial.](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/) Este módulo tem funções tanto para a leitura como para a escrita de dados espaciais e também inclui uma simples camada de dados que pode facilmente render dados de qualquer um destes formatos de dados espaciais. Para ler os dados num ficheiro de dados espaciais, basta passar num URL, ou dados brutos como cadeia ou bolha na função `atlas.io.read`. Isto devolverá todos os dados analisados do ficheiro que podem ser adicionados ao mapa. O KML é um pouco mais complexo do que a maioria do formato de dados espaciais, uma vez que inclui muito mais informação sobre o estilo. A classe `SpatialDataLayer` suporta a maioria destes estilos, no entanto, as imagens de ícones têm de ser carregadas no mapa antes de carregar os dados da funcionalidade, e as sobreposições terrestres têm de ser adicionadas como camadas para o mapa separadamente. Ao carregar os dados através de um URL, este deve ser hospedado num ponto final ativado por CORs, ou um serviço de procuração deve ser passado como uma opção para a função de leitura. 

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

<center>

![imagem do Azure Maps sobrepõe](media/migrate-google-maps-web-app/azure-maps-kml.png)</center>

**Recursos adicionais:**

- [atlas.io.read função](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io?view=azure-maps-typescript-latest#read-string---arraybuffer---blob--spatialdatareadoptions-)
- [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
- [Opções simpledatalayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

## <a name="additional-code-samples"></a>Amostras de código adicionais

Seguem-se algumas amostras de código adicionais relacionadas com a migração do Google Maps:

- [Ferramentas de desenho](map-add-drawing-toolbar.md)
- [Mapa limite para dois dedos panning](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Limite zoom da roda do pergaminho](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Criar um Controlo fullscreen](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Serviços:**

- [Utilização do módulo de serviços Azure Maps](how-to-use-services-module.md)
- [Procurar pontos de interesse](map-search-location.md)
- [Obtenha informações de uma coordenada (geocódigo inverso)](map-get-information-from-coordinate.md)
- [Mostrar direções de A a B](map-route.md)
- [Pesquisar Automaticamente com JQuery UI](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Google Maps V3 para Web SDK mapeamento da classe Web SDK

O apêndice seguinte fornece uma referência cruzada das classes comumente utilizadas no Google Maps V3 e do equivalente Azure Maps Web SDK.

### <a name="core-classes"></a>Aulas de Núcleo

| Google Maps   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [atlas. Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [atlas. Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [atlas. PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Posição](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [atlas.data.BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [atlas. Opções de Câmara](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[atlas. Opções de Câmara](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[atlas. Opções de Serviço](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[atlas. StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[atlas. Opções de Interação de Utilizadores](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [atlas. Pixel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Aulas de Sobreposição

| Google Maps  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [atlas. HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[atlas.data.Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [atlas. Opções htmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[atlas. Opções de Matador de Símbolos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[atlas. Opções de Ícones](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[atlas. Opções de Texto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[atlas. Opções de bolhas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [atlas.data.Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [atlas. Opções polygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [atlas. Opções de Camadas de Linha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [atlas.data.LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[atlas. Opções de Camadas de Linha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | Ver [Adicionar um círculo ao mapa](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [atlas. TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [atlas. TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[atlas. Opções de Camadas de Imagem](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Aulas de Serviço

O Azure Maps Web SDK inclui um módulo de serviços, que pode ser carregado separadamente. Este módulo envolve os serviços Azure Maps REST com uma API web e pode ser usado em aplicações JavaScript, TypeScript e Node.js.

| Google Maps | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [atlas. Opções de Endereços de Pesquisa](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[atlas. Opções SearchAddressRevrse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[atlas. SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[atlas. Opções estruturadas de SearchAddress](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[atlas. Opções searchAlongRoute](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[atlas. Opções de PesquisaFuzzy](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[atlas. Opções de Pesquisar InsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[atlas. SearchNearByOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[atlas. Opções de PesquisaPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[atlas. Opções de SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [atlas. CalcularRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Bibliotecas

As bibliotecas adicionam funcionalidade adicional ao mapa. Muitas destas bibliotecas estão no núcleo SDK do Azure Maps. Aqui estão algumas classes equivalentes para usar no lugar destas bibliotecas do Google Maps

| Google Maps           | Azure Maps   |
|-----------------------|--------------|
| Biblioteca de desenho       | [Módulo de ferramentas de desenho](set-drawing-options.md) |
| Biblioteca de geometria      | [atlas.math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Biblioteca de visualização | [Camada de mapa de calor](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Azure Maps Web SDK.

> [!div class="nextstepaction"]
> [How to use the map control](how-to-use-map-control.md) (Como utilizar o controlo de mapa)

> [!div class="nextstepaction"]
> [Como utilizar o módulo de serviços](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Como utilizar o módulo de ferramentas de desenho](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)

