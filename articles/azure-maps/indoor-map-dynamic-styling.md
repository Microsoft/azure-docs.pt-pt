---
title: Implementar estilo dinâmico para mapas interiores do Azure Maps Creator (Preview)
description: Saiba como implementar o estilo dinâmico para mapas interiores do Criador (Preview)
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 266dc5d62f6224495075546528ad71d806d415ac
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903450"
---
# <a name="implement-dynamic-styling-for-creator-preview-indoor-maps"></a>Implementar estilo dinâmico para mapas interiores do Criador (Preview)

> [!IMPORTANT]
> Os serviços do Azure Maps Creator estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O [serviço Azure](/rest/api/maps/featurestate) Maps Creator Feature State permite aplicar estilos baseados nas propriedades dinâmicas das funcionalidades de dados do mapa interno.  Por exemplo, você pode renderizar salas de reuniões de instalações com uma cor específica para refletir o estado de ocupação. Neste artigo, vamos mostrar-lhe como tornar dinamicamente as funcionalidades do mapa interior com o [serviço Feature State](/rest/api/maps/featurestate) e o Módulo Web [Interior.](how-to-use-indoor-module.md)

## <a name="prerequisites"></a>Pré-requisitos

1. [Criar uma conta do Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição.
3. [Criar um recurso Criador (Pré-visualização)](how-to-manage-creator.md)
4. Descarregue o [pacote de desenho de amostras.](https://github.com/Azure-Samples/am-creator-indoor-data-examples)
5. [Crie um mapa interior](tutorial-creator-indoor-maps.md) para obter um `tilesetId` e `statesetId` .
6. Construa uma aplicação web seguindo os passos de [Como utilizar o módulo mapa interior.](how-to-use-indoor-module.md)

Este tutorial usa a aplicação [Do Carteiro,](https://www.postman.com/) mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="implement-dynamic-styling"></a>Implementar estilo dinâmico

Assim que completar os pré-requisitos, deverá ter uma simples aplicação web configurada com a sua chave de `tilesetId` subscrição, e `statesetId` .

### <a name="select-features"></a>Selecionar funcionalidades

Para implementar um estilo dinâmico, uma característica, como uma sala de reuniões ou de conferência, deve ser referenciada pela sua característica `id` . Utilizará a funcionalidade `id` para atualizar a propriedade dinâmica ou o *estado* dessa funcionalidade. Para visualizar as funcionalidades definidas num conjunto de dados, pode utilizar um dos seguintes métodos:

* WFS API (Serviço de Funcionalidades Web). Os conjuntos de dados podem ser consultados utilizando a API do WFS. O WFS segue as funcionalidades API do Consórcio Geoespacial Aberto. A API WFS é útil para consulta de funcionalidades dentro de um conjunto de dados. Por exemplo, você pode usar WFS para encontrar todas as salas de reuniões de tamanho médio de uma determinada instalação e nível de piso.

* Implemente código personalizado que permita ao utilizador selecionar funcionalidades num mapa usando a sua aplicação web. Neste artigo, vamos aproveitar esta opção.  

O seguinte script implementa o evento de clique do rato. O código recupera a funcionalidade `id` com base no ponto clicado. Na sua aplicação, pode inserir o código abaixo do seu bloco de códigos Indoor Manager. Execute a sua aplicação e verifique a consola para obter a característica `id` do ponto clicado.

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor");

    var result = features.reduce(function (ids, feature) {
        if (feature.layer.id == "indoor_unit_office") {
            console.log(feature);
        }
    }, []);
});
```

O Criar um tutorial [de mapa interior](tutorial-creator-indoor-maps.md) configura o estado de recurso para aceitar atualizações do estado para `occupancy` .

Na próxima secção, vamos definir o *estado* de ocupação para `UNIT26` `true` . enquanto o escritório `UNIT27` será definido para `false` .

### <a name="set-occupancy-status"></a>Definir estado de ocupação

 Vamos agora atualizar o estado dos dois escritórios, `UNIT26` `UNIT27` e:

1. Na aplicação Do Carteiro, selecione **New**. Na janela **Criar Novo,** selecione **Request**. Insira um **nome De pedido** e selecione uma coleção. Clique em **Guardar**

2. Utilize a [API dos Estados de Atualização de Funcionalidades](/rest/api/maps/featurestate/updatestatespreview) para atualizar o estado. Passe a identificação do estado, e `UNIT26` para uma das duas unidades. Apecir a sua chave de subscrição Azure Maps. Aqui está a URL de um pedido **de POST** para atualizar o estado:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID=UNIT26&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Nos **Cabeçalhos** do pedido **DOM,** definido `Content-Type` para `application/json` . No **corpo** do pedido **POST,** escreva o seguinte JSON com as atualizações de funcionalidades. A atualização só será guardada se o carimbo de tempo registado for após o carimbo de tempo utilizado nos pedidos de atualização do estado de funcionalidade anterior para a mesma funcionalidade `ID` . Passe o "ocupado" `keyName` para atualizar o seu valor.

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

4. Redo o passo 2 e 3 `UNIT27` utilizando, com o seguinte JSON.

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

### <a name="visualize-dynamic-styles-on-a-map"></a>Visualize estilos dinâmicos em um mapa

A aplicação web que abriu anteriormente num browser deve agora refletir o estado atualizado das funcionalidades do mapa. `UNIT27`(151) deve parecer verde e `UNIT26` (157) deve parecer vermelho.

![Quarto livre em verde e quarto ocupado em vermelho](./media/indoor-map-dynamic-styling/room-state.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais lendo:

> [!div class="nextstepaction"]
> [Criador (Pré-visualização) para mapeamento interior](creator-indoor-maps.md)

Consulte as referências para as APIs mencionadas neste artigo:

> [!div class="nextstepaction"]
> [Upload de dados](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Conversão de dados](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Conjunto de dados](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Teeste](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Conjunto de estado de recurso](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [Serviço WFS](creator-indoor-maps.md#web-feature-service-api)