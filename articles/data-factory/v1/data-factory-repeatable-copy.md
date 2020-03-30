---
title: Cópia repetível na Fábrica de Dados Azure
description: Aprenda a evitar duplicados, mesmo que uma fatia que copie os dados seja executada mais de uma vez.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7188cb5774699fc6e31fc3b8c78068bb33c6f552
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281148"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Cópia repetível na Fábrica de Dados Azure

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetível de fontes relacionais
Ao copiar dados de lojas de dados relacionais, tenha em mente a repetível para evitar resultados não intencionais. Na Azure Data Factory, pode reproduzir uma fatia manualmente. Também pode configurar a política de retry para um conjunto de dados para que uma fatia seja reexecutada quando ocorre uma falha. Quando uma fatia é reexecutada de qualquer forma, você precisa ter certeza de que os mesmos dados são lidos, não importa quantas vezes uma fatia é executada.  
 
> [!NOTE]
> As seguintes amostras são para o Azure SQL, mas são aplicáveis a qualquer loja de dados que suporte conjuntos de dados retangulares. Pode ter de ajustar o **tipo** de origem e a propriedade de **consulta** (por exemplo: consulta em vez de sqlReaderQuery) para a loja de dados.   

Normalmente, ao ler em lojas relacionais, pretende ler apenas os dados correspondentes a essa fatia. Uma forma de o fazer seria utilizando as variáveis do sistema WindowStart e WindowEnd disponíveis na Azure Data Factory. Leia sobre as variáveis e funções na Azure Data Factory aqui no artigo [Azure Data Factory - Funções e Variáveis](data-factory-functions-variables.md) do Sistema. Exemplo: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Esta consulta lê os dados que se inserem na faixa de duração da fatia (WindowStart -> WindowEnd) da tabela MyTable. A reexecução desta fatia também garantirá sempre que os mesmos dados são lidos. 

Noutros casos, poderá querer ler toda a tabela e definir o sqlReaderQuery da seguinte forma:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Escrita repetivel para SqlSink
Ao copiar dados para **o Servidor Azure SQL/SQL** de outras lojas de dados, é necessário ter em mente a repetível para evitar resultados não intencionais. 

Ao copiar dados para a Base de Dados do Servidor Azure SQL/SQL, a atividade de cópia apara os dados para a tabela do lavatório por padrão. Diga que está a copiar dados de um ficheiro CSV (valores separados de vírem) contendo dois registos para a tabela seguinte numa base de dados de servidorEs Azure SQL/SQL. Quando uma fatia corre, os dois registos são copiados para a mesa SQL. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Suponha que encontrou erros no ficheiro fonte e atualizou a quantidade de Down Tube de 2 a 4. Se reexecutar a fatia de dados para esse período manualmente, encontrará dois novos registos anexados à Base de Dados do Servidor Azure SQL/SQL. Este exemplo pressupõe que nenhuma das colunas da tabela tem a principal restrição de chave.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Para evitar este comportamento, é necessário especificar a semântica UPSERT utilizando um dos dois seguintes mecanismos:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mecanismo 1: utilização de sqlWriterCleanupScript
Pode utilizar a propriedade **sqlWriterCleanupScript** para limpar os dados da tabela do lavatório antes de inserir os dados quando uma fatia é executada. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Quando uma fatia corre, o script de limpeza é executado primeiro para eliminar dados que correspondem à fatia da tabela SQL. A atividade de cópia insere os dados na Tabela SQL. Se a fatia for reexecutada, a quantidade é atualizada conforme desejado.

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

A atividade de cópia executou o script de limpeza para eliminar os dados correspondentes para essa fatia. Em seguida, leu a entrada do csv (que continha apenas um disco) e inseriu-a na Tabela. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mecanismo 2: utilização de sliceIdentifierColumnName
> [!IMPORTANT]
> Atualmente, a sliceIdentifierColumnName não é suportada para o Armazém de Dados Azure SQL. 

O segundo mecanismo para alcançar a repetível é ter uma coluna dedicada (sliceIdentifierColumnName) na tabela-alvo. Esta coluna seria utilizada pela Azure Data Factory para garantir que a fonte e o destino permanecem sincronizados. Esta abordagem funciona quando há flexibilidade na alteração ou definição do esquema de mesa SQL de destino. 

Esta coluna é utilizada pela Azure Data Factory para fins de repetição e no processo a Azure Data Factory não faz alterações de esquema sintetizadas na Tabela. Forma de utilizar esta abordagem:

1. Defina uma coluna de tipo **binário (32)** na tabela SQL de destino. Não deve haver constrangimentos nesta coluna. Vamos nomear esta coluna como AdfSliceIdentifier para este exemplo.


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

1. Utilize-a na atividade da cópia da seguinte forma:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

A Azure Data Factory povoa esta coluna de acordo com a sua necessidade de garantir que a fonte e o destino permanecem sincronizados. Os valores desta coluna não devem ser utilizados fora deste contexto. 

Semelhante ao mecanismo 1, a Copy Activity limpa automaticamente os dados da fatia dada a partir da tabela SQL de destino. Em seguida, insere dados da fonte na tabela de destino. 

## <a name="next-steps"></a>Passos seguintes
Reveja os seguintes artigos de conector que para exemplos completos da JSON: 

- [Base de Dados SQL do Azure](data-factory-azure-sql-connector.md)
- [Armazém de dados Azure SQL](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
