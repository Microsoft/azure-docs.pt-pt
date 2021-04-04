---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 9447cec55c53861ca57d5416a91ffefd35fdd20b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91571989"
---
## <a name="repeatability-during-copy"></a>Repetibilidade durante a cópia
Ao copiar dados para O Azure SQL/SQL Server de outras lojas de dados, é necessário ter em mente a repetibilidade para evitar resultados não intencionais. 

Ao copiar dados para a Base de Dados do Servidor Azure SQL/SQL, a atividade de cópia por predefinição apend o conjunto de dados para a tabela do lavatório por predefinição. Por exemplo, ao copiar dados de uma fonte de ficheiro CSV (dados de valores separados) contendo dois registos para a Base de Dados do Servidor Azure SQL/SQL, é assim que a tabela se parece:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Suponha que encontrou erros no ficheiro de origem e atualizou a quantidade de Down Tube de 2 a 4 no ficheiro de origem. Se reencaistar a fatia de dados para esse período, encontrará dois novos registos anexados à Base de Dados do Servidor Azure SQL/SQL. Abaixo assume que nenhuma das colunas da tabela tem o principal constrangimento da chave.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Para evitar isto, terá de especificar a semântica UPSERT aproveitando um dos 2 mecanismos abaixo indicados abaixo.

> [!NOTE]
> Uma fatia pode ser reecotada automaticamente na Azure Data Factory de acordo com a política de retenção especificada.
> 
> 

### <a name="mechanism-1"></a>Mecanismo 1
Pode aproveitar a propriedade **sqlWriterCleanUpScript** para realizar a ação de limpeza pela primeira vez quando uma fatia é executada. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

O script de limpeza seria executado primeiro durante a cópia para uma determinada fatia que eliminaria os dados da Tabela SQL correspondente a essa fatia. A atividade inserirá posteriormente os dados na Tabela SQL. 

Se a fatia for agora re-executada, então verá que a quantidade é atualizada conforme desejado.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Suponha que o registo de máquinas de lavar plana seja removido do csv original. Em seguida, a re-execução da fatia produziria o seguinte resultado: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Nada de novo tinha de ser feito. A atividade da cópia executou o script de limpeza para apagar os dados correspondentes para essa fatia. Em seguida, leu a entrada do csv (que continha apenas 1 registo) e inseriu-a na tabela. 

### <a name="mechanism-2"></a>Mecanismo 2
> [!IMPORTANT]
> sliceIdentifierColumnName não é suportado para Azure Synapse Analytics neste momento. 

Outro mecanismo para obter a repetibilidade é ter uma coluna dedicada **(sliceIdentifierColumnName**) na tabela-alvo. Esta coluna seria utilizada pela Azure Data Factory para garantir que a origem e o destino permanecessem sincronizados. Esta abordagem funciona quando há flexibilidade na alteração ou definição do esquema de tabela SQL de destino. 

Esta coluna seria utilizada pela Azure Data Factory para fins de repetibilidade e no processo a Azure Data Factory não escava qualquer alteração de esquema na Tabela. Forma de utilizar esta abordagem:

1. Defina uma coluna de tipo binário (32) na tabela SQL de destino. Não deve haver constrangimentos nesta coluna. Vamos nomear esta coluna como 'ColumnForADFuseOnly' para este exemplo.
2. Utilize-o na atividade de cópia da seguinte forma:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

A Azure Data Factory irá povoar esta coluna de acordo com a sua necessidade de garantir que a origem e o destino permaneçam sincronizados. Os valores desta coluna não devem ser utilizados fora deste contexto pelo utilizador. 

Semelhante ao mecanismo 1, a Copy Activity limpará automaticamente os dados da fatia dada a partir da tabela SQL de destino e, em seguida, executará a atividade de cópia normalmente para inserir os dados de origem para destino para essa fatia. 

