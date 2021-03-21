---
title: 'Tutorial: Migrar uma aplicação web do Bing Maps | Microsoft Azure Maps'
description: Tutorial sobre como migrar uma aplicação web de Bing Maps para Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: 2b072107275fba1ff83ab3ddac63ed8bf7766356
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389115"
---
# <a name="tutorial-migrate-a-web-app-from-bing-maps"></a>Tutorial: Migrar uma aplicação web a partir de Bing Maps

As aplicações web que usam bing Maps usam frequentemente o Bing Maps V8 JavaScript SDK. O Azure Maps Web SDK é o SDK baseado em Azure adequado para migrar. O Azure Maps Web SDK permite personalizar mapas interativos com o seu próprio conteúdo e imagens para exibição nas suas aplicações web ou móveis. Este controlo utiliza o WebGL, permitindo-lhe compor grandes conjuntos de dados com elevado desempenho. Desenvolva-se com este SDK utilizando JavaScript ou TypeScript. Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Carregue um mapa
> * Localize um mapa
> * Adicione pinos, polilinas e polígonos.
> * Mostrar informações num popup ou infobox
> * Carregar e exibir dados KML e GeoJSON
> * Pinos de impulso de cluster
> * Sobrepor uma camada de azulejo
> * Mostrar dados de tráfego
> * Adicione uma sobreposição de solo

Se migrar uma aplicação web existente, verifique se está a utilizar uma biblioteca de controlo de mapas de código aberto, como Cesium, Folheto e OpenLayers. Se for e preferir continuar a usar essa biblioteca, pode ligá-la aos serviços de azulejos Azure Maps[](/rest/api/maps/render/getmaptile) \| [(azulejos de telhas de](/rest/api/maps/render/getmapimagerytile)estrada). Os links abaixo fornecem detalhes sobre como usar o Azure Maps em algumas bibliotecas de controlo de mapas de código aberto comumente utilizadas.

* [Cesium](https://cesiumjs.org/) - Um controlo de mapa 3D para a web. [Amostras de código](https://azuremapscodesamples.azurewebsites.net/?search=Cesium) \| [Repo plugin]()
* [Folheto](https://leafletjs.com/) – Controlo de mapa 2D leve para a web. [Amostras de código](https://azuremapscodesamples.azurewebsites.net/?search=leaflet) \| [Repo plugin]()
* [OpenLayers](https://openlayers.org/) - Um controlo de mapa 2D para a web que suporta projeções. [Amostras de código](https://azuremapscodesamples.azurewebsites.net/?search=openlayers) \| [Repo plugin]()

Se desenvolver usando uma estrutura JavaScript, um dos seguintes projetos de código aberto pode ser útil:

* [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps) - Angular 10 wrapper em torno de mapas Azure.
* [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) - Um componente Blazor Azure Maps.
* [Componente de reação do Azure Maps](https://github.com/WiredSolutions/react-azure-maps) - Um invólucro de reação para o controlo Azure Maps.
* [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) - Um componente Azure Maps para aplicação Vue.

## <a name="prerequisites"></a>Pré-requisitos

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](how-to-manage-authentication.md)

## <a name="key-features-support"></a>Suporte de funcionalidades principais

A tabela que se segue lista as principais funcionalidades da API no Bing Maps V8 JavaScript SDK e o suporte de uma API similar no Azure Maps Web SDK.

| Recurso Bing Maps        | Suporte SDK web Azure Maps                                                             |
|--------------------------|:--------------------------------------------------------------------------------------:|
| Pinos de empurrar                 | ✓                                                                                      |
| Agrupamento de pinos de empurrar       | ✓                                                                                      |
| Polígonos & Poligóis     | ✓                                                                                      |
| Sobreposições de solo          | ✓                                                                                      |
| Mapas de calor                | ✓                                                                                      |
| Camadas de azulejos              | ✓                                                                                      |
| Camada KML                | ✓                                                                                      |
| Camada de contorno            | [Amostras](https://azuremapscodesamples.azurewebsites.net/?search=contour)              |
| Camada de binário de dados       | Incluído no módulo de fonte aberta Azure Maps [Gridded Data Source](https://github.com/Azure-Samples/azure-maps-gridded-data-source)       |
| Camada de azulejo animado      | Incluído no módulo de [animação](https://github.com/Azure-Samples/azure-maps-animations) Azure Maps de código aberto |
| Ferramentas de desenho            | ✓                                                                                      |
| Serviço geocoder         | ✓                                                                                      |
| Serviço de direções       | ✓                                                                                      |
| Serviço Matrix distância  | ✓                                                                                      |
| Serviço de Dados Espaciais     | N/D                                                                                    |
| Imagens de satélite/aérea | ✓                                                                                      |
| Imagens de olhos de pássaros         | N/D                                                                                |
| Imagens de rua       | N/D                                                                                |
| Suporte GeoJSON          | ✓                                                                                      |
| Suporte GeoXML           | ✓ [Módulo IO espacial](how-to-use-spatial-io-module.md)                                                                                     |
| suporte de texto Well-Known  | ✓                                                                                      |
| Estilos de mapa personalizados        | Parcial                                                                                |

O Azure Maps também tem muitos módulos de código aberto adicionais [para a web SDK](open-source-projects.md#open-web-sdk-modules) que ampliam as suas capacidades.

## <a name="notable-differences-in-the-web-sdks"></a>Diferenças notáveis nos SDKs web

Seguem-se algumas das principais diferenças entre os Bing Maps e os Azure Maps Web SDKs a ter em conta:

* Além de fornecer um ponto final hospedado para aceder ao Azure Maps Web SDK, um pacote NPM também está disponível para incorporar o Web SDK em aplicações, se preferir. Para mais informações, consulte esta [documentação](./how-to-use-map-control.md) para obter mais informações. Este pacote também inclui definições typeScript.
* Bing Maps fornece dois ramos hospedados do seu SDK; Lançamento e Experimental. O ramo Experimental pode receber várias atualizações por dia quando estiver a decorrer um novo desenvolvimento. O Azure Maps acolhe apenas um ramo de lançamento, no entanto as funcionalidades experimentais são criadas como módulos personalizados no projeto de amostras de código Azure Maps de código de código de código. Bing Maps também tinha um ramo congelado que foi atualizado com menos frequência, reduzindo assim o risco de quebra de alterações devido a um lançamento. No Azure Maps pode utilizar o módulo NPM e apontar para qualquer versão menor anterior.

> [!TIP]
> A Azure Maps publica versões minificadas e não administradas do SDK. Simples remover `.min` dos nomes dos ficheiros. A versão nãominada é útil para depurar problemas, mas certifique-se de usar a versão minificada na produção para tirar partido do tamanho de ficheiro mais pequeno.

* Depois de criar uma instância da classe Map em Azure Maps, o seu código deve esperar que os mapas `ready` ou `load` eventos disparem antes de interagir com o mapa. Estes eventos garantem que todos os recursos do mapa foram carregados e estão prontos para serem acedidos.
* Ambas as plataformas utilizam um sistema de inclinação semelhante para os mapas base, no entanto os azulejos em Bing Maps são de 256 pixels de dimensão, enquanto os azulejos em Azure Maps são de 512 pixels de dimensão. Como tal, para obter a mesma vista do mapa em Azure Maps como Bing Maps, um nível de zoom usado em Bing Maps precisa ser subtraído por um em Azure Maps.
* As coordenadas em Bing Maps são referidas como `latitude, longitude` enquanto o Azure Maps `longitude, latitude` utiliza. Este formato alinha-se com o padrão `[x, y]` que é seguido pela maioria das plataformas SIG.

* As formas no Azure Maps Web SDK são baseadas no esquema geoJSON. As classes de ajudantes são expostas através do [atlas.data namespace](/javascript/api/azure-maps-control/atlas.data). Há também o [atlas. Classe](/javascript/api/azure-maps-control/atlas.shape) de forma que pode ser usada para embrulhar objetos GeoJSON e torná-los fáceis de atualizar e manter de forma binde de dados.
* As coordenadas em Azure Maps são definidas como objetos de posição que podem ser especificados como uma simples matriz de números no formato `[longitude, latitude]` ou `new atlas.data.Position(longitude, latitude)` .

> [!TIP]
> A classe Position tem uma função de ajudante estático para importar coordenadas que estão em `latitude, longitude` formato. A função [atlas.data.position.fromLatLng](/javascript/api/azure-maps-control/atlas.data.position)pode muitas vezes substituir a `new Microsoft.Maps.Location` função no código Bing Maps.

* Em vez de especificar informações de estilo em cada formato que é adicionado ao mapa, o Azure Maps separa os estilos dos dados. Os dados são armazenados em fontes de dados e estão ligados a camadas de renderização que o código Azure Maps utiliza para renderizar os dados. Esta abordagem proporciona um benefício de desempenho melhorado. Além disso, muitas camadas suportam o estilo orientado por dados onde a lógica do negócio pode ser adicionada às opções de estilo de camada que mudarão a forma como as formas individuais são renderizadas dentro de uma camada com base em propriedades definidas na forma.
* O Azure Maps fornece um monte de funções de matemática espacial úteis no espaço de `atlas.math` nomes, no entanto estas diferem das do módulo de matemática espacial Bing Maps. A principal diferença é que o Azure Maps não fornece funções incorporadas para operações binárias como a união e a intersecção, no entanto, uma vez que o Azure Maps é baseado no GeoJSON que é um padrão aberto, existem muitas bibliotecas de código aberto disponíveis. Uma opção popular que funciona bem com o Azure Maps e fornece uma tonelada de capacidades de matemática espacial é [o relvado js.](http://turfjs.org/)

Consulte também o [Glossário Azure Maps](./glossary.md) para obter uma lista aprofundada de terminologia associada ao Azure Maps.

## <a name="web-sdk-side-by-side-examples"></a>Exemplos web SDK lado a lado

Segue-se uma recolha de amostras de código para cada plataforma que cobre casos de uso comum para ajudá-lo a migrar a sua aplicação web de Bing Maps V8 JavaScript SDK para o Azure Maps Web SDK. As amostras de código relacionadas com aplicações web são fornecidas no JavaScript; no entanto, o Azure Maps também fornece definições typeScript como uma opção adicional através de um [módulo NPM](./how-to-use-map-control.md).

**Tópicos**

* [Carregue um mapa](#load-a-map)
* [Localização do mapa](#localizing-the-map)
* [Definição da vista do mapa](#setting-the-map-view)
* [Adicionando um pino de pressão](#adding-a-pushpin)
* [Adicionando um pino personalizado](#adding-a-custom-pushpin)
* [Adicionar uma polilina](#adding-a-polyline)
* [Adicionar um polígono](#adding-a-polygon)
* [Mostrar uma caixa de informação](#display-an-infobox)
* [Agrupamento de pinos de empurrar](#pushpin-clustering)
* [Adicione um mapa de calor](#add-a-heat-map)
* [Sobrepor uma camada de azulejo](#overlay-a-tile-layer)
* [Mostrar dados de tráfego](#show-traffic-data)
* [Adicione uma sobreposição de solo](#add-a-ground-overlay)
* [Adicione dados KML ao mapa](#add-kml-data-to-the-map)
* [Adicionar ferramentas de desenho](#add-drawing-tools)

### <a name="load-a-map"></a>Carregue um mapa

Carregar um mapa em ambos os SDK segue o mesmo conjunto de passos;

* Adicione uma referência ao Map SDK.
* Adicione uma `div` etiqueta ao corpo da página que funcionará como um espaço reservado para o mapa.
* Crie uma função JavaScript que é chamada quando a página estiver carregada.
* Crie um exemplo da respetiva classe de mapas.

**Algumas diferenças fundamentais**

* Os mapas bing requerem uma chave de conta a especificar na referência de script da API ou como uma opção de mapa. As credenciais de autenticação para Azure Maps são especificadas como opções da classe de mapa. Esta pode ser uma chave de subscrição ou informações do Azure Ative Directory.
* Bing Maps assume uma função de retorno na referência de script da API que é usada para chamar uma função de inicialização para carregar o mapa. Com o Azure Maps, o evento de carga da página deve ser usado.
* Ao utilizar um ID para referenciar o `div` elemento em que o mapa será prestado, o Bing Maps utiliza um seletor HTML (ou `#myMap` seja), enquanto o Azure Maps utiliza apenas o valor de ID (ou `myMap` seja).
* As coordenadas em Azure Maps são definidas como objetos de posição que podem ser especificados como uma simples matriz de números no formato `[longitude, latitude]` .
* O nível de zoom no Azure Maps é um nível inferior ao exemplo do Bing Maps devido à diferença nos tamanhos do sistema de inclinação entre as plataformas.
* Por padrão, o Azure Maps não adiciona quaisquer controlos de navegação à tela do mapa, como botões de zoom e botões de estilo de mapa. Existem, no entanto, controlos para adicionar um picker de estilo de mapa, botões de zoom, bússola ou controlo de rotação, e um controlo de altura.
* Um manipulador de eventos é adicionado no Azure Maps para monitorizar o `ready` caso da instância do mapa. Isto disparará quando o mapa terminar de carregar o contexto WebGL e todos os recursos necessários. Qualquer código de carga postal pode ser adicionado neste manipulador de eventos.

Os exemplos abaixo mostram como carregar um mapa básico tal que está centrado sobre Nova Iorque nas coordenadas (longitude: -73.985, latitude: 40.747) e está no nível de zoom 12 em Bing Maps.

**Antes: Bing Maps**

O código a seguir é um exemplo de como exibir um Bing Map centrado e ampliado sobre um local.

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
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Executar este código num browser apresentará um mapa que se parece com a seguinte imagem:

![Mapa de Bing Maps](media/migrate-bing-maps-web-app/bing-maps-load-map.jpg)

**Depois: Mapas Azure**

O código que se segue mostra como carregar um mapa com a mesma vista em Azure Maps juntamente com um controlo de estilo de mapa e botões de zoom.

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom and map style controls to top right of map.
                map.controls.add([
                        new atlas.control.StyleControl(),
                        new atlas.control.ZoomControl()
                    ], {
                        position: 'top-right'
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

![Mapa de Azure](media/migrate-bing-maps-web-app/azure-maps-load-map.jpg)

Documentação detalhada sobre como configurar e usar o controlo do mapa Azure Maps numa aplicação web pode ser encontrada [aqui.](how-to-use-map-control.md)

> [!TIP]
> A Azure Maps publica versões minificadas e não administradas do SDK. `.min`Retire dos nomes dos ficheiros. A versão nãominada é útil para depurar problemas, mas certifique-se de usar a versão minificada na produção para tirar partido do tamanho de ficheiro mais pequeno.

**Recursos adicionais**

* O Azure Maps também fornece controlos de navegação para rodar e lançar a vista do mapa como documentado [aqui.](map-add-controls.md)

### <a name="localizing-the-map"></a>Localização do mapa

Se o seu público estiver espalhado por vários países ou falar línguas diferentes, a localização é importante.

**Antes: Bing Maps**

Para localizar os Mapas de Bing, a língua e a região são especificadas utilizando o `setLang` e `UR` os parâmetros são adicionados à `<script>` referência à API. Algumas funcionalidades no Bing Maps só estão disponíveis em determinados mercados, uma vez que tal o mercado do utilizador é especificado utilizando o `setMkt` parâmetro.

```html
<script type="text/javascript" src="https://www.bing.com/api/maps/mapcontrol?callback=initMap&setLang=[language_code]&setMkt=[market]&UR=[region_code]" async defer></script>
```

Aqui está um exemplo de Bing Maps com a linguagem definida como "fr-FR".

![Mapa de Bing localizado](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)

**Depois: Mapas Azure**

O Azure Maps apenas oferece opções para definir a linguagem e a visão regional do mapa. Um parâmetro de mercado não é usado para limitar as características. Há duas maneiras diferentes de definir a linguagem e a visão regional do mapa. A primeira opção é adicionar esta informação ao espaço de `atlas` nome global que resultará em todas as instâncias de controlo de mapas na sua aplicação por defeito nestas definições. O que se segue define a língua para o francês ("fr-FR") e a visão `"Auto"` regional:

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

A segunda opção é passar esta informação para as opções do mapa ao carregar o mapa como:

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
> Com o Azure Maps é possível carregar várias instâncias de mapas na mesma página com diferentes configurações de idioma e região. Além disso, também é possível atualizar estas definições no mapa depois de ter sido carregado. Uma lista detalhada de línguas apoiadas no Azure Maps pode ser encontrada [aqui.](./supported-languages.md)

Aqui está um exemplo de Azure Maps com o idioma definido para "fr" e a região de utilizadores definida como "fr-FR".

![Mapa do Azure Localizado](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)

### <a name="setting-the-map-view"></a>Definição da vista do mapa

Os mapas dinâmicos tanto no Bing como no Azure Maps podem ser movidos programáticamente para novas localizações geográficas, chamando as funções apropriadas no JavaScript. Os exemplos abaixo mostram como fazer o mapa exibir imagens aéreas de satélite, centrar o mapa sobre um local com coordenadas (longitude: -111.0225, latitude: 35.0272) e alterar o nível de zoom para 15 em Bing Maps.

> [!NOTE]
> Bing Maps usa azulejos que são 256 pixels em dimensões, enquanto o Azure Maps usa um azulejo maior de 512 pixels. Isto reduz o número de pedidos de rede necessários pelo Azure Maps para carregar a mesma área de mapa que o Bing Maps. No entanto, devido à forma como as pirâmides de azulejos funcionam nos controlos de mapas, os azulejos maiores em Azure Maps significam que para alcançar essa mesma área visível como um mapa em Bing Maps, você precisa subtrair o nível de zoom usado em Bing Maps por 1 quando usar O Azure Maps.

**Antes: Bing Maps**

O controlo do mapa Bing Maps pode ser movido programáticamente usando a `setView` função que lhe permite especificar o centro do mapa e um nível de zoom.

```javascript
map.setView({
    mapTypeId: Microsoft.Maps.MapTypeId.aerial,
    center: new Microsoft.Maps.Location(35.0272, -111.0225),
    zoom: 15
});
```

![Bing Maps definir vista do mapa](media/migrate-bing-maps-web-app/bing-maps-set-map-view.jpg)

**Depois: Mapas Azure**

No Azure Maps, a posição do mapa pode ser alterada programáticamente utilizando a `setCamera` função do mapa e o estilo do mapa pode ser alterado usando a `setStyle` função. Note que as coordenadas em Azure Maps estão em formato "longitude, latitude", e o valor do nível de zoom é subtraído por 1.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite_with_roads'
});
```

![Azure Maps definir a vista do mapa](media/migrate-bing-maps-web-app/azure-maps-set-map-view.jpg)

**Recursos adicionais**

-   [Escolher um estilo de mapa](./choose-map-style.md)
-   [Estilos de mapas suportados](./supported-map-styles.md)

### <a name="adding-a-pushpin"></a>Adicionando um pino de pressão

No Azure Maps existem várias formas de os dados de pontos poderem ser renderizados no mapa;

* Marcadores HTML – Faz pontos utilizando elementos DOM tradicionais. HTML Os marcadores suportam arrastar.
* Camada de Símbolo – Torna pontos com um ícone e/ou texto dentro do contexto WebGL.
* Camada de Bolha – Renderiza pontos como círculos no mapa. O raios dos círculos pode ser dimensionado com base em propriedades nos dados.

As camadas de Símbolo e Bolha são renderizadas no contexto WebGL e são capazes de renderizar conjuntos muito grandes de pontos no mapa. Estas camadas requerem que os dados sejam armazenados numa fonte de dados. As fontes de dados e as camadas de renderização devem ser adicionadas ao mapa após o `ready` evento ter disparado. Os marcadores HTML são renderizados como elementos DOM dentro da página e não utilizam uma fonte de dados. Quanto mais elementos DOM uma página tem, mais lenta a página se torna. Se renderizar mais de algumas centenas de pontos num mapa, recomenda-se a utilização de uma das camadas de renderização.

Os exemplos abaixo adicionam um marcador ao mapa em (longitude: -0.2, latitude: 51.5) com o número 10 sobreposto como etiqueta.

**Antes: Bing Maps**

Com bing maps, os marcadores são adicionados ao mapa usando a `Microsoft.Maps.Pushpin` classe*. Os pinos são adicionados ao mapa utilizando uma de duas funções.

A primeira função é criar uma camada, inserir o pino de pressão para que e, em seguida, adicionar a camada à propriedade do `layers` mapa.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

A segunda é adicioná-lo usando a propriedade do `entities` mapa. Esta função é marcada precmenteda na documentação do Bing Maps V8, no entanto manteve-se parcialmente funcional para cenários básicos.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

map.entities.add(pushpin);
```

![Bing Maps adiciona puspin](media/migrate-bing-maps-web-app/bing-maps-add-pushpin.jpg)

**Depois: Mapas Azure usando marcadores HTML**

No Azure Maps, os marcadores HTML podem ser usados para exibir facilmente um ponto no mapa e são recomendados para aplicações simples que apenas precisam de exibir um pequeno número de pontos no mapa. Para utilizar um marcador HTML, crie uma instância da `atlas.HtmlMarker` classe, desa estale as opções de texto e posição e adicione o marcador ao mapa utilizando a `map.markers.add` função.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

![Azure Maps adicionam marcador](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)

**Depois: Mapas Azure usando uma camada de símbolo**

Ao utilizar uma camada de Símbolo, os dados devem ser adicionados a uma fonte de dados e à fonte de dados anexada à camada. Além disso, a fonte de dados e a camada devem ser adicionadas ao mapa após o `ready` evento ter disparado. Para tornar um valor de texto único acima de um símbolo, a informação de texto precisa de ser armazenada como uma propriedade do ponto de dados e essa propriedade referenciada na `textField` opção da camada. Isto é um pouco mais de trabalho do que usar marcadores HTML, mas proporciona muitas vantagens de desempenho.

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

![Azure Maps adiciona camada de símbolo](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)

**Recursos adicionais**

-   [Criar uma origem de dados](./create-data-source-web-sdk.md)
-   [Adicione uma camada de símbolo](./map-add-pin.md)
-   [Adicione uma camada de bolha](./map-add-bubble-layer.md)
-   [Dados de ponto do cluster](./clustering-point-data-web-sdk.md)
-   [Adicionar marcadores HTML](./map-add-custom-html.md)
-   [Utilizar expressões de estilo com base em dados](./data-driven-style-expressions-web-sdk.md)
-   [Opções de ícone de camada de símbolo](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Opção de texto de camada de símbolo](/javascript/api/azure-maps-control/atlas.textoptions)
-   [Classe de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [Opções de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-pushpin"></a>Adicionando um pino personalizado

Imagens personalizadas podem ser usadas para representar pontos num mapa. A imagem a seguir é utilizada nos exemplos abaixo e utiliza uma imagem personalizada para exibir um ponto no mapa em (latitude: 51.5, longitude: -0.2) e compensa a posição do marcador de modo a que o ponto do ícone do pino de pressão se alinhe com a posição correta no mapa.

| ![Azure Maps adiciona puspin](media/migrate-bing-maps-web-app/yellow-pushpin.png)|
|:-----------------------------------------------------------------------:|
| yellow-pushpin.png                                                        |

**Antes: Bing Maps**

Em Bing Maps, um marcador personalizado é criado passando um URL para uma imagem nas `icon` opções de um pushpin. A `anchor` opção é usada para alinhar o ponto da imagem do pino com a coordenada no mapa. O valor de âncora em Bing Maps em relação ao canto superior esquerdo da imagem.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    icon: 'ylw-pushpin.png',
    anchor: new Microsoft.Maps.Point(5, 30)
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

![Bing Maps adiciona puspin personalizado](media/migrate-bing-maps-web-app/bing-maps-add-custom-pushpin.jpg)

**Depois: Mapas Azure usando marcadores HTML**

Para personalizar um marcador HTML no Azure Maps um HTML `string` ou pode ser passado para a `HTMLElement` `htmlContent` opção do marcador. No Azure Maps, é utilizada uma `anchor` opção para especificar a posição relativa do marcador em relação à coordenada de posição utilizando um dos nove pontos de referência definidos; "centro", "superior", "inferior", "esquerda", "direita", "superior-esquerda", "superior-direita", "inferior-esquerda", "inferior-direita", "inferior-direita". O conteúdo é ancorado e é definido para "fundo" por padrão, que é o centro inferior do conteúdo html. Para facilitar a migração do código a partir de Bing Maps, coloque a âncora em "top-left", e use a `offset` opção com o mesmo offset usado no Bing Maps. As compensações em Azure Maps movem-se na direção oposta de Bing Maps, por isso multiplique-os por menos um.

> [!TIP]
> Adicione `pointer-events:none` como um estilo no conteúdo HTML para desativar o comportamento de arrasto padrão em MS Edge que irá exibir um ícone indesejado.

```html
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

![Azure Maps adicionam marcador personalizado](media/migrate-bing-maps-web-app/azure-maps-add-custom-marker.jpg)

**Depois: Mapas Azure usando uma camada de símbolo**

As camadas de símbolo no Azure Maps também suportam imagens personalizadas, mas a imagem precisa de ser carregada primeiro nos recursos do mapa e atribuiu um ID único. A camada de símbolo pode então referenciar este ID. O símbolo pode ser compensado para alinhar-se com o ponto correto da imagem utilizando a `offset` opção ícone. No Azure Maps, é utilizada uma `anchor` opção para especificar a posição relativa do símbolo em relação à coordenada de posição utilizando um dos nove pontos de referência definidos; "centro", "superior", "inferior", "esquerda", "direita", "superior-esquerda", "superior-direita", "inferior-esquerda", "inferior-direita", "inferior-direita". O conteúdo é ancorado e definido para "fundo" por predefinição que é o centro inferior do conteúdo HTML. Para facilitar a migração do código a partir de Bing Maps, coloque a âncora em "top-left", e use a `offset` opção com o mesmo offset usado no Bing Maps. As compensações em Azure Maps movem-se na direção oposta de Bing Maps, por isso multiplique-os por menos um.

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
                map.imageSprite.add('my-yellow-pin', 'ylw-pushpin.png').then(function () {

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

![Bing Maps adicionar camada de símbolo personalizado](media/migrate-bing-maps-web-app/azure-maps-add-custom-symbol-layer.jpg)

> [!TIP]
> Para criar uma renderização personalizada avançada de pontos, utilize várias camadas de renderização juntas. Por exemplo, se quiser ter múltiplos pinos que têm o mesmo ícone em diferentes círculos coloridos, em vez de criar um monte de imagens para cada cor sobrepõe uma camada de símbolo em cima de uma camada de bolha e faça com que eles refiram a mesma fonte de dados. Isto será muito mais eficiente do que criar, e ter o mapa a manter um monte de imagens diferentes.

**Recursos adicionais**

-   [Criar uma origem de dados](./create-data-source-web-sdk.md)
-   [Adicione uma camada de símbolo](./map-add-pin.md)
-   [Adicionar marcadores HTML](./map-add-custom-html.md)
-   [Utilizar expressões de estilo com base em dados](./data-driven-style-expressions-web-sdk.md)
-   [Opções de ícone de camada de símbolo](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Opção de texto de camada de símbolo](/javascript/api/azure-maps-control/atlas.textoptions)
-   [Classe de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [Opções de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Adicionar uma polilina

Os polilinas são usados para representar uma linha ou caminho no mapa. Os exemplos abaixo mostram como criar um polilina tracejado no mapa.

**Antes: Bing Maps**

Em Bing Maps, a classe Polyline acolhe uma variedade de locais e um conjunto de opções.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polyline.
var polyline = new Microsoft.Maps.Polyline([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1)
    ], {
        strokeColor: 'red',
        strokeThickness: 4,
        strokeDashArray: [3, 3]
    });

//Add the polyline to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polyline);
map.layers.insert(layer);
```

![Bing Maps polilina](media/migrate-bing-maps-web-app/bing-maps-line.jpg)

**Depois: Mapas Azure**

Nos Mapas Azure, os polilinas são referidos aos termos ou objetos geoespaciais mais `LineString` `MultiLineString` comuns. Estes objetos podem ser adicionados a uma fonte de dados e renderizados através de uma camada de linha. As opções de cor, largura e traço de traço são quase idênticas entre as plataformas.

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

![Linha Azure Maps](media/migrate-bing-maps-web-app/azure-maps-line.jpg)

**Recursos adicionais**

-   [Adicione linhas ao mapa](./map-add-line-layer.md)
-   [Opções de camada de linha](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Utilizar expressões de estilo com base em dados](./data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Adicionar um polígono

Os polígonos são usados para representar uma área no mapa. Azure Maps e Bing Maps fornecem suporte muito semelhante para polígonos. Os exemplos abaixo mostram como criar um polígono que forma um triângulo com base na coordenada central do mapa.

**Antes: Bing Maps**

Em Bing Maps, a classe Polygon recebe uma variedade de coordenadas ou anéis de coordenadas e um conjunto de opções.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polygon.
var polygon = new Microsoft.Maps.Polygon([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1),
        center
    ], {
        fillColor: 'rgba(0, 255, 0, 0.5)',
        strokeColor: 'red',
        strokeThickness: 2
    });

//Add the polygon to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polygon);
map.layers.insert(layer);
```

![Bing Maps poliogn](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)

**Depois: Mapas Azure**

Em Azure Maps, os objetos de Polígono e MultiPolygon podem ser adicionados a uma fonte de dados e renderizados no mapa usando camadas. A área de um polígono pode ser renderizada numa camada de polígono. O contorno de um polígono pode ser renderizado usando uma camada de linha.

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

![Azure Maps polyogn](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)

**Recursos adicionais**

-   [Adicione um polígono ao mapa](./map-add-shape.md#use-a-polygon-layer)
-   [Adicione um círculo ao mapa](./map-add-shape.md#add-a-circle-to-the-map)
-   [Opções de camada de polígono](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
-   [Opções de camada de linha](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Utilizar expressões de estilo com base em dados](./data-driven-style-expressions-web-sdk.md)

### <a name="display-an-infobox"></a>Mostrar uma caixa de informação

Informações adicionais para uma entidade podem ser exibidas no mapa como uma `Microsoft.Maps.Infobox` classe em Bing Maps, em Azure Maps isso pode ser alcançado usando a `atlas.Popup` classe. Os exemplos abaixo adicionam um pushpin/marcador ao mapa, e quando clicados, exibem uma infobox/popup.

**Antes: Bing Maps**

Com o Bing Maps, uma caixa de informação é criada utilizando o `Microsoft.Maps.Infobox` construtor.

```javascript
//Add a pushpin where we want to display an infobox.
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(47.6, -122.33));

//Add the pushpin to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);

//Create an infobox and bind it to the map.
var infobox = new Microsoft.Maps.Infobox(new Microsoft.Maps.Location(47.6, -122.33), {
    description: '<div style="padding:5px"><b>Hello World!</b></div>',
    visible: false
});
infobox.setMap(map);

//Add a click event to the pushpin to open the infobox.
Microsoft.Maps.Events.addHandler(pushpin, 'click', function () {
    infobox.setOptions({ visible: true });
});
```

![Infobox Bing Maps](media/migrate-bing-maps-web-app/bing-maps-infobox.jpg)

**Depois: Mapas Azure**

No Azure Maps, um pop-up pode ser usado para exibir informações adicionais para uma localização. Um HTML `string` ou objeto pode ser passado para a `HTMLElement` `content` opção do popup. Os popups podem ser exibidos independentemente de qualquer forma se desejarem e assim exigirem que seja especificado um `position` valor. Para exibir um popup, ligue para a `open` função e passe no mapa onde o popup deve ser exibido.

```javascript
//Add a marker to the map that to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:10px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

![Popup Azure Maps](media/migrate-bing-maps-web-app/azure-maps-popup.jpg)

> [!NOTE]
> Para fazer a mesma coisa com um símbolo, bolha, linha ou camada de polígono, passe a camada para o código de evento dos mapas em vez de um marcador.

**Recursos adicionais**

-   [Adicionar um pop-up](./map-add-popup.md)
-   [Popup com Conteúdo de Mídia](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
-   [Popups em Formas](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
-   [Reutilizar Popup com múltiplos pinos](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
-   [Classe popup](/javascript/api/azure-maps-control/atlas.popup)
-   [Opções popup](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="pushpin-clustering"></a>Agrupamento de pinos de empurrar

Ao visualizar muitos pontos de dados no mapa, os pontos sobrepõem-se uns aos outros, o mapa parece desordenado e torna-se difícil de ver e usar. O agrupamento de dados de pontos pode ser usado para melhorar esta experiência do utilizador e também melhorar o desempenho. Os dados de ponto de agrupamento são o processo de combinar dados de pontos que estão próximos uns dos outros e representá-los no mapa como um único ponto de dados agrupados. À medida que o utilizador faz zoom no mapa, os clusters separam-se nos seus pontos de dados individuais.

Os exemplos abaixo carregam um feed GeoJSON de dados de terramotos da semana passada e adicioná-lo ao mapa. Os aglomerados são renderizados como círculos dimensionados e coloridos, dependendo do número de pontos que contêm.

> [!NOTE]
> Existem vários algoritmos diferentes usados para o agrupamento de pinos. Bing Maps usa uma função simples baseada em grelha, enquanto o Azure Maps usa métodos de clustering mais avançados e visualmente apelativos.

**Antes: Bing Maps**

No Bing Maps, os dados da GeoJSON podem ser carregados utilizando o módulo GeoJSON. Os pinos podem ser agrupados carregando no módulo de agrupamento e utilizando a camada de agrupamento que contém.

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2
            });

            //Load the GeoJSON and Clustering modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.Clustering'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (pins) {

                    //Create a ClusterLayer with options and add it to the map.
                    clusterLayer = new Microsoft.Maps.ClusterLayer(pins, {
                        clusteredPinCallback: createCustomClusteredPin,
                        gridSize: 100
                    });

                    map.layers.insert(clusterLayer);
                });
            });
        }

        //A function that defines how clustered pins are rendered.
        function createCustomClusteredPin(cluster) {
            //Get the number of pushpins in the cluster
            var clusterSize = cluster.containedPushpins.length;

            var radius = 20;    //Default radius to 20 pixels.
            var fillColor = 'lime';     //Default to lime green.

            if (clusterSize >= 750) {
                radius = 40;   //If point_count >= 750, radius is 40 pixels.
                fillColor = 'red';    //If the point_count >= 750, color is red.
            } else if (clusterSize >= 100) {
                radius = 30;    //If point_count >= 100, radius is 30 pixels.
                fillColor = 'yellow';    //If the point_count >= 100, color is yellow.
            }

            //Create an SVG string of a circle with the specified radius and color.
            var svg = ['<svg xmlns="http://www.w3.org/2000/svg" width="', (radius * 2), '" height="', (radius * 2), '">',
                '<circle cx="', radius, '" cy="', radius, '" r="', radius, '" fill="', fillColor, '"/>',
                '<text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" style="font-size:12px;font-family:arial;fill:black;" >{text}</text>',
                '</svg>'];

            //Customize the clustered pushpin using the generated SVG and anchor on its center.
            cluster.setOptions({
                icon: svg.join(''),
                anchor: new Microsoft.Maps.Point(radius, radius),
                textOffset: new Microsoft.Maps.Point(0, radius - 8) //Subtract 8 to compensate for height of text.
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Agrupamento de mapas bing](media/migrate-bing-maps-web-app/bing-maps-clustering.jpg)

**Depois: Mapas Azure**

No Azure Maps, os dados são adicionados e geridos por uma fonte de dados. As camadas ligam-se a fontes de dados e prestam os dados neles. A `DataSource` aula em Azure Maps oferece várias opções de clustering.

* `cluster` – Informa a fonte de dados para o cluster de dados. 
* `clusterRadius` - O raio dos pixels para os pontos de agrupamento juntos.
* `clusterMaxZoom` - O nível máximo de zoom que o agrupamento ocorre. Se fizer zoom mais do que isto, todos os pontos são renderizados como símbolos.
* `clusterProperties` - Define propriedades personalizadas que são calculadas usando expressões contra todos os pontos dentro de cada cluster e adicionadas às propriedades de cada ponto de cluster.

Quando o agrupamento estiver ativado, a fonte de dados enviará pontos de dados agrupados e não agrupados para camadas para renderização. A fonte de dados é capaz de agrupar centenas de milhares de pontos de dados. Um ponto de dados agrupado tem as seguintes propriedades:

| Nome da propriedade               | Tipo    | Description                                    |
|-----------------------------|---------|------------------------------------------------|
| `cluster`                   | boolean | Indica se a característica representa um cluster.     |
| `cluster_id`                | string  | Um ID único para o cluster que pode ser usado com as `DataSource` `getClusterExpansionZoom` `getClusterChildren` classes, e `getClusterLeaves` funções. |
| `point_count`               | número  | O número de pontos que o cluster contém.     |
| `point_count_abbreviated`   | string  | Uma corda que abrevia o `point_count` valor se for longa. (por exemplo, 4.000 torna-se 4K) |

A `DataSource` classe tem a seguinte função de ajudante para aceder a informações adicionais sobre um cluster utilizando o `cluster_id` .

| Função       | Tipo de retorno        | Description     |
|----------------|--------------------|-----------------|
| `getClusterChildren(clusterId: number)`                              | `Promise<Feature<Geometry, any> | Shape>` | Recupera as crianças do aglomerado dado no próximo nível de zoom. Estas crianças podem ser uma combinação de formas e sub-agrupamentos. Os sub-clusters serão funcionalidades com propriedades correspondentes às propriedades do cluster. |
| `getClusterExpansionZoom(clusterId: number)`                         | `Promise<number>`                            | Calcula um nível de zoom que o cluster começará a expandir-se ou a separar-se.    |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | `Promise<Feature<Geometry, any> | Shape>` | Recupera todos os pontos num aglomerado. Defina o `limit` para devolver um subconjunto dos pontos e use a `offset` página para página através dos pontos.    |

Ao renderizar dados agrupados no mapa, é muitas vezes mais fácil usar duas ou mais camadas. O exemplo abaixo usa três camadas, uma camada de bolha para desenhar círculos coloridos escalonados com base no tamanho dos clusters, uma camada de símbolo para tornar o tamanho do cluster como texto, e uma segunda camada de símbolo para renderizar os pontos não aglomerados. Existem muitas outras formas de renderizar dados agrupados no Azure Maps destacados na documentação de dados do [ponto cluster.](./clustering-point-data-web-sdk.md)

Os dados da GeoJSON podem ser importados diretamente no Azure Maps utilizando a `importDataFromUrl` função na `DataSource` classe.

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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
                        filter: ['has', 'point_count'] //Only rendered data points that have a point_count property, which clusters do.
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

![Agrupamento de mapas Azure](media/migrate-bing-maps-web-app/azure-maps-clustering.jpg)

**Recursos adicionais**

-   [Adicione uma camada de símbolo](./map-add-pin.md)
-   [Adicione uma camada de bolha](./map-add-bubble-layer.md)
-   [Dados de ponto do cluster](./clustering-point-data-web-sdk.md)
-   [Utilizar expressões de estilo com base em dados](./data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Adicione um mapa de calor

Os mapas de calor, também conhecidos como mapas de densidade de pontos, são um tipo de visualização de dados usado para representar a densidade de dados usando uma gama de cores. São frequentemente usados para mostrar os dados "hot spots" num mapa e são uma ótima maneira de tornar grandes conjuntos de dados de pontos.

Os exemplos abaixo carregam um feed GeoJSON de todos os terramotos no mês passado do USGS e os torna como um mapa de calor.

**Antes: Bing Maps**

Em Bing Maps, para criar um mapa de calor, carregue no módulo do mapa de calor. Da mesma forma, o módulo GeoJSON é carregado para adicionar suporte para dados GeoJSON.

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2,
                mapTypeId: Microsoft.Maps.MapTypeId.aerial
            });

            //Load the GeoJSON and HeatMap modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.HeatMap'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (shapes) {

                    //Create a heat map and add it to the map.
                    var heatMap = new Microsoft.Maps.HeatMapLayer(shapes, {
                        opacity: 0.65,
                        radius: 10
                    });
                    map.layers.insert(heatMap);
                });
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Mapas de Bing](media/migrate-bing-maps-web-app/bing-maps-heatmap.jpg)

**Depois: Mapas Azure**

No Azure Maps, carregue os dados do GeoJSON numa fonte de dados e ligue a fonte de dados a uma camada de mapa de calor. Os dados da GeoJSON podem ser importados diretamente no Azure Maps utilizando a `importDataFromUrl` função na `DataSource` classe.

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
                style: 'satellite_with_roads',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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

![Mapas de Calor Azure Maps](media/migrate-bing-maps-web-app/azure-maps-heatmap.jpg)

**Recursos adicionais**

-   [Adicionar uma camada de mapa térmico](./map-add-heat-map-layer.md)
-   [Classe de camada de mapa de calor](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
-   [Opções de camada de mapa de calor](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
-   [Utilizar expressões de estilo com base em dados](./data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Sobrepor uma camada de azulejo

As camadas de azulejos permitem sobrepor-se a grandes imagens que foram divididas em imagens de azulejos menores que se alinham com o sistema de inclinação de mapas. Esta é uma forma comum de sobrepor grandes imagens ou conjuntos de dados muito grandes.

Os exemplos abaixo sobrepõem uma camada de azulejos de radar meteorológico do Iowa Environmental Mesonet da Universidade Estadual de Iowa que usa um esquema de nomeação X, Y, Zoom tiling.

**Antes: Bing Maps**

No Bing Maps, as camadas de azulejos podem ser criadas utilizando a `Microsoft.Maps.TileLayer` classe.

```javascript
var weatherTileLayer = new Microsoft.Maps.TileLayer({
    mercator: new Microsoft.Maps.TileSource({
        uriConstructor: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{zoom}/{x}/{y}.png'
    })
});
map.layers.insert(weatherTileLayer);
```

![Mapas de Bing mapeias ponderados mapas](media/migrate-bing-maps-web-app/bing-maps-weighted-heatmap.jpg)

**Depois: Mapas Azure**

Em Azure Maps, uma camada de azulejo pode ser adicionada ao mapa da mesma forma que qualquer outra camada. Um URL formatado que tem em x, y, ampliadores; `{x}`, `{y}` `{z}` respectivamente, é utilizado para dizer a camada onde aceder aos azulejos. As camadas de azulejos Azure Maps também `{quadkey}` suportam, `{bbox-epsg-3857}` e espaços `{subdomain}` reservados.

> [!TIP]
> Nas camadas Azure Maps podem ser facilmente renderizadas abaixo de outras camadas, incluindo camadas de mapas base. Muitas vezes é desejável tornar as camadas de azulejos abaixo dos rótulos dos mapas para que sejam fáceis de ler. A `map.layers.add` função requer um segundo parâmetro que é o ID de uma segunda camada para inserir a nova camada abaixo. Para inserir uma camada de azulejos abaixo das etiquetas do mapa, pode utilizar-se o seguinte código:
>
> `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

![Mapas azuis ponderados mapas de calor](media/migrate-bing-maps-web-app/azure-maps-weighted-heatmap.jpg)

> [!TIP]
> Os pedidos de azulejos podem ser capturados usando a `transformRequest` opção do mapa. Isto permitir-lhe-á modificar ou adicionar cabeçalhos ao pedido, se desejar.

**Recursos adicionais**

-   [Adicionar camadas de mosaico](./map-add-tile-layer.md)
-   [Classe de camada de azulejos](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
-   [Opções de camada de azulejos](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Mostrar dados de tráfego

Os dados de tráfego podem ser sobrepostos tanto nos mapas de Bing como no Azure.

**Antes: Bing Maps**

No Bing Maps, os dados de tráfego podem ser sobrepostos no mapa usando o módulo de tráfego.

```javascript
Microsoft.Maps.loadModule('Microsoft.Maps.Traffic', function () {
    var manager = new Microsoft.Maps.Traffic.TrafficManager(map);
    manager.show();
});
```

![Tráfego de Bing Maps](media/migrate-bing-maps-web-app/bing-maps-traffic.jpg)

**Depois: Mapas Azure**

O Azure Maps oferece várias opções diferentes para exibir tráfego. Incidentes de trânsito, como encerramentos de estradas e acidentes, podem ser exibidos como ícones no mapa. O fluxo de tráfego, as estradas codificadas por cores, podem ser sobrepostos no mapa e as cores podem ser modificadas para serem baseadas em relação ao limite de velocidade registado, em relação ao atraso normal esperado, ou atraso absoluto. Os dados do incidente no Azure Maps são atualizados a cada minuto e os dados de fluxo a cada 2 minutos.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

![Tráfego Azure Maps](media/migrate-bing-maps-web-app/azure-maps-traffic.jpg)

Se clicar num dos ícones de tráfego no Azure Maps, são apresentadas informações adicionais num pop-up.

![Popup de tráfego Azure Maps](media/migrate-bing-maps-web-app/azure-maps-traffic-popup.jpg)

**Recursos adicionais**

-   [Mostrar tráfego no mapa](./map-show-traffic.md)
-   [Opções de sobreposição de tráfego](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)
-   [Controlo de tráfego](https://azuremapscodesamples.azurewebsites.net/?sample=Traffic%20controls)

### <a name="add-a-ground-overlay"></a>Adicione uma sobreposição de solo

Tanto os mapas de Bing como Azure suportam a sobreposição de imagens georreferenciadas no mapa para que se movam e escalam à medida que você panorâmica e zoom o mapa. Em Bing Maps estes são conhecidos como sobreposições de solo enquanto em Azure Maps são referidos como camadas de imagem. Estes são ótimos para construir plantas de piso, sobreposição de mapas antigos, ou imagens de um drone.

**Antes: Bing Maps**

Ao criar uma sobreposição de solo em Bing Maps, você precisa especificar o URL para a imagem para sobreposição e uma caixa de delimitação para ligar a imagem ao mapa. Este exemplo sobrepõe-se a uma imagem de mapa de [Newark New Jersey de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa.

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.740, -74.18),
                zoom: 12
            });

            var overlay = new Microsoft.Maps.GroundOverlay({
                //Create a LocationRect from the edges of the bounding box; north, west, south, east.
                bounds: Microsoft.Maps.LocationRect.fromEdges(40.773941, -74.22655, 40.712216, -74.12544),
                imageUrl: 'newark_nj_1922.jpg'
            });
            map.layers.insert(overlay);
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Executar este código num browser apresentará um mapa que se parece com a seguinte imagem:

![Sobreposição do solo de Bing Maps](media/migrate-bing-maps-web-app/bing-maps-ground-overlay.jpg)

**Depois: Mapas Azure**

No Azure Maps, as imagens georreferenciadas podem ser sobrepostas usando a `atlas.layer.ImageLayer` classe. Esta classe requer um URL para uma imagem e um conjunto de coordenadas para os quatro cantos da imagem. A imagem deve ser hospedada no mesmo domínio ou ter CORs ativados.

> [!TIP]
> Se tiver apenas informações de norte, sul, leste, oeste e rotação, e não coordenadas para cada canto da imagem, pode usar a função [estática atlas.layer.ImageLayer.getCoordinatesFromEdges.](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-)

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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

![Sobreposição de solo de Azure Maps](media/migrate-bing-maps-web-app/azure-maps-ground-overlay.jpg)

**Recursos adicionais**

-   [Sobrepor uma imagem](./map-add-image-layer.md)
-   [Classe de camada de imagem](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>Adicione dados KML ao mapa

Tanto os mapas Azure como os de Bing podem importar e renderizar dados de KML, KMZ, GeoRSS, GeoJSON e Well-Known Text (WKT) no mapa. O Azure Maps também suporta GPX, GML, ficheiros de CSV espaciais, serviços Web-Mapping (WMS), Web-Mapping Serviços de Azulejos (WMTS) e Serviços de Funcionalidade Web (WFS).

**Antes: Bing Maps**

Executar este código num browser apresentará um mapa que se parece com a seguinte imagem:

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
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
                
            Microsoft.Maps.loadModule('Microsoft.Maps.GeoXml', function () {
                var callback = function (dataset) {
                    if (dataset.shapes) {
                        var l = new Microsoft.Maps.Layer();
                        l.add(dataset.shapes);
                        map.layers.insert(l);
                    }
                    if (dataset.layers) {
                        for (var i = 0, len = dataset.layers.length; i < len; i++) {
                            map.layers.insert(dataset.layers[i]);
                        }
                    }
                };
                Microsoft.Maps.GeoXml.readFromUrl('myKMLFile.kml', { error: function (msg) { alert(msg); } }, callback);
            });                
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Bing Maps kml](media/migrate-bing-maps-web-app/bing-maps-kml.jpg)

**Depois: Mapas Azure**

No Azure Maps, o GeoJSON é o principal formato de dados utilizado na web SDK, os formatos de dados espaciais adicionais podem ser facilmente integrados na utilização do [módulo IO espacial.](/javascript/api/azure-maps-spatial-io/) Este módulo tem funções tanto para ler como escrever dados espaciais e também inclui uma camada de dados simples que pode facilmente renderizar dados de qualquer um destes formatos de dados espaciais. Para ler os dados num ficheiro de dados espaciais, passe num URL ou dados brutos como string ou blob para a `atlas.io.read` função. Isto irá devolver todos os dados analisados do ficheiro que podem ser adicionados ao mapa. O KML é um pouco mais complexo do que a maioria do formato de dados espaciais, pois inclui muito mais informações de estilo. A `SpatialDataLayer` classe suporta a renderização da maioria destes estilos, no entanto as imagens de ícones têm de ser carregadas no mapa antes de carregar os dados da funcionalidade, e as sobreposições do solo têm de ser adicionadas como camadas ao mapa separadamente. Ao carregar dados através de um URL, deve ser hospedado num ponto final ativado por CORs, ou um serviço de procuração deve ser transmitido como uma opção para a função de leitura.

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

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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
                atlas.io.read('myKMLFile.kml').then(async r => {
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

![Azure Maps kml](media/migrate-bing-maps-web-app/azure-maps-kml.jpg)

**Recursos adicionais**

-   [função atlas.io.read](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
-   [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
-   [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

### <a name="add-drawing-tools"></a>Adicionar ferramentas de desenho

Tanto o Bing como o Azure Maps fornecem um módulo que adiciona a capacidade de o utilizador desenhar e editar formas no mapa utilizando o rato ou outro dispositivo de entrada. Ambos suportam a desenhar pinos, linhas e polígonos. O Azure Maps também oferece opções para desenhar círculos e retângulos.

**Antes: Bing Maps**

No Bing Maps o `DrawingTools` módulo é carregado utilizando a `Microsoft.Maps.loadModule` função. Uma vez carregado, pode ser criado um exemplo da classe DrawingTools e a `showDrawingManager` função é chamada adicionar uma barra de ferramentas ao mapa.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
    var map, drawingManager;

    function initMap() {
        map = new Microsoft.Maps.Map('#myMap', {
            credentials: '<Your Bing Maps Key>'
        });

        //Load the DrawingTools module
        Microsoft.Maps.loadModule('Microsoft.Maps.DrawingTools', function () {
            //Create an instance of the DrawingTools class and bind it to the map.
            var tools = new Microsoft.Maps.DrawingTools(map);

            //Show the drawing toolbar and enable editting on the map.
            tools.showDrawingManager(function (manager) {
                //Store a reference to the drawing manager as it will be useful later.
                drawingManager = manager;
            });
        });
    }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>

```

![Bing Maps ferramentas de desenho](media/migrate-bing-maps-web-app/bing-maps-drawing-tools.jpg)

**Depois: Mapas Azure**

No Azure Maps, o módulo de ferramentas de desenho precisa de ser carregado carregando os ficheiros JavaScript e CSS que precisam de ser referenciados na aplicação. Uma vez carregado o mapa, uma instância da `DrawingManager` classe pode ser criada e um caso `DrawingToolbar` anexado.

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

    <!-- Add references to the Azure Maps Map Drawing Tools JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
    
    <script type='text/javascript'>
        var map, drawingManager;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an instance of the drawing manager and display the drawing toolbar.
                drawingManager = new atlas.drawing.DrawingManager(map, {
                    toolbar: new atlas.control.DrawingToolbar({ position: 'top-left' })
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

![Ferramentas de desenho Azure Maps](media/migrate-bing-maps-web-app/azure-maps-drawing-tools.jpg)

> [!TIP]
> Nas camadas Azure Maps, as ferramentas de desenho fornecem múltiplas formas de os utilizadores desenharem formas. Por exemplo, ao desenhar um polígono, o utilizador pode clicar para adicionar cada ponto, ou premir o botão do rato esquerdo para baixo e arrastar o rato para desenhar um caminho. Isto pode ser modificado utilizando a `interactionType` opção do `DrawingManager` .

**Recursos adicionais**

-   [Documentação](./set-drawing-options.md)
-   [Amostras de código](https://azuremapscodesamples.azurewebsites.net/#Drawing-Tools-Module)

## <a name="additional-resources"></a>Recursos adicionais

Veja os [módulos SDK web Azure Maps de código aberto.](open-source-projects.md#open-web-sdk-modules) Estes módulos fornecem uma tonelada de funcionalidade adicional e são totalmente personalizáveis.

Rever amostras de código relacionadas com a migração de outras funcionalidades do Bing Maps:

**Visualizações de dados**

> [!div class="nextstepaction"]
> [Camada de contorno](https://azuremapscodesamples.azurewebsites.net/?search=contour)

> [!div class="nextstepaction"]
> [Binning de dados](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)

**Serviços**

> [!div class="nextstepaction"]
> [Utilizando o módulo de serviços Azure Maps](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Procurar pontos de interesse](./map-search-location.md)

> [!div class="nextstepaction"]
> [Obtenha informações a partir de uma coordenada (geocódigo inverso)](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Mostrar as direções de A para B](./map-route.md)

> [!div class="nextstepaction"]
> [Pesse autosuggest com JQuery UI](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

Saiba mais sobre o Azure Maps Web SDK.

> [!div class="nextstepaction"]
> [Como usar o controlo do mapa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Como utilizar o módulo de serviços](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Como usar o módulo de ferramentas de desenho](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Amostras de código](/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Documentação de referência de referência da Azure Maps Web SDK Service API](/javascript/api/azure-maps-control/)

## <a name="clean-up-resources"></a>Limpar os recursos

Não há recursos para ser limpo.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre migrar de Bing Maps para Azure Maps.

> [!div class="nextstepaction"]
> [Migrar um serviço Web](migrate-from-bing-maps-web-services.md)