---
title: Migrar dados da Amazon S3 para a Azure Data Lake Storage Gen2
description: Aprenda a usar um modelo de solução para migrar dados do Amazon S3 utilizando uma tabela de controlo externo para armazenar uma lista de divisórias no AWS S3 com a Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: e25299c2ce5d31da8f3caa5b02ab8def816b31ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91398225"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Migrar dados da Amazon S3 para a Azure Data Lake Storage Gen2

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Use os modelos para migrar petabytes de dados que consistem em centenas de milhões de ficheiros da Amazon S3 para a Azure Data Lake Storage Gen2. 

 > [!NOTE]
 > If you want to copy small data volume from AWS S3 to Azure (for example, less than 10 TB), it's more efficient and easy to use the [Azure Data Factory Copy Data tool](copy-data-tool.md). O modelo descrito neste artigo é mais do que o que você precisa.

## <a name="about-the-solution-templates"></a>Sobre os modelos de solução

Recomenda-se a partição de dados especialmente quando migra mais de 10 TB de dados. Para dividir os dados, aproveite a definição de 'prefixo' para filtrar as pastas e ficheiros no Amazon S3 pelo nome, e então cada trabalho de cópia ADF pode copiar uma partição de cada vez. Pode executar vários trabalhos de cópia ADF simultaneamente para uma melhor produção.

A migração de dados requer normalmente uma migração de dados históricos único, além de sincronizar periodicamente as alterações de AWS S3 para Azure. Existem dois modelos abaixo, onde um modelo cobre a migração de dados históricos de uma vez e outro modelo cobre sincronizar as alterações de AWS S3 para Azure.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Para o modelo migrar dados históricos do Amazon S3 para a Azure Data Lake Storage Gen2

Este modelo *(nome do modelo: migrar dados históricos de AWS S3 para Azure Data Lake Storage Gen2*) assume que escreveu uma lista de divisórias numa tabela de controlo externo na Base de Dados Azure SQL. Assim, utilizará uma atividade *de Procura* para recuperar a lista de divisórias da tabela de controlo externo, iterar sobre cada divisória, e fazer com que cada trabalho de cópia ADF copie uma partição de cada vez. Uma vez concluído qualquer trabalho de cópia, utiliza a atividade *do Procedimento Armazenado* para atualizar o estado de cópia de cada divisória na tabela de controlo.

O modelo contém cinco atividades:
- **A procura** recupera as divisórias que não foram copiadas para a Azure Data Lake Storage Gen2 a partir de uma tabela de controlo externo. O nome da tabela é *s3_partition_control_table* e a consulta para carregar dados da tabela é *"SELECT PartitionPrefix FROM s3_partition_control_table WHERE SuccessOrFailure = 0".*
- **ForEach** obtém a lista de partição da atividade *de Lookup* e itera cada partição para a atividade *TriggerCopy.* Pode definir o *loteCount* para executar vários trabalhos de cópia ADF simultaneamente. Definimos 2 neste modelo.
- **ExecutePipeline** executa *copyFolderPartitionFromS3* pipeline. A razão pela qual criamos outro oleoduto para fazer cada trabalho de cópia copiar uma partição é porque vai torná-lo fácil de refazer o trabalho de cópia falhado para recarregar essa divisória específica novamente a partir de AWS S3. Todos os outros trabalhos de cópia que carregam outras divisórias não serão afetados.
- **Copia** cada divisória de AWS S3 a Azure Data Lake Storage Gen2.
- **SqlServerStoredProcedure** atualiza o estado da cópia de cada divisória na tabela de controlo.

O modelo contém dois parâmetros:
- **AWS_S3_bucketName** é o seu nome de balde no AWS S3 de onde pretende migrar dados. Se quiser migrar dados de vários baldes em AWS S3, pode adicionar mais uma coluna na sua tabela de controlo externo para armazenar o nome do balde para cada partição e também atualizar o seu pipeline para obter dados dessa coluna em conformidade.
- **Azure_Storage_fileSystem** é o nome do seu ficheiroSystem no Azure Data Lake Storage Gen2, para onde pretende migrar dados para.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Para que o modelo copie ficheiros alterados apenas de Amazon S3 para Azure Data Lake Storage Gen2

Este modelo *(nome de modelo: copiar dados delta de AWS S3 a Azure Data Lake Storage Gen2*) utiliza LastModifiedTime de cada ficheiro para copiar os ficheiros novos ou atualizados apenas de AWS S3 a Azure. Esteja ciente se os seus ficheiros ou pastas já foram divididos com informações de cronometragens como parte do ficheiro ou nome da pasta no AWS S3 (por exemplo, /yyyy/mm/dd/file.csv), pode ir a este [tutorial](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) para obter a abordagem mais performante para carregar novos ficheiros incrementais. Este modelo pressupõe que tenha escrito uma lista de divisórias numa tabela de controlo externo na Base de Dados Azure SQL. Assim, utilizará uma atividade *de Procura* para recuperar a lista de divisórias da tabela de controlo externo, iterar sobre cada divisória, e fazer com que cada trabalho de cópia ADF copie uma partição de cada vez. Quando cada trabalho de cópia começa a copiar os ficheiros da AWS S3, conta com a propriedade LastModifiedTime para identificar e copiar apenas os ficheiros novos ou atualizados. Uma vez concluído qualquer trabalho de cópia, utiliza a atividade *do Procedimento Armazenado* para atualizar o estado de cópia de cada divisória na tabela de controlo.

O modelo contém sete atividades:
- **A procura** recupera as divisórias de uma mesa de controlo externa. O nome da tabela é *s3_partition_delta_control_table* e a consulta para carregar dados da tabela é *"selecione PartitionPrefix distinto de s3_partition_delta_control_table".*
- **ForEach** obtém a lista de partição da atividade *de Lookup* e itera cada partição para a atividade *TriggerDeltaCopy.* Pode definir o *loteCount* para executar vários trabalhos de cópia ADF simultaneamente. Definimos 2 neste modelo.
- **ExecutePipeline** executa o oleoduto *DeltaCopyFolderPartitionFromS3.* A razão pela qual criamos outro oleoduto para fazer cada trabalho de cópia copiar uma partição é porque vai torná-lo fácil de refazer o trabalho de cópia falhado para recarregar essa divisória específica novamente a partir de AWS S3. Todos os outros trabalhos de cópia que carregam outras divisórias não serão afetados.
- **A Procura** recupera o último tempo de funcionaamento do trabalho de cópia a partir da tabela de controlo externo para que os ficheiros novos ou atualizados possam ser identificados através do LastModifiedTime. O nome da tabela é *s3_partition_delta_control_table* e a consulta para carregar dados da tabela é *"select max (JobRunTime) as LastModifiedTime from s3_partition_delta_control_table where PartitionPrefix = '@{pipeline().parâmetros.prefixStr}' e SuccessOrFailure = 1"*.
- **Copie** ficheiros novos ou alterados apenas para cada partição de AWS S3 a Azure Data Lake Storage Gen2. A propriedade do *ModificadoDatetimeStart* está definida para o último tempo de funciona de trabalho de cópia. A propriedade do *ModedTimeEnd* está definida para o tempo de execução do trabalho de cópia atual. Esteja ciente de que o tempo é aplicado ao fuso horário UTC.
- **SqlServerStoredProcedure** atualiza o estado de cópia de cada divisória e tempo de execução de cópia na tabela de controlo quando tiver sucesso. A coluna de SuccessOrFailure está definida para 1.
- **SqlServerStoredProcedure** atualiza o estado de cópia de cada divisória e tempo de execução de cópia na tabela de controlo quando este falha. A coluna de SuccessOrFailure está definida para 0.

O modelo contém dois parâmetros:
- **AWS_S3_bucketName** é o seu nome de balde no AWS S3 de onde pretende migrar dados. Se quiser migrar dados de vários baldes em AWS S3, pode adicionar mais uma coluna na sua tabela de controlo externo para armazenar o nome do balde para cada partição e também atualizar o seu pipeline para obter dados dessa coluna em conformidade.
- **Azure_Storage_fileSystem** é o nome do seu ficheiroSystem no Azure Data Lake Storage Gen2, para onde pretende migrar dados para.

## <a name="how-to-use-these-two-solution-templates"></a>Como usar estes dois modelos de solução

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Para o modelo migrar dados históricos do Amazon S3 para a Azure Data Lake Storage Gen2 

1. Crie uma tabela de controlo na Base de Dados Azure SQL para armazenar a lista de divisórias de AWS S3. 

    > [!NOTE]
    > O nome da mesa é s3_partition_control_table.
    > O esquema da tabela de controlo é PartitionPrefix e SuccessOrFailure, onde partitionPrefix é a definição de prefixo em S3 para filtrar as pastas e ficheiros no Amazon S3 pelo nome, e SuccessOrFailure é o estado de cópia de cada partição: 0 significa que esta partição não foi copiada para Azure e 1 significa que esta partição foi copiada para Azure com sucesso.
    > Existem 5 divisórias definidas na tabela de controlo e o estado predefinido da cópia de cada divisória é 0.

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. Crie um Procedimento Armazenado na mesma Base de Dados Azure SQL para a tabela de controlo. 

    > [!NOTE]
    > O nome do Procedimento Armazenado é sp_update_partition_success. Será invocado pela atividade de Projeto Deprocedure sqlServerStored no seu oleoduto ADF.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. Vá ao **modelo de Migração de Dados Históricos de AWS S3 a Azure Data Lake Storage Gen2.** Insira as ligações para a sua tabela de controlo externo, AWS S3 como a loja de fontes de dados e Azure Data Lake Storage Gen2 como a loja de destino. Tenha em atenção que o quadro de controlo externo e o procedimento armazenado se referem à mesma ligação.

    ![Screenshot que mostra os dados históricos migratórios de AWS S3 para Azure Data Lake Storage Gen2 modelo.](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Selecione **Utilize este modelo.**

    ![Screenshot que realça o botão Use este modelo.](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. Os 2 oleodutos e 3 conjuntos de dados foram criados, como mostra o exemplo seguinte:

    ![Screenshot que mostra os dois oleodutos e três conjuntos de dados que foram criados usando o modelo.](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Selecione **Debug,** introduza os **parâmetros**e, em seguida, selecione **Terminar**.

    ![Screenshot que mostra onde selecionar Debug e introduzir os parâmetros antes de selecionar Terminar.](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. Vê-se resultados semelhantes ao seguinte exemplo:

    ![Screenshot que mostra os resultados devolvidos.](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Para que o modelo copie ficheiros alterados apenas de Amazon S3 para Azure Data Lake Storage Gen2

1. Crie uma tabela de controlo na Base de Dados Azure SQL para armazenar a lista de divisórias de AWS S3. 

    > [!NOTE]
    > O nome da mesa é s3_partition_delta_control_table.
    > O esquema da mesa de controlo é PartitionPrefix, JobRunTime e SuccessOrFailure, onde a PartitionPrefix é a definição de prefixo em S3 para filtrar as pastas e ficheiros no Amazon S3 pelo nome, JobRunTime é o valor da data quando os trabalhos de cópia são executados, e SuccessOrFailure é o estado de cópia de cada partição: 0 significa que esta partição não foi copiada para Azure e 1 significa que esta partição foi copiada para Azure com sucesso.
    > Existem 5 divisórias definidas na tabela de controlo. O valor predefinido para JobRunTime pode ser o momento em que a migração de dados históricos de uma só vez começa. A atividade de cópia da ADF copiará os ficheiros da AWS S3 que foram modificados pela última vez após esse período. O estado predefinido da cópia de cada divisória é 1.

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. Crie um Procedimento Armazenado na mesma Base de Dados Azure SQL para a tabela de controlo. 

    > [!NOTE]
    > O nome do Procedimento Armazenado é sp_insert_partition_JobRunTime_success. Será invocado pela atividade de Projeto Deprocedure sqlServerStored no seu oleoduto ADF.

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. Aceda aos **dados delta da Cópia de AWS S3 para o modelo Azure Data Lake Storage Gen2.** Insira as ligações para a sua tabela de controlo externo, AWS S3 como a loja de fontes de dados e Azure Data Lake Storage Gen2 como a loja de destino. Tenha em atenção que o quadro de controlo externo e o procedimento armazenado se referem à mesma ligação.

    ![Criar uma nova ligação](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Selecione **Utilize este modelo.**

    ![Utilizar este modelo](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. Os 2 oleodutos e 3 conjuntos de dados foram criados, como mostra o exemplo seguinte:

    ![Reveja o oleoduto](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. Selecione **Debug,** introduza os **parâmetros**e, em seguida, selecione **Terminar**.

    ![Clique em **Debug**](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. Vê-se resultados semelhantes ao seguinte exemplo:

    ![Reveja o resultado](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. Também pode verificar os resultados da tabela de controlo através de uma consulta *"select * from s3_partition_delta_control_table"*, verá a saída semelhante ao seguinte exemplo:

    ![Screenshot que mostra os resultados da tabela de controlo depois de executar a consulta.](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Passos seguintes

- [Copiar ficheiros de vários contentores](solution-template-copy-files-multiple-containers.md)
- [Mover ficheiros](solution-template-move-files.md)