---
title: Migrar um aplicativo Web do Google Maps | Microsoft Docs
description: Um tutorial sobre como migrar um aplicativo Web do Google Maps para mapas de Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a414d7b15f81ab81783b66f8297a207afe569365
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562175"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Migrar um aplicativo Web do Google Maps

A maioria dos aplicativos Web que usam o Google Maps está usando o SDK do JavaScript do Google Maps v3. O SDK da Web do Azure Maps é o SDK baseado no Azure adequado para migrar para o. O SDK da Web do Azure Maps permite que você personalize mapas interativos com seu próprio conteúdo e imagens para exibição em seus aplicativos móveis ou Web. Este controlo utiliza o WebGL, permitindo-lhe compor grandes conjuntos de dados com elevado desempenho. Desenvolva com este SDK usando JavaScript ou TypeScript.

Se estiver migrando um aplicativo Web existente, verifique se ele está usando uma biblioteca de controle de mapa de código aberto, como Cesium, Leaflet e OpenLayers. Se for e você não quiser usar o SDK da Web do Azure Maps, outra opção para migrar seu aplicativo será continuar usando o controle de mapeamento de código-fonte aberto e conectá-lo aos serviços de bloco do Azure Maps ([blocos de estrada](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [blocos satélites](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)). Veja a seguir os detalhes sobre como usar o Azure Maps em algumas bibliotecas de controle de mapa de código aberto comumente usadas.

- Cesium-um controle de mapa 3D para a Web. [Exemplo de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) de \| [documentação](https://cesiumjs.org/)
- Leaflet – controle de mapa 2D leve para a Web. [Exemplo de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) de \| [documentação](https://leafletjs.com/)
- OpenLayers-um controle de mapa 2D para a Web que dá suporte a projeções. [Exemplo de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) de \| [documentação](https://openlayers.org/)

## <a name="key-features-support"></a>Suporte aos principais recursos

A tabela a seguir lista os principais recursos da API no SDK do JavaScript do Google Maps v3 e o suporte de uma API semelhante no SDK da Web do Azure Maps.

| Recurso do Google Maps     | Suporte do SDK para Web do Azure Maps |
|-------------------------|:--------------------------:|
| Marcadores                 | ✓                          |
| Agrupamento de marcadores       | ✓                          |
| Polilinhas & polígonos    | ✓                          |
| Camadas de dados             | ✓                          |
| Sobreposições de aterramento         | ✓                          |
| Mapas gráficos               | ✓                          |
| Camadas do bloco             | ✓                          |
| Camada KML               | ✓                          |
| Ferramentas de desenho           | ✓                          |
| Serviço geocodificador        | ✓                          |
| Serviço de direções      | ✓                          |
| Serviço de matriz de distância | ✓                          |
| Serviço de elevação       | Planeado                    |

## <a name="notable-differences-in-the-web-sdks"></a>Diferenças notáveis nos SDKs da Web

A seguir estão algumas das principais diferenças entre os SDKs da Web do Google Maps e do Azure Maps que devem estar cientes:

- Além de fornecer um ponto de extremidade hospedado para acessar o SDK da Web do Azure Maps, um pacote NPM também está disponível para inserir o SDK da Web em aplicativos, se preferir. Consulte esta [documentação](how-to-use-map-control.md) para obter mais informações. Esse pacote também inclui definições de TypeScript.
- Depois de criar uma instância da classe Map no Azure Maps, seu código deve aguardar até que os mapas `ready` ou `load` evento sejam acionados antes de interagir com o mapa. Isso garantirá que todos os recursos de mapa tenham sido carregados e estejam prontos para serem acessados.
- Ambas as plataformas usam um sistema de divisão semelhante para os mapas base, no entanto, os blocos no Google Maps são 256 pixels em dimensão, enquanto os blocos no Azure Maps são 512 pixels na dimensão. Assim, para obter a mesma exibição de mapa no Azure Maps que o Google Maps, um nível de zoom usado no Google Maps precisa ser subtraído em um dos mapas do Azure.
- As coordenadas no Google Maps são chamadas de "latitude, longitude" enquanto o Azure Maps usa "longitude, Latitude". Isso é alinhado com o `[x, y]` padrão, seguido pela maioria das plataformas GIS.
- As formas no SDK da Web do Azure Maps são baseadas no esquema geojson. As classes auxiliares são expostas por meio do namespace do [ *Atlas. Data* ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest). Também há o [*Atlas.* ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape)Classe de forma que pode ser usada para encapsular objetos geojson e torná-los fáceis de atualizar e manter em uma maneira vinculável de dados.
- As coordenadas nos mapas do Azure são definidas como objetos de posição que podem ser especificados como uma matriz de número simples no formato `[longitude, latitude]` ou novo Atlas. Data. Position (longitude, latitude).
    > [!TIP]
    > A classe position tem um método auxiliar estático para importar coordenadas que estão no formato "latitude, longitude". O método [Atlas. Data. Position. fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) geralmente pode substituir o método `new google.maps.LatLng` no código do Google Maps.
- Em vez de especificar informações de estilo em cada forma que é adicionada ao mapa, o Azure Maps separa os estilos dos dados. Os dados são armazenados em fontes de dados e estão conectados à renderização de camadas que o código do Azure Maps usa para renderizar os dados. Essa abordagem fornece um benefício de desempenho aprimorado. Além disso, muitas camadas dão suporte ao estilo controlado por dados, em que a lógica de negócios pode ser adicionada às opções de estilo de camada que mudarão como as formas individuais são renderizadas dentro de uma camada com base nas propriedades definidas na forma.

## <a name="web-sdk-side-by-side-examples"></a>Exemplos lado a lado do SDK da Web

Veja a seguir uma coleção de exemplos de código para cada plataforma que abrange casos de uso comuns para ajudá-lo a migrar seu aplicativo Web do SDK do JavaScript do Google Maps V3 para o SDK da Web do Azure Maps. Exemplos de código relacionados a aplicativos Web são fornecidos em JavaScript; no entanto, o Azure Maps também fornece definições de TypeScript como uma opção adicional por meio de um [módulo NPM](how-to-use-map-control.md).

### <a name="load-a-map"></a>Carregar um mapa

O carregamento de um mapa em ambos os SDKs segue o mesmo conjunto de etapas:

- Adicione uma referência ao SDK do MAP.
- Adicione uma marca de `div` ao corpo da página que atuará como um espaço reservado para o mapa.
- Crie uma função JavaScript que é chamada quando a página é carregada.
- Crie uma instância da respectiva classe de mapa.

**Algumas diferenças principais**

- O Google Maps requer que uma chave de conta seja especificada na referência de script da API. As credenciais de autenticação para mapas do Azure são especificadas como opções da classe do mapa. Pode ser uma chave de assinatura ou informações de Azure Active Directory.
- O Google Maps usa uma função de retorno de chamada na referência de script da API, que é usada para chamar uma função de inicialização para carregar o mapa. Com o Azure Maps, o evento OnLoad da página deve ser usado.
- Ao referenciar o elemento `div` no qual o mapa será renderizado, a classe `Map` no Azure Maps requer apenas o valor `id`, enquanto o Google Maps requer um objeto `HTMLElement`.
- As coordenadas nos mapas do Azure são definidas como objetos de posição que podem ser especificados como uma matriz de número simples no formato `[longitude, latitude]`.
- O nível de zoom no mapas do Azure é um nível inferior ao exemplo do Google Maps devido à diferença nos tamanhos de sistema de divisão entre as plataformas.
- Por padrão, o mapas do Azure não adiciona nenhum controle de navegação à tela do mapa, como botões de zoom e botões de estilo de mapa. No entanto, há controles para adicionar um seletor de estilo de mapa, botões de zoom, controle de bússola ou rotação e um controle de densidade.
- Um manipulador de eventos é adicionado no Azure Maps para monitorar o `ready` evento da instância do mapa. Isso será acionado quando o mapa terminar de carregar o contexto de WebGL e todos os recursos necessários. Qualquer código post Load pode ser adicionado neste manipulador de eventos.

Os exemplos a seguir mostram como carregar um mapa básico, que é centralizado em uma Nova York em coordenadas (Longitude:-73,985, Latitude: 40,747) e está no nível de zoom 12 no Google Maps.

**Antes: Google Maps**

O código a seguir é um exemplo de como exibir um mapa do Google centralizado e ampliado em um local.

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

Executar esse código em um navegador exibirá um mapa semelhante à imagem a seguir:

<center>

![](media/migrate-google-maps-web-app/simple-google-map.png)</center> simples do Google Maps

**Após: mapas do Azure**

O código a seguir mostra como carregar um mapa com o mesmo modo de exibição no Azure Maps, juntamente com um controle de estilo de mapa e botões de zoom.

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

Executar esse código em um navegador exibirá um mapa semelhante à imagem a seguir:

<center>

![mapas simples do Azure](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

A documentação detalhada sobre como configurar e usar o controle de mapeamento do Azure Maps em um aplicativo Web pode ser encontrada [aqui](how-to-use-map-control.md).

> [!NOTE]
> Ao contrário do Google Maps, o mapas do Azure não exige que um centro inicial e um nível de zoom sejam especificados durante o carregamento do mapa. Se essas informações não forem fornecidas ao carregar o mapa, o mapa tentará determinar em qual cidade o usuário está e será centralizado e aplicará zoom no mapa.

**Recursos adicionais:**

- O mapas do Azure também fornece controles de navegação para girar e pitch a exibição de mapa conforme documentado [aqui](map-add-controls.md).

### <a name="localizing-the-map"></a>Localizando o mapa

Se seu público estiver espalhado em vários países ou falar em idiomas diferentes, a localização será importante.

**Antes: Google Maps**

Para localizar os parâmetros do Google Maps, idioma e região são adicionados a

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Aqui está um exemplo do Google Maps com o idioma definido como "fr-FR".

<center>

![localização do Google Maps](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**Após: mapas do Azure**

O mapas do Azure fornece duas maneiras diferentes de definir o idioma e a exibição regional do mapa. A primeira opção é adicionar essas informações ao namespace global do *Atlas* , o que resultará em todas as instâncias de controle de mapa em seu aplicativo padronizando essas configurações. O seguinte define o idioma como francês ("fr-FR") e a exibição regional como "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

A segunda opção é passar essas informações para as opções de mapa ao carregar o mapa, como:

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
> Com o Azure Maps, é possível carregar várias instâncias de mapa na mesma página com diferentes configurações de idioma e região. Além disso, também é possível atualizar essas configurações no mapa após ele ter sido carregado. Uma lista detalhada de idiomas com suporte no Azure Maps pode ser encontrada [aqui](supported-languages.md).

Aqui está um exemplo de mapas do Azure com o idioma definido como "fr" e a região do usuário definida como "fr-FR".

<center>

![a localização do Azure Maps](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>Configurando a exibição do mapa

Mapas dinâmicos no Azure e no Google Maps podem ser movidos programaticamente para novos locais geográficos chamando as funções apropriadas em JavaScript. Os exemplos a seguir mostram como fazer com que o mapa exiba imagens aéreas satélite, centralize o mapa em um local com coordenadas (Longitude:-111, 225, Latitude: 35, 272) e altere o nível de zoom para 15 no Google Maps.

> [!NOTE]
> O Google Maps usa blocos que são 256 pixels em dimensões enquanto o Azure Maps usa um bloco maior de 512 pixels. Isso reduz o número de solicitações de rede necessárias ao mapas do Azure para carregar a mesma área do mapa que o Google Maps. No entanto, devido à maneira como as pirâmides de bloco funcionam nos controles de mapa, os blocos maiores no Azure Maps significam que para alcançar essa mesma área visível como um mapa no Google Maps, você precisa subtrair o nível de zoom usado no Google Maps por um ao usar mapas do Azure.

**Antes: Google Maps**

O controle de mapa do Google Maps pode ser movido programaticamente usando o método `setOptions`, que permite especificar o centro do mapa e um nível de zoom.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![modo de exibição de conjunto do Google Maps](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**Após: mapas do Azure**

No Azure Maps, a posição do mapa pode ser alterada programaticamente usando o método `setCamera` do mapa e a alteração do estilo do mapa ser alterada usando o método `setStyle`. Observe que as coordenadas nos mapas do Azure estão no formato "longitude, Latitude" e o valor do nível de zoom é subtraído por um.

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

![exibição de conjunto de mapas do Azure](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**Recursos adicionais:**

- [Escolher um estilo de mapa](choose-map-style.md)
- [Estilos de mapa com suporte](supported-map-styles.md)

### <a name="adding-a-marker"></a>Adicionando um marcador

No Azure Maps, há várias maneiras em que os dados de ponto podem ser renderizados no mapa;

- **Marcadores de HTML** – renderiza pontos usando elementos DOM tradicionais. Os marcadores de HTML dão suporte ao arrastar.
- **Camada de símbolo** – renderiza pontos com um ícone e/ou texto dentro do contexto WebGL.
- **Camada de bolha** – renderiza pontos como círculos no mapa. O raios dos círculos pode ser dimensionado com base nas propriedades dos dados.

As camadas de símbolo e de bolha são renderizadas no contexto WebGL e são capazes de renderizar conjuntos muito grandes de pontos no mapa. Essas camadas exigem que os dados sejam armazenados em uma fonte de dados. As fontes de dados e as camadas de renderização devem ser adicionadas ao mapa depois que o evento de `ready` disparado. Os marcadores HTML são renderizados como elementos DOM dentro da página e não usam uma fonte de dados. Quanto mais elementos DOM uma página tiver, mais lenta será a página. Ao renderizar mais de centenas de pontos em um mapa, é recomendável usar uma das camadas de renderização em vez disso.

Os exemplos a seguir adicionam um marcador ao mapa em (Longitude:-0,2, Latitude: 51,5) com o número 10 sobreposto como um rótulo.

**Antes: Google Maps**

Com o Google Maps, os marcadores são adicionados ao mapa usando a classe `google.maps.Marker` e especificando o mapa como uma das opções.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![](media/migrate-google-maps-web-app/google-maps-marker.png)</center> de marcador do Google Maps

**Após: o Azure mapeia usando marcadores de HTML**

No Azure Maps, os marcadores de HTML podem ser usados para exibir um ponto no mapa e são recomendados para aplicativos simples que só precisam exibir um pequeno número de pontos no mapa. Para usar um marcador HTML, basta criar uma instância da classe `atlas.HtmlMarker`, definir as opções de texto e de posição e adicionar o marcador ao mapa usando o método `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![marcador HTML do Azure Maps](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**Após: o Azure mapeia usando uma camada de símbolo**

Ao usar uma camada de símbolo, os dados devem ser adicionados a uma fonte de dados e a fonte de dados anexada à camada. Além disso, a fonte de dados e a camada devem ser adicionadas ao mapa depois que o evento de `ready` foi acionado. Para renderizar um valor de texto exclusivo acima de um símbolo, as informações de texto precisam ser armazenadas como uma propriedade do ponto de dados e essa propriedade referenciada na `textField` opção da camada. Isso é um pouco mais de trabalho do que usar marcadores de HTML, mas oferece muitas vantagens de desempenho.

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

![camada de símbolo do Azure Maps](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**Recursos adicionais:**

- [Criar uma fonte de dados](create-data-source-web-sdk.md)
- [Adicionar uma camada de símbolo](map-add-pin.md)
- [Adicionar uma camada de bolha](map-add-bubble-layer.md)
- [Dados do ponto de cluster](clustering-point-data-web-sdk.md)
- [Adicionar marcadores HTML](map-add-custom-html.md)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)
- [Opções de ícone de camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Opção de texto da camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Classe de marcador HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Opções de marcador HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Adicionando um marcador personalizado

Imagens personalizadas podem ser usadas para representar pontos em um mapa. A imagem a seguir é usada nos exemplos abaixo, use uma imagem personalizada para exibir um ponto no mapa em (Latitude: 51,5, Longitude:-0,2) e desloca a posição do marcador para que o ponto do ícone de pino fique alinhado com a posição correta no mapa.

<center>

![imagem de pino amarelo](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
YLW\_</center> de anotações. png

**Antes: Google Maps**

No Google Maps, um marcador personalizado é criado especificando um objeto de `Icon` que contém a `url` da imagem, um ponto de `anchor` para alinhar o ponto da imagem do pino com a coordenada no mapa. O valor de âncora no Google Maps em relação ao canto superior esquerdo da imagem.

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

![marcador personalizado do Google Maps](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**Após: o Azure mapeia usando marcadores de HTML**

Para personalizar um marcador HTML no Azure Maps, um `string` HTML ou `HTMLElement` pode ser passado para a opção `htmlContent` do marcador. No Azure Maps, uma opção `anchor` é usada para especificar a posição relativa do marcador em relação à coordenada de posição usando um dos nove pontos de referência definidos; "Center", "superior", "inferior", "esquerda", "direita", "superior esquerdo", "superior direito", "inferior esquerdo", "inferior direito". O conteúdo é ancorado no centro inferior do conteúdo HTML por padrão. Para facilitar a migração de código do Google Maps, defina o `anchor` como "da parte superior esquerda" e, em seguida, use a opção `pixelOffset` com o mesmo deslocamento usado no Google Maps. Os deslocamentos nos mapas do Azure são movidos na direção oposta do Google Maps, portanto, multiplique-os por menos um.

> [!TIP]
> Adicione `pointer-events:none` como um estilo no conteúdo HTML para desabilitar o comportamento de arrastar padrão no Microsoft Edge, que exibirá um ícone indesejado.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![marcador HTML personalizado do Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**Após: o Azure mapeia usando uma camada de símbolo**

As camadas de símbolo no Azure Maps também dão suporte a imagens personalizadas, mas a imagem precisa ser carregada nos recursos de mapa primeiro e atribuída uma ID exclusiva. Em seguida, a camada de símbolo pode fazer referência a essa ID. O símbolo pode ser deslocado para alinhar-se ao ponto correto na imagem usando o ícone `offset` opção. No Azure Maps, uma opção `anchor` é usada para especificar a posição relativa do símbolo em relação à coordenada de posição usando um dos nove pontos de referência definidos; "Center", "superior", "inferior", "esquerda", "direita", "superior esquerdo", "superior direito", "inferior esquerdo", "inferior direito". O conteúdo é ancorado no centro inferior do conteúdo HTML por padrão. Para facilitar a migração de código do Google Maps, defina o `anchor` como "da parte superior esquerda" e, em seguida, use a opção `offset` com o mesmo deslocamento usado no Google Maps. Os deslocamentos nos mapas do Azure são movidos na direção oposta do Google Maps, portanto, multiplique-os por menos um.

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

![camada de símbolos de ícone personalizado do Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> Para criar renderização personalizada avançada de pontos, use várias camadas de renderização juntas. Por exemplo, se você quiser ter várias anotações que têm o mesmo ícone em diferentes círculos coloridos, em vez de criar várias imagens para cada sobreposição de cor de uma camada de símbolo na parte superior de uma camada de bolha e fazer com que elas referenciem a mesma fonte de dados. Isso será muito mais eficiente do que criar e fazer com que o mapa mantenha várias imagens diferentes.

**Recursos adicionais:**

- [Criar uma fonte de dados](create-data-source-web-sdk.md)
- [Adicionar uma camada de símbolo](map-add-pin.md)
- [Adicionar marcadores HTML](map-add-custom-html.md)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)
- [Opções de ícone de camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Opção de texto da camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Classe de marcador HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Opções de marcador HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Adicionando uma polilinha

As polilinhas são usadas para representar uma linha ou um caminho no mapa. Os exemplos a seguir mostram como criar uma polilinha tracejada no mapa.

**Antes: Google Maps**

No Google Maps, a classe Polyline usa um conjunto de opções. Uma matriz de coordenadas é passada na opção `path` da polilinha.

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

![](media/migrate-google-maps-web-app/google-maps-polyline.png)</center> de polilinha do Google Maps

**Após: mapas do Azure**

No Azure Maps, as polilinhas são chamadas de objetos LineString ou MultiLineString. Esses objetos podem ser adicionados a uma fonte de dados e renderizados usando uma camada de linha.

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

![](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center> de polilinha do Azure Maps

**Recursos adicionais:**

- [Adicionar linhas ao mapa](map-add-line-layer.md)
- [Opções de camada de linha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Adicionando um polígono

Os polígonos são usados para representar uma área no mapa. O Azure Maps e o Google Maps fornecem suporte muito semelhante para polígonos. Os exemplos a seguir mostram como criar um polígono que forma um triângulo com base na coordenada central do mapa.

**Antes: Google Maps**

No Google Maps, a classe Polygon usa um conjunto de opções. Uma matriz de coordenadas é passada na opção de `paths` do polígono.

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

![polígono do Google Maps](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**Após: mapas do Azure**

Nos mapas do Azure, os objetos Polygon e MultiPolygon podem ser adicionados a uma fonte de dados e renderizados no mapa usando camadas. A área de um polígono pode ser renderizada em uma camada de polígono. O contorno de um polígono pode ser renderizado usando uma camada de linha.

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

![polígono do Azure Maps](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**Recursos adicionais:**

- [Adicionar um polígono ao mapa](map-add-shape.md)
- [Adicionar um círculo ao mapa](map-add-shape.md#add-a-circle-to-the-map)
- [Opções de camada de polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Opções de camada de linha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Exibir uma janela de informações

Informações adicionais para uma entidade podem ser exibidas no mapa como uma classe de `google.maps.InfoWindow` no Google Maps, no Azure Maps, isso pode ser feito usando a classe `atlas.Popup`. Os exemplos a seguir adicionam um marcador ao mapa e, quando clicado, exibem uma janela de informações/Popup.

**Antes: Google Maps**

Com o Google Maps, uma janela de informações é criada usando o Construtor `google.maps.InfoWindow`.

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

![pop-up do Google Maps](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**Após: mapas do Azure**

No Azure Maps, um pop-up pode ser usado para exibir informações adicionais para um local. Um objeto `string` ou `HTMLElement` HTML pode ser passado para a opção `content` do pop-up. Os pop-ups podem ser exibidos independentemente de qualquer forma, se desejado e, portanto, exigem que um valor de `position` seja especificado. Para exibir um pop-up, chame o método `open` e passe o `map` no qual o Popup deve ser exibido.

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

![pop-up de mapas do Azure](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> Para fazer a mesma coisa com uma camada Symbol, Bubble, line ou Polygon, basta passar a camada para o código do evento Maps, em vez de um marcador.

**Recursos adicionais:**

- [Adicionar um pop-up](map-add-popup.md)
- [Pop-up com conteúdo de mídia](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Pop-ups em formas](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Reutilizando o popup com vários Pins](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Classe Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Opções de pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>Importar um arquivo geojson

O Google Maps dá suporte ao carregamento e ao estilo dinâmico de dados geojson por meio da classe `google.maps.Data`. A funcionalidade dessa classe alinha muito mais com o estilo controlado por dados dos mapas do Azure. Uma das principais diferenças é que, com o Google Maps, você especifica uma função de retorno de chamada e a lógica de negócios para estilizar cada recurso que ele processou individualmente no thread da interface do usuário. No Azure Maps, as camadas dão suporte à especificação de expressões controladas por dados como opções de estilo. Essas expressões são processadas no momento da renderização em um thread separado e fornecem maior desempenho de renderização e permitem que conjuntos de dados maiores sejam processados mais rapidamente.

Os exemplos a seguir carregam um feed geojson de todos os terremotos nos últimos sete dias do USGS e os renderiza como círculos dimensionados no mapa. A cor e a escala de cada círculo baseiam-se na magnitude de cada terremoto que é armazenado na propriedade `"mag"` de cada recurso no conjunto de dados. Se a magnitude for maior ou igual a cinco, o círculo será vermelho, se for maior ou igual a três, mas menor que cinco, o círculo será laranja, se for menor que três, o círculo ficará verde. O raio de cada círculo será o exponencial da magnitude multiplicada por 0,1.

**Antes: Google Maps**

No Google Maps, uma única função de retorno de chamada pode ser especificada no método `map.data.setStyle`, que será usado para aplicar a lógica de negócios a cada recurso carregado do feed do geojson por meio do método `map.data.loadGeoJson`.

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

![](media/migrate-google-maps-web-app/google-maps-geojson.png)</center> geojson do Google Maps

**Após: mapas do Azure**

Geojson é o tipo de dados base no Azure Maps e pode ser facilmente importado para uma fonte de dados usando o método `datasource.importFromUrl`. Uma camada de bolha fornece funcionalidade para renderizar círculos dimensionados com base nas propriedades dos recursos em uma fonte de dados. Em vez de ter uma função de retorno de chamada, a lógica de negócios é convertida em uma expressão e passada para as opções de estilo. As expressões definem como a lógica de negócios funciona para que possa ser passada para outro thread e avaliada em relação aos dados do recurso. Várias fontes de dados e camadas podem ser adicionadas ao Azure Maps, cada uma com lógica de negócios diferente, permitindo assim que vários conjuntos de dados sejam renderizados no mapa de maneiras diferentes.

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

![](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center> geojson do Azure Maps

**Recursos adicionais:**

- [Adicionar uma camada de símbolo](map-add-pin.md)
- [Adicionar uma camada de bolha](map-add-bubble-layer.md)
- [Dados do ponto de cluster](clustering-point-data-web-sdk.md)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Agrupamento de marcadores

Ao Visualizar muitos pontos de dados no mapa, os pontos se sobrepõem, o mapa fica confuso e torna-se difícil ver e usar. O clustering de dados de ponto pode ser usado para melhorar essa experiência do usuário e também melhorar o desempenho. Os dados do ponto de cluster são o processo de combinação de dados de ponto que estão próximos uns dos outros e os representa no mapa como um único ponto de dados clusterizado. À medida que o usuário se aplica ao mapa, os clusters se dividem em seus pontos de dados individuais.

Os exemplos a seguir carregam um feed geojson de dados de terremoto da última semana e os adiciona ao mapa. Os clusters são renderizados como círculos em escala e coloridos, dependendo do número de pontos que eles contêm.

> [!NOTE]
> Há vários algoritmos diferentes usados para o clustering de marcador. O Google e o mapas do Azure usam algoritmos ligeiramente diferentes. Dessa forma, às vezes a distribuição de ponto nos clusters pode variar.

**Antes: Google Maps**

Nos marcadores do Google Maps podem ser clusterizados carregando na biblioteca MarkerClusterer. Os ícones de cluster são limitados a imagens que têm os números de um a cinco como seu nome e são hospedados no mesmo diretório.

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

![clustering do Google Maps](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**Após: mapas do Azure**

No Azure Maps, os dados são adicionados e gerenciados por uma fonte de dados. As camadas se conectam a fontes de dados e renderizam os dados nelas. A classe `DataSource` no Azure Maps fornece várias opções de clustering.

- `cluster` – informa a fonte de dados aos dados do ponto de cluster.
- `clusterRadius`-o raio em pixels para os pontos de cluster juntos.
- `clusterMaxZoom`-o nível máximo de zoom no qual o clustering ocorre. Se você ampliar mais do que isso, todos os pontos serão renderizados como símbolos.
- `clusterProperties`-define propriedades personalizadas que são calculadas usando expressões em todos os pontos de cada cluster e adicionadas às propriedades de cada ponto de cluster.

Quando o clustering estiver habilitado, a fonte de dados enviará pontos de dados clusterizados e não clusterizados para camadas para renderização. A fonte de dados é capaz de clusterizar centenas de milhares de pontos de dados. Um ponto de dados clusterizado tem as seguintes propriedades:

| Nome da propriedade             | Tipo    | Descrição   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Indica se o recurso representa um cluster. |
| `cluster_id`              | string  | Uma ID exclusiva para o cluster que pode ser usada com os métodos DataSource `getClusterExpansionZoom`, `getClusterChildren`e `getClusterLeaves`. |
| `point_count`             | número  | O número de pontos que o cluster contém.  |
| `point_count_abbreviated` | string  | Uma cadeia de caracteres que abrevia o valor de `point_count` se for longo. (por exemplo, 4.000 torna-se 4K)  |

A classe `DataSource` tem a seguinte função auxiliar para acessar informações adicionais sobre um cluster usando o `cluster_id`.

| Método | Tipo de retorno | Descrição |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | O recurso de&lt;de matriz de promessa&lt;&lt;geometria, qualquer&gt; \|&gt;de forma &gt; | Recupera os filhos do cluster fornecido no próximo nível de zoom. Esses filhos podem ser uma combinação de formas e subclusters. Os subclusters serão recursos com propriedades correspondentes a ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Número de&lt;de promessa&gt; | Calcula um nível de zoom no qual o cluster começará a se expandir ou separar. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | O recurso de&lt;de matriz de promessa&lt;&lt;geometria, qualquer&gt; \|&gt;de forma &gt; | Recupera todos os pontos em um cluster. Defina o `limit` para retornar um subconjunto dos pontos e use a `offset` para paginar os pontos. |

Ao renderizar dados clusterizados no mapa, geralmente é mais fácil usar duas ou mais camadas. O exemplo a seguir usa três camadas, uma camada de bolha para desenhar círculos coloridos em escala com base no tamanho dos clusters, uma camada de símbolo para renderizar o tamanho do cluster como texto e uma segunda camada de símbolo para renderizar os pontos não clusterizados. Há muitas outras maneiras de renderizar dados clusterizados no Azure Maps realçado na documentação de [dados do ponto de cluster](clustering-point-data-web-sdk.md) .

Os dados geojson podem ser importados diretamente no Azure Maps usando a função `importDataFromUrl` na classe `DataSource`.

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

![o clustering do Azure Maps](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**Recursos adicionais:**

- [Adicionar uma camada de símbolo](map-add-pin.md)
- [Adicionar uma camada de bolha](map-add-bubble-layer.md)
- [Dados do ponto de cluster](clustering-point-data-web-sdk.md)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Adicionar um mapa de calor

Mapas de calor, também conhecidos como mapas de densidade de ponto, são um tipo de visualização de dados usado para representar a densidade de dados usando um intervalo de cores. Geralmente, eles são usados para mostrar os dados "pontos de acesso" em um mapa e são uma ótima maneira de renderizar conjuntos de dados de ponto grande.

Os exemplos a seguir carregam um feed geojson de todos os terremotos no último mês do USGS e os renderiza como um mapa de calor ponderado em que a propriedade `"mag"` é usada como o peso.

**Antes: Google Maps**

No Google Maps, para criar um mapa de calor, a biblioteca de "visualização" precisa ser carregada adicionando `&libraries=visualization` à URL do script de API. A camada do mapa de calor no Google Maps não dá suporte diretamente a dados geojson e, em vez disso, os dados precisam primeiro ser baixados e convertidos em uma matriz de pontos de dados ponderados.

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

![mapa de calor do Google Maps](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**Após: mapas do Azure**

No Azure Maps, carregue os dados geojson em uma fonte de dados e conecte a fonte de dados a uma camada do mapa de calor. A propriedade que será usada para o peso pode ser passada para a opção `weight` usando uma expressão. Os dados geojson podem ser importados diretamente no Azure Maps usando a função `importDataFromUrl` na classe `DataSource`.

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

- [Adicionar uma camada do mapa de calor](map-add-heat-map-layer.md)
- [Classe da camada do mapa de calor](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Opções da camada do mapa de calor](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Sobrepor uma camada de peça

As camadas de bloco, também conhecidas como sobreposições de imagem no Google Maps, permitem que você sobreponha imagens grandes que foram divididas em imagens com blocos inferiores menores que se alinham com o sistema de divisão de mapas. Essa é uma maneira comum de sobrepor imagens grandes ou conjuntos de dados muito grandes.

Os exemplos a seguir sobrepõem uma camada de bloco de radar meteorológico da Mesonet ambiental Iowa da Universidade de estado Iowa.

**Antes: Google Maps**

No Google Maps, camadas de bloco podem ser criadas usando a classe `google.maps.ImageMapType`.

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

![camada de bloco do Google Maps](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**Após: mapas do Azure**

No Azure Maps, uma camada de peça pode ser adicionada ao mapa praticamente da mesma forma que qualquer outra camada. Uma URL formatada que tem em x, y, espaços reservados de zoom; `{x}`, `{y}`, `{z}` respectivamente, é usado para informar à camada onde acessar os blocos. As camadas de bloco do Azure Maps também oferecem suporte a espaços reservados `{quadkey}`, `{bbox-epsg-3857}` e `{subdomain}`.

> [!TIP]
> No Azure Maps, as camadas podem ser facilmente renderizadas abaixo de outras camadas, incluindo as camadas do mapa base. Geralmente, é desejável renderizar camadas de bloco abaixo dos rótulos de mapa para que sejam fáceis de ler. O método `map.layers.add` usa um segundo parâmetro que é a ID da camada na qual inserir a nova camada abaixo. Para inserir uma camada de bloco abaixo dos rótulos de mapa, o código a seguir pode ser usado: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

camada de bloco do ![mapas do Azure](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> As solicitações de bloco podem ser capturadas usando a opção `transformRequest` do mapa. Isso permitirá que você modifique ou adicione cabeçalhos à solicitação, se desejado.

**Recursos adicionais:**

- [Adicionar camadas de bloco](map-add-tile-layer.md)
- [Classe da camada do bloco](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Opções da camada de peças](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Mostrar trânsito

Os dados de tráfego podem ser sobrepostos ao Azure e ao Google Maps.

**Antes: Google Maps**

No Google Maps, os dados de tráfego podem ser sobrepostos ao mapa usando a camada de tráfego.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![o tráfego do Google Maps](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**Após: mapas do Azure**

O mapas do Azure fornece várias opções diferentes para exibir o tráfego. Incidentes de tráfego, como fechamentos e acidentes de estrada, podem ser exibidos como ícones no mapa. O fluxo de tráfego, as estradas codificadas por cor, podem ser sobrepostas no mapa e as cores podem ser modificadas para serem baseadas em relação ao limite de velocidade lançado, em relação ao atraso normal esperado ou ao atraso absoluto. Os dados de incidentes no Azure Maps são atualizados a cada minuto e fluem os dados a cada dois minutos.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![o tráfego do Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Se você clicar em um dos ícones de tráfego no Azure Maps, informações adicionais serão exibidas em um pop-up.

<center>

![o incidente de tráfego do Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**Recursos adicionais:**

- [Mostrar o tráfego no mapa](map-show-traffic.md)
- [Opções de sobreposição de tráfego](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Adicionar uma sobreposição de aterramento

O Azure e o Google Maps dão suporte à sobreposição de imagens georeferenciadas no mapa para que elas sejam movidas e dimensionadas conforme você panorâmica e Aplique zoom no mapa. No Google Maps, eles são conhecidos como sobreposições de aterramento enquanto no Azure Maps são chamados de camadas de imagem. Eles são ótimos para a criação de planos de piso, sobreposição de mapas antigos ou imagens de um drone.

**Antes: Google Maps**

Ao criar uma sobreposição de aterramento no Google Maps, você precisa especificar a URL para a imagem a ser sobreposta e uma caixa delimitadora à qual associar a imagem no mapa. Este exemplo sobrepõe uma imagem de mapa de [Newark New Jersey de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa.

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

Executar esse código em um navegador exibirá um mapa semelhante à imagem a seguir:

<center>

![sobreposição de imagem do Google Maps](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**Após: mapas do Azure**

No Azure Maps, as imagens georeferenciadas podem ser sobrepostas usando a classe `atlas.layer.ImageLayer`. Essa classe requer uma URL para uma imagem e um conjunto de coordenadas para os quatro cantos da imagem. A imagem deve ser hospedada no mesmo domínio ou ter o CORs habilitado.

> [!TIP]
> Se você tiver apenas informações de norte, Sul, leste, oeste e de rotação e não coordenadas para cada canto da imagem, poderá usar o método de [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) estático.

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

![sobreposição de imagem do mapas do Azure](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**Recursos adicionais:**

- [Sobrepor uma imagem](map-add-image-layer.md)
- [Classe da camada da imagem](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>Exemplos de código adicionais

Veja a seguir alguns exemplos de código adicionais relacionados à migração do Google Maps:

- [Ferramentas de desenho](map-add-drawing-toolbar.md)
- [Limitar o mapa a dois panorâmicas de dedo](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Limitar zoom da roda de rolagem](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Criar um controle de tela inteira](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Serviços**

- [Usando o módulo de serviços do Azure Maps](how-to-use-services-module.md)
- [Procurar pontos de interesse](map-search-location.md)
- [Obter informações de uma coordenada (Inverter código)](map-get-information-from-coordinate.md)
- [Mostrar instruções de a a B](map-route.md)
- [Pesquisar a sugestão automática com a interface do usuário do JQuery](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Mapeamento de classe do SDK para Web do Google Maps V3 para Azure Maps

O apêndice a seguir fornece um mapeamento de referência cruzada das classes usadas com mais frequência no Google Maps V3 para seus equivalentes do SDK da Web do Azure Maps.

### <a name="core-classes"></a>Classes principais

| Google Maps   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [Atlas. Mapeada](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [Atlas. Pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [Atlas. Pop-ups](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [Atlas. Data. Position](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [Atlas. Data. BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [Atlas. Câmara de câmera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[Atlas. CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Serviceoptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Estilo da](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Userinterling](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [Atlas. 16x16](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Classes de sobreposição

| Google Maps  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [Atlas. HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[Atlas. Data. Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [Atlas. HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. Layer. SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[Atlas. SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. Íconeoptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[Atlas. TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Layer. BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[Atlas. BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [Atlas. Data. Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[Atlas. Layer. PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [Atlas. PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [Atlas. Layer. LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [Atlas. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [Atlas. Data. LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [Atlas. Layer. LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[Atlas. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | Consulte [Adicionar um círculo ao mapa](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [Atlas. TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [Atlas. TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [Atlas. Layer. ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[Atlas. ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Classes de serviço

O SDK da Web do Azure Maps inclui um [módulo de serviços](how-to-use-services-module.md) que pode ser carregado separadamente. Esse módulo encapsula os serviços REST do Azure Maps com uma API Web e pode ser usado em aplicativos JavaScript, TypeScript e node. js.

| Google Maps | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [Atlas. Service. SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [Atlas. SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [Atlas. Service. RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [Atlas. CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [Atlas. Service. SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Bibliotecas

As bibliotecas adicionam funcionalidade adicional ao mapa. Muitos deles estão no principal SDK do Azure Maps. Aqui estão algumas classes equivalentes a serem usadas no lugar dessas bibliotecas do Google Maps

| Google Maps           | Azure Maps   |
|-----------------------|--------------|
| Biblioteca de desenho       | [Módulo de ferramentas de desenho](set-drawing-options.md) |
| Biblioteca de geometria      | [Atlas. Math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Biblioteca de visualização | [Camada do mapa de calor](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o SDK da Web do Azure Maps.

> [!div class="nextstepaction"]
> [How to use the map control](how-to-use-map-control.md) (Como utilizar o controlo de mapa)

> [!div class="nextstepaction"]
> [Como usar o módulo de serviços](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Como usar o módulo ferramentas de desenho](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)

