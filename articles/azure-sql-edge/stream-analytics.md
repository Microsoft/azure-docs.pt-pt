---
title: Utilização de trabalhos de borda de análise de fluxo azure com borda Azure SQL (pré-visualização)
description: Saiba mais sobre a utilização de trabalhos stream analytics em Azure SQL Edge (Pré-visualização)
keywords: SQL Edge, análise de fluxo,
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3b57cb8cae80381a6c2cd88358dd9284ba56c919
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597318"
---
# <a name="using-azure-stream-analytics-jobs-with-sql-edge"></a>Utilização de trabalhos de Analytics Azure Stream com SQL Edge

Azure SQL Edge (Preview) é um motor de base de dados relacional otimizado, orientado para implantações ioT e borda. É construído com base nas versões mais recentes do Microsoft SQL Server Database Engine, que fornece capacidades de desempenho, segurança e processamento de consultas líderes na indústria. Juntamente com as principais capacidades de gestão de bases de dados relacionais líderes na indústria do SQL Server, o Azure SQL Edge fornece capacidade de streaming incorporada para análise em tempo real e processamento complexo de eventos.

O Azure SQL Edge tem uma implementação nativa do tempo de execução da análise do fluxo. Esta implementação permite-lhe criar um trabalho de borda Azure Stream Analytics e implementar esse trabalho como um trabalho de streaming SQL Edge. Os trabalhos de Azure Stream Analytics podem ser implantados para o SQL Edge utilizando o parâmetro ASAJobInfo exposto através `module twin's desired properties` da opção do módulo SQL Edge:

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
| ASAJobInfo | Armazenamento Azure Blob URI para o trabalho asa edge.

## <a name="create-an-azure-stream-analytics-edge-job"></a>Crie um Trabalho de Borda de Análise de Fluxo Azure

1. Aceda ao portal do Azure.

2. Crie um novo Azure Stream Analytics no trabalho **ioT Edge.** Escolha o ambiente de hospedagem que visa **edge**.

3. Defina uma entrada e saída para o trabalho de Azure Stream Analytics. Cada saída SQL, que vai instalar aqui, está ligada a uma única tabela na base de dados. Se precisar de transmitir dados para várias tabelas, terá de criar várias saídas de Base de Dados SQL. Pode configurar as saídas SQL para apontar para diferentes bases de dados.

    **Entrada**. Escolha o EdgeHub como entrada para o trabalho de borda e forneça a informação sobre recursos.

    **Saída**. Selecione base de dados SQL como saída. Selecione fornecer as definições de base de **dados SQL manualmente**. Forneça os detalhes de configuração para a base de dados e a tabela.

    |Campo      | Descrição |
    |---------------|-------------|
    |Alias de saída | Nome do pseudónimo de saída.|
    |Base de Dados | Nome da base de dados SQL. Tem de ser um nome válido de uma base de dados que exista na instância SQL Edge.|
    |Nome do servidor | Nome (ou endereço IP) e detalhes do número da porta para a instância SQL. Para uma implementação SQL Edge, pode utilizar **tcp:..1433** para o nome do servidor.|
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

6. Em **Configurar, selecione** **Publicar**e, em seguida, selecione o botão **Publicar.** Guarde o SAS URI para utilização com o módulo SQL Edge.

## <a name="deploy-azure-stream-analytics-edge-job-to-sql-edge"></a>Implemente o trabalho de Azure Stream Analytics Edge para sQL Edge

Para implementar o trabalho de streaming para o módulo SQL Edge, atualize a configuração do módulo SQL Edge para incluir o SAS URI para o trabalho de streaming a partir do passo anterior. Para atualizar o módulo SQL Edge:

1. No portal Azure, vá ao seu ioT Hub implantação.

2. No painel esquerdo, selecione **IoT Edge**.

3. Na página **IoT Edge,** encontre e selecione o IoT Edge onde o módulo SQL Edge está implantado.

4. Na página do **dispositivo IoT Edge Device,** selecione **Módulo de Conjunto**.

5. Na página **de módulos set,** selecione **Configure** contra o módulo SQL Edge.

6. No painel **ioT Edge Custom Modules,** selecione **set module twin propriedades desejadas**. Atualize as propriedades desejadas para incluir o URI para a `ASAJobInfo` opção, como mostra o seguinte exemplo.

    > [!NOTE]
    > O SAS URI no JSON seguinte é apenas um exemplo. Substitua o URI pelo URI real da sua implantação.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "<<<SAS URL For the published ASA Edge Job>>>>"
            }
        }
    ```

7. Selecione **Guardar**.

8. Na página **de módulos set,** selecione **Next**.

9. Na página **de módulos set,** selecione **Seguinte** e, em seguida, **Submeta**.

10. Após a atualização do módulo, o ficheiro de trabalho de análise de fluxo é descarregado, desapertado e implantado contra a instância SQL Edge.

## <a name="next-steps"></a>Passos seguintes

- [Implementar o SQL Edge através](deploy-portal.md)do portal Azure .

- [Dados de fluxo](stream-data.md)

- [Machine learning e IA com ONNX em SQL Edge (Pré-visualização)](onnx-overview.md)
