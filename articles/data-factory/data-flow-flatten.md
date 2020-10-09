---
title: Transformação de achatado no fluxo de dados de mapeamento
description: Desnormalizar dados hierárquicos usando a transformação do achatado
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81413684"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Transformação de achatado no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação achatada para tomar valores de matriz dentro de estruturas hierárquicas como JSON e desenrolar-los em linhas individuais. Este processo é conhecido como desnormalização.

## <a name="configuration"></a>Configuração

A transformação do achatado contém as seguintes definições de configuração

![Definições de achatado](media/data-flow/flatten1.png "Definições de achatado")

### <a name="unroll-by"></a>Desenrolar por

Selecione uma matriz para desenrolar. Os dados de saída terão uma linha por item em cada matriz. Se o desenrolar por matriz na linha de entrada for nulo ou vazio, haverá uma linha de saída com valores desenrolados como nulos.

### <a name="unroll-root"></a>Raiz de desenrolar

Por defeito, a transformação achatada desenrola uma matriz até ao topo da hierarquia em que existe. Pode selecionar opcionalmente uma matriz como a sua raiz de desenrolar. A raiz de desenrolar deve ser uma matriz de objetos complexos que ou é ou contém o desenrolar por matriz. Se for selecionada uma raiz de desenrolar, os dados de saída conterão pelo menos uma linha por itens na raiz de desenrolar. Se a linha de entrada não tiver itens na raiz de desenrolar, será retirado dos dados de saída. Escolher uma raiz desenrolada irá sempre descodundo um número inferior ou igual de linhas do que o comportamento padrão.

### <a name="flatten-mapping"></a>Mapeamento achatado

Semelhante à transformação selecionada, escolha a projeção da nova estrutura a partir de campos de entrada e da matriz desnormalizada. Se uma matriz desnormalizada for mapeada, a coluna de saída será o mesmo tipo de dados que a matriz. Se o desenrolar por matriz é uma matriz de objetos complexos que contém subarrays, mapear um item desse subarry irá descoduar uma matriz.

Consulte o separador de inspeção e a pré-visualização de dados para verificar a sua saída de mapeamento.

## <a name="examples"></a>Exemplos

Consulte o seguinte objeto JSON para os exemplos abaixo da transformação do achatado

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>Sem raiz de rolo com matriz de cordas

| Desenrolar por | Raiz de desenrolar | Projeção |
| --------- | ----------- | ---------- |
| bens.clientes | Nenhum | name <br> cliente = bens.cliente |

#### <a name="output"></a>Saída

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>Sem raiz de desenrolar com matriz complexa

| Desenrolar por | Raiz de desenrolar | Projeção |
| --------- | ----------- | ---------- |
| goods.orders.envio.orderItems | Nenhum | name <br> orderId = goods.orders.orderId <br> itemName = goods.orders.hipped.orderItems.itemName <br> itemQty = goods.orders.envio.orderItems.itemQty <br> localização = localização |

#### <a name="output"></a>Saída

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>Mesma raiz que a matriz de desenrolar

| Desenrolar por | Raiz de desenrolar | Projeção |
| --------- | ----------- | ---------- |
| bens.encomendas | bens.encomendas | name <br> goods.orders.envio.orderItems.itemName <br> bens.clientes <br> localização |

#### <a name="output"></a>Saída

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Unroll raiz com matriz complexa

| Desenrolar por | Raiz de desenrolar | Projeção |
| --------- | ----------- | ---------- |
| goods.orders.hipped.orderItem | bens.encomendas |name <br> orderId = goods.orders.orderId <br> itemName = goods.orders.hipped.orderItems.itemName <br> itemQty = goods.orders.envio.orderItems.itemQty <br> localização = localização |

#### <a name="output"></a>Saída

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Syntax

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
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>Passos seguintes

* Use a [transformação do pivô](data-flow-pivot.md) para pisar linhas em colunas.
* Use a [transformação unpivot](data-flow-unpivot.md) para girar colunas em linhas.
