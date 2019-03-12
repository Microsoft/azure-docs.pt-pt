---
title: Da cópia de um banco de dados em massa através de uma tabela de controle com o Azure Data Factory | Documentos da Microsoft
description: Saiba como utilizar um modelo de solução para copiar dados em massa de uma base de dados ao utilizar uma tabela de controlo externo para armazenar uma lista de partições de tabelas de origem com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.openlocfilehash: c4224693642e8c9f76deedc0c8ad8586e122cc23
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530574"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Cópia em massa de uma base de dados com uma tabela de controle

Para copiar dados de um armazém de dados no servidor Oracle, Netezza, Teradata ou o SQL Server para o Azure SQL Data Warehouse, terá de carregar grandes quantidades de dados de várias tabelas. Normalmente, os dados têm de ser particionados em cada tabela para que pode carregar linhas com vários threads em paralelo a partir de uma única tabela. Este artigo descreve um modelo para utilizar nesses cenários.

 >! Tenha em atenção de que se pretender copiar dados de um pequeno número de tabelas com o volume de dados relativamente pequeno para o SQL Data Warehouse, é mais eficiente para utilizar o [ferramenta copiar dados do Azure Data Factory](copy-data-tool.md). O modelo que é descrito neste artigo é mais do que precisa para esse cenário.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo recupera uma lista de partições de base de dados de origem para copiar a partir de uma tabela de controlo externo. Em seguida, ele itera cada partição na base de dados de origem e copia os dados para o destino.

O modelo contém três atividades:
- **Pesquisa** obtém a lista de partições de base de dados-se de uma tabela de controlo externo.
- **ForEach** obtém a lista de partição da atividade Lookup e itera cada partição para a atividade de cópia.
- **Cópia** copia cada partição de arquivo de banco de dados de origem para o arquivo de destino.

O modelo define cinco parâmetros:
- *Control_Table_Name* é sua tabela de controlo externa, que armazena a lista de partição para a base de dados de origem.
- *Control_Table_Schema_PartitionID* é o nome do nome de coluna na tabela controlo externo que armazena a cada ID de partição. Certifique-se de que o ID de partição é exclusivo para cada partição na base de dados de origem.
- *Control_Table_Schema_SourceTableName* é sua tabela de controlo externo que armazena o nome de cada tabela da base de dados de origem.
- *Control_Table_Schema_FilterQuery* é o nome da coluna na tabela controlo externo que armazena a consulta de filtro para obter os dados de cada partição na base de dados de origem. Por exemplo, se particionada os dados por ano, a consulta que é armazenada em cada linha seria semelhante a "selecionar * da origem de dados onde LastModifytime > = ' 2015-01-01 00:00:00 ' e LastModifytime < = ' 2015-12-31 23:59:59.999 ' '.
- *Data_Destination_Folder_Path* é o caminho onde os dados são copiados para o arquivo de destino. Este parâmetro só é visível se o destino que escolher é o armazenamento baseado em ficheiros. Se escolher o SQL Data Warehouse como arquivo de destino, este parâmetro não é necessário. Mas os nomes de tabela e o esquema no SQL Data Warehouse tem de ser o mesmo que aquelas à base de dados.

## <a name="how-to-use-this-solution-template"></a>Como utilizar este modelo de solução

1. Crie uma tabela de controle no SQL Server ou SQL Database do Azure para armazenar a lista de partição de base de dados de origem para cópia em massa. No exemplo a seguir, há cinco partições da base de dados de origem. Três partições são para o *datasource_table*, e dois são para o *project_table*. A coluna *LastModifytime* é utilizado para particionar os dados na tabela *datasource_table* da base de dados de origem. A consulta que é usada para ler a primeira partição é "selecionar * de datasource_table onde LastModifytime > = ' 2015-01-01 00:00:00 ' e LastModifytime < = ' 2015-12-31 23:59:59.999 ' '. Pode utilizar uma consulta semelhante para ler os dados de outras partições.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Vá para o **cópia em massa da base de dados** modelo. Criar uma **New** ligação para a tabela de controlo externo que criou no passo 1.

    ![Criar uma nova ligação para a tabela de controle](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Criar uma **New** ligação para a base de dados de origem que está a copiar dados a partir de.

     ![Criar uma nova ligação para a base de dados de origem](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Criar uma **New** armazenar de ligação para os dados de destino que está a copiar os dados para.

    ![Criar uma nova ligação para o arquivo de destino](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Selecione **Utilize este modelo**.

    ![Utilizar este modelo](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Verá o pipeline, conforme mostrado no exemplo a seguir:

    ![Reveja o pipeline](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Selecione **depurar**, introduza o **parâmetros**e, em seguida, selecione **concluir**.

    ![Clique em * * * * de depuração](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Veja os resultados que são semelhantes ao seguinte exemplo:

    ![O resultado da revisão](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Opcional) Se tiver escolhido como o destino de dados do SQL Data Warehouse, tem de introduzir uma ligação para o armazenamento de Blobs do Azure para teste, conforme exigido pelo Polybase do SQL Data Warehouse. Certifique-se de que o contentor no armazenamento de BLOBs já foi criado.
    
    ![Definição de Polybase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Passos Seguintes

- [Introdução ao Azure Data Factory](introduction.md)
