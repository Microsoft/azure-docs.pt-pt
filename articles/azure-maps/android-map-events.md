---
title: Lidar com eventos de mapas em mapas Android | Microsoft Azure Maps
description: Saiba quais os eventos que são disparados quando os utilizadores interagem com mapas. Veja uma lista de todos os eventos de mapas suportados. Veja como usar o Azure Maps Android SDK para lidar com eventos.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ebe61e5956dc0f35794211a336eb7d884ee18d76
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608906"
---
# <a name="interact-with-the-map-android-sdk"></a>Interaja com o mapa (Android SDK)

Este artigo mostra-lhe como usar o gestor de eventos de mapas.

## <a name="interact-with-the-map"></a>Interaja com o mapa

O mapa gere todos os eventos através da sua `events` propriedade. A tabela que se segue lista todos os eventos de mapas suportados.

| Evento                  | Formato de manipulador de eventos | Description |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Disparado após o último quadro prestado antes do mapa entrar num estado "ocioso":<ul><li>Não há transições de câmaras em andamento.</li><li>Todos os azulejos atualmente solicitados carregaram.</li><li>Todas as animações de fade/transição completaram.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Disparado repetidamente durante uma transição animada de uma vista para outra, como resultado da interação ou métodos do utilizador. |
| `OnCameraMoveCanceled` | `()`                 | Disparado quando um pedido de movimento para a câmara foi cancelado. |
| `OnCameraMoveStarted`  | `(int reason)`       | Disparado pouco antes do mapa iniciar uma transição de uma vista para outra, como resultado da interação ou métodos do utilizador. O `reason` argumento do ouvinte do evento devolve um valor inteiro que fornece detalhes de como o movimento da câmara foi iniciado. A lista que se segue descreve as razões possíveis:<ul><li>1: Gesto</li><li>2: Animação do desenvolvedor</li><li>3: Animação API</li></ul>   |
| `OnClick`              | `(double lat, double lon): boolean` | Disparado quando o mapa é pressionado e libertado no mesmo ponto do mapa. Este manipulador de eventos devolve um valor booleano indicando se o evento deve ser consumido ou passado mais para outros ouvintes do evento. |
| `OnFeatureClick`       | `(List<Feature>): boolean`    | Disparado quando o mapa é pressionado e libertado no mesmo ponto de uma característica. Este manipulador de eventos devolve um valor booleano indicando se o evento deve ser consumido ou passado mais para outros ouvintes do evento. |
| `OnLayerAdded` | `(Layer layer)` | Disparado quando uma camada é adicionada ao mapa. |
| `OnLayerRemoved` | `(Layer layer)` | Disparado quando uma camada é removida do mapa. |
| `OnLoaded` | `()` | Disparado imediatamente após todo o necessário recursos ter sido descarregado e ocorreu a primeira renderização visualmente completa do mapa. |
| `OnLongClick`          | `(double lat, double lon): boolean` | Disparado quando o mapa é pressionado, mantido por um momento, e depois libertado no mesmo ponto no mapa. Este manipulador de eventos devolve um valor booleano indicando se o evento deve ser consumido ou passado mais para outros ouvintes do evento. |
| `OnLongFeatureClick `  | `(List<Feature>): boolean`    | Disparado quando o mapa é pressionado, mantido por um momento, e depois lançado no mesmo ponto numa característica. Este manipulador de eventos devolve um valor booleano indicando se o evento deve ser consumido ou passado mais para outros ouvintes do evento. |
| `OnReady`              | `(AzureMap map)`     | Disparado quando o mapa está inicialmente carregado ou quando a orientação da aplicação muda e os recursos mínimos necessários do mapa são carregados e o mapa está pronto para ser interagido programáticamente com. |
| `OnSourceAdded` | `(Source source)` | Disparado quando um `DataSource` ou é adicionado ao `VectorTileSource` mapa. |
| `OnSourceRemoved` | `(Source source)` | Disparado quando um `DataSource` ou é removido do `VectorTileSource` mapa. |
| `OnStyleChange` | `()` | Disparado quando o estilo do mapa é carregado ou muda. |

O código que se segue mostra como adicionar o `OnClick` `OnFeatureClick` , e `OnCameraMove` eventos ao mapa.

::: zone pivot="programming-language-java-android"

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.events.add(OnClick { lat: Double, lon: Double -> 
    //Map clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return false
})

map.events.add(OnFeatureClick { features: List<Feature?>? -> 
    //Feature clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return false
})

map.events.add(OnCameraMove {
    //Map camera moved.
})
```

::: zone-end

Para mais informações, consulte a [documentação do mapa](how-to-use-android-map-control-library.md#navigating-the-map) sobre como interagir com o mapa e desencadear eventos.

## <a name="scope-feature-events-to-layer"></a>Eventos de característica de âmbito para camada

Ao adicionar o `OnFeatureClick` ou `OnLongFeatureClick` eventos ao mapa, uma instância de camada ou iD de camada pode ser passada como um segundo parâmetro. Quando uma camada é passada, o evento só dispara se o evento ocorrer nessa camada. Os eventos a nível de camadas são suportados pelas camadas de símbolo, bolha, linha e polígono.

::: zone pivot="programming-language-java-android"

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, layer);

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source.
val source = DataSource()
map.sources.add(source)

//Add data to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a layer and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnFeatureClick { features: List<Feature?>? -> 
        //One or more features clicked.

        //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
        return false
    },
    layer
)

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnLongFeatureClick { features: List<Feature?>? -> 
         //One or more features long clicked.

        //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
        return false
    },
    layer
)
```

::: zone-end

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Navegando no mapa](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linhas](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](how-to-add-shapes-to-android-map.md)
