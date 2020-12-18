---
title: Lidar com eventos de mapas em mapas Android Microsoft Azure Maps
description: Saiba quais os eventos que são disparados quando os utilizadores interagem com mapas. Veja uma lista de todos os eventos de mapas suportados. Veja como usar o Azure Maps Android SDK para lidar com eventos.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 818d33947fa079a130c3009a34dcb9b72ad52f91
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681666"
---
# <a name="interact-with-the-map-android-sdk"></a>Interaja com o mapa (Android SDK)

Este artigo mostra-lhe como usar o gestor de eventos de mapas.

## <a name="interact-with-the-map"></a>Interaja com o mapa

O mapa gere todos os eventos através da sua `events` propriedade. A tabela que se segue lista todos os eventos de mapas suportados.

| Evento                  | Formato de manipulador de eventos | Descrição |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Disparado após o último quadro prestado antes do mapa entrar num estado "ocioso":<ul><li>Não há transições de câmaras em andamento.</li><li>Todos os azulejos atualmente solicitados carregaram.</li><li>Todas as animações de fade/transição completaram.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Disparado repetidamente durante uma transição animada de uma vista para outra, como resultado da interação ou métodos do utilizador. |
| `OnCameraMoveCanceled` | `()`                 | Disparado quando um pedido de movimento para a câmara foi cancelado. |
| `OnCameraMoveStarted`  | `(int reason)`       | Disparado pouco antes do mapa iniciar uma transição de uma vista para outra, como resultado da interação ou métodos do utilizador. O `reason` argumento do ouvinte do evento devolve um valor inteiro que fornece detalhes de como o movimento da câmara foi iniciado. Segue-se a lista de possíveis razões:<ul><li>1: Gesto</li><li>2: Animação do desenvolvedor</li><li>3: Animação API</li></ul>   |
| `OnClick`              | `(double lat, double lon)` | Disparado quando o mapa é pressionado e libertado no mesmo ponto do mapa. |
| `OnFeatureClick`       | `(List<Feature>)`    | Disparado quando o mapa é pressionado e libertado no mesmo ponto de uma característica.  |
| `OnLongClick`          | `(double lat, double lon)` | Disparado quando o mapa é pressionado, mantido por um momento, e depois libertado no mesmo ponto no mapa. |
| `OnLongFeatureClick `  | `(List<Feature>)`    | Disparado quando o mapa é pressionado, mantido por um momento, e depois lançado no mesmo ponto numa característica. |
| `OnReady`              | `(AzureMap map)`     | Disparado quando o mapa está inicialmente carregado ou quando a orientação da aplicação muda e os recursos mínimos necessários do mapa são carregados e o mapa está pronto para ser interagido programáticamente com. |

O código que se segue mostra como adicionar o `OnClick` `OnFeatureClick` , e `OnCameraMove` eventos ao mapa.

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

Para mais informações, consulte a [documentação do mapa](how-to-use-android-map-control-library.md#navigating-the-map) sobre como interagir com o mapa e desencadear eventos.

## <a name="scope-feature-events-to-layer"></a>Eventos de característica de âmbito para camada

Ao adicionar o `OnFeatureClick` ou `OnLongFeatureClick` eventos ao mapa, um ID de camada pode ser passado como um segundo parâmetro. Quando um ID de camada é passado, o evento só dispara se o evento ocorrer nessa camada. Os eventos a nível de camadas são suportados pelas camadas de símbolo, bolha, linha e polígono.

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
}, layer.getId());

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.
}, layer.getId());
```

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
