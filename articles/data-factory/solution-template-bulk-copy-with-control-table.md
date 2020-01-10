---
title: Cópia em massa de um banco de dados usando a tabela de controle
description: Saiba como usar um modelo de solução para copiar dados em massa de um banco de dado usando uma tabela de controle externo para armazenar uma lista de partições de tabelas de origem usando Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: 3a42d7da21cfb2e3066fbdd81b27c82155d8456f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439941"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Cópia em massa de um banco de dados com uma tabela de controle

Para copiar dados de um data warehouse no Oracle Server, Netezza, Teradata ou SQL Server para o Azure SQL Data Warehouse, você precisa carregar enormes quantidades de dados de várias tabelas. Normalmente, os dados têm que ser particionados em cada tabela para que você possa carregar linhas com vários threads em paralelo a partir de uma única tabela. Este artigo descreve um modelo a ser usado nesses cenários.

 >! Observação Se você quiser copiar dados de um pequeno número de tabelas com volume de dados relativamente pequeno para SQL Data Warehouse, será mais eficiente usar a ferramenta de [copiar dados de Azure data Factory](copy-data-tool.md). O modelo descrito neste artigo é mais do que você precisa para esse cenário.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo recupera uma lista de partições de banco de dados de origem para copiar de uma tabela de controle externa. Em seguida, ela faz a iteração em cada partição no banco de dados de origem e a copia para o destino.

O modelo contém três atividades:
- **Lookup** recupera a lista de partições de banco de dados de uma tabela de controle externa.
- **Foreach** Obtém a lista de partições da atividade de pesquisa e itera cada partição para a atividade de cópia.
- **Copiar** copia cada partição do repositório de banco de dados de origem para o repositório de destino.

O modelo define os seguintes parâmetros:
- *Control_Table_Name* é a tabela de controle externa, que armazena a lista de partições para o banco de dados de origem.
- *Control_Table_Schema_PartitionID* é o nome do nome da coluna na tabela de controle externo que armazena cada ID de partição. Verifique se a ID da partição é exclusiva para cada partição no banco de dados de origem.
- *Control_Table_Schema_SourceTableName* é a tabela de controle externo que armazena cada nome de tabela do banco de dados de origem.
- *Control_Table_Schema_FilterQuery* é o nome da coluna na tabela de controle externo que armazena a consulta de filtro para obter os dados de cada partição no banco de dados de origem. Por exemplo, se você particionou os dados por ano, a consulta armazenada em cada linha pode ser semelhante a ' Select * from DataSource, em que LastModifytime > = ' ' ' 2015-01-01 00:00:00 ' ' e LastModifytime < = ' ' 2015-12-31 23:59:59.999 ' ' '.
- *Data_Destination_Folder_Path* é o caminho em que os dados são copiados no armazenamento de destino (aplicável quando o destino escolhido é "sistema de arquivos" ou "Azure data Lake Storage Gen1"). 
- *Data_Destination_Container* é o caminho da pasta raiz para onde os dados são copiados no armazenamento de destino. 
- *Data_Destination_Directory* é o caminho do diretório sob a raiz onde os dados são copiados para o armazenamento de destino. 

Os últimos três parâmetros, que definem o caminho no armazenamento de destino, só estarão visíveis se o destino escolhido for um armazenamento baseado em arquivo. Se você escolher "Azure Synapse Analytics (anteriormente conhecido como SQL DW)" como o armazenamento de destino, esses parâmetros não serão necessários. Mas os nomes de tabela e o esquema em SQL Data Warehouse devem ser iguais aos do banco de dados de origem.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Crie uma tabela de controle no SQL Server ou no banco de dados SQL do Azure para armazenar a lista de partições de banco de dados de origem para cópia em massa. No exemplo a seguir, há cinco partições no banco de dados de origem. Três partições são para o *datasource_table*e duas são para o *project_table*. A coluna *LastModifytime* é usada para particionar os dados na tabela *datasource_table* do banco de dados de origem. A consulta usada para ler a primeira partição é ' Select * from datasource_table em que LastModifytime > = ' ' 2015-01-01 00:00:00 ' ' e LastModifytime < = ' ' 2015-12-31 23:59:59.999 ' ' '. Você pode usar uma consulta semelhante para ler dados de outras partições.

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

2. Vá para o modelo **cópia em massa do banco de dados** . Crie uma **nova** conexão com a tabela de controle externo que você criou na etapa 1.

    ![Criar uma nova conexão com a tabela de controle](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Crie uma **nova** conexão com o banco de dados de origem para o qual você está copiando o dado.

    ![Criar uma nova conexão com o banco de dados de origem](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Crie uma **nova** conexão com o armazenamento de dados de destino para o qual você está copiando os dados.

    ![Criar uma nova conexão com o repositório de destino](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Selecione **Utilizar este modelo**.

6. Você verá o pipeline, conforme mostrado no exemplo a seguir:

    ![Examinar o pipeline](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Selecione **depurar**, insira os **parâmetros**e, em seguida, selecione **concluir**.

    ![Clique em * * Depurar * *](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Você verá resultados semelhantes ao exemplo a seguir:

    ![Examinar o resultado](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. Adicional Se você escolheu "Azure Synapse Analytics (anteriormente conhecido como SQL DW)" como o destino de dados, deverá inserir uma conexão com o armazenamento de BLOBs do Azure para preparo, conforme exigido pelo SQL Data Warehouse polybase. O modelo gerará automaticamente um caminho de contêiner para o armazenamento de BLOBs. Verifique se o contêiner foi criado após a execução do pipeline.
    
    ![Configuração do polybase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Data Factory](introduction.md)
