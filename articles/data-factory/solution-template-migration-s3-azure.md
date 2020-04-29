---
title: Migrar dados da Amazon S3 para o Azure Data Lake Storage Gen2
description: Aprenda a usar um modelo de solução para migrar dados da Amazon S3 usando uma tabela de controlo externo para armazenar uma lista de partições no AWS S3 com a Azure Data Factory.
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
ms.openlocfilehash: 23d799f84cb3ac3ca911a5669041b0a25394a7ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414766"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Migrar dados da Amazon S3 para o Azure Data Lake Storage Gen2

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Use os modelos para migrar petabytes de dados que consistem em centenas de milhões de ficheiros da Amazon S3 para o Azure Data Lake Storage Gen2. 

 > [!NOTE]
 > Se pretender copiar pequenos volumes de dados da AWS S3 para o Azure (por exemplo, menos de 10 TB), é mais eficiente e fácil de utilizar a ferramenta de dados de cópia de cópia de dados da fábrica de [dados do Azure](copy-data-tool.md)Data . O modelo descrito neste artigo é mais do que o que você precisa.

## <a name="about-the-solution-templates"></a>Sobre os modelos de solução

A partilha de dados é recomendada especialmente quando migra mais de 10 TB de dados. Para dividir os dados, aproveite a definição de 'prefixo' para filtrar as pastas e ficheiros no Amazon S3 pelo nome, e em seguida, cada trabalho de cópia ADF pode copiar uma partição de cada vez. Pode executar vários trabalhos de cópia ADF simultaneamente para uma melhor utilização.

A migração de dados normalmente requer uma migração de dados históricos única sincronizando periodicamente as mudanças de AWS S3 para Azure. Há dois modelos abaixo, onde um modelo cobre a migração de dados históricos uma vez e outro modelo cobre sincronizar as mudanças de AWS S3 para Azure.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Para o modelo de migrar dados históricos da Amazon S3 para Azure Data Lake Storage Gen2

Este modelo *(nome do modelo: migrar dados históricos de AWS S3 para Azure Data Lake Storage Gen2*) assume que escreveu uma lista de partições numa tabela de controlo externo na Base de Dados Azure SQL. Assim, utilizará uma atividade de *lookup* para recuperar a lista de divisórias da tabela de controlo externo, iterar sobre cada divisória, e fazer com que cada cópia da ADF copie uma partição de cada vez. Uma vez concluída qualquer cópia, utiliza a atividade *do Procedimento Armazenado* para atualizar o estado de cópia de cada partição na tabela de controlo.

O modelo contém cinco atividades:
- **A procura** recupera as divisórias que não foram copiadas para o Azure Data Lake Storage Gen2 de uma mesa de controlo externa. O nome da tabela é *s3_partition_control_table* e a consulta para carregar dados da tabela é *"SELECT PartitionPrefix FROM s3_partition_control_table Where SuccessOrFailure = 0"*.
- **ForEach** obtém a lista de partição da atividade *Lookup* e itera cada partição para a atividade *TriggerCopy.* Pode definir o *loteCount* para executar vários trabalhos de cópia ADF simultaneamente. Definimos 2 neste modelo.
- **ExecutePipeline** executa o pipeline *CopyFolderPartitionFromS3.* A razão pela qual criamos outro pipeline para fazer de cada cópia de cópia uma partição é porque vai facilitar a reexecução do trabalho de cópia falhado para recarregar essa partição específica novamente da AWS S3. Todos os outros trabalhos de cópia que carregam outras divisórias não serão afetados.
- **Copie** cópias de cada partição de AWS S3 para Azure Data Lake Storage Gen2.
- **SqlServerStoredProcedure** atualizar o estado de copiar cada partição na tabela de controlo.

O modelo contém dois parâmetros:
- **AWS_S3_bucketName** é o seu nome de balde no AWS S3 de onde pretende migrar dados. Se pretender migrar dados de vários baldes no AWS S3, pode adicionar mais uma coluna na sua tabela de controlo externo para armazenar o nome do balde para cada partição e também atualizar o seu pipeline para recuperar dados dessa coluna em conformidade.
- **Azure_Storage_fileSystem** é o nome do sistema de ficheiros no Azure Data Lake Storage Gen2 para onde pretende migrar dados.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Para o modelo copiar ficheiros alterados apenas de Amazon S3 para Azure Data Lake Storage Gen2

Este modelo (nome de*modelo: copiar dados delta de AWS S3 para Azure Data Lake Storage Gen2*) utiliza o LastModificadoTime de cada ficheiro para copiar os ficheiros novos ou atualizados apenas de AWS S3 para Azure. Esteja ciente de que os seus ficheiros ou pastas já foram divididos com informações de timeslice como parte do nome do ficheiro ou da pasta no AWS S3 (por exemplo, /yyyy/mm/dd/file.csv), pode ir a este [tutorial](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) para obter a abordagem mais performante para carregamento incremental de novos ficheiros. Este modelo pressupõe que tenha escrito uma lista de partição numa tabela de controlo externo na Base de Dados Azure SQL. Assim, utilizará uma atividade de *lookup* para recuperar a lista de divisórias da tabela de controlo externo, iterar sobre cada divisória, e fazer com que cada cópia da ADF copie uma partição de cada vez. Quando cada trabalho de cópia começa a copiar os ficheiros da AWS S3, conta com a propriedade LastModifiedTime para identificar e copiar apenas os ficheiros novos ou atualizados. Uma vez concluída qualquer cópia, utiliza a atividade *do Procedimento Armazenado* para atualizar o estado de cópia de cada partição na tabela de controlo.

O modelo contém sete atividades:
- **A procura** recupera as divisórias de uma mesa de controlo externa. O nome da tabela é *s3_partition_delta_control_table* e a consulta para carregar dados da tabela é *"selecione PartitionPrefix distinto de s3_partition_delta_control_table".*
- **ForEach** obtém a lista de partições da atividade *Lookup* e itera cada partição para a atividade *TriggerDeltaCopy.* Pode definir o *loteCount* para executar vários trabalhos de cópia ADF simultaneamente. Definimos 2 neste modelo.
- **ExecutePipeline** executa o pipeline *DeltaCopyFolderPartitionFromS3.* A razão pela qual criamos outro pipeline para fazer de cada cópia de cópia uma partição é porque vai facilitar a reexecução do trabalho de cópia falhado para recarregar essa partição específica novamente da AWS S3. Todos os outros trabalhos de cópia que carregam outras divisórias não serão afetados.
- **A lookup** recupera o último tempo de execução de trabalho de cópia a partir da tabela de controlo externo para que os novos ficheiros ou atualizados possam ser identificados através do LastModifiedTime. O nome da tabela é *s3_partition_delta_control_table* e a consulta para carregar os dados da tabela é *"select max (JobRunTime) como LastModifiedTime from s3_partition_delta_control_table where PartitionPrefix = '@{pipeline().parâmetros.prefixStr}' e SuccessOrFailure = 1"*.
- **Copie** cópias de ficheiros novos ou alterados apenas para cada partição de AWS S3 para Azure Data Lake Storage Gen2. A propriedade de *DatetimeStart modificado* está definida para o último tempo de execução de trabalho de cópia. A propriedade de *DatetimeEnd modificado* está definida para o tempo de execução do trabalho de cópia atual. Tenha em atenção que o tempo é aplicado no fuso horário UTC.
- **SqlServerStoredProcedure** atualiza o estado de copiar cada divisória e copiar tempo de execução na tabela de controlo quando for bem sucedido. A coluna de SuccessOrFailure está definida para 1.
- **SqlServerStoredProcedure** atualiza o estado de copiar cada divisória e copiar tempo de execução na tabela de controlo quando falha. A coluna de SuccessOrFailure está definida para 0.

O modelo contém dois parâmetros:
- **AWS_S3_bucketName** é o seu nome de balde no AWS S3 de onde pretende migrar dados. Se pretender migrar dados de vários baldes no AWS S3, pode adicionar mais uma coluna na sua tabela de controlo externo para armazenar o nome do balde para cada partição e também atualizar o seu pipeline para recuperar dados dessa coluna em conformidade.
- **Azure_Storage_fileSystem** é o nome do sistema de ficheiros no Azure Data Lake Storage Gen2 para onde pretende migrar dados.

## <a name="how-to-use-these-two-solution-templates"></a>Como usar estes dois modelos de solução

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Para o modelo de migrar dados históricos da Amazon S3 para Azure Data Lake Storage Gen2 

1. Crie uma tabela de controlo na Base de Dados Azure SQL para armazenar a lista de partições do AWS S3. 

    > [!NOTE]
    > O nome da mesa é s3_partition_control_table.
    > O esquema da tabela de controlo é PartitionPrefix e SuccessOrFailure, onde partitionPrefix é a definição prefixo em S3 para filtrar as pastas e ficheiros na Amazon S3 pelo nome, e SuccessOrFailure é o estado de copiar cada partição: 0 significa que esta partição não foi copiada para Azure e 1 significa que esta partição foi copiada para Azure com sucesso.
    > Existem 5 divisórias definidas na tabela de controlo e o estado padrão de copiar cada divisória é 0.

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

2. Crie um procedimento armazenado na mesma base de dados Azure SQL para tabela de controlo. 

    > [!NOTE]
    > O nome do Procedimento Armazenado é sp_update_partition_success. Será invocado pela atividade do SqlServerStoredProcedure no seu pipeline ADF.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. Vá ao modelo de Gen2 de Armazenamento de Lago de **Dados Da AWS s3 para Azure Data Lake.** Insera as ligações à sua tabela de controlo externo, AWS S3 como a loja de fonte de dados e o Azure Data Lake Storage Gen2 como loja de destino. Esteja ciente de que a tabela de controlo externo e o procedimento armazenado são de referência à mesma ligação.

    ![Criar uma nova ligação](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Selecione **Utilize este modelo**.

    ![Utilizar este modelo](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. Você vê os 2 oleodutos e 3 conjuntos de dados foram criados, como mostra o seguinte exemplo:

    ![Reveja o oleoduto](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Selecione **Debug,** introduza os **Parâmetros**e, em seguida, selecione **Terminar**.

    ![Clique em **Debug**](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. Vê resultados semelhantes aos seguintes exemplos:

    ![Reveja o resultado](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Para o modelo copiar ficheiros alterados apenas de Amazon S3 para Azure Data Lake Storage Gen2

1. Crie uma tabela de controlo na Base de Dados Azure SQL para armazenar a lista de partições do AWS S3. 

    > [!NOTE]
    > O nome da mesa é s3_partition_delta_control_table.
    > O esquema da tabela de controlo é PartitionPrefix, JobRunTime e SuccessOrFailure, onde partitionPrefix é a definição de prefixo em S3 para filtrar as pastas e ficheiros na Amazon S3 pelo nome, JobRunTime é o valor de data em que os trabalhos de cópia são executados, e SuccessOrFailure é o estado de copiar cada partição: 0 significa que esta partição não foi copiada para Azure e 1 significa que esta partição foi copiada para azure com sucesso.
    > Existem 5 divisórias definidas na tabela de controlo. O valor padrão para jobRunTime pode ser o momento em que a migração de dados históricos começa. A atividade de cópia ADF copiará os ficheiros da AWS S3 que foram modificados pela última vez após esse período. O estado predefinido de copiar cada partição é 1.

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

2. Crie um procedimento armazenado na mesma base de dados Azure SQL para tabela de controlo. 

    > [!NOTE]
    > O nome do Procedimento Armazenado é sp_insert_partition_JobRunTime_success. Será invocado pela atividade do SqlServerStoredProcedure no seu pipeline ADF.

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


3. Vá aos **dados delta da Cópia de AWS S3 para o** modelo de Armazenamento de Lago de Dados Azure Gen2. Insera as ligações à sua tabela de controlo externo, AWS S3 como a loja de fonte de dados e o Azure Data Lake Storage Gen2 como loja de destino. Esteja ciente de que a tabela de controlo externo e o procedimento armazenado são de referência à mesma ligação.

    ![Criar uma nova ligação](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Selecione **Utilize este modelo**.

    ![Utilizar este modelo](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. Você vê os 2 oleodutos e 3 conjuntos de dados foram criados, como mostra o seguinte exemplo:

    ![Reveja o oleoduto](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. Selecione **Debug,** introduza os **Parâmetros**e, em seguida, selecione **Terminar**.

    ![Clique em **Debug**](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. Vê resultados semelhantes aos seguintes exemplos:

    ![Reveja o resultado](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. Também pode verificar os resultados da tabela de controlo por uma consulta *"select * from s3_partition_delta_control_table",* verá a saída semelhante ao seguinte exemplo:

    ![Reveja o resultado](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Passos seguintes

- [Copiar ficheiros de vários contentores](solution-template-copy-files-multiple-containers.md)
- [Mover ficheiros](solution-template-move-files.md)