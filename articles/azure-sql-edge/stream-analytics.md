---
title: Utilizando trabalhos Azure Stream Analytics Edge com Azure SQL Edge (Pré-visualização)
description: Saiba mais sobre a utilização de trabalhos stream analytics em Azure SQL Edge (Preview)
keywords: SQL Edge, análise de fluxo,
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e323c56f22c3a7d32bda5afe03a3462d3263f0d0
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85254077"
---
# <a name="using-azure-stream-analytics-jobs-with-sql-edge"></a>Utilizando trabalhos de Azure Stream Analytics com SQL Edge

Azure SQL Edge (Preview) é um motor de base de dados relacional otimizado, orientado para implementações de IoT e bordas. É construído nas versões mais recentes do Microsoft SQL Server Database Engine, que fornece capacidades de processamento de desempenho, segurança e consulta líderes do setor. Juntamente com as capacidades de gestão de bases de dados relacionais líderes no setor do SQL Server, o Azure SQL Edge fornece capacidade de streaming incorporada para análise em tempo real e processamento complexo de eventos.

Azure SQL Edge tem uma implementação nativa do tempo de execução da análise de fluxo. Esta implementação permite-lhe criar um trabalho de borda Azure Stream Analytics e implementar esse trabalho como um trabalho de streaming SQL Edge. Os trabalhos do Azure Stream Analytics podem ser implementados na SQL Edge utilizando o parâmetro ASAJobInfo exposto através `module twin's desired properties` da opção do módulo SQL Edge:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Campo | Description |
|------|-------------|
| SqlPackage | Azure Blob armazenamento URI para o ficheiro *.zip que contém o pacote DAC base de dados SQL.
| ASAJobInfo | Azure Blob armazenamento URI para o trabalho ASA Edge.

## <a name="create-an-azure-stream-analytics-edge-job"></a>Criar um trabalho de borda azure stream analytics

1. Aceda ao portal do Azure.

2. Crie um novo **Azure Stream Analytics no trabalho IoT Edge.** Escolha o ambiente de hospedagem que visa **edge**.

3. Defina uma entrada e saída para o trabalho Azure Stream Analytics. Cada saída SQL, que irá instalar aqui, está ligada a uma única tabela na base de dados. Se precisar de transmitir dados para várias tabelas, terá de criar várias saídas de Base de Dados SQL. Pode configurar as saídas SQL para apontar para diferentes bases de dados.

    **Entrada**. Escolha o EdgeHub como entrada para o trabalho de borda e forneça a informação de recursos.

    **Saída**. Selecione a Base de Dados SQL como saída. **Selecione Fornecer manualmente as definições da base de dados SQL**. Forneça os detalhes de configuração para a base de dados e tabela.

    |Campo      | Description |
    |---------------|-------------|
    |Alias de saída | Nome do pseudónimo de saída.|
    |Base de Dados | O nome da base de dados. Tem de ser um nome válido de uma base de dados que exista no caso SQL Edge.|
    |Nome do servidor | Nome (ou endereço IP) e detalhes do número de porta para a instância SQL. Para uma implementação SQL Edge, pode utilizar **o tcp:.1433** para o nome do servidor.|
    |Nome de utilizador | Conta de acesso SQL que tem o leitor de dados e o data writer acesso à base de dados que especificou anteriormente.|
    |Palavra-passe | Palavra-passe para a conta de entrada SQL que especificou anteriormente.|
    |Tabela | Nome da tabela que será a saída para o trabalho de streaming.|
    |Herança herdeira| Permite herdar o esquema de partição do seu passo ou entrada de consulta anterior. Quando esta opção está ativada, pode esperar ver melhor produção quando escrever para uma mesa baseada em disco e ter uma topologia totalmente paralela para o seu trabalho.|
    |Tamanho do lote| O número máximo de registos enviados com cada transação de inserção a granel.|

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
    > Para obter mais informações sobre o adaptador de saída SQL para Azure Stream Analytics, consulte [a saída Azure Stream Analytics para a Base de Dados Azure SQL](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Defina a consulta de trabalho da ASA para o trabalho de borda. Esta consulta deve utilizar os pseudónimos de entrada/saída definidos como os nomes de entrada e saída na consulta. Para obter mais informações, consulte [a referência para a linguagem da consulta stream Analytics.](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

5. Defina as definições da conta de armazenamento para o trabalho de borda. A conta de armazenamento é usada como o alvo de publicação para o trabalho de borda.

6. Em **Configuração**, **selecione Publicar**e, em seguida, selecione o botão **Publicar.** Guarde o SAS URI para utilização com o módulo SQL Edge.

## <a name="deploy-azure-stream-analytics-edge-job-to-sql-edge"></a>Implementar o trabalho de Azure Stream Analytics Edge para SQL Edge

Para implementar o trabalho de streaming no módulo SQL Edge, atualize a configuração do módulo SQL Edge para incluir o SAS URI para o trabalho de streaming a partir do passo anterior. Para atualizar o módulo SQL Edge:

1. No portal Azure, vá para a sua implantação IoT Hub.

2. No painel esquerdo, selecione **IoT Edge**.

3. Na página **IoT Edge,** encontre e selecione o IoT Edge onde o módulo SQL Edge está implantado.

4. Na página do **dispositivo IoT Edge Device,** selecione **O Módulo de Conjunto**.

5. Na página **de módulos set,** selecione **Configurar** contra o módulo SQL Edge.

6. No painel **IoT Edge Custom Modules,** selecione **Definir as propriedades desejadas do módulo Twin**. Atualize as propriedades desejadas para incluir o URI para a `ASAJobInfo` opção, como mostra o exemplo seguinte.

    > [!NOTE]
    > O SAS URI no seguinte JSON é apenas um exemplo. Substitua o URI pelo URI real da sua implantação.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "<<<SAS URL For the published ASA Edge Job>>>>"
            }
        }
    ```

7. Selecione **Guardar**.

8. Na página **de módulos set,** selecione **Seguinte**.

9. Na página **de módulos set,** selecione **Seguinte** e, em seguida, **Submeta.**

10. Após a atualização do módulo, o ficheiro de trabalho de análise de streaming é descarregado, desapertado e implantado contra a instância SQL Edge.

## <a name="next-steps"></a>Passos seguintes

- [Implementar sql edge através do portal Azure](deploy-portal.md).

- [Dados de Fluxo](stream-data.md)

- [Machine learning e IA com ONNX em SQL Edge (Pré-visualização)](onnx-overview.md)
