---
title: Filtragem de eventos para Azure Event Grid
description: Descreve como filtrar eventos ao criar uma subscrição da Azure Event Grid.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: b5439b77b86d42d062cf9da66ce678f04f46f813
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256094"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Compreender a filtragem do evento para subscrições de Grade de Eventos

Este artigo descreve as diferentes formas de filtrar quais os eventos enviados para o seu ponto final. Ao criar uma subscrição de eventos, tem três opções para filtrar:

* Tipos de evento
* O assunto começa com ou termina com
* Campos e operadores avançados

## <a name="event-type-filtering"></a>Filtragem do tipo de evento

Por predefinição, todos os [tipos de eventos](event-schema.md) para a fonte do evento são enviados para o ponto final. Pode decidir enviar apenas certos tipos de eventos para o seu ponto final. Por exemplo, pode ser notificado das atualizações dos seus recursos, mas não notificado para outras operações, como supressões. Nesse caso, filtre pelo tipo de `Microsoft.Resources.ResourceWriteSuccess` evento. Forneça uma matriz com os tipos de eventos, ou especifique `All` para obter todos os tipos de eventos para a fonte do evento.

A sintaxe JSON para filtragem por tipo de evento é:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filtragem do sujeito

Para uma filtragem simples por sujeito, especifique um valor inicial ou final para o sujeito. Por exemplo, pode especificar que o assunto termina apenas com `.txt` eventos relacionados com o upload de um ficheiro de texto para a conta de armazenamento. Ou, pode filtrar o assunto começa com `/blobServices/default/containers/testcontainer` a obter todos os eventos para aquele recipiente, mas não outros recipientes na conta de armazenamento.

Ao publicar eventos para tópicos personalizados, crie temas para os seus eventos que facilitem aos subscritores saber se estão interessados no evento. Os assinantes usam a propriedade do assunto para filtrar e encaminhar eventos. Considere adicionar o caminho para onde o evento aconteceu, para que os subscritores possam filtrar por segmentos desse caminho. O caminho permite que os assinantes filtram eventos de forma estreita ou ampla. Se fornecer um caminho de três segmentos como `/A/B/C` no assunto, os subscritores podem filtrar pelo primeiro segmento `/A` para obter um vasto conjunto de eventos. Esses assinantes obtêm eventos com temas como `/A/B/C` ou `/A/D/E` . Outros subscritores podem filtrar `/A/B` para obter um conjunto mais restrito de eventos.

A sintaxe JSON para filtragem por sujeito é:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Filtragem avançada

Para filtrar por valores nos campos de dados e especificar o operador de comparação, utilize a opção de filtragem avançada. Na filtragem avançada, especifica::

* tipo de operador - O tipo de comparação.
* tecla - O campo nos dados do evento que está a utilizar para filtragem. Pode ser um número, booleano, corda, ou uma matriz.
* valores - O valor ou valores para comparar com a chave.

## <a name="key"></a>Chave
A chave é o campo nos dados do caso que está a usar para filtragem. Pode ser um dos seguintes tipos:

- Número
- Booleano
- Cadeia
- A matriz. Você precisa definir a `enableAdvancedFilteringOnArrays` propriedade para ser verdadeira para usar esta funcionalidade. Atualmente, o portal Azure não suporta ativar esta funcionalidade. 

    ```json
    "filter":
    {
        "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
        "subjectEndsWith": ".jpg",
        "enableAdvancedFilteringOnArrays": true
    }
    ```

Para eventos no **esquema da Grelha de Eventos,** utilize os seguintes valores para a chave: , , , , , , ou `ID` `Topic` `Subject` `EventType` `DataVersion` dados de evento `data.key1` (como).

Para eventos no **esquema de Eventos em Nuvem,** utilize os seguintes valores para a chave: `eventid` , , , , ou `source` `eventtype` `eventtypeversion` dados de evento `data.key1` (como).

Para **esquemas de entrada personalizados,** utilize os campos de dados do evento (como). `data.key1` Para aceder aos campos na secção de dados, utilize a `.` notação (ponto). Por `data.sitename` exemplo, `data.appEventTypeDetail.action` aceder ou para o seguinte evento de `sitename` `action` amostra.

```json
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
```

## <a name="values"></a>Valores
Os valores podem ser: número, corda, boolean ou matriz

## <a name="operators"></a>Operadores

Os operadores disponíveis para **números** são:

## <a name="numberin"></a>NúmeroIn
O operador NumberIn avalia de verdade se o **valor-chave** for um dos valores especificados do **filtro.** No exemplo seguinte, verifica se o valor do `counter` atributo na secção é `data` 5 ou 1. 

```json
"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]
```


Se a chave for uma matriz, todos os valores da matriz são verificados contra a matriz dos valores do filtro. Aqui está o pseudo código com a chave: `[v1, v2, v3]` e o filtro: `[a, b, c]` . Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="numbernotin"></a>NúmeroNotIn
O NumberNotIn avalia a verdade se o valor **chave** **não** for nenhum dos valores especificados do **filtro.** No exemplo seguinte, verifica se o valor do `counter` atributo na secção não é `data` 41 e 0. 

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0
    ]
}]
```

Se a chave for uma matriz, todos os valores da matriz são verificados contra a matriz dos valores do filtro. Aqui está o pseudo código com a chave: `[v1, v2, v3]` e o filtro: `[a, b, c]` . Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```

## <a name="numberlessthan"></a>NúmeroSLessTh
O operador NumberLessThan avalia como verdadeiro se o valor **da chave** for inferior ao valor **especificado** do **filtro.** No exemplo seguinte, verifica se o valor do `counter` atributo na secção é inferior a `data` 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

Se a chave for uma matriz, todos os valores da matriz são verificados com o valor do filtro. Aqui está o pseudo código com a chave: `[v1, v2, v3]` . Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH key IN (v1, v2, v3)
    IF key < filter
        MATCH
```

## <a name="numbergreaterthan"></a>NúmeroGreaterThan
O operador NumberGreaterThan avalia como verdadeiro se o valor **chave** for **maior do que** o valor do **filtro** especificado. No exemplo seguinte, verifica se o valor do `counter` atributo na secção é superior a `data` 20. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

Se a chave for uma matriz, todos os valores da matriz são verificados com o valor do filtro. Aqui está o pseudo código com a chave: `[v1, v2, v3]` . Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH key IN (v1, v2, v3)
    IF key > filter
        MATCH
```

## <a name="numberlessthanorequals"></a>NumberLessThanOrEquals
O operador NumberLessThanOrEquals avalia como verdadeiro se o valor **da chave** for **inferior ou igual** ao valor do **filtro** especificado. No exemplo seguinte, verifica se o valor do `counter` atributo na secção é inferior ou igual a `data` 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

Se a chave for uma matriz, todos os valores da matriz são verificados com o valor do filtro. Aqui está o pseudo código com a chave: `[v1, v2, v3]` . Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH key IN (v1, v2, v3)
    IF key <= filter
        MATCH
```

## <a name="numbergreaterthanorequals"></a>NúmeroGreaterThanOrEquals
O operador NumberGreaterThanOrEquals avalia de verdade se o valor **da chave** for **superior ou igual** ao valor do **filtro** especificado. No exemplo seguinte, verifica se o valor do `counter` atributo na secção é superior ou igual a `data` 30. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

Se a chave for uma matriz, todos os valores da matriz são verificados com o valor do filtro. Aqui está o pseudo código com a chave: `[v1, v2, v3]` . Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH key IN (v1, v2, v3)
    IF key >= filter
        MATCH
```

## <a name="numberinrange"></a>NúmeroInRange
O operador NumberInRange avalia de verdade se o **valor-chave** estiver numa das gamas de **filtros** especificadas . No exemplo seguinte, verifica se o valor do `key1` atributo na secção se encontra numa das `data` duas gamas: 3.14159 - 999,95, 3000 - 4000. 

```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```

A `values` propriedade é uma variedade de gamas. No exemplo anterior, é uma matriz de duas gamas. Aqui está um exemplo de uma matriz com um alcance para verificar. 

**Matriz com uma gama:** 
```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Se a chave for uma matriz, todos os valores da matriz são verificados contra a matriz dos valores do filtro. Aqui está o pseudo código com a chave: `[v1, v2, v3]` e o filtro: uma variedade de gamas. Neste pseudo código, `a` e `b` são valores baixos e altos de cada gama na matriz. Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
       IF key >= a AND key <= b
           MATCH
```


## <a name="numbernotinrange"></a>NúmeroNotInRange
O operador NumberNotInRange avalia de verdade se o **valor-chave** **não** estiver em nenhuma das gamas de **filtros** especificadas . No exemplo seguinte, verifica se o valor do `key1` atributo na secção se encontra numa das `data` duas gamas: 3.14159 - 999,95, 3000 - 4000. Se for, o operador retorna falso. 

```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```
A `values` propriedade é uma variedade de gamas. No exemplo anterior, é uma matriz de duas gamas. Aqui está um exemplo de uma matriz com um alcance para verificar.

**Matriz com uma gama:** 
```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Se a chave for uma matriz, todos os valores da matriz são verificados contra a matriz dos valores do filtro. Aqui está o pseudo código com a chave: `[v1, v2, v3]` e o filtro: uma variedade de gamas. Neste pseudo código, `a` e `b` são valores baixos e altos de cada gama na matriz. Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
        IF key >= a AND key <= b
            FAIL_MATCH
```


O operador disponível para **booleans** é: 

## <a name="boolequals"></a>BoolEquals
O operador BoolEquals avalia com verdade se o **valor-chave** for o **filtro** de valor boolean especificado . No exemplo seguinte, verifica se o valor do `isEnabled` atributo na secção é `data` `true` . 

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```

Se a chave for uma matriz, todos os valores da matriz são verificados com o valor do filtro boolean. Aqui está o pseudo código com a chave: `[v1, v2, v3]` . Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH key IN (v1, v2, v3)
    IF filter == key
        MATCH
```

Os operadores disponíveis para **cordas** são:

## <a name="stringcontains"></a>CordasContains
O **StringContains** avalia-se verdadeiramente se o valor **da chave** **contiver** algum dos valores especificados do **filtro** (como sublamentos). No exemplo seguinte, verifica se o valor do `key1` atributo na secção contém uma das `data` sublagem especificadas: `microsoft` ou `azure` . Por exemplo, `azure data factory` tem `azure` nele. 

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

Se a chave for uma matriz, todos os valores da matriz são verificados contra a matriz dos valores do filtro. Aqui está o pseudo código com a chave: `[v1, v2, v3]` e o filtro: `[a,b,c]` . Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            MATCH
```

## <a name="stringnotcontains"></a>StringNotContains
O operador **StringNotContains** avalia de forma verdadeira se a **tecla** **não contiver** os valores especificados do **filtro** como sublagem. Se a chave contiver um dos valores especificados como sublagem, o operador avalia-o como falso. No exemplo seguinte, o operador só retorna verdadeiro se o valor do `key1` atributo na secção não tiver e como `data` `contoso` `fabrikam` sublemposições. 

```json
"advancedFilters": [{
    "operatorType": "StringNotContains",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam"
    ]
}]
```

Se a chave for uma matriz, todos os valores da matriz são verificados contra a matriz dos valores do filtro. Aqui está o pseudo código com a chave: `[v1, v2, v3]` e o filtro: `[a,b,c]` . Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            FAIL_MATCH
```
Consulte a secção [limitações](#limitations) para a limitação atual deste operador.

## <a name="stringbeginswith"></a>StringBeginsWith
O operador **StringBeginsWith** avalia-se verdadeiramente se o valor **da chave** **começa com** qualquer um dos valores especificados do **filtro.** No exemplo seguinte, verifica se o valor do `key1` atributo na secção começa com ou `data` `event` `grid` . Por exemplo, `event hubs` começa `event` por.  

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Se a chave for uma matriz, todos os valores da matriz são verificados contra a matriz dos valores do filtro. Aqui está o pseudo código com a chave: `[v1, v2, v3]` e o filtro: `[a,b,c]` . Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            MATCH
```

## <a name="stringnotbeginswith"></a>StringNotBeginsWith
O operador **StringNotBeginsWith** avalia-se verdadeiramente se o valor **da chave** **não começar com** nenhum dos valores do **filtro** especificados. No exemplo seguinte, verifica se o valor do `key1` atributo na secção não começa com ou `data` `event` `message` .

```json
"advancedFilters": [{
    "operatorType": "StringNotBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Se a chave for uma matriz, todos os valores da matriz são verificados contra a matriz dos valores do filtro. Aqui está o pseudo código com a chave: `[v1, v2, v3]` e o filtro: `[a,b,c]` . Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            FAIL_MATCH
```

## <a name="stringendswith"></a>StringEndsWith
O operador **StringEndsWith** avalia-se verdadeiramente se o valor da **chave** termina com um dos **valores especificados** do **filtro.** No exemplo seguinte, verifica se o valor do `key1` atributo na secção termina com ou ou `data` `jpg` `jpeg` `png` . Por exemplo, `eventgrid.png` termina com `png` .


```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Se a chave for uma matriz, todos os valores da matriz são verificados contra a matriz dos valores do filtro. Aqui está o pseudo código com a chave: `[v1, v2, v3]` e o filtro: `[a,b,c]` . Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            MATCH
```

## <a name="stringnotendswith"></a>StringNotEndsWith
O operador **StringNotEndsWith** avalia-se verdadeiramente se o valor **da chave** **não terminar com** nenhum dos valores de **filtro** especificados. No exemplo seguinte, verifica se o valor do `key1` atributo na secção não termina com ou ou `data` `jpg` `jpeg` `png` . 


```json
"advancedFilters": [{
    "operatorType": "StringNotEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Se a chave for uma matriz, todos os valores da matriz são verificados contra a matriz dos valores do filtro. Aqui está o pseudo código com a chave: `[v1, v2, v3]` e o filtro: `[a,b,c]` . Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            FAIL_MATCH
```

## <a name="stringin"></a>StringIn
O operador **StringIn** verifica se o valor da **chave** **corresponde exatamente** a um dos valores especificados do **filtro.** No exemplo seguinte, verifica se o valor do `key1` atributo na secção é ou `data` `contoso` `fabrikam` `factory` . 

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam", 
        "factory"
    ]
}]
```

Se a chave for uma matriz, todos os valores da matriz são verificados contra a matriz dos valores do filtro. Aqui está o pseudo código com a chave: `[v1, v2, v3]` e o filtro: `[a,b,c]` . Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="stringnotin"></a>StringNotIn
O operador **StringNotIn** verifica se o valor da **chave** **não corresponde** a nenhum dos valores do **filtro** especificados. No exemplo seguinte, verifica se o valor do `key1` atributo na secção não é e `data` `aws` `bridge` . 

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

Se a chave for uma matriz, todos os valores da matriz são verificados contra a matriz dos valores do filtro. Aqui está o pseudo código com a chave: `[v1, v2, v3]` e o filtro: `[a,b,c]` . Quaisquer valores-chave com tipos de dados que não correspondam ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```


Todas as comparações de cordas não são sensíveis a casos.

> [!NOTE]
> Se o evento JSON não contiver a chave de filtro avançada, o filtro é evaulado como **não corresponde aos** seguintes operadores: NumberGreaterThan, NumberGreaterThanOrEquals, NumberLessThan, NumberLessThanOrEquals, NumberIn, BoolEquals, StringContains, StringNotContains, StringBeginsWith, StringNotBeginsWith, StringEndsWith, StringEndsWith, StringNotEndsWith, StringIn.
> 
>O filtro é evaulado conforme **combinado** com os seguintes operadores:NumberNotIn, StringNotIn.


## <a name="isnullorundefined"></a>IsNullOrUndefined
O operador IsNullOrUndefined avalia com verdade se o valor da chave for NULO ou indefinido. 

```json
{
    "operatorType": "IsNullOrUndefined",
    "key": "data.key1"
}
```

No exemplo seguinte, falta a key1, pelo que o operador avaliará a verdade. 

```json
{ 
    "data": 
    { 
        "key2": 5 
    } 
}
```

No exemplo seguinte, a chave1 é definida como nula, para que o operador avalie a verdade.

```json
{
    "data": 
    { 
        "key1": null
    }
}
```

se a chave1 tiver algum outro valor nestes exemplos, o operador avaliará o falso. 

## <a name="isnotnull"></a>IsNotNull
O operador IsNotNull avalia a verdade se o valor da chave não é NULO ou indefinido. 

```json
{
    "operatorType": "IsNotNull",
    "key": "data.key1"
}
```

## <a name="or-and-and"></a>OR e E
Se especificar um único filtro com vários valores, é efetuada uma operação **OR,** pelo que o valor do campo-chave deve ser um destes valores. Segue-se um exemplo:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Se especificar vários filtros diferentes, é feita uma operação **E,** pelo que cada condição do filtro deve ser satisfeita. Eis um exemplo: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

## <a name="cloudevents"></a>Eventos CloudEvents 
Para eventos no **esquema cloudEvents,** utilize os seguintes valores para a chave: `eventid` , , , , , ou `source` `eventtype` `eventtypeversion` dados de evento `data.key1` (como). 

Também pode utilizar [atributos de contexto de extensão em CloudEvents 1.0](https://github.com/cloudevents/spec/blob/v1.0.1/spec.md#extension-context-attributes). No exemplo seguinte, `comexampleextension1` e `comexampleothervalue` são atributos de contexto de extensão. 

```json
{
    "specversion" : "1.0",
    "type" : "com.example.someevent",
    "source" : "/mycontext",
    "id" : "C234-1234-1234",
    "time" : "2018-04-05T17:31:00Z",
    "subject": null,
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
        "appinfoA" : "abc",
        "appinfoB" : 123,
        "appinfoC" : true
    }
}
```

Aqui está um exemplo de usar um atributo de contexto de extensão num filtro.

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "comexampleothervalue",
    "values": [
        "5", 
        "1"
    ]
}]
```


## <a name="limitations"></a>Limitações

A filtragem avançada tem as seguintes limitações:

* 5 filtros avançados e 25 valores de filtro em todos os filtros por subscrição da grelha de evento
* 512 caracteres por valor de corda
* Cinco valores para **dentro** e **não nos** operadores
* O `StringNotContains` operador não se encontra disponível no portal.
* Chaves com **`.` (ponto)** caráter neles. Por exemplo: `http://schemas.microsoft.com/claims/authnclassreference` ou `john.doe@contoso.com` . . Atualmente, não há suporte para personagens de fuga em teclas. 

A mesma chave pode ser utilizada em mais de um filtro.





## <a name="next-steps"></a>Passos seguintes

* Para conhecer os eventos de filtragem com PowerShell e Azure CLI, consulte [os eventos filter para a Grade de Eventos.](how-to-filter-events.md)
* Para começar rapidamente a utilizar a Grade de Eventos, consulte [Criar e encaminhar eventos personalizados com a Azure Event Grid](custom-event-quickstart.md).
