---
title: Mapeando a transformação do fluxo de dados
description: Fluxo de dados de mapeamento de fábrica de dados azure
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 415a093fd8a8fbe27e1d240b061548e18f2ca6b6
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164734"
---
# <a name="azure-data-factory-flatten-transformation"></a>Transformação de flatten da fábrica de dados azure

A transformação achatada pode ser usada para impulsionar valores de matriz dentro de uma estrutura hierárquica em novas linhas, essencialmente desnormalizando os seus dados.

![Caixa de ferramentas de transformação](media/data-flow/flatten5.png "Caixa de ferramentas de transformação")

![Transformação achatada 1](media/data-flow/flatten7.png "Transformação achatada 1")

## <a name="unroll-by"></a>Desenrolar por

Primeiro, escolha a coluna de matriz que deseja desenrolar e girar.

![Ajustes de transformação achatados](media/data-flow/flatten1.png "Ajustes de transformação achatados")

## <a name="unroll-root"></a>Raiz de desenrolar

Por padrão, a ADF irá aplainar a estrutura na matriz de desenrolar que escolheu acima. Ou pode escolher uma parte diferente da hierarquia para desenrolar. "Unroll root" é uma definição opcional.

## <a name="input-columns"></a>Colunas de entrada

Por último, escolha a projeção da sua nova estrutura com base nos campos de entrada, bem como a coluna normalizada que desenrolou.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>Exemplo

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source2
source2 foldDown(unroll(goods.orders.shipped.orderItems),
    mapColumn(
        name,
        each(goods.orders, match(type == 'integer')),
        each(goods.orders.shipped.orderItems, match(true())),
        location
    )) ~> Flatten1
Flatten1 sink(allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> sink1
```    

## <a name="next-steps"></a>Passos seguintes

* Utilize a [transformação pivot](data-flow-pivot.md) para girar linhas para colunas.
* Utilize a [transformação unpivot](data-flow-unpivot.md) para colunas de pivô em linhas.
