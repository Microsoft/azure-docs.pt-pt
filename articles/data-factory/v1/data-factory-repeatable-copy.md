---
title: Cópia repetível na Azure Data Factory
description: Aprenda a evitar duplicados mesmo que uma fatia que copie dados seja executada mais de uma vez.
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
ms.openlocfilehash: cd0f389615c95ef9b9bc8280b6486740ddba4fb4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96456823"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Cópia repetível na Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetível de fontes relacionais
Ao copiar dados de lojas de dados relacionais, tenha em mente a repetibilidade para evitar resultados não intencionais. Na Azure Data Factory, pode repetir manualmente uma fatia. Também pode configurar a política de reagem para um conjunto de dados para que uma fatia seja re-executada quando ocorre uma falha. Quando uma fatia é reexame de qualquer forma, você precisa ter certeza de que os mesmos dados são lidos, não importa quantas vezes uma fatia é executada.  
 
> [!NOTE]
> As seguintes amostras são para Azure SQL, mas são aplicáveis a qualquer loja de dados que suporte conjuntos de dados retangulares. Poderá ter de ajustar o **tipo** de origem e a propriedade **de consulta** (por exemplo: consulta em vez de sqlReaderQuery) para a loja de dados.   

Normalmente, ao ler nas lojas relacionais, pretende-se ler apenas os dados correspondentes a essa fatia. Uma forma de o fazer seria utilizando as variáveis do sistema WindowStart e WindowEnd disponíveis na Azure Data Factory. Leia sobre as variáveis e funções na Azure Data Factory aqui na Fábrica de [Dados Azure - Funções e Variáveis do Sistema.](data-factory-functions-variables.md) Exemplo: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Esta consulta lê dados que caem na gama de duração da fatia (WindowStart -> WindowEnd) da tabela MyTable. A repetição desta fatia também garantiria sempre que os mesmos dados são lidos. 

Noutros casos, poderá pretender ler toda a tabela e definir o SqlReaderQuery da seguinte forma:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Escreva repetível para SqlSink
Ao copiar dados para **O Azure SQL/SQL Server** de outras lojas de dados, é necessário ter em mente a repetibilidade para evitar resultados não intencionais. 

Ao copiar dados para a Base de Dados do Servidor Azure SQL/SQL, a atividade de cópia anexa os dados à tabela do lavatório por predefinição. Digamos que está a copiar dados de um ficheiro CSV (valores separados por vírgulas) contendo dois registos para a seguinte tabela numa Base de Dados de Servidor Azure SQL/SQL. Quando uma fatia corre, os dois discos são copiados para a mesa SQL. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Suponha que encontrou erros no ficheiro de origem e atualizou a quantidade de Down Tube de 2 a 4. Se voltar a fazer a fatia de dados para esse período manualmente, encontrará dois novos registos anexados à Base de Dados do Servidor Azure SQL/SQL. Este exemplo pressupõe que nenhuma das colunas da tabela tem o principal constrangimento da chave.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Para evitar este comportamento, tem de especificar a semântica UPSERT utilizando um dos dois mecanismos seguintes:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mecanismo 1: utilização de sqlWriterCleanupScript
Pode utilizar a propriedade **sqlWriterCleanUpScript** para limpar os dados da tabela do lavatório antes de inserir os dados quando uma fatia é executada. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Quando uma fatia é executada, o script de limpeza é executado primeiro para apagar dados que correspondem à fatia da tabela SQL. A atividade de cópia insere os dados na Tabela SQL. Se a fatia for novamente concluída, a quantidade é atualizada conforme desejado.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Suponha que o registo de máquinas de lavar plana seja removido do csv original. Em seguida, a repetição da fatia produziria o seguinte resultado: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

A atividade da cópia executou o script de limpeza para apagar os dados correspondentes para essa fatia. Em seguida, leu a entrada do csv (que continha apenas um registo) e inseriu-a na tabela. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mecanismo 2: utilização de sliceIdentifierColumnName
> [!IMPORTANT]
> Atualmente, o sliceIdentifierColumnName não é suportado para a Azure Synapse Analytics. 

O segundo mecanismo para obter a repetibilidade é ter uma coluna dedicada (sliceIdentifierColumnName) na tabela-alvo. Esta coluna seria utilizada pela Azure Data Factory para garantir que a origem e o destino permanecessem sincronizados. Esta abordagem funciona quando há flexibilidade na alteração ou definição do esquema de tabela SQL de destino. 

Esta coluna é utilizada pela Azure Data Factory para fins de repetibilidade e no processo a Azure Data Factory não faz alterações de esquema na Tabela. Forma de utilizar esta abordagem:

1. Defina uma coluna de **tipo binário (32)** na tabela SQL de destino. Não deve haver constrangimentos nesta coluna. Vamos nomear esta coluna como AdfSliceIdentifier para este exemplo.


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

1. Utilize-o na atividade de cópia da seguinte forma:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

A Azure Data Factory povoa esta coluna de acordo com a sua necessidade de garantir que a origem e o destino permaneçam sincronizados. Os valores desta coluna não devem ser utilizados fora deste contexto. 

Semelhante ao mecanismo 1, a Copy Activity limpa automaticamente os dados da fatia dada a partir da tabela SQL de destino. Em seguida, insere dados da fonte para a tabela de destino. 

## <a name="next-steps"></a>Passos seguintes
Reveja os seguintes artigos de conector que para obter exemplos completos de JSON: 

- [Base de Dados SQL do Azure](data-factory-azure-sql-connector.md)
- [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
