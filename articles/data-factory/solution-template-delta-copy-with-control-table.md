---
title: Cópia delta de uma base de dados usando uma tabela de controlo
description: Aprenda a usar um modelo de solução para copiar gradualmente linhas novas ou atualizadas apenas a partir de uma base de dados com a Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/24/2018
ms.openlocfilehash: 01a6d796a9a8306da5bb707111b07786136a66cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414793"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Cópia delta de uma base de dados com uma tabela de controlo
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve um modelo que está disponível para carregar gradualmente linhas novas ou atualizadas de uma tabela de bases de dados para o Azure, utilizando uma tabela de controlo externo que armazena um valor de marca de água elevado.

Este modelo requer que o esquema da base de dados de origem contenha uma coluna de carimbo de tempo ou uma chave de incremento para identificar linhas novas ou atualizadas.

>[!NOTE]
> Se tiver uma coluna de carimbo de tempo na sua base de dados de origem para identificar linhas novas ou atualizadas, mas não pretende criar uma tabela de controlo externo para utilizar para cópia delta, pode utilizar a [ferramenta Azure Data](copy-data-tool.md) copy data para obter um pipeline. Esta ferramenta utiliza um tempo programado pelo gatilho como variável para ler novas linhas a partir da base de dados de origem.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo primeiro recupera o valor antigo da marca de água e compara-o com o valor atual da marca de água. Depois disso, copia apenas as alterações da base de dados de origem, com base numa comparação entre os dois valores da marca de água. Finalmente, armazena o novo valor de alta marca de água para uma tabela de controlo externo para carregamento de dados delta da próxima vez.

O modelo contém quatro atividades:
- **A procura** recupera o antigo valor da marca de água, que é armazenado numa mesa de controlo externo.
- Outra atividade de **pesquisa** recupera o valor atual de alta marca de água da base de dados de origem.
- **Cópias** cópias apenas alteram da base de dados de origem para a loja de destino. A consulta que identifica as alterações na base de dados de origem é semelhante a 'SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > "última marca de água alta" e TIMESTAMP_Column <= "marca de água alta atual".
- **SqlServerStoredProcedure** escreve o valor atual de marca de água alta para um quadro de controlo externo para cópia delta da próxima vez.

O modelo define os seguintes parâmetros:
- *Data_Source_Table_Name* é a tabela na base de dados de origem a que pretende carregar dados.
- *Data_Source_WaterMarkColumn* é o nome da coluna na tabela de origem que é usada para identificar linhas novas ou atualizadas. O tipo desta coluna é *tipicamente*data, *INT,* ou similar.
- *Data_Destination_Container* é o caminho raiz do local onde os dados são copiados na sua loja de destino.
- *Data_Destination_Directory* é o caminho do diretório sob a raiz do local onde os dados são copiados na sua loja de destino.
- *Data_Destination_Table_Name* é o local onde os dados são copiados na sua loja de destino (aplicável quando "Azure Synapse Analytics (anteriormente SQL DW)" é selecionado como Data Destination).
- *Data_Destination_Folder_Path* é o local onde os dados são copiados na sua loja de destino (aplicável quando "File System" ou "Azure Data Lake Storage Gen1" for selecionado como Data Destination).
- *Control_Table_Table_Name* é o quadro de controlo externo que armazena o valor de alta marca de água.
- *Control_Table_Column_Name* é a coluna na tabela de controlo externo que armazena o valor de marca de água elevada.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Explore a tabela de origem que pretende carregar e defina a coluna de marca de água alta que pode ser usada para identificar linhas novas ou atualizadas. O tipo desta coluna pode *ser*data, *INT,* ou similar. O valor desta coluna aumenta à medida que novas linhas são adicionadas. A partir da tabela de origem da amostra seguinte (data_source_table), podemos usar a coluna *LastModificartime* como coluna de marca de alta água.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. Crie uma tabela de controlo no SQL Server ou na Base de Dados Azure SQL para armazenar o valor de marca de água elevada para carregamento de dados delta. No exemplo seguinte, o nome do quadro de controlo é *náuito.* Neste quadro, *watermarkValue* é a coluna que armazena o valor de marca de água elevada, e o seu tipo é *data.*

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Crie um procedimento armazenado na mesma instância de Base de Dados SQL Server ou Azure SQL que usou para criar a tabela de controlo. O procedimento armazenado é utilizado para escrever o novo valor de marca de água elevada para o quadro de controlo externo para o carregamento de dados delta da próxima vez.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Vá à cópia Delta do modelo **de base de dados.** Crie uma **nova** ligação à base de dados de origem da que pretende copiar dados.

    ![Criar uma nova ligação à tabela de origem](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Crie uma **nova** ligação à loja de dados de destino para a a a que pretende copiar os dados.

    ![Criar uma nova ligação à tabela de destino](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Crie uma **nova** ligação à tabela de controlo externo e ao procedimento armazenado que criou nos passos 2 e 3.

    ![Criar uma nova ligação à loja de dados da tabela de controlo](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Selecione **Utilize este modelo**.
    
8. Vê o gasoduto disponível, como mostra o seguinte exemplo:
  
    ![Reveja o oleoduto](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Selecione **Procedimento Armazenado**. Para **o nome do procedimento armazenado,** escolha **[dbo].[ update_watermark]**. Selecione **parâmetro de importação**e, em seguida, selecione Adicionar conteúdo **dinâmico**.  

    ![Definir a atividade do procedimento armazenado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. Escreva o conteúdo ** \@{activity ('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}**, e, em seguida, selecione **Finish**.  

    ![Escreva o conteúdo para os parâmetros do procedimento armazenado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. Selecione **Debug,** introduza os **Parâmetros**e, em seguida, selecione **Terminar**.

    ![Selecione **Debug**](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. São apresentados resultados semelhantes ao seguinte exemplo:

    ![Reveja o resultado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Pode criar novas filas na sua mesa de origem. Aqui está a amostra de linguagem SQL para criar novas linhas:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. Para voltar a executar o gasoduto, selecione **Debug**, introduza os **Parâmetros**e, em seguida, selecione **Terminar**.

    Verá que só novas filas foram copiadas para o destino.

15. (Opcional:) Se selecionar o Azure Synapse Analytics (anteriormente SQL DW) como destino de dados, também deve fornecer uma ligação ao armazenamento Azure Blob para encenação, o que é exigido pela SQL Data Warehouse Polybase. O modelo irá gerar um caminho de contentor para si. Após a execução do gasoduto, verifique se o recipiente foi criado no armazenamento blob.
    
    ![Configure Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Passos seguintes

- [Cópia a granel de uma base de dados utilizando uma tabela de controlo com a Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Copiar ficheiros de vários contentores com fábrica de dados Azure](solution-template-copy-files-multiple-containers.md)
