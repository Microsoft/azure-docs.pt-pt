---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/08/2020
ms.author: orspodek
ms.openlocfilehash: f9788e4623ce60ad55d79558d1d77a17eb2a9f26
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779960"
---
### <a name="event-system-properties-mapping"></a>Mapeamento de propriedades do sistema de eventos

Se você selecionou **Propriedades do sistema de eventos** na seção fonte de **dados** da tabela acima, vá para a [interface do usuário da Web](https://dataexplorer.azure.com/) para executar o comando KQL relevante para a criação de mapeamento adequada.

   **Para mapeamento de CSV:**

    ```kusto
    .create table MyTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "messageid", "DataType":"string", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "userid", "DataType":"string", "Properties":{"Ordinal":"1"}},'
    '   { "column" : "other", "DataType":"int", "Properties":{"Ordinal":"2"}}'
    ']'
    ```
 
   **Para mapeamento de JSON:**

    ```kusto
    .create table MyTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "messageid", "datatype" : "string", "Properties":{"Path":"$.message-id"}},'
    '    { "column" : "userid", "Properties":{"Path":"$.user-id"}},'
    '    { "column" : "other", "Properties":{"Path":"$.other"}}'
    ']'
    ```

   > [!TIP]
   > * Você deve incluir todas as propriedades selecionadas no mapeamento. 
   > * A ordem das propriedades é importante no mapeamento de CSV. As propriedades do sistema devem ser listadas antes de todas as outras propriedades e na mesma ordem em que aparecem na lista suspensa **Propriedades do sistema de eventos** .