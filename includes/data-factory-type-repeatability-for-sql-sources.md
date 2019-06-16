---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 24bb7a1fcb1569922fb34034fb3c0d003cdd7061
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66147246"
---
## <a name="repeatability-during-copy"></a>Capacidade de repetição durante a cópia
Quando armazena a cópia de dados para o Azure SQL e do SQL Server de outros dados, é necessário ter a capacidade de repetição em mente para evitar resultados indesejados. 

Quando se copiam dados para o banco de dados do Azure SQL/SQL Server, a atividade de cópia será por predefinição ACRESCENTAR o conjunto de dados para a tabela do sink por predefinição. Por exemplo, quando se copiam dados a partir de uma origem de ficheiro CSV (dados de valores separados por vírgulas) que contém dois registros à base de dados do Azure SQL/SQL Server, este é o que a tabela é semelhante a:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Suponha que encontrou erros no ficheiro de origem e atualizado a quantidade de baixo Tube de 2 a 4 no arquivo de origem. Se volte a executar o setor de dados durante esse período, encontrará dois novos registos anexados ao banco de dados do Azure SQL/SQL Server. A seguir pressupõe que nenhuma das colunas na tabela tenha a restrição de chave primária.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Para evitar isto, terá de especificar a semântica UPSERT ao tirar partido de um do abaixo 2 mecanismos indicados abaixo.

> [!NOTE]
> Um setor pode ser reexecutado automaticamente no Azure Data Factory de acordo com a política de repetição especificada.
> 
> 

### <a name="mechanism-1"></a>Mecanismo de 1
Pode tirar partido **sqlWriterCleanupScript** propriedade execute primeiramente a ação de limpeza quando um setor é executado. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Seria possível executar o script de limpeza primeiro durante a cópia de um determinado setor que teria de eliminar os dados da tabela de SQL correspondente a esse setor. A atividade, em seguida, irá inserir os dados para a tabela de SQL. 

Se o setor está agora volte a executar, em seguida, encontrará que a quantidade é atualizada como pretendido.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Suponha que o registo de Washer simples é removido do original csv. Em seguida, voltar a executar o setor produziria o seguinte resultado: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Nada de novo tinha que ser feito. A atividade de cópia foi executado o script de limpeza para eliminar os dados correspondentes para essas fatias. Em seguida, a entrada leem o csv (que, em seguida, contidos apenas 1 registo) e inseridos-lo na tabela. 

### <a name="mechanism-2"></a>Mecanismo de 2
> [!IMPORTANT]
> sliceIdentifierColumnName não é suportada para o Azure SQL Data Warehouse neste momento. 

Outro mecanismo para atingir a capacidade de repetição é ter uma coluna dedicada (**sliceIdentifierColumnName**) na tabela de destino. Esta coluna seria usada pelo Azure Data Factory para garantir que a origem e de destino ser mantidos em sincronia. Essa abordagem funciona quando houver flexibilidade para alterar ou definir o esquema de tabela SQL de destino. 

Esta coluna vai ser utilizada pelo Azure Data Factory para fins de capacidade de repetição e o processo do Azure Data Factory irá não fazer quaisquer alterações de esquema para a tabela. Forma de usar essa abordagem:

1. Defina uma coluna do tipo binário (32) no destino da tabela SQL. Não deve haver nenhuma restrição nesta coluna. Vamos dar o nome desta coluna como 'ColumnForADFuseOnly' para este exemplo.
2. Utilizá-la na atividade de cópia da seguinte forma:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

O Azure Data Factory irá preencher nesta coluna, de acordo com a sua necessidade de garantir que a origem e de destino ser mantidos em sincronia. Os valores desta coluna não devem ser utilizados fora deste contexto pelo utilizador. 

Assim como o mecanismo de 1, atividade de cópia será automaticamente pela primeira vez, limpar os dados para o determinado setor de tabela SQL de destino e, em seguida, executar a atividade de cópia normalmente para inserir os dados de origem para destino para essas fatias. 

