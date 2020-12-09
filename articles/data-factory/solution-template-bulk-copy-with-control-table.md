---
title: Cópia a granel de uma base de dados usando tabela de controlo
description: Aprenda a utilizar um modelo de solução para copiar dados a granel de uma base de dados utilizando uma tabela de controlo externo para armazenar uma lista de partilhas de tabelas de origem utilizando a Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: d89fd8b4102333603fa71b2cc28a49b732b91b08
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920885"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Cópia a granel de uma base de dados com uma tabela de controlo

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Para copiar dados de um armazém de dados em Oracle Server, Netezza, Teradata ou SQL Server para Azure Synapse Analytics, tem de carregar enormes quantidades de dados de várias tabelas. Normalmente, os dados têm de ser divididos em cada tabela para que possa carregar linhas com múltiplos fios em paralelo a partir de uma única tabela. Este artigo descreve um modelo para usar nestes cenários.

 >!NOTA Se pretender copiar dados de um pequeno número de tabelas com volume de dados relativamente pequeno para a Azure Synapse Analytics, é mais eficiente utilizar a [ferramenta Azure Data Factory Copy Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Data Copy Data Data .](copy-data-tool.md) O modelo descrito neste artigo é mais do que o necessário para esse cenário.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo recupera uma lista de divisórias de base de dados de origem para copiar a partir de uma tabela de controlo externo. Em seguida, itera sobre cada partição na base de dados de origem e copia os dados para o destino.

O modelo contém três atividades:
- **A procura** recupera a lista de divisórias de base de dados de uma tabela de controlo externa.
- **ForEach** obtém a lista de partição da atividade De Procuração e itera cada partição para a atividade Copy.
- **Copie** cada divisória da loja de base de dados de origem para a loja de destino.

O modelo define os seguintes parâmetros:
- *Control_Table_Name* é a sua tabela de controlo externo, que armazena a lista de divisórias para a base de dados de origem.
- *Control_Table_Schema_PartitionID* é o nome do nome da coluna na sua mesa de controlo externa que armazena cada ID de partição. Certifique-se de que o ID de partição é único para cada partição na base de dados de origem.
- *Control_Table_Schema_SourceTableName* é a sua tabela de controlo externo que armazena cada nome de tabela a partir da base de dados de origem.
- *Control_Table_Schema_FilterQuery* é o nome da coluna na sua tabela de controlo externo que armazena a consulta do filtro para obter os dados de cada partição na base de dados de origem. Por exemplo, se dividir os dados por ano, a consulta armazenada em cada linha pode ser semelhante a 'selecione * de fonte de dados onde LastModifytime >= ''2015-01-01 00:00:00'' e LastModifytime <= ''2015-12-31 23:59:59.99''''.
- *Data_Destination_Folder_Path* é o caminho onde os dados são copiados para a sua loja de destino (aplicável quando o destino que escolher é "File System" ou "Azure Data Lake Storage Gen1"). 
- *Data_Destination_Container* é o caminho da pasta raiz onde os dados são copiados na sua loja de destino. 
- *Data_Destination_Directory* é o caminho do diretório sob a raiz onde os dados são copiados para a sua loja de destino. 

Os últimos três parâmetros, que definem o caminho na sua loja de destino só são visíveis se o destino que escolher for o armazenamento baseado em ficheiros. Se escolher "Azure Synapse Analytics" como loja de destino, estes parâmetros não são necessários. Mas os nomes das tabelas e o esquema no Azure Synapse Analytics devem ser os mesmos que estão na base de dados de origem.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Crie uma tabela de controlo no SQL Server ou na Base de Dados Azure SQL para armazenar a lista de partição da base de dados de origem para cópia a granel. No exemplo seguinte, existem cinco divisórias na base de dados de origem. Três divisórias são para o *datasource_table*, e duas são para o *project_table*. A coluna *LastModifytime* é utilizada para dividir os dados na tabela *datasource_table* da base de dados de origem. A consulta que é usada para ler a primeira partição é 'select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' e LastModifytime <= ''2015-12-31 23:59:59'''. Pode utilizar uma consulta semelhante para ler dados de outras divisórias.

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

2. Aceda ao modelo de Cópia a granel do modelo **de base de dados.** Crie uma **nova** ligação à tabela de controlo externo que criou no passo 1.

    ![Criar uma nova ligação à mesa de controlo](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Crie uma **nova** ligação à base de dados de origem da qual está a copiar dados.

    ![Criar uma nova ligação à base de dados de origem](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Crie uma **nova** ligação à loja de dados de destino para a qual está a copiar os dados.

    ![Criar uma nova ligação à loja de destino](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Selecione **Utilize este modelo.**

6. Vê-se o gasoduto, como mostra o seguinte exemplo:

    ![Reveja o oleoduto](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Selecione **Debug,** introduza os **parâmetros** e, em seguida, selecione **Terminar**.

    ![Clique em **Debug**](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Vê-se resultados semelhantes ao seguinte exemplo:

    ![Reveja o resultado](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Opcional) Se escolheu "Azure Synapse Analytics" como destino de dados, deve introduzir uma ligação com o armazenamento Azure Blob para a realização, conforme exigido pela Azure Synapse Analytics Polybase. O modelo gerará automaticamente um caminho de contentor para o seu armazenamento Blob. Verifique se o recipiente foi criado após o funcionado do gasoduto.
    
    ![Definição de base poli-base](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Data Factory](introduction.md)
