---
title: Dados do SQL Server para a Base de Dados SQL com Azure Data Factory - Processo de Ciência de Dados de Equipas
description: Crie um oleoduto ADF que compõe duas atividades de migração de dados que, em conjunto, movimentam dados diariamente entre bases de dados no local e na nuvem.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e477e4bb3b31477f9407e981d4c8da2340411f55
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615742"
---
# <a name="move-data-from-a-sql-server-database-to-sql-database-with-azure-data-factory"></a>Mover dados de uma base de dados do SQL Server para a Base de Dados SQL com Azure Data Factory

Este artigo mostra como mover dados de uma base de dados do SQL Server para Azure SQL Database via Azure Blob Storage usando a Azure Data Factory (ADF): este método é uma abordagem de legado suportada que tem as vantagens de uma cópia de encenação replicada, embora [sugerimos olhar para a nossa página de migração de dados para as opções mais recentes.](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1)

Para obter uma tabela que ressumo várias opções para mover dados para uma Base de Dados Azure SQL, consulte [mover dados para uma Base de Dados Azure SQL para aprendizagem automática Azure](move-sql-azure.md).

## <a name="introduction-what-is-adf-and-when-should-it-be-used-to-migrate-data"></a><a name="intro"></a>Introdução: O que é a ADF e quando deve ser usado para migrar dados?
AZure Data Factory é um serviço de integração de dados totalmente gerido baseado na nuvem que orquestra e automatiza o movimento e transformação de dados. O conceito-chave no modelo ADF é o pipeline. Um pipeline é um agrupamento lógico de Atividades, cada uma das quais define as ações a executar nos dados contidos nos Conjuntos de Dados. Os serviços ligados são utilizados para definir as informações necessárias para que a Data Factory se conecte aos recursos de dados.

Com a ADF, os serviços de processamento de dados existentes podem ser compostos em oleodutos de dados altamente disponíveis e geridos na nuvem. Estes oleodutos de dados podem ser programados para ingerir, preparar, transformar, analisar e publicar dados, e a ADF gere e orquestra as complexas dependências de dados e processamento. As soluções podem ser rapidamente construídas e implementadas na nuvem, conectando um número crescente de fontes de dados no local e na nuvem.

Considere usar a ADF:

* quando os dados precisam de ser continuamente migrados num cenário híbrido que acede tanto aos recursos no local como à nuvem
* quando os dados precisam de transformações ou têm lógica de negócio adicionada a ele quando são migrados.

A ADF permite o agendamento e monitorização de postos de trabalho utilizando scripts JSON simples que gerem o movimento de dados numa base periódica. A ADF também tem outras capacidades, como o apoio a operações complexas. Para obter mais informações sobre a ADF, consulte a documentação na [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="the-scenario"></a><a name="scenario"></a>O Cenário
Criamos um oleoduto ADF que compõe duas atividades de migração de dados. Juntos, movimentam dados diariamente entre uma base de dados do SQL Server e a Base de Dados Azure SQL. As duas atividades são:

* Copie os dados de uma base de dados do SQL Server para uma conta de armazenamento Azure Blob
* Copie os dados da conta de armazenamento Azure Blob para a Base de Dados Azure SQL.

> [!NOTE]
> Os passos aqui mostrados foram adaptados do tutorial mais detalhado fornecido pela equipa ADF: [Copiar dados de uma base de dados do SQL Server para Azure Blob](../../data-factory/tutorial-hybrid-copy-portal.md) Referências às secções relevantes desse tópico são fornecidas quando apropriado.
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Pré-requisitos
Este tutorial pressupõe que tem:

* Uma **subscrição do Azure**. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento Azure.** Utilize uma conta de armazenamento Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md). Depois de ter criado a conta de armazenamento, tem de obter a chave de conta utilizada para aceder ao armazenamento. Consulte [as teclas de acesso à conta de armazenamento](../../storage/common/storage-account-keys-manage.md).
* Acesso a uma **Base de Dados Azure SQL**. Se tiver de configurar uma Base de Dados Azure SQL, o tópico ["Começar com a Base de Dados SQL do Microsoft Azure](../../azure-sql/database/single-database-create-quickstart.md) fornece informações sobre como fornecer uma nova instância de uma Base de Dados SQL Azure."
* Instalado e configurado **Azure PowerShell** localmente. Para obter instruções, consulte [como instalar e configurar a Azure PowerShell](/powershell/azure/).

> [!NOTE]
> Este procedimento utiliza o [portal Azure.](https://portal.azure.com/)
>
>

## <a name="upload-the-data-to-your-sql-server-instance"></a><a name="upload-data"></a> Faça o upload dos dados para a sua instância do SQL Server
Usamos o [conjunto de dados do Táxi de NYC](https://chriswhong.com/open-data/foil_nyc_taxi/) para demonstrar o processo de migração. O conjunto de dados do Táxi nyc está disponível, como indicado nesse post, no armazenamento de blob Azure [NYC .](https://www.andresmh.com/nyctaxitrips/) Os dados têm dois ficheiros, o ficheiro trip_data.csv, que contém detalhes da viagem, e o ficheiro trip_far.csv, que contém detalhes da tarifa paga por cada viagem. Uma amostra e descrição destes ficheiros são fornecidas na Descrição do Conjunto de [Datas de Viagens de Táxi de NYC](sql-walkthrough.md#dataset).

Pode adaptar o procedimento fornecido aqui a um conjunto dos seus próprios dados ou seguir os passos descritos utilizando o conjunto de dados do Táxi de NYC. Para enviar o conjunto de dados do NYC Taxi para a sua base de dados do SQL Server, siga o procedimento descrito em [Dados de Importação a Granel para a base de dados do SQL Server](sql-walkthrough.md#dbload).

## <a name="create-an-azure-data-factory"></a><a name="create-adf"></a> Criar uma Fábrica de Dados Azure
As instruções para a criação de uma nova Fábrica de Dados Azure e um grupo de recursos no [portal Azure](https://portal.azure.com/) são fornecidas [Criar uma Fábrica de Dados Azure](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Nomeie o novo *adf instance adfdsp* e nome o grupo de recursos criou *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Instalar e configurar o tempo de integração da fábrica de dados Azure
O Integration Runtime é uma infraestrutura de integração de dados gerida pelo cliente utilizada pela Azure Data Factory para fornecer capacidades de integração de dados em diferentes ambientes de rede. Este tempo de funcionação foi anteriormente chamado de "Data Management Gateway".

Para configurar, [siga as instruções para criar um oleoduto](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)

## <a name="create-linked-services-to-connect-to-the-data-resources"></a><a name="adflinkedservices"></a>Criar serviços ligados para ligar aos recursos de dados
Um serviço ligado define as informações necessárias para que a Azure Data Factory se conecte a um recurso de dados. Dispomos de três recursos neste cenário para os quais são necessários serviços ligados:

1. Servidor SQL no local
2. Armazenamento de Blobs do Azure
3. Base de Dados SQL do Azure

O procedimento passo a passo para a criação de serviços ligados é prestado na [Criação de serviços ligados.](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)


## <a name="define-and-create-tables-to-specify-how-to-access-the-datasets"></a><a name="adf-tables"></a>Definir e criar tabelas para especificar como aceder aos conjuntos de dados
Criar tabelas que especifiquem a estrutura, localização e disponibilidade dos conjuntos de dados com os seguintes procedimentos baseados em scripts. Os ficheiros JSON são usados para definir as tabelas. Para obter mais informações sobre a estrutura destes ficheiros, consulte [Datasets](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> Deverá executar o `Add-AzureAccount` cmdlet antes de executar o cmdlet [New-AzureDataFactoryTable](/previous-versions/azure/dn835096(v=azure.100)) para confirmar que a subscrição Azure certa está selecionada para a execução do comando. Para documentação deste cmdlet, consulte [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount).
>
>

As definições baseadas em JSON nas tabelas utilizam os seguintes nomes:

* o nome da **tabela** no SQL Server é *nyctaxi_data*
* o nome do **recipiente** na conta de armazenamento Azure Blob é nome *de recipiente*

São necessárias três definições de tabela para este gasoduto ADF:

1. [SqL no local Tabela](#adf-table-onprem-sql)
2. [Mesa Blob](#adf-table-blob-store)
3. [Mesa Azure SQL](#adf-table-azure-sql)

> [!NOTE]
> Estes procedimentos utilizam a Azure PowerShell para definir e criar as atividades da ADF. Mas estas tarefas também podem ser realizadas usando o portal Azure. Para mais detalhes, consulte [Criar conjuntos de dados](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="sql-on-premises-table"></a><a name="adf-table-onprem-sql"></a>SqL no local Tabela
A definição de tabela para o SQL Server é especificada no seguinte ficheiro JSON:

```json
{
    "name": "OnPremSQLTable",
    "properties":
    {
        "location":
        {
            "type": "OnPremisesSqlServerTableLocation",
            "tableName": "nyctaxi_data",
            "linkedServiceName": "adfonpremsql"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1,
            "waitOnExternal":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Os nomes das colunas não foram incluídos aqui. Pode subselectar os nomes das colunas, incluindo-os aqui (para mais detalhes consulte o tópico da [documentação ADF.](../../data-factory/copy-activity-overview.md)

Copie a definição JSON da tabela num ficheiro chamado *onpremtabledef.jsem* ficheiro e guarde-a para um local conhecido (aqui assumiu-se *queC:\temp\onpremtabledef.js).* Criar a tabela em ADF com o seguinte cmdlet Azure PowerShell:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json
```


### <a name="blob-table"></a><a name="adf-table-blob-store"></a>Mesa Blob
A definição para a tabela para a localização do blob de saída está no seguinte (isto mapeia os dados ingeridos das instalações para a bolha de Azure):

```json
{
    "name": "OutputBlobTable",
    "properties":
    {
        "location":
        {
            "type": "AzureBlobLocation",
            "folderPath": "containername",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            },
            "linkedServiceName": "adfds"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Copie a definição JSON da tabela num ficheiro chamado *bloboutputtabledef.jsem* ficheiro e guarde-a para um local conhecido (aqui assumiu-se *queC:\temp\bloboutputtabledef.js).* Criar a tabela em ADF com o seguinte cmdlet Azure PowerShell:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json
```

### <a name="sql-azure-table"></a><a name="adf-table-azure-sql"></a>Mesa Azure SQL
A definição para a tabela para a saída SQL Azure está no seguinte (este esquema mapeia os dados provenientes da bolha):

```json
{
    "name": "OutputSQLAzureTable",
    "properties":
    {
        "structure":
        [
            { "name": "column1", "type": "String"},
            { "name": "column2", "type": "String"}
        ],
        "location":
        {
            "type": "AzureSqlTableLocation",
            "tableName": "your_db_name",
            "linkedServiceName": "adfdssqlazure_linked_servicename"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Copie a definição JSON da tabela num ficheiro chamado *AzureSqlTable.jsem* ficheiro e guarde-a para um local conhecido (aqui assumiu-se *queC:\temp\AzureSqlTable.js).* Criar a tabela em ADF com o seguinte cmdlet Azure PowerShell:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json
```


## <a name="define-and-create-the-pipeline"></a><a name="adf-pipeline"></a>Definir e criar o gasoduto
Especifique as atividades que pertencem ao oleoduto e crie o oleoduto com os seguintes procedimentos baseados em scripts. Um ficheiro JSON é utilizado para definir as propriedades do gasoduto.

* O script pressupõe que o nome do **pipeline** é *AMLDSProcessPipeline*.
* Note também que definimos a periodicidade do gasoduto a ser executado diariamente e usamos o tempo de execução padrão para o trabalho (12 am UTC).

> [!NOTE]
> Os seguintes procedimentos utilizam a Azure PowerShell para definir e criar o gasoduto ADF. Mas esta tarefa também pode ser realizada usando o portal Azure. Para mais detalhes, consulte [o pipeline Create](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

Utilizando as definições de tabela fornecidas anteriormente, a definição de gasoduto para o ADF é especificada da seguinte forma:

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has two activities: the first one copies data from SQL Server to Azure Blob, and the second one copies from Azure Blob to Azure Database Table",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from SQL Server to blob",
                "type": "CopyActivity",
                "inputs": [ {"name": "OnPremSQLTable"} ],
                "outputs": [ {"name": "OutputBlobTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from nyctaxi_data"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            },
            {
                "name": "CopyFromBlobtoSQLAzure",
                "description": "Push data to Sql Azure",
                "type": "CopyActivity",
                "inputs": [ {"name": "OutputBlobTable"} ],
                "outputs": [ {"name": "OutputSQLAzureTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "BlobSource"
                    },
                    "sink":
                    {
                        "type": "SqlSink",
                        "WriteBatchTimeout": "00:5:00",
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 2,
                    "timeout": "02:00:00"
                }
            }
        ]
    }
}
```

Copie esta definição JSON do gasoduto num ficheiro chamado *pipelinedef.jsem* ficheiro e guarde-o para um local conhecido (aqui assumido como *C:\temp\pipelinedef.js).* Criar o gasoduto em ADF com o seguinte cmdlet Azure PowerShell:

```azurepowershell
New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json
```


## <a name="start-the-pipeline"></a><a name="adf-pipeline-start"></a>Inicie o Pipeline
O gasoduto pode agora ser executado utilizando o seguinte comando:

```azurepowershell
Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline
```

Os valores dos parâmetros de *data de início* e de fim de *data* devem ser substituídos pelas datas reais entre as quais pretende que o gasoduto seja executado.

Uma vez que o gasoduto seja executado, você deve ser capaz de ver os dados aparecerem no recipiente selecionado para a bolha, um ficheiro por dia.

Não aproveitamos a funcionalidade fornecida pela ADF para canalizar dados de forma incremental. Para obter mais informações sobre como fazê-lo e outras capacidades fornecidas pela ADF, consulte a documentação da [ADF](https://azure.microsoft.com/services/data-factory/).