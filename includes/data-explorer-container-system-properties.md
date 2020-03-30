---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "79128914"
---
### <a name="event-system-properties-mapping"></a>Mapeamento de propriedades do sistema de eventos

> [!Note]
> * As propriedades do sistema são suportadas para eventos de um único recorde.
> * Para `csv` mapeamento, as propriedades são adicionadas no início do registo. Para `json` mapeamento, as propriedades são adicionadas de acordo com o nome que aparece na lista de drop-down.

Se selecionou propriedades do **sistema de eventos** na secção Fonte de **Dados** da tabela, deve incluir as seguintes propriedades no esquema de mesa e mapeamento.

**Exemplo de esquema de mesa**

Se os seus dados`Timespan`incluirem `Value`três colunas ( `x-opt-enqueued-time` e `x-opt-offset` `Metric`) e as propriedades que inclui são e, criar ou alterar o esquema da tabela utilizando este comando:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Exemplo de mapeamento CSV**

Executar os seguintes comandos para adicionar dados ao início do registo. Nota valores ordinários.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**Exemplo de mapeamento JSON**

Os dados são adicionados utilizando os nomes das propriedades do sistema tal como aparecem na lista de propriedades do **sistema de** eventos de ligação de **dados.** Execute estes comandos:

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
