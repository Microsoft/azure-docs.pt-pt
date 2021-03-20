---
title: Adicione uma camada de referência ao Azure Maps Power BI | Microsoft Azure Maps
description: Neste artigo, você aprenderá a usar a camada de referência no Microsoft Azure Maps visual para Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: eb6c9fed42f263ca6cfaa1ea975d31cb8f4a75cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86261948"
---
# <a name="add-a-reference-layer"></a>Adicionar uma camada de referência

A função de camada de referência permite que um conjunto de dados espaciais secundário seja carregado para o visual e sobreposto no mapa para fornecer contexto de adição. Este conjunto de dados é hospedado pelo Power BI e deve ser um [ficheiro GeoJSON](https://wikipedia.org/wiki/GeoJSON) com uma `.json` extensão de `.geojson` ficheiro ou de ficheiro.

Para adicionar um ficheiro **GeoJSON** como camada de referência, vá ao painel **de formato,** expanda a secção **camada de referência** e prima o botão de ficheiro + Adicionar **local.**

Depois de um ficheiro GeoJSON ser adicionado à camada de referência, o nome do ficheiro aparecerá no lugar do botão de **ficheiro + Adicionar local** com um **X** ao seu lado. Prima o botão **X** para remover os dados do visual e eliminar o ficheiro GeoJSON do Power BI.

O mapa a seguir é exibido [em 2016 recenseamentos para o Colorado](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson), coloridos pela população.

> [!div class="mx-imgBorder"]
> ![Um mapa que mostra os tratos de recenseamento de 2016 para o Colorado, colorido pela população como uma camada de referência](media/power-bi-visual/reference-layer-CO-census-tract.png)

Seguem-se todas as definições no painel **de formato** que estão disponíveis na secção camada **de referência.**

| Definições              | Descrição   |
|----------------------|---------------|
| Dados da camada de referência | O ficheiro GeoJSON de dados para carregar para o visual como uma camada adicional dentro do mapa. O botão **de ficheiro + Adicionar local** abre um diálogo de ficheiro que o utilizador pode usar para selecionar um ficheiro GeoJSON que tenha uma `.json` extensão de ficheiro ou de `.geojson` ficheiro. |

> [!NOTE]
> Nesta pré-visualização do visual Azure Maps, a camada de referência apenas carregará as primeiras 5.000 características de forma para o mapa. Este limite será aumentado numa futura atualização.

## <a name="styling-data-in-a-reference-layer"></a>Dados de estilo numa camada de referência

As propriedades podem ser adicionadas a cada recurso dentro do ficheiro GeoJSON para personalizar como é modelado no mapa. Esta funcionalidade utiliza a função de camada de dados simples no Azure Maps Web SDK. Para obter mais informações, consulte este documento sobre [propriedades de estilo suportado.](spatial-io-add-simple-data-layer.md#default-supported-style-properties) As imagens de ícones personalizados não são suportadas dentro do visual do Azure Maps como precaução de segurança.

Segue-se um exemplo de uma característica de ponto GeoJSON que define a sua cor exibida para vermelho.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "color": "red"
    }
}
```
## <a name="next-steps"></a>Passos seguintes

Adicione mais contexto ao mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de mosaico](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Mostrar tráfego em tempo real](power-bi-visual-show-real-time-traffic.md)
