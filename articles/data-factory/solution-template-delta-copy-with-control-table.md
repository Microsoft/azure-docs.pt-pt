---
title: Cópia Delta de um banco de dados usando uma tabela de controle
description: Saiba como usar um modelo de solução para copiar de forma incremental as linhas novas ou atualizadas somente de um banco de dados com Azure Data Factory.
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
ms.openlocfilehash: 4c72bd37a636ec31c13737705c22aaa895b9ad72
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928201"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Cópia Delta de um banco de dados com uma tabela de controle

Este artigo descreve um modelo que está disponível para carregar incrementalmente novas linhas ou atualizadas de uma tabela de banco de dados no Azure usando uma tabela de controle externo que armazena um valor de alta-água.

Este modelo requer que o esquema do banco de dados de origem contenha uma coluna de carimbo de data/hora ou uma chave de incremento para identificar linhas novas ou atualizadas.

>[!NOTE]
> Se você tiver uma coluna de carimbo de data/hora em seu banco de dados de origem para identificar linhas novas ou atualizadas, mas não quiser criar uma tabela de controle externo para usar para cópia Delta, você poderá usar a [ferramenta Azure Data Factory copiar dados](copy-data-tool.md) para obter um pipeline. Essa ferramenta usa um tempo agendado para o gatilho como uma variável para ler novas linhas do banco de dados de origem.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Esse modelo primeiro recupera o valor antigo da marca d' água e o compara com o valor da marca d' água atual. Depois disso, ele copia apenas as alterações do banco de dados de origem, com base em uma comparação entre os dois valores de marca d' água. Por fim, ele armazena o novo valor de marca d' água alta em uma tabela de controle externo para carregamento de dados Delta na próxima vez.

O modelo contém quatro atividades:
- **Lookup** recupera o antigo valor de alta marca d' água, que é armazenado em uma tabela de controle externo.
- Outra atividade de **pesquisa** recupera o valor de alta-água atual do banco de dados de origem.
- **Copiar** copia somente as alterações do banco de dados de origem para o repositório de destino. A consulta que identifica as alterações no banco de dados de origem é semelhante a ' selecionar * de Data_Source_Table em que TIMESTAMP_Column > "última marca d' água alta" e TIMESTAMP_Column < = "alta-marca d' água atual".
- **SqlServerStoredProcedure** grava o valor de alta-água atual em uma tabela de controle externa para cópia Delta na próxima vez.

O modelo define cinco parâmetros:
- *Data_Source_Table_Name* é a tabela no banco de dados de origem para a qual você deseja carregar o.
- *Data_Source_WaterMarkColumn* é o nome da coluna na tabela de origem usada para identificar linhas novas ou atualizadas. O tipo dessa coluna normalmente é *DateTime*, *int*ou semelhante.
- *Data_Destination_Folder_Path* ou *Data_Destination_Table_Name* é o local onde os dados são copiados no armazenamento de destino.
- *Control_Table_Table_Name* é a tabela de controle externo que armazena o valor de marca d' água alta.
- *Control_Table_Column_Name* é a coluna na tabela de controle externo que armazena o valor de marca d' água alta.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Explore a tabela de origem que deseja carregar e defina a coluna de marca d' água alta que pode ser usada para identificar linhas novas ou atualizadas. O tipo dessa coluna pode ser *DateTime*, *int*ou semelhante. O valor dessa coluna aumenta conforme novas linhas são adicionadas. Na tabela de origem de exemplo a seguir (data_source_table), podemos usar a coluna *LastModifytime* como a coluna de marca d' água alta.

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
    
2. Crie uma tabela de controle no SQL Server ou no banco de dados SQL do Azure para armazenar o valor de marca d' água alta para o carregamento de dados Delta. No exemplo a seguir, o nome da tabela de controle é *watermarktable*. Nesta tabela, *watermarkvalue* é a coluna que armazena o valor de marca d' água alta e seu tipo é *DateTime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Crie um procedimento armazenado na mesma SQL Server ou instância do banco de dados SQL do Azure que você usou para criar a tabela de controle. O procedimento armazenado é usado para gravar o novo valor de marca d' água alta na tabela de controle externo para carregamento de dados Delta na próxima vez.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Vá para a **cópia Delta do modelo de banco de dados** . Crie uma **nova** conexão com o banco de dados de origem a partir do qual você deseja fazer a cópia.

    ![Criar uma nova conexão com a tabela de origem](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Crie uma **nova** conexão com o armazenamento de dados de destino para o qual você deseja copiar os dados.

    ![Criar uma nova conexão com a tabela de destino](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Crie uma **nova** conexão com a tabela de controle externo e o procedimento armazenado que você criou nas etapas 2 e 3.

    ![Criar uma nova conexão com o armazenamento de dados da tabela de controle](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Selecione **Utilizar este modelo**.

     ![Utilizar este modelo](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. Você verá o pipeline disponível, conforme mostrado no exemplo a seguir:

     ![Examinar o pipeline](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Selecione o **procedimento armazenado**. Para **nome do procedimento armazenado**, escolha **[update_watermark]** . Selecione **importar parâmetro**e, em seguida, selecione **adicionar conteúdo dinâmico**.  

     ![Definir a atividade de procedimento armazenado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. Grave o conteúdo **\@{Activity (' LookupCurrentWaterMark '). Output. firstRow. NewWatermarkValue}** e, em seguida, selecione **concluir**.  

     ![Gravar o conteúdo para os parâmetros do procedimento armazenado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Selecione **depurar**, insira os **parâmetros**e, em seguida, selecione **concluir**.

    ![Selecione * * depurar * *](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Os resultados semelhantes ao exemplo a seguir são exibidos:

    ![Examinar o resultado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Você pode criar novas linhas na tabela de origem. Aqui está uma linguagem SQL de exemplo para criar novas linhas:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
14. Para executar o pipeline novamente, selecione **depurar**, insira os **parâmetros**e, em seguida, selecione **concluir**.

    ![Selecione * * depurar * *](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

    Você verá que apenas as novas linhas foram copiadas para o destino.

15. Adicional Se você selecionou SQL Data Warehouse como o destino de dados, também deverá fornecer uma conexão com o armazenamento de BLOBs do Azure para preparo, o que é exigido pelo SQL Data Warehouse polybase. Verifique se o contêiner já foi criado no armazenamento de BLOBs.
    
    ![Configurar o polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Passos seguintes

- [Cópia em massa de um banco de dados usando uma tabela de controle com Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Copiar arquivos de vários contêineres com Azure Data Factory](solution-template-copy-files-multiple-containers.md)
