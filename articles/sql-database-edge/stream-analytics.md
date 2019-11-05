---
title: Usando o pacote de DAC do banco de dados SQL e trabalhos de Stream Analytics com o Azure SQL Database Edge | Microsoft Docs
description: Saiba mais sobre como usar trabalhos de Stream Analytics na borda do banco de dados SQL
keywords: borda do banco de dados SQL, Stream Analytics, SqlPackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 1b6b3f4e4be9d056bc53fac2eb2f1f3fcd768085
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514995"
---
# <a name="using-sql-database-dac-package-and-stream-analytics-job-with-sql-database-edge"></a>Usando o pacote de DAC do banco de dados SQL e Stream Analytics trabalho com borda do banco de dados SQL

Visualização de borda do banco de dados SQL do Azure é um mecanismo de banco de dados relacional otimizado voltado para implantações de IoT e Edge. Ele se baseia nas versões mais recentes do Mecanismo de Banco de Dados do Microsoft SQL Server, que fornece recursos de desempenho, segurança e processamento de consulta líderes do setor. Juntamente com os recursos de gerenciamento de banco de dados relacional líderes do setor do SQL Server, a borda do banco de dados SQL do Azure fornece recursos de streaming internos para análise em tempo real e processamento de eventos complexos.

A borda do banco de dados SQL do Azure também fornece uma implementação nativa do SqlPackage. exe que permite aos usuários implantar um pacote de [DAC do banco de dados SQL](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) durante a implantação da borda do banco de dados SQL.

A borda do banco de dados SQL do Azure expõe dois parâmetros opcionais por meio da opção de *propriedades desejadas do módulo/s* do módulo IOT Edge.

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
| SQLPackage | URI do armazenamento de BLOBs do Azure para o arquivo *. zip que contém o pacote de DAC do banco de dados SQL.
| ASAJobInfo | URI do armazenamento de BLOBs do Azure para o trabalho do ASA Edge. Para obter mais informações sobre como publicar o trabalho do ASA Edge, consulte [publicando um trabalho do asa Edge para a borda do banco de dados SQL]().

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Usando pacotes de DAC do banco de dados SQL com borda do banco de dados SQL

Para usar um pacote de DAC do banco de dados SQL (*. dacpac) com a borda do banco de dados SQL, siga as etapas mencionadas abaixo.

1. Criar ou extrair um pacote de DAC do banco de dados SQL. Você pode usar os conceitos mencionados na [extração de DAC de um banco de dados existente](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) para gerar um DacPac para um banco de dados SQL existente.

2. Zip o * *. dacpac* e carregar para uma conta de armazenamento de BLOBs do Azure. Para obter mais informações sobre como carregar arquivos no armazenamento de BLOBs do Azure, consulte [carregar, baixar e listar BLOBs com o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Gere uma assinatura SAS para o arquivo zip usando portal do Azure. Para obter mais informações, consulte [delegar acesso com SAS (assinaturas de acesso compartilhado)](../storage/common/storage-sas-overview.md).

4. Atualize a configuração do módulo de borda do banco de dados SQL para incluir o URI de SAS para o pacote de DAC. Para atualizar o módulo de borda do banco de dados SQL

    1. Na portal do Azure, navegue até a implantação do Hub IoT.

    2. No painel do lado esquerdo, clique em **IOT Edge**.

    3. Na página **IOT Edge** , localize e clique no IOT Edge em que o módulo de borda do banco de dados SQL é implantado.

    4. Na página dispositivo *IOT Edge dispositivo* , clique em **Definir módulo**. 

    5. Na página **definir módulos** , clique em *Configurar* no módulo borda do banco de dados SQL. 

    6. No painel **IOT Edge módulos personalizados** , selecione as *propriedades desejadas do set modules* e, em seguida, atualize as propriedades desejadas para incluir o URI para a opção SqlPackage, conforme mostrado no exemplo abaixo. 

        > [!NOTE]
        > O URI de SAS abaixo é apenas para fins ilustrativos. Substitua o URI pelo URI real da sua implantação.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Clique em **Guardar**.

    8. Na página **definir módulos** , clique em *Avançar*.

    9. Na página **definir módulos** , clique em *Avançar* e, em seguida, clique em **Enviar**.

5. Poste a atualização do módulo, o arquivo dacpac é baixado, descompactado e implantado na instância de borda do banco de dados SQL.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Usando trabalhos de streaming com a borda do banco de dados SQL

A borda do banco de dados SQL do Azure tem uma implementação nativa do tempo de execução do Stream Analytics. Isso permite que os usuários criem um trabalho de borda Azure Stream Analytics e implantem esse trabalho como um trabalho de streaming de borda do banco de dados SQL. Para criar um trabalho de borda do Stream Analytics, siga as etapas abaixo.

1. Navegue até o portal do Azure usando a [URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)de visualização. Essa URL de visualização permite que os usuários configurem a saída do banco de dados SQL para um trabalho de borda do Stream Analytics.

2. Crie um novo trabalho de **Azure Stream Analytics no IOT Edge** e escolha o ambiente de Hospedagem de destino de **borda**.

3. Defina *entrada* e *saída* para o trabalho de Azure Stream Analytics. Cada saída do SQL (configurada abaixo) é vinculada a uma única tabela no banco de dados. Se houver a necessidade de transmitir dados para várias tabelas, você precisará criar várias saídas de banco de dado SQL. As saídas do SQL podem ser configuradas para apontar para bancos de dados diferentes.

    *Entrada-escolha EdgeHub como a entrada para o trabalho de borda e preencha as informações do recurso.*

    *Saída – selecione banco de dados SQL como saída, "fornecer configurações do banco de dados SQL manualmente" e forneça os detalhes de configuração para o banco de dados e a tabela.*

    |Campo      | Descrição |
    |---------------|-------------|
    |Alias de saída | Nome do alias de saída.|
    |Base de Dados | Nome do banco de dados SQL. Isso precisa ser um nome de banco de dados válido, que existe na instância de borda do banco de dados SQL.|
    |Nome do servidor | Nome (ou endereço IP) e detalhes do número da porta para a instância do SQL. Para uma implantação de borda de banco de dados SQL, você pode usar **TCP:., 1433** como o nome do servidor.|
    |Nome de utilizador | Conta de logon do SQL que tem o leitor de dados e o acesso do gravador de dados ao banco de dado mencionado acima.|
    |Palavra-passe | Senha para a conta de logon do SQL mencionada acima.|
    |Tabela | Nome da tabela que será saída para o trabalho de streaming.|
    |Herdar particionamento| Essa opção de configuração de saída do SQL permite herdar o esquema de particionamento da entrada ou etapa anterior da consulta. Com isso habilitado, gravando em uma tabela baseada em disco e tendo uma topologia totalmente paralela para seu trabalho, espere ver uma melhor taxa de transferência.|
    |Tamanho do lote| Tamanho do lote é o número máximo de registros que é enviado com cada transação de inserção em massa.|

    Exemplo de configuração de entrada/saída abaixo:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output :
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

6. Em configurar, selecione publicar e clique no botão publicar. Salve a URL SAS para uso com o módulo de borda do banco de dados SQL.

### <a name="deploy-the-stream-analytics-edge-job-to-the-sql-database-edge"></a>Implantar o trabalho de borda do Stream Analytics na borda do banco de dados SQL

Para implantar o trabalho de streaming no módulo de borda do banco de dados SQL, atualize a configuração do módulo de borda do banco de dados SQL para incluir o URI de SAS para o trabalho de streaming da etapa anterior. Para atualizar o módulo de borda do banco de dados SQL

1. Na portal do Azure, navegue até a implantação do Hub IoT.

2. No painel do lado esquerdo, clique em **IOT Edge**.

3. Na página **IOT Edge** , localize e clique no IOT Edge em que o módulo de borda do banco de dados SQL é implantado.

4. Na página dispositivo *IOT Edge dispositivo* , clique em **Definir módulo**. 

5. Na página **definir módulos** , clique em *Configurar* no módulo borda do banco de dados SQL. 

6. No painel **IOT Edge módulos personalizados** , selecione as *propriedades desejadas do set modules* e, em seguida, atualize as propriedades desejadas para incluir o URI da opção ASAJobInfo, conforme mostrado no exemplo abaixo. 

    > [!NOTE]
    > O URI de SAS abaixo é apenas para fins ilustrativos. Substitua o URI pelo URI real da sua implantação.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Clique em **Guardar**.

8. Na página **definir módulos** , clique em *Avançar*.

9. Na página **definir módulos** , clique em *Avançar* e, em seguida, clique em **Enviar**.

10. Poste a atualização do módulo, o arquivo de trabalho do Stream Analytics é baixado, descompactado e implantado na instância de borda do banco de dados SQL.

## <a name="next-steps"></a>Passos seguintes

- Para obter os preços e os detalhes relacionados à disponibilidade, consulte [borda do banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database-edge/).
- Solicitação para habilitar a borda do banco de dados SQL do Azure para sua assinatura.
- Para começar, consulte o seguinte:
  - [Implantar a borda do banco de dados SQL por meio do portal do Azure](deploy-portal.md)
