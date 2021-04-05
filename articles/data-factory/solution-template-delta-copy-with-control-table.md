---
title: Cópia delta de uma base de dados usando uma tabela de controlo
description: Saiba como utilizar um modelo de solução para copiar gradualmente linhas novas ou atualizadas apenas a partir de uma base de dados com a Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: b3b5679b254a07b275cc7fd1295ba4ca5b405fbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100362017"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Cópia delta de uma base de dados com uma tabela de controlo

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve um modelo disponível para carregar gradualmente linhas novas ou atualizadas de uma tabela de base de dados para Azure usando uma tabela de controlo externo que armazena um valor de marca de água de alta qualidade.

Este modelo requer que o esquema da base de dados de origem contenha uma coluna de hora ou chave de incremento para identificar linhas novas ou atualizadas.

>[!NOTE]
> Se tiver uma coluna de tempotamp na sua base de dados de origem para identificar linhas novas ou atualizadas, mas não pretende criar uma tabela de controlo externo para utilizar para cópia delta, pode utilizar a [ferramenta Azure Data Factory Copy Data data](copy-data-tool.md) para obter um pipeline. Esta ferramenta usa um tempo programado para o gatilho como variável para ler novas linhas a partir da base de dados de origem.

## <a name="about-this-solution-template"></a>Sobre este modelo de solução

Este modelo recupera primeiro o valor da marca de água antiga e compara-o com o valor atual da marca de água. Depois disso, copia apenas as alterações da base de dados de origem, com base numa comparação entre os dois valores da marca de água. Finalmente, armazena o novo valor de marca de alta água para uma tabela de controlo externo para carregamento de dados delta da próxima vez.

O modelo contém quatro atividades:
- **A procura** recupera o antigo valor de marca de água, que é armazenado numa tabela de controlo externo.
- Outra atividade **de procura** recupera o valor atual de marca de alta água a partir da base de dados de origem.
- **Copiar** cópias apenas alterações da base de dados de origem para a loja de destino. A consulta que identifica as alterações na base de dados de origem é semelhante a 'SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > "last high-watermark" e TIMESTAMP_Column <= "current high-watermark".
- **SqlServerStoredProcedure** escreve o valor atual de alta marca de água para uma tabela de controlo externo para cópia delta da próxima vez.

O modelo define os seguintes parâmetros:
- *Data_Source_Table_Name* é a tabela na base de dados de origem a partir da qual pretende carregar dados.
- *Data_Source_WaterMarkColumn* é o nome da coluna na tabela de origem que é usada para identificar linhas novas ou atualizadas. O tipo desta coluna é tipicamente *tempo de data*, *INT,* ou similar.
- *Data_Destination_Container* é o caminho principal do local onde os dados são copiados na sua loja de destino.
- *Data_Destination_Directory* é o caminho do diretório sob a raiz do local onde os dados são copiados na sua loja de destino.
- *Data_Destination_Table_Name* é o local onde os dados são copiados na sua loja de destino (aplicável quando "Azure Synapse Analytics" é selecionado como Data Destination).
- *Data_Destination_Folder_Path* é o local onde os dados são copiados na sua loja de destino (aplicável quando "File System" ou "Azure Data Lake Storage Gen1" são selecionados como Data Destination).
- *Control_Table_Table_Name* é a tabela de controlo externo que armazena o valor de marca de água.
- *Control_Table_Column_Name* é a coluna no quadro de controlo externo que armazena o valor de marca de água.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Explore a tabela de origem que pretende carregar e defina a coluna de marca de alta água que pode ser usada para identificar linhas novas ou atualizadas. O tipo desta coluna pode ser *tempo de data*, *INT,* ou similar. O valor desta coluna aumenta à medida que novas linhas são adicionadas. A partir da seguinte tabela de origem da amostra (data_source_table), podemos utilizar a coluna *LastModifytime* como coluna de marca de alta água.

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
    
2. Crie uma tabela de controlo no SQL Server ou na Base de Dados Azure SQL para armazenar o valor de marca de alta água para o carregamento de dados delta. No exemplo seguinte, o nome do quadro de controlo é *de marca de água.* Nesta tabela, *watermarkValue* é a coluna que armazena o valor de marca de alta água, e o seu tipo é *a hora da data.*

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Crie um procedimento armazenado na mesma instância SQL Server ou Azure SQL Database que usou para criar a tabela de controlo. O procedimento armazenado é utilizado para escrever o novo valor de marca de alta água para a tabela de controlo externo para carregamento de dados delta da próxima vez.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Aceda à cópia Delta do modelo **de base de dados.** Crie uma **nova** ligação à base de dados de origem da a que pretende copiar dados.

    ![Criar uma nova ligação à tabela de origem](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Criar uma **nova** ligação à loja de dados de destino para a que pretende copiar os dados.

    ![Criar uma nova ligação à tabela de destino](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Criar uma **nova** ligação à tabela de controlo externo e ao procedimento armazenado que criou nos passos 2 e 3.

    ![Criar uma nova ligação à loja de dados da tabela de controlo](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Selecione **Utilize este modelo.**
    
8. Veja o gasoduto disponível, como mostra o seguinte exemplo:
  
    ![Reveja o oleoduto](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Selecione **Procedimento armazenado**. Para **nome de procedimento armazenado,** escolha **[dbo].[ update_watermark]**. . Selecione **o parâmetro Import** e, em seguida, selecione Adicionar conteúdo **dinâmico**.  

    ![Definir a atividade de procedimento armazenado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. Escreva o conteúdo **\@ {activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** e, em seguida, **selecione Finish**.  

    ![Escreva o conteúdo para os parâmetros do procedimento armazenado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. Selecione **Debug,** introduza os **parâmetros** e, em seguida, selecione **Terminar**.

    ![Selecione **Debug**](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. São apresentados resultados semelhantes ao seguinte exemplo:

    ![Reveja o resultado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Pode criar novas linhas na sua tabela de origem. Aqui está a amostra da linguagem SQL para criar novas linhas:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. Para voltar a executar o gasoduto, selecione **Debug,** introduza os **Parâmetros** e, em seguida, selecione **Terminar**.

    Verá que apenas novas filas foram copiadas para o destino.

15. (Opcional:) Se selecionar a Azure Synapse Analytics como destino de dados, também deve fornecer uma ligação ao armazenamento Azure Blob para a realização, o que é exigido pela Azure Synapse Analytics Polybase. O modelo gerará um caminho de contentor para si. Após o funcionaamento do gasoduto, verifique se o recipiente foi criado no armazenamento blob.
    
    ![Configure a Base Polidisóia](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Passos seguintes

- [Cópia a granel de uma base de dados usando uma tabela de controlo com Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Copiar ficheiros de vários contentores com Azure Data Factory](solution-template-copy-files-multiple-containers.md)
