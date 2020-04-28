---
title: Utilização de pacotes DAC da Base de Dados SQL e trabalhos stream analytics com borda de base de dados Azure SQL [ Microsoft Docs
description: Saiba mais sobre a utilização de trabalhos de Stream Analytics no SQL Database Edge
keywords: borda de base de dados sql, análise de fluxo, pacote de sql
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74384164"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>Utilização de pacotes DAC da Base de Dados SQL e trabalhos de Streaming Analytics com Borda de Base de Dados SQL

A Pré-visualização do Edge De base de dados Azure SQL é um motor de base de dados relacional otimizado, orientado para implementações ioT e borda. É construído com base nas versões mais recentes do Microsoft SQL Server Database Engine, que fornece capacidades de desempenho, segurança e processamento de consultas líderes na indústria. Juntamente com as principais capacidades de gestão de bases de dados relacionais líderes na indústria do SQL Server, o Azure SQL Database Edge fornece capacidade de streaming incorporada para análise em tempo real e processamento complexo de eventos.

O Azure SQL Database Edge também fornece uma implementação nativa de SqlPackage.exe que lhe permite implementar um pacote DAC de Base de [Dados SQL](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) durante a implementação do SQL Database Edge.

A Azure SQL Database Edge expõe `module twin's desired properties` dois parâmetros opcionais através da opção do módulo IoT Edge:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Campo | Descrição |
|------|-------------|
| SqlPackage | Armazenamento Azure Blob URI para o ficheiro *.zip que contém o pacote DAC da Base de Dados De Base de Dados SQL.
| ASAJobInfo | Armazenamento Azure Blob URI para o trabalho asa edge. Para mais informações, consulte [A Publicação de um trabalho ASA Edge para SQL Database Edge](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Utilização de pacotes DAC de base de dados SQL com Borda de Base de Dados SQL

Para utilizar um pacote DAC de Base de Dados SQL (*.dacpac) com SQL Database Edge, tome estas medidas:

1. Crie ou extraia um pacote DAC de Base de Dados SQL. Consulte [a extração de um DAC a partir de uma base](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) de dados para obter informações sobre como gerar um pacote DAC para uma base de dados existente do Servidor SQL.

2. Feche o *.dacpac e carregue-o para uma conta de armazenamento Azure Blob. Para obter mais informações sobre o upload de ficheiros para o armazenamento do Blob Azure, consulte upload, download e lista de [blobs com o portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Gere uma assinatura de acesso partilhado para o ficheiro zip utilizando o portal Azure. Para mais informações, consulte o [acesso do Delegado com assinaturas de acesso partilhado (SAS)](../storage/common/storage-sas-overview.md).

4. Atualize a configuração do módulo SQL Database Edge para incluir o URI de acesso partilhado para o pacote DAC. Para atualizar o módulo SQL Database Edge, tome estas medidas:

    1. No portal Azure, vá ao seu ioT Hub implantação.

    2. No painel esquerdo, selecione **IoT Edge**.

    3. Na página **IoT Edge,** encontre e selecione a borda IoT onde o módulo SQL Database Edge é implantado.

    4. Na página do **dispositivo IoT Edge Device,** selecione **Módulo de Conjunto**.

    5. Na página **de módulos set,** selecione **Configure** contra o módulo SQL Database Edge.

    6. No painel **ioT Edge Custom Modules,** selecione **set module twin propriedades desejadas**. Atualize as propriedades desejadas `SQLPackage` para incluir o URI para a opção, como mostra o seguinte exemplo.

        > [!NOTE]
        > O SAS URI no JSON seguinte é apenas um exemplo. Substitua o URI pelo URI real da sua implantação.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Selecione **Guardar**.

    8. Na página **de módulos set,** selecione **Next**.

    9. Na página **de módulos set,** selecione **Seguinte** e, em seguida, **Submeta**.

5. Após a atualização do módulo, o ficheiro de pacote SC é descarregado, desapertado e implantado contra a instância SQL Database Edge.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Utilização de trabalhos de streaming com Borda de Base de Dados SQL

O Azure SQL Database Edge tem uma implementação nativa do tempo de execução da análise do fluxo. Esta implementação permite-lhe criar um trabalho de borda azure Stream Analytics e implementar esse trabalho como um trabalho de streaming SQL Database Edge. Para criar um trabalho de borda stream analytics, complete estes passos:

1. Vá ao portal Azure utilizando o [URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)de pré-visualização . Este URL de pré-visualização permite-lhe configurar a saída de Base de Dados SQL para um trabalho de borda stream Analytics.

2. Crie um novo Azure Stream Analytics no trabalho **ioT Edge.** Escolha o ambiente de hospedagem que visa **edge**.

3. Defina uma entrada e saída para o trabalho de Azure Stream Analytics. Cada saída SQL, que vai instalar aqui, está ligada a uma única tabela na base de dados. Se precisar de transmitir dados para várias tabelas, terá de criar várias saídas de Base de Dados SQL. Pode configurar as saídas SQL para apontar para diferentes bases de dados.

    **Entrada**. Escolha o EdgeHub como entrada para o trabalho de borda e forneça a informação sobre recursos.

    **Saída**. Selecione base de dados SQL como saída. Selecione fornecer as definições de base de **dados SQL manualmente**. Forneça os detalhes de configuração para a base de dados e a tabela.

    |Campo      | Descrição |
    |---------------|-------------|
    |Alias de saída | Nome do pseudónimo de saída.|
    |Base de Dados | Nome da base de dados SQL. Tem de ser um nome válido de uma base de dados que exista na instância SQL Database Edge.|
    |Nome do servidor | Nome (ou endereço IP) e detalhes do número da porta para a instância SQL. Para uma implementação sql Database Edge, pode utilizar **tcp:..1433** para o nome do servidor.|
    |Nome de utilizador | Conta de entrada sQL que tem o leitor de dados e o leitor de dados acesso à base de dados que especificou anteriormente.|
    |Palavra-passe | Palavra-passe para a conta de entrada SQL que especificou anteriormente.|
    |Tabela | Nome da tabela que será a saída para o trabalho de streaming.|
    |Divisão Herdada| Permite herdar o esquema de partição do seu passo ou entrada de consulta anterior. Quando esta opção estiver ativada, pode esperar ver melhor a sua entrada quando escreve para uma tabela baseada em disco e tem uma topologia totalmente paralela para o seu trabalho.|
    |Tamanho do lote| O número máximo de registos que são enviados com todas as transações de inserção a granel.|

    Aqui está uma configuração de entrada/saída de amostra:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > Para obter mais informações sobre o adaptador de saída SQL para Azure Stream Analytics, consulte a saída do Azure Stream Analytics para a Base de [Dados Azure SQL](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Defina a consulta de trabalho da ASA para o trabalho de borda. Esta consulta deve utilizar os pseudónimos de entrada/saída definidos como os nomes de entrada e saída na consulta. Para mais informações, consulte a [referência da Linguagem consulta da Análise do Fluxo](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Delineie as definições da conta de armazenamento para o trabalho de borda. A conta de armazenamento é usada como o alvo editorial para o trabalho de borda.

6. Em **Configurar, selecione** **Publicar**e, em seguida, selecione o botão **Publicar.** Guarde o SAS URI para utilização com o módulo SQL Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>Implemente o trabalho de borda stream analytics para SQL Database Edge

Para implementar o trabalho de streaming no módulo SQL Database Edge, atualize a configuração do módulo SQL Database Edge para incluir o SAS URI para o trabalho de streaming a partir do passo anterior. Para atualizar o módulo SQL Database Edge:

1. No portal Azure, vá ao seu ioT Hub implantação.

2. No painel esquerdo, selecione **IoT Edge**.

3. Na página **IoT Edge,** encontre e selecione a borda IoT onde o módulo SQL Database Edge é implantado.

4. Na página do **dispositivo IoT Edge Device,** selecione **Módulo de Conjunto**.

5. Na página **de módulos set,** selecione **Configure** contra o módulo SQL Database Edge.

6. No painel **ioT Edge Custom Modules,** selecione **set module twin propriedades desejadas**. Atualize as propriedades desejadas `ASAJobInfo` para incluir o URI para a opção, como mostra o seguinte exemplo.

    > [!NOTE]
    > O SAS URI no JSON seguinte é apenas um exemplo. Substitua o URI pelo URI real da sua implantação.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Selecione **Guardar**.

8. Na página **de módulos set,** selecione **Next**.

9. Na página **de módulos set,** selecione **Seguinte** e, em seguida, **Submeta**.

10. Após a atualização do módulo, o ficheiro de trabalho de análise de fluxo é descarregado, desapertado e implantado contra a instância SQL Database Edge.

## <a name="next-steps"></a>Passos seguintes

- Para obter preços e disponibilidade, consulte O Edge de Base de [Dados Azure SQL](https://azure.microsoft.com/services/sql-database-edge/).
- Solicite ativar o Edge de Base de Dados Azure SQL para a sua subscrição.
- Para começar, consulte a implantação da Base de [Dados SQL Edge através](deploy-portal.md)do portal Azure .
