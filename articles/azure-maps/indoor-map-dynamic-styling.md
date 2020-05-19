---
title: Implementar estilo dinâmico para mapas interiores do Criador do Azure Maps
description: Saiba como implementar o estilo dinâmico para mapas interiores do Criador
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 6edc114a2d69dfe8f1e6e5d3c0a2d4af26dbad67
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598774"
---
# <a name="implement-dynamic-styling-for-creator-indoor-maps"></a>Implementar estilo dinâmico para mapas interiores do Criador

O serviço Azure Maps Creator [Feature State](https://docs.microsoft.com/rest/api/maps/featurestate/featurestate) permite-lhe aplicar estilos com base nas propriedades dinâmicas das funcionalidades de dados do mapa interior.  Por exemplo, você pode render salas de reuniões de instalações com uma cor específica para refletir o estado de ocupação. Neste artigo, vamos mostrar-lhe como renderizar dinamicamente as funcionalidades do mapa interior com o [serviço Feature State](https://docs.microsoft.com/rest/api/maps/featurestate/featurestate) e o Módulo Web [Interior](how-to-use-indoor-module.md).

## <a name="prerequisites"></a>Pré-requisitos

1. [Criar uma conta do Azure Maps](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave principal ou a chave de subscrição.
3. [Criar um recurso Criador](how-to-manage-creator.md)
4. Descarregue o [pacote de desenho da amostra.](https://github.com/Azure-Samples/am-creator-indoor-data-examples)
5. [Crie um mapa interior](tutorial-creator-indoor-maps.md) para obter a `tilesetId` e `statesetId` .
6. Construa uma aplicação web seguindo os passos em [Como utilizar o módulo Indoor Map](how-to-use-indoor-module.md).

Este tutorial utiliza a aplicação [Postman,](https://www.postman.com/) mas pode escolher um ambiente de desenvolvimento diferente da API.

## <a name="implement-dynamic-styling"></a>Implementar o estilo dinâmico

Uma vez preenchidos os pré-requisitos, deverá ter uma simples aplicação web configurada com a sua chave de `tilesetId` subscrição, e `statesetId` .

### <a name="select-features"></a>Selecionar funcionalidades

Para implementar um estilo dinâmico, uma funcionalidade, como uma reunião ou sala de conferências, deve ser referenciada pela sua funcionalidade `id` . Utilizará a funcionalidade `id` para atualizar a propriedade dinâmica ou o *estado* dessa funcionalidade. Para visualizar as funcionalidades definidas num conjunto de dados, pode utilizar um dos seguintes métodos:

* WFS API (Serviço de FuncionalidadeWeb). Os conjuntos de dados podem ser consultados utilizando a API WFS. O WFS segue as funcionalidades da API do Consórcio Geoespacial Aberto. A API WFS é útil para consulta de funcionalidades dentro de um conjunto de dados. Por exemplo, você pode usar WFS para encontrar todas as salas de reuniões de tamanho médio de uma determinada instalação e nível de piso.

* Implemente um código personalizado que permite ao utilizador selecionar funcionalidades num mapa utilizando a sua aplicação web. Neste artigo, vamos fazer uso desta opção.  

O seguinte script implementa o evento de clique do rato. O código recupera a funcionalidade `id` com base no ponto clicado. Na sua aplicação, pode inserir o código abaixo do seu bloco de código Indoor Manager. Execute a sua aplicação e verifique a consola para obter a funcionalidade `id` do ponto clicado.

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor")

    var result = features.reduce(function (ids, feature) {
        if (feature.layer.id == "indoor_unit_office") {
            console.log(feature);
        }
    }, []);
});
```

O [tutorial Create a indoor map](tutorial-creator-indoor-maps.md) configurado o estado de funcionalidade para aceitar atualizações estatais para `occupancy` .

Na próxima secção, vamos definir o *estado* de ocupação do cargo `UNIT26` para `true` . enquanto o escritório `UNIT27` será definido para `false` .

### <a name="set-occupancy-status"></a>Definir estado de ocupação

 Vamos agora atualizar o estado dos dois escritórios, `UNIT26` `UNIT27` e:

1. Na aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Pedido**. Insira um **nome De Pedido** e selecione uma coleção. Clique em **Guardar**

2. Utilize a API de [Atualização](https://docs.microsoft.com/rest/api/maps/featurestate/updatestatespreview) de Recursos para atualizar o estado. Passe a identificação do estado, e `UNIT26` para uma das duas unidades. Aprete a sua chave de subscrição do Azure Maps. Aqui está o URL de um pedido **post** para atualizar o estado:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID=UNIT26&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Nos **Cabeçalhos** do PEDIDO **POST,** definido `Content-Type` para `application/json` . No **pedido DO** **POST,** escreva o seguinte JSON com as atualizações da funcionalidade. A atualização só será guardada se o carimbo de tempo registado for posterior ao carimbo de tempo utilizado nos pedidos de atualização do estado de recurso anteriores para a mesma funcionalidade `ID` . Passe o "ocupado" `keyName` para atualizar o seu valor.

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

4. Refazer os passos 2 e 3 `UNIT27` utilizando, com o seguinte JSON.

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>Visualizar estilos dinâmicos num mapa

A aplicação web que abriu anteriormente num navegador deve agora refletir o estado atualizado das funcionalidades do mapa. `UNIT27`(151) deve parecer verde e `UNIT26` (157) deve parecer vermelho.

![Quarto gratuito em sala verde e ocupado em vermelho](./media/indoor-map-dynamic-styling/room-state.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais lendo:

> [!div class="nextstepaction"]
> [Criador para mapeamento interior](creator-indoor-maps.md)

Consulte as referências para as APIs mencionadas neste artigo:

> [!div class="nextstepaction"]
> [Upload de dados](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Conversão de Dados](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Conjunto de dados](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Azulejo](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Conjunto de Estado de Recurso](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [Serviço WFS](creator-indoor-maps.md#web-feature-service-api)

