---
title: Cópia do delta de um banco de dados através de uma tabela de controle com o Azure Data Factory | Documentos da Microsoft
description: Saiba como utilizar um modelo de solução para copiar de forma incremental linhas novas ou atualizadas apenas a partir de uma base de dados com o Azure Data Factory.
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
ms.date: 12/24/2018
ms.openlocfilehash: c32592ce539eeb2dec71792e4a6eb31e7d904eff
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771162"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Cópia do delta de um banco de dados com uma tabela de controle

Este artigo descreve um modelo que está disponível de forma incremental carregar novas ou atualizadas linhas de uma tabela de base de dados para o Azure através de uma tabela de controlo externo que armazena um valor de limite superior de tamanho.

Este modelo requer que o esquema da base de dados de origem contém uma chave de coluna ou incrementando timestamp para identificar linhas novas ou atualizadas.

>[!NOTE]
> Se tiver uma coluna timestamp na sua base de dados de origem para identificar as linhas de novas ou atualizadas, mas não quiser criar uma tabela de controlo externo para utilizar para as cópias delta, em vez disso, pode utilizar o [ferramenta copiar dados do Azure Data Factory](copy-data-tool.md) para obter um pipeline. Essa ferramenta usa uma hora agendada de Acionador como uma variável para ler novas linhas de base de dados de origem.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo primeiro obtém o valor de limite de tamanho antigo e o compara com o valor de limite de tamanho atual. Depois disso, ele copia apenas as alterações da base de dados de origem, com base numa comparação entre os valores de marca d' dois água. Por fim, ele armazena o novo valor de limite superior de tamanho para uma tabela de controlo externo para o próximo tempo de carregamento de dados delta.

O modelo contém quatro atividades:
- **Pesquisa** recupera o valor de limite superior de tamanho antigo, o que é armazenado numa tabela de controlo externo.
- Outro **Lookup** atividade recupera o valor de limite superior de tamanho atual da base de dados de origem.
- **Cópia** copia apenas as alterações da base de dados de origem para o arquivo de destino. A consulta que identifica as alterações à base de dados é semelhante a "SELECIONAR * de Data_Source_Table onde TIMESTAMP_Column >"último limite superior de tamanho"e TIMESTAMP_Column < ="atual limite superior de tamanho"'.
- **SqlServerStoredProcedure** grava o valor de limite superior de tamanho atual para uma tabela de controlo externo para as cópias delta próxima vez.

O modelo define cinco parâmetros:
- *Data_Source_Table_Name* é a tabela da base de dados de origem que pretende carregar dados a partir de.
- *Data_Source_WaterMarkColumn* é o nome da coluna na tabela de origem que tenha utilizado para identificar novas ou atualizadas linhas. O tipo desta coluna é normalmente *datetime*, *INT*, ou semelhante.
- *Data_Destination_Folder_Path* ou *Data_Destination_Table_Name* é o local onde os dados são copiados para em seu arquivo de destino.
- *Control_Table_Table_Name* é a tabela de controlo externo que armazena o valor de limite superior de tamanho.
- *Control_Table_Column_Name* é a coluna na tabela de controlo externo que armazena o valor de limite superior de tamanho.

## <a name="how-to-use-this-solution-template"></a>Como utilizar este modelo de solução

1. Explore a origem de tabela de que pretende carregar e definir a coluna de limite superior de tamanho que pode ser utilizada para identificar linhas novas ou atualizadas. O tipo desta coluna poderá ser *datetime*, *INT*, ou semelhante. Valor nesta coluna aumenta à medida que novas linhas são adicionadas. No seguinte exemplo tabela de origem (data_source_table), podemos usar o *LastModifytime* coluna como a coluna de limite superior de tamanho.

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
    
2. Crie uma tabela de controle no SQL Server ou SQL Database do Azure para armazenar o valor de limite superior de tamanho de carregamento de dados delta. No exemplo a seguir, é o nome da tabela de controle *watermarktable*. Nesta tabela, *WatermarkValue* é a coluna que armazena o valor de limite superior de tamanho, e seu tipo é *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Crie um procedimento armazenado na mesma instância do SQL Server ou SQL Database do Azure que utilizou para criar a tabela de controle. O procedimento armazenado é utilizado para escrever o novo valor de limite superior de tamanho para a tabela de controlo externo para o próximo tempo de carregamento de dados delta.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Vá para o **cópias Delta da base de dados** modelo. Criar uma **New** ligação para a base de dados de origem que pretende copiar dados de.

    ![Criar uma nova ligação para a tabela de origem](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Criar uma **New** ligação para o arquivo de dados de destino que pretende copiar os dados.

    ![Criar uma nova ligação para a tabela de destino](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Criar uma **New** ligação para a tabela de controlo externo e o procedimento armazenado que criou nos passos 2 e 3.

    ![Criar uma nova ligação para o arquivo de dados de tabela de controle](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Selecione **Utilize este modelo**.

     ![Utilizar este modelo](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. Verá o pipeline disponível, conforme mostrado no exemplo a seguir:

     ![Reveja o pipeline](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Selecione **procedimento armazenado**. Para **nome do procedimento armazenado**, escolha **[update_watermark]**. Selecione **importar parâmetro**e, em seguida, selecione **adicionar conteúdo dinâmico**.  

     ![Defina a atividade de procedimento armazenado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. Escrever o conteúdo  **\@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** e, em seguida, selecione **concluir**.  

     ![Escrever o conteúdo para os parâmetros do procedimento armazenado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Selecione **depurar**, introduza o **parâmetros**e, em seguida, selecione **concluir**.

    ![Selecione * * * * de depuração](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Os resultados semelhantes ao exemplo a seguir são apresentados:

    ![O resultado da revisão](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Pode criar novas linhas na tabela de origem. Esta é a linguagem SQL de exemplo para criar novas linhas:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
14. Para executar novamente o pipeline, selecione **depurar**, introduza o **parâmetros**e, em seguida, selecione **concluir**.

    ![Selecione * * * * de depuração](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

    Verá que as linhas apenas os novos foram copiadas para o destino.

15. (Opcional:) Se tiver selecionado o SQL Data Warehouse como o destino de dados, também tem de fornecer uma ligação para o armazenamento de Blobs do Azure para teste, o que é exigida pelo Polybase do SQL Data Warehouse. Certifique-se de que o contentor já foi criado no armazenamento de Blobs.
    
    ![Configurar o Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Passos Seguintes

- [Cópia em massa de uma base de dados através de uma tabela de controle com o Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Copiar ficheiros de vários contentores com o Azure Data Factory](solution-template-copy-files-multiple-containers.md)
