---
title: Cópia a granel de uma base de dados utilizando tabela de controlo
description: Aprenda a utilizar um modelo de solução para copiar dados a granel a partir de uma base de dados utilizando uma tabela de controlo externo para armazenar uma lista de tabelas de divisórias utilizando a Azure Data Factory.
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
ms.openlocfilehash: d6d634d9a32ae1728e1122d863ddabd94f73ee27
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414835"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Cópia a granel de uma base de dados com uma tabela de controlo
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Para copiar dados de um armazém de dados no Oracle Server, Netezza, Teradata ou SQL Server para o Azure SQL Data Warehouse, tem de carregar enormes quantidades de dados a partir de várias tabelas. Normalmente, os dados têm de ser divididos em cada tabela para que possa carregar linhas com vários fios em paralelo a partir de uma única tabela. Este artigo descreve um modelo para usar nestes cenários.

 >! NOTA Se pretender copiar dados de um pequeno número de tabelas com volume de dados relativamente pequeno para o SQL Data Warehouse, é mais eficiente utilizar a ferramenta de dados de cópia de cópia de dados da fábrica de [dados do Azure Data](copy-data-tool.md). O modelo descrito neste artigo é mais do que você precisa para esse cenário.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo recupera uma lista de divisórias de base de dados de origem para copiar a partir de uma tabela de controlo externo. Em seguida, iterates sobre cada divisória na base de dados de origem e copia os dados para o destino.

O modelo contém três atividades:
- **A procura** recupera a lista de divisórias seguras de uma tabela de controlo externo.
- **ForEach** obtém a lista de partição da atividade Lookup e itera cada partição para a atividade copy.
- **Copie** cópias de cada partição da loja de bases de dados de origem para a loja de destino.

O modelo define os seguintes parâmetros:
- *Control_Table_Name* é a sua tabela de controlo externo, que armazena a lista de partição para a base de dados de origem.
- *Control_Table_Schema_PartitionID* é o nome do nome da coluna na sua tabela de controlo externo que armazena cada id de partição. Certifique-se de que o ID de partição é único para cada partição na base de dados de origem.
- *Control_Table_Schema_SourceTableName* é a sua tabela de controlo externo que armazena cada nome de mesa a partir da base de dados fonte.
- *Control_Table_Schema_FilterQuery* é o nome da coluna na sua tabela de controlo externo que armazena a consulta do filtro para obter os dados de cada partição na base de dados de origem. Por exemplo, se dividiu os dados por ano, a consulta que é armazenada em cada linha pode ser semelhante a 'select * a partir de datasource onde lastModificartime >= ''2015-01-01 00:00:00'' e LastModifytime <= ''2015-12-31 23:59:59.999'''"
- *Data_Destination_Folder_Path* é o caminho onde os dados são copiados para a sua loja de destino (aplicável quando o destino que escolhe é "File System" ou "Azure Data Lake Storage Gen1"). 
- *Data_Destination_Container* é o caminho da pasta de raiz para onde os dados são copiados na sua loja de destino. 
- *Data_Destination_Directory* é o caminho do diretório sob a raiz onde os dados são copiados para a sua loja de destino. 

Os três últimos parâmetros, que definem o caminho na sua loja de destino, só são visíveis se o destino que escolher for armazenamento baseado em ficheiros. Se escolher "Azure Synapse Analytics (anteriormente SQL DW)" como loja de destino, estes parâmetros não são necessários. Mas os nomes das tabelas e o esquema no Armazém de Dados SQL devem ser os mesmos que os da base de dados de origem.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Crie uma tabela de controlo no SQL Server ou na Base de Dados Azure SQL para armazenar a lista de divisórias de base de dados de origem para cópia a granel. No exemplo seguinte, existem cinco divisórias na base de dados de origem. Três divisórias são para o *datasource_table*, e duas são para o *project_table*. A coluna *LastModificatime* é utilizada para dividir os dados na tabela *datasource_table* a partir da base de dados fonte. A consulta que é usada para ler a primeira partição é 'select * de datasource_table onde lastModificatime >= ''2015-01-01 00:00:00'' e LastModifytime <= ''2015-12-31 23:59:59.999'''. Pode utilizar uma consulta semelhante para ler dados de outras divisórias.

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

2. Vá à cópia a granel do modelo **base de dados.** Crie uma **nova** ligação à tabela de controlo externo que criou no passo 1.

    ![Criar uma nova ligação à tabela de controlo](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Crie uma **nova** ligação à base de dados de origem da qual está a copiar dados.

    ![Criar uma nova ligação à base de dados de origem](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Crie uma **nova** ligação à loja de dados de destino para a qual está a copiar os dados.

    ![Criar uma nova ligação à loja de destino](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Selecione **Utilize este modelo**.

6. Vê o gasoduto, como mostra o seguinte exemplo:

    ![Reveja o oleoduto](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Selecione **Debug,** introduza os **Parâmetros**e, em seguida, selecione **Terminar**.

    ![Clique em **Debug**](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Vê resultados semelhantes aos seguintes exemplos:

    ![Reveja o resultado](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Opcional) Se escolheu "Azure Synapse Analytics (anteriormente SQL DW)" como destino de dados, deve introduzir uma ligação ao armazenamento Azure Blob para encenação, conforme exigido pela SQL Data Warehouse Polybase. O modelo gerará automaticamente um caminho de contentor estoque para o seu armazenamento Blob. Verifique se o recipiente foi criado após a execução do gasoduto.
    
    ![Definição de polibase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Data Factory](introduction.md)
