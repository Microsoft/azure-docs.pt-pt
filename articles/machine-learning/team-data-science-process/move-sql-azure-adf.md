---
title: Dados do SQL Server para SQL Azure com o Azure Data Factory - Team Data Science Process
description: Configure um pipeline do ADF que compõe a duas atividades de migração de dados que movem em conjunto os dados numa base diária entre bases de dados no local e na cloud.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 85edaeab67c7b5164dabbc817429e2ab82f5ea11
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56864673"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Mover dados de um servidor SQL no local para o SQL Azure com o Azure Data Factory

Este artigo mostra como mover dados de uma base de dados do servidor de SQL no local para uma base de dados do SQL Azure através do armazenamento de Blobs do Azure com o Azure Data Factory (ADF).

Para uma tabela que resume as várias opções para mover dados para uma base de dados do SQL do Azure, consulte [mover dados para uma base de dados do SQL do Azure para o Azure Machine Learning](move-sql-azure.md).

## <a name="intro"></a>Introdução: O que é o ADF e quando deve, ser utilizado para migrar os dados?
O Azure Data Factory é um serviço de integração de dados totalmente gerido com base na cloud que orquestra e automatiza o movimento e transformação de dados. O conceito-chave no modelo do ADF é o pipeline. Os pipelines são agrupamentos lógicos de atividades, cada um deles define as ações a efetuar nos dados contidos em conjuntos de dados. Serviços ligados são utilizados para definir as informações necessárias para o Data Factory se ligue aos recursos de dados.

Com o ADF, serviços de processamento de dados existentes podem ser compostos em pipelines de dados que são de elevada disponibilidade e gerido na cloud. Esses pipelines de dados podem ser agendados para ingerir, preparar, transformar, analisar e publicar os dados e o ADF gere e orquestra as dependências de processamento e dados complexos. Soluções podem ser criadas e implementadas na cloud, ligar um número cada vez maior de locais rapidamente e origens de dados na cloud.

Considere a utilização do ADF:

* Quando os dados têm de ser migrados continuamente num cenário híbrido que acede ao ambos no local e recursos na cloud
* Quando os dados são transacionados ou tem de ser modificado ou se tiver adicionada a ele quando a ser migradas de lógica de negócios.

ADF permite o agendamento e monitorização de tarefas utilizando scripts JSON simples que gerem o movimento de dados periodicamente. ADF tem também outras capacidades como o suporte para operações complexas. Para obter mais informações sobre o ADF, consulte a documentação em [do Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>O cenário
Configuramos um pipeline do ADF que compõe a duas atividades de migração de dados. Juntos eles mover dados numa base diária entre uma base de dados do SQL no local e uma base de dados do SQL do Azure na cloud. As duas atividades são:

* copiar dados de uma base de dados do SQL Server no local para uma conta de armazenamento de Blobs do Azure
* copiar dados da conta de armazenamento de Blobs do Azure para uma base de dados do SQL do Azure.

> [!NOTE]
> As etapas mostradas aqui tem sido adaptadas a partir do tutorial mais detalhado fornecido pela equipe do ADF: [Copiar dados de uma base de dados do SQL Server no local para o armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) referências para as secções relevantes desse tópico são fornecidas quando apropriado.
>
>

## <a name="prereqs"></a>Pré-requisitos
Este tutorial parte do princípio de que tem:

* Uma **subscrição do Azure**. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento do Azure**. Utilize uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md). Depois de ter criado a conta de armazenamento, tem de obter a chave de conta utilizada para aceder ao armazenamento. Ver [gerir as chaves de acesso de armazenamento](../../storage/common/storage-account-manage.md#access-keys).
* Acesso a uma **base de dados SQL do Azure**. Se tem de configurar uma SQL Database do Azure, o tópico [introdução ao Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) fornece informações sobre como aprovisionar uma nova instância de uma base de dados do SQL do Azure.
* Instalou e configurou **do Azure PowerShell** localmente. Para obter instruções, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Este procedimento utiliza a [portal do Azure](https://portal.azure.com/).
>
>

## <a name="upload-data"></a> Carregar os dados para o SQL Server no local
Vamos utilizar o [conjunto de dados de táxis de NYC](http://chriswhong.com/open-data/foil_nyc_taxi/) para demonstrar o processo de migração. O conjunto de dados de táxis de NYC está disponível, conforme observado nessa mensagem, no armazenamento de Blobs do Azure [dados de táxis de NYC](http://www.andresmh.com/nyctaxitrips/). Os dados têm dois arquivos, o ficheiro de trip_data.csv, que contém os detalhes de viagem, e o ficheiro de trip_far.csv, que contém detalhes de Europeia pago para cada viagem. Um exemplo e uma descrição destes ficheiros são fornecidos na [descrição de conjunto de dados de viagens de táxis de NYC](sql-walkthrough.md#dataset).

Pode adaptar o procedimento aqui apresentado para um conjunto de seus próprios dados ou siga os passos, conforme descrito usando o conjunto de dados de táxis de NYC. Para carregar o conjunto de dados de táxis de NYC para a base de dados do SQL Server no local, siga o procedimento descrito em [dados de importação em massa na base de dados do SQL Server](sql-walkthrough.md#dbload). Estas instruções são para um SQL Server numa máquina Virtual do Azure, mas o procedimento para carregar para o SQL Server no local é o mesmo.

## <a name="create-adf"></a> Criar uma fábrica de dados do Azure
As instruções para criar uma nova fábrica de dados do Azure e o grupo de recursos na [portal do Azure](https://portal.azure.com/) são fornecidos [criar uma fábrica de dados do Azure](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Nomeie a nova instância do ADF *adfdsp* e dê o nome do grupo de recursos que criou *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Instalar e configurar o Azure Data Factory Integration Runtime
O Runtime de integração é uma infraestrutura de integração de dados geridos de cliente utilizada pelo Azure Data Factory para fornecer capacidades de integração de dados em ambientes de rede diferentes. Este tempo de execução era anteriormente denominado "Gateway de gestão de dados".

Para configurar, [siga instrutions para criar um pipeline](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline)

## <a name="adflinkedservices"></a>Criar serviços ligados para ligar aos recursos de dados
Um serviço ligado define as informações necessárias para ligar a um recurso de dados do Azure Data Factory. Temos três recursos neste cenário para o qual os serviços ligados são necessários:

1. SQL Server no local
2. Armazenamento de Blobs do Azure
3. Base de dados SQL do Azure

O procedimento passo a passo para criar serviços ligados é fornecido no [criar serviços ligados](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="adf-tables"></a>Definir e criar tabelas para especificar como acessar os conjuntos de dados
Crie tabelas que especificam a estrutura, a localização e a disponibilidade dos conjuntos de dados com os seguintes procedimentos baseada em script. Ficheiros JSON são utilizados para definir as tabelas. Para obter mais informações sobre a estrutura desses arquivos, consulte [conjuntos de dados](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> Deverá executar o `Add-AzureAccount` cmdlet antes de executar o [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) cmdlet para confirmar que está selecionada a subscrição do Azure certo para a execução de comando. Para obter documentação deste cmdlet, consulte [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

As definições de acesso baseado em JSON nas tabelas utilizam os seguintes nomes:

* o **nome da tabela** no SQL no local é servidor *nyctaxi_data*
* o **nome do contentor** no armazenamento de Blobs do Azure é a conta *containername*

Três definições de tabela são necessários para este pipeline do ADF:

1. [Tabela do SQL no local](#adf-table-onprem-sql)
2. [Tabela de BLOBs](#adf-table-blob-store)
3. [SQL Azure tabela](#adf-table-azure-sql)

> [!NOTE]
> Estes procedimentos utilizam o Azure PowerShell para definir e criar as atividades do ADF. Mas estas tarefas também podem ser realizadas com o portal do Azure. Para obter detalhes, consulte [criar conjuntos de dados](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="adf-table-onprem-sql"></a>Tabela do SQL no local
A definição da tabela para o SQL Server no local é especificada no seguinte ficheiro de JSON:

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

Os nomes das colunas não foram incluídos aqui. Pode selecionar inferiores nos nomes das colunas ao incluí-los aqui (para obter detalhes, veja a [documentação ADF](../../data-factory/copy-activity-overview.md) tópico.

Copiar a definição de JSON da tabela num arquivo chamada *onpremtabledef.json* do ficheiro e guarde-o para um local conhecido (aqui, pressupõe-se para ser *C:\temp\onpremtabledef.json*). Crie a tabela no ADF com o seguinte cmdlet do PowerShell do Azure:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>Tabela de BLOBs
Definição da tabela para a localização do blob de saída está a ser o seguinte (que mapeia os dados ingeridos no local ao blob do Azure):

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

Copiar a definição de JSON da tabela num arquivo chamada *bloboutputtabledef.json* do ficheiro e guarde-o para um local conhecido (aqui, pressupõe-se para ser *C:\temp\bloboutputtabledef.json*). Crie a tabela no ADF com o seguinte cmdlet do PowerShell do Azure:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="adf-table-azure-sql"></a>SQL Azure tabela
Definição da tabela para o SQL Azure de saída é o seguinte (neste esquema mapeia os dados originários do blob):

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

Copiar a definição de JSON da tabela num arquivo chamada *AzureSqlTable.json* do ficheiro e guarde-o para um local conhecido (aqui, pressupõe-se para ser *C:\temp\AzureSqlTable.json*). Crie a tabela no ADF com o seguinte cmdlet do PowerShell do Azure:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="adf-pipeline"></a>Definir e criar o pipeline
Especifique as atividades que pertencem ao pipeline e criar o pipeline com os seguintes procedimentos baseada em script. Um ficheiro JSON é utilizado para definir as propriedades do pipeline.

* O script pressupõe que o **nome do pipeline** é *AMLDSProcessPipeline*.
* Observe também que definimos a periodicidade do pipeline para ser executado numa base diária e utilizar o tempo de execução padrão para a tarefa (12am UTC).

> [!NOTE]
> Os procedimentos seguintes utilizam o Azure PowerShell para definir e criar o pipeline do ADF. Mas essa tarefa também pode ser realizada com o portal do Azure. Para obter detalhes, consulte [criar pipeline](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

Utilizar as definições de tabela fornecidas anteriormente, a definição de pipeline para o ADF é especificada da seguinte forma:

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has one Copy activity that copies data from an on-premises SQL to Azure blob",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from on-premises SQL server to blob",
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

Esta definição de JSON do pipeline num arquivo chamada de cópia *pipelinedef.json* do ficheiro e guarde-o para um local conhecido (aqui, pressupõe-se para ser *C:\temp\pipelinedef.json*). Crie o pipeline no ADF com o seguinte cmdlet do PowerShell do Azure:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>Iniciar o Pipeline
Agora pode ser executado o pipeline com o seguinte comando:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

O *startdate* e *enddate* valores de parâmetro precisam ser substituídos com as datas reais entre os quais pretende que a execução do pipeline.

Assim que o pipeline é executado, deverá conseguir ver os dados aparecem no contentor selecionado para o blob, um ficheiro por dia.

Tenha em atenção que não podemos ter aproveitar a funcionalidade fornecida pelo ADF dados pipe de forma incremental. Para obter mais informações sobre como fazer isso e outros recursos fornecidos pelo ADF, consulte a [documentação ADF](https://azure.microsoft.com/services/data-factory/).
