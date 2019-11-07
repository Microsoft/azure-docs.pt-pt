---
title: Cópia repetível no Azure Data Factory
description: Saiba como evitar duplicatas, embora uma fatia que copia dados seja executada mais de uma vez.
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
ms.openlocfilehash: e4264bb198a0c167e33f35958079b0523303d29d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682379"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Cópia repetível no Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, tenha em mente a capacidade de repetição para evitar resultados indesejados. No Azure Data Factory, você pode executar novamente uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de uma para que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente de qualquer forma, você precisa garantir que os mesmos dados sejam lidos, independentemente de quantas vezes uma fatia é executada.  
 
> [!NOTE]
> Os exemplos a seguir são para o SQL do Azure, mas são aplicáveis a qualquer armazenamento de dados que ofereça suporte a conjuntos de dados retangulares. Talvez seja necessário ajustar o **tipo** de origem e a propriedade de **consulta** (por exemplo: consulta em vez de sqlReaderQuery) para o armazenamento de dados.   

Normalmente, ao ler de repositórios relacionais, você deseja ler somente os dados correspondentes a essa fatia. Uma maneira de fazer isso seria usando as variáveis de sistema WindowStart e WindowEnd disponíveis em Azure Data Factory. Leia sobre as variáveis e funções em Azure Data Factory aqui no artigo [funções de Azure data Factory e variáveis do sistema](data-factory-functions-variables.md) . Exemplo: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Essa consulta lê dados que se enquadram no intervalo de duração da fatia (WindowStart-> WindowEnd) da tabela MyTable. A execução dessa fatia também garantirá sempre que os mesmos dados forem lidos. 

Em outros casos, talvez você queira ler a tabela inteira e definir o sqlReaderQuery da seguinte maneira:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Gravação repetível em sqlsink
Ao copiar dados para o **SQL/SQL Server do Azure** de outros armazenamentos de dados, você precisa manter a capacidade de repetição em mente para evitar resultados indesejados. 

Ao copiar dados para o Azure SQL/SQL Server Database, a atividade de cópia acrescenta dados à tabela de coletor por padrão. Digamos que você esteja copiando dados de um arquivo CSV (valores separados por vírgula) contendo dois registros para a tabela a seguir em um banco de dados SQL/SQL Server do Azure. Quando uma fatia é executada, os dois registros são copiados para a tabela SQL. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Suponha que você encontrou erros no arquivo de origem e atualizou a quantidade de tubo abaixo de 2 para 4. Se você executar novamente a fatia de dados para esse período manualmente, encontrará dois novos registros anexados ao banco de dado SQL/SQL Server do Azure. Este exemplo supõe que nenhuma das colunas na tabela tem a restrição PRIMARY KEY.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Para evitar esse comportamento, você precisa especificar a semântica UPSERT usando um dos dois mecanismos a seguir:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mecanismo 1: usando sqlWriterCleanupScript
Você pode usar a propriedade **sqlWriterCleanupScript** para limpar os dados da tabela de coletor antes de inserir os dados quando uma fatia é executada. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Quando uma fatia é executada, o script de limpeza é executado primeiro para excluir os dados que correspondem à fatia da tabela SQL. Em seguida, a atividade de cópia insere dados na tabela SQL. Se a fatia for executada novamente, a quantidade será atualizada conforme desejado.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Suponha que o registro de arruela plana seja removido do CSV original. Em seguida, executar novamente a fatia produziria o seguinte resultado: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

A atividade de cópia executou o script de limpeza para excluir os dados correspondentes para essa fatia. Em seguida, ele lê a entrada do CSV (que continha apenas um registro) e a inseriu na tabela. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mecanismo 2: usando sliceIdentifierColumnName
> [!IMPORTANT]
> No momento, não há suporte para sliceIdentifierColumnName no Azure SQL Data Warehouse. 

O segundo mecanismo para obter a capacidade de repetição é ter uma coluna dedicada (sliceIdentifierColumnName) na tabela de destino. Essa coluna seria usada pelo Azure Data Factory para garantir que a origem e o destino permaneçam sincronizados. Essa abordagem funciona quando há flexibilidade na alteração ou definição do esquema de tabela SQL de destino. 

Essa coluna é usada por Azure Data Factory para fins de repetição e no Azure Data Factory de processo não faz nenhuma alteração de esquema na tabela. Maneira de usar essa abordagem:

1. Defina uma coluna do tipo **binary (32)** na tabela SQL de destino. Não deve haver nenhuma restrição nessa coluna. Vamos nomear esta coluna como AdfSliceIdentifier para este exemplo.


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

1. Use-o na atividade de cópia da seguinte maneira:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory popula essa coluna de acordo com a necessidade de garantir que a origem e o destino permaneçam sincronizados. Os valores desta coluna não devem ser usados fora deste contexto. 

Semelhante ao mecanismo 1, a atividade de cópia limpa automaticamente os dados para a fatia determinada da tabela SQL de destino. Em seguida, ele insere dados da origem na tabela de destino. 

## <a name="next-steps"></a>Passos seguintes
Examine os seguintes artigos do conector para obter exemplos de JSON completos: 

- [Base de Dados SQL do Azure](data-factory-azure-sql-connector.md)
- [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
