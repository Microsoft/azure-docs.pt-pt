---
title: Cópia passível de repetição no Azure Data Factory | Documentos da Microsoft
description: Saiba como evitar duplicatas, apesar de um setor que copia dados é executado mais do que uma vez.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b15dcd9ae5f2f1668db3925b659625497eca491f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079763"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Cópia passível de repetição no Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Leitura passível de repetição de fontes relacionais
Quando armazena a cópia de dados de dados relacionais, tenha a capacidade de repetição em mente para evitar resultados indesejados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor será novamente executado de qualquer forma, terá de certificar-se de que os mesmos dados é de leitura não questão número de vezes que um setor é executado.  
 
> [!NOTE]
> Os exemplos seguintes são para o SQL do Azure, mas são aplicáveis a qualquer arquivo de dados que suporta conjuntos de dados retangulares. Poderá ter de ajustar a **tipo** de origem e o **consulta** propriedade (por exemplo: consulta em vez de sqlReaderQuery) para os dados de armazenamento.   

Normalmente, ao ler a partir de repositórios relacionais, queira ler apenas os dados correspondentes para essas fatias. Uma maneira de fazer isso seria utilizando variáveis de sistema WindowStart e WindowEnd disponíveis no Azure Data Factory. Leia sobre as variáveis e funções no Azure Data Factory aqui no [do Azure Data Factory - funções e variáveis de sistema](data-factory-functions-variables.md) artigo. Exemplo: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Esta consulta lê os dados que se situe no intervalo de duração de setor (WindowStart -> WindowEnd) da tabela MyTable. Volte a executar de neste setor seria também sempre Certifique-se de que os mesmos dados é de leitura. 

Em outros casos, pode pretender ler a tabela inteira e pode definir o sqlReaderQuery da seguinte forma:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Escrita passível de repetição para SqlSink
Quando se copiam dados para **do Azure SQL e do SQL Server** de outros arquivos de dados, terá de ter a capacidade de repetição em mente para evitar resultados indesejados. 

Quando se copiam dados para o banco de dados do Azure SQL/SQL Server, a atividade de cópia acrescenta dados para a tabela do sink por predefinição. Por exemplo, está a copiar dados de um ficheiro CSV (valores separados por vírgulas) que contém dois registos para a tabela a seguir num banco de dados do Azure SQL/SQL Server. Quando um setor é executada, os dois registros são copiados para a tabela SQL. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Suponha que encontrou erros no ficheiro de origem e atualizado a quantidade de baixo Tube de 2 a 4. Se voltar a executar o setor de dados durante esse período manualmente, encontrará dois novos registos anexados ao banco de dados do Azure SQL/SQL Server. Este exemplo parte do princípio de que nenhuma das colunas na tabela tem restrição de chave primária.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Para evitar este comportamento, terá de especificar semântica UPSERT utilizando um dos dois mecanismos seguintes:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mecanismo de 1: sqlWriterCleanupScript usando o
Pode utilizar o **sqlWriterCleanupScript** propriedade a limpeza dos dados da tabela do sink antes de inserir os dados quando um setor é executado. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Quando um setor é executado, o script de limpeza é executado primeiro para eliminar dados de que corresponde ao setor da tabela SQL. A atividade de cópia, em seguida, insere dados na tabela SQL. Se o setor será novamente executado, a quantidade é atualizada conforme pretendido.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Suponha que o registo de Washer simples é removido do original csv. Em seguida, volte a executar o setor produziria o seguinte resultado: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

A atividade de cópia foi executado o script de limpeza para eliminar os dados correspondentes para essas fatias. Em seguida, a entrada leem o csv (que contidas, em seguida, apenas um registo) e inseridos-lo na tabela. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mecanismo de 2: sliceIdentifierColumnName usando o
> [!IMPORTANT]
> Atualmente, sliceIdentifierColumnName não é suportada para o Azure SQL Data Warehouse. 

O mecanismo de segundo para atingir a capacidade de repetição é ter uma coluna dedicada (sliceIdentifierColumnName) na tabela de destino. Esta coluna seria usada pelo Azure Data Factory para garantir que a origem e de destino ser mantidos em sincronia. Essa abordagem funciona quando houver flexibilidade para alterar ou definir o esquema de tabela SQL de destino. 

Esta coluna é utilizada pelo Azure Data Factory para fins de capacidade de repetição e no processo do Azure Data Factory não faz quaisquer alterações de esquema para a tabela. Forma de usar essa abordagem:

1. Definir uma coluna do tipo **binário (32)** no destino da tabela SQL. Não deve haver nenhuma restrição nesta coluna. Vamos dar o nome desta coluna como AdfSliceIdentifier para este exemplo.


Tabela de origem:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

Tabela de destino: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

1. Utilizá-la na atividade de cópia da seguinte forma:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

O Azure Data Factory preenche nesta coluna, de acordo com a sua necessidade de garantir que a origem e de destino ser mantidos em sincronia. Os valores desta coluna não devem ser utilizados fora deste contexto. 

Assim como o mecanismo de 1, atividade de cópia limpa automaticamente os dados para o determinado setor de tabela SQL de destino. Em seguida, insere dados de origem para a tabela de destino. 

## <a name="next-steps"></a>Passos Seguintes
Reveja o conector seguinte artigos que para obter exemplos JSON completos: 

- [Base de Dados SQL do Azure](data-factory-azure-sql-connector.md)
- [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
