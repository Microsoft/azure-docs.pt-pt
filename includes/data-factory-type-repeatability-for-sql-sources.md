---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 24bb7a1fcb1569922fb34034fb3c0d003cdd7061
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67184726"
---
## <a name="repeatability-during-copy"></a>Repetibilidade durante a Cópia
Ao copiar dados para o Servidor Azure SQL/SQL de outras lojas de dados é necessário ter em mente a repetível para evitar resultados não intencionais. 

Ao copiar dados para a Base de Dados do Servidor Azure SQL/SQL, a atividade de cópia será por padrão anexar o conjunto de dados à tabela do lavatório por padrão. Por exemplo, ao copiar dados de uma fonte de ficheiroCSV (comma separados) contendo dois registos para a Base de Dados de Servidores Azure SQL/SQL, é assim que a tabela se parece:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Suponha que encontrou erros no ficheiro fonte e atualizou a quantidade de Down Tube de 2 a 4 no ficheiro fonte. Se reexecutar a fatia de dados durante esse período, encontrará dois novos registos anexados à Base de Dados do Servidor Azure SQL/SQL. A parte seguinte pressupõe que nenhuma das colunas da tabela tem a principal restrição de chave.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Para evitar isto, terá de especificar a semântica UPSERT, aproveitando um dos mecanismos abaixo de 2 indicados abaixo.

> [!NOTE]
> Uma fatia pode ser reexecutada automaticamente na Azure Data Factory de acordo com a política de retry especificada.
> 
> 

### <a name="mechanism-1"></a>Mecanismo 1
Pode aproveitar a propriedade **sqlWriterCleanupScript** para primeiro realizar a ação de limpeza quando uma fatia é executada. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

O script de limpeza seria executado primeiro durante a cópia para uma determinada fatia que eliminaria os dados da Tabela SQL correspondente a essa fatia. A atividade inserirá posteriormente os dados na Tabela SQL. 

Se a fatia for agora reexecutada, verá que a quantidade está atualizada conforme desejado.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Suponha que o registo da Máquina de Aresta Plana seja removido do csv original. Em seguida, a reexecução da fatia produziria o seguinte resultado: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Nada de novo tinha de ser feito. A atividade de cópia executou o script de limpeza para eliminar os dados correspondentes para essa fatia. Em seguida, leu a entrada do csv (que continha apenas 1 disco) e inseriu-a na Tabela. 

### <a name="mechanism-2"></a>Mecanismo 2
> [!IMPORTANT]
> fatiaIdentificarColumnName não é suportado para O Armazém de Dados Azure SQL neste momento. 

Outro mecanismo para alcançar a repetível é ter uma coluna dedicada **(sliceIdentifierColumnName**) na tabela-alvo. Esta coluna seria utilizada pela Azure Data Factory para garantir que a fonte e o destino permanecem sincronizados. Esta abordagem funciona quando há flexibilidade na alteração ou definição do esquema de mesa SQL de destino. 

Esta coluna seria utilizada pela Azure Data Factory para efeitos de repetição e, no processo, a Azure Data Factory não fará alterações de esquema sinuosa à Tabela. Forma de utilizar esta abordagem:

1. Defina uma coluna de tipo binário (32) na tabela SQL de destino. Não deve haver constrangimentos nesta coluna. Vamos nomear esta coluna como 'ColumnForADFuseOnly' para este exemplo.
2. Utilize-a na atividade da cópia da seguinte forma:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

A Azure Data Factory irá povoar esta coluna de acordo com a sua necessidade de garantir que a fonte e o destino permaneçam sincronizados. Os valores desta coluna não devem ser utilizados fora deste contexto pelo utilizador. 

Semelhante ao mecanismo 1, a Copy Activity limpará automaticamente os dados da fatia da dada a partir da Tabela SQL de destino e, em seguida, executará a atividade de cópia normalmente para inserir os dados de origem em destino para essa fatia. 

