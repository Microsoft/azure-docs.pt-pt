---
title: Usando pacotes de DAC do banco de dados SQL e Stream Analytics trabalhos com a borda do banco de dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre como usar trabalhos de Stream Analytics na borda do banco de dados SQL
keywords: borda do banco de dados SQL, Stream Analytics, SqlPackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384164"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>Usando pacotes de DAC do banco de dados SQL e Stream Analytics trabalhos com a borda do banco de dados SQL

Visualização de borda do banco de dados SQL do Azure é um mecanismo de banco de dados relacional otimizado voltado para implantações de IoT e Edge. Ele foi criado com base nas versões mais recentes do Mecanismo de Banco de Dados do Microsoft SQL Server, que fornece recursos de desempenho, segurança e processamento de consulta líderes do setor. Juntamente com os recursos de gerenciamento de banco de dados relacional líderes do setor do SQL Server, a borda do banco de dados SQL do Azure fornece recursos de streaming internos para análise em tempo real e processamento de eventos complexos.

A borda do banco de dados SQL do Azure também fornece uma implementação nativa do SqlPackage. exe que permite implantar um pacote de [DAC do banco de dados SQL](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) durante a implantação da borda do banco de dados SQL.

A borda do banco de dados SQL do Azure expõe dois parâmetros opcionais por meio da opção `module twin's desired properties` do módulo IoT Edge:

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
| SqlPackage | URI do armazenamento de BLOBs do Azure para o arquivo *. zip que contém o pacote de DAC do banco de dados SQL.
| ASAJobInfo | URI do armazenamento de BLOBs do Azure para o trabalho do ASA Edge. Para obter mais informações, consulte [publicando um trabalho do asa Edge para a borda do banco de dados SQL](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Usando pacotes de DAC do banco de dados SQL com borda do banco de dados SQL

Para usar um pacote de DAC do banco de dados SQL (*. dacpac) com a borda do banco de dados SQL, execute estas etapas:

1. Criar ou extrair um pacote de DAC do banco de dados SQL. Consulte [extraindo um DAC de um banco de dados](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) para obter informações sobre como gerar um pacote de DAC para um banco de dados SQL Server existente.

2. Compacte o *. dacpac e carregue-o em uma conta de armazenamento de BLOBs do Azure. Para obter mais informações sobre como carregar arquivos no armazenamento de BLOBs do Azure, consulte [carregar, baixar e listar BLOBs com o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Gere uma assinatura de acesso compartilhado para o arquivo zip usando o portal do Azure. Para obter mais informações, consulte [delegar acesso com SAS (assinaturas de acesso compartilhado)](../storage/common/storage-sas-overview.md).

4. Atualize a configuração do módulo de borda do banco de dados SQL para incluir o URI de acesso compartilhado para o pacote de DAC. Para atualizar o módulo de borda do banco de dados SQL, siga estas etapas:

    1. Na portal do Azure, vá para a implantação do Hub IoT.

    2. No painel esquerdo, selecione **IOT Edge**.

    3. Na página **IOT Edge** , localize e selecione o IOT Edge em que o módulo de borda do banco de dados SQL é implantado.

    4. Na página dispositivo **IOT Edge dispositivo** , selecione **Definir módulo**.

    5. Na página **definir módulos** , selecione **Configurar** no módulo de borda do banco de dados SQL.

    6. No painel **IOT Edge módulos personalizados** , selecione **definir propriedades desejadas do módulo**. Atualize as propriedades desejadas para incluir o URI para a opção `SQLPackage`, conforme mostrado no exemplo a seguir.

        > [!NOTE]
        > O URI de SAS no JSON a seguir é apenas um exemplo. Substitua o URI pelo URI real da sua implantação.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Selecione **Guardar**.

    8. Na página **definir módulos** , selecione **Avançar**.

    9. Na página **definir módulos** , selecione **Avançar** e **Enviar**.

5. Após a atualização do módulo, o arquivo do pacote de DAC é baixado, descompactado e implantado na instância de borda do banco de dados SQL.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Usando trabalhos de streaming com a borda do banco de dados SQL

A borda do banco de dados SQL do Azure tem uma implementação nativa do tempo de execução do Stream Analytics. Essa implementação permite que você crie um trabalho de borda Azure Stream Analytics e implante esse trabalho como um trabalho de streaming de borda do banco de dados SQL. Para criar um trabalho de borda Stream Analytics, conclua estas etapas:

1. Vá para o portal do Azure usando a [URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)de visualização. Essa URL de visualização permite que você configure a saída do banco de dados SQL para um trabalho de borda Stream Analytics.

2. Crie um novo trabalho de **Azure Stream Analytics no IOT Edge** . Escolha o ambiente de hospedagem que tem como alvo o **Edge**.

3. Defina uma entrada e uma saída para o trabalho de Azure Stream Analytics. Cada saída do SQL, que você configurará aqui, está vinculada a uma única tabela no banco de dados. Se você precisar transmitir dados para várias tabelas, precisará criar várias saídas de banco de dado SQL. Você pode configurar as saídas do SQL para apontar para bancos de dados diferentes.

    **Entrada**. Escolha EdgeHub como a entrada para o trabalho de borda e forneça as informações do recurso.

    **Saída**. Selecione banco de dados SQL como saída. Selecione **fornecer configurações do banco de dados SQL manualmente**. Forneça os detalhes de configuração para o banco de dados e a tabela.

    |Campo      | Descrição |
    |---------------|-------------|
    |Alias de saída | Nome do alias de saída.|
    |Base de dados | Nome do banco de dados SQL. Ele precisa ser um nome válido de um banco de dados que exista na instância de borda do banco de dados SQL.|
    |Nome do servidor | Nome (ou endereço IP) e detalhes do número da porta para a instância do SQL. Para uma implantação de borda de banco de dados SQL, você pode usar **TCP:., 1433** para o nome do servidor.|
    |Nome de Utilizador | Conta de entrada do SQL que tem o leitor de dados e o acesso ao gravador de dados ao banco de dado que você especificou anteriormente.|
    |Palavra-passe | Senha para a conta de entrada do SQL que você especificou anteriormente.|
    |Tabela | Nome da tabela que será saída para o trabalho de streaming.|
    |Herdar particionamento| Permite herdar o esquema de particionamento da entrada ou etapa anterior da consulta. Quando essa opção está habilitada, você pode esperar obter uma melhor taxa de transferência ao gravar em uma tabela baseada em disco e ter uma topologia totalmente paralela para seu trabalho.|
    |Tamanho do lote| O número máximo de registros que é enviado com cada transação de inserção em massa.|

    Aqui está um exemplo de configuração de entrada/saída:

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
    > Para obter mais informações sobre o adaptador de saída do SQL para Azure Stream Analytics, consulte [Azure Stream Analytics saída para o banco de dados SQL do Azure](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Defina a consulta do trabalho ASA para o trabalho de borda. Essa consulta deve usar os aliases de entrada/saída definidos como os nomes de entrada e saída na consulta. Para obter mais informações, consulte [Stream Analytics referência de linguagem de consulta](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Defina as configurações da conta de armazenamento para o trabalho de borda. A conta de armazenamento é usada como o destino de publicação para o trabalho de borda.

6. Em **Configurar**, selecione **publicar**e, em seguida, selecione o botão **publicar** . Salve o URI de SAS para uso com o módulo de borda do banco de dados SQL.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>Implantar o trabalho de borda do Stream Analytics na borda do banco de dados SQL

Para implantar o trabalho de streaming no módulo de borda do banco de dados SQL, atualize a configuração do módulo de borda do banco de dados SQL para incluir o URI de SAS para o trabalho de streaming da etapa anterior. Para atualizar o módulo de borda do banco de dados SQL:

1. Na portal do Azure, vá para a implantação do Hub IoT.

2. No painel esquerdo, selecione **IOT Edge**.

3. Na página **IOT Edge** , localize e selecione o IOT Edge em que o módulo de borda do banco de dados SQL é implantado.

4. Na página dispositivo **IOT Edge dispositivo** , selecione **Definir módulo**.

5. Na página **definir módulos** , selecione **Configurar** no módulo de borda do banco de dados SQL.

6. No painel **IOT Edge módulos personalizados** , selecione **definir propriedades desejadas do módulo**. Atualize as propriedades desejadas para incluir o URI para a opção `ASAJobInfo`, conforme mostrado no exemplo a seguir.

    > [!NOTE]
    > O URI de SAS no JSON a seguir é apenas um exemplo. Substitua o URI pelo URI real da sua implantação.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Selecione **Guardar**.

8. Na página **definir módulos** , selecione **Avançar**.

9. Na página **definir módulos** , selecione **Avançar** e **Enviar**.

10. Após a atualização do módulo, o arquivo de trabalho do Stream Analytics é baixado, descompactado e implantado na instância de borda do banco de dados SQL.

## <a name="next-steps"></a>Passos Seguintes

- Para obter detalhes sobre preços e disponibilidade, consulte [borda do banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database-edge/).
- Solicitação que habilita a borda do banco de dados SQL do Azure para sua assinatura.
- Para começar, consulte [implantar o Edge do banco de dados SQL por meio de portal do Azure](deploy-portal.md).
