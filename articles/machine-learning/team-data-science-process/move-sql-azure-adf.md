---
title: Dados do Servidor SQL para o SQL Azure com a Azure Data Factory - Team Data Science Process
description: Criar um oleoduto ADF que componha duas atividades de migração de dados que, em conjunto, movem dados diariamente entre bases de dados no local e na nuvem.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8f696f1c6c414cd9db082e79e0f34c56156e1ee0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76722497"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Mova dados de um servidor SQL no local para O SQL Azure com a Azure Data Factory

Este artigo mostra como mover dados de uma base de dados de servidores SQL no local para uma Base de Dados SQL Azure via Armazenamento Azure Blob utilizando a Azure Data Factory (ADF): este método é uma abordagem de legado suportada que tem as vantagens de uma cópia de encenação replicada, embora [sugerimos olhar para a nossa página](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1)de migração de dados para as opções mais recentes .

Para uma tabela que resume várias opções para mover dados para uma base de dados Azure SQL, consulte mover dados para uma base de [dados Azure SQL para aprendizagem automática azure](move-sql-azure.md).

## <a name="introduction-what-is-adf-and-when-should-it-be-used-to-migrate-data"></a><a name="intro"></a>Introdução: O que é ADF e quando deve ser usado para migrar dados?
A Azure Data Factory é um serviço de integração de dados totalmente gerido na nuvem que orquestra e automatiza o movimento e transformação de dados. O conceito-chave no modelo ADF é o pipeline. Um oleoduto é um agrupamento lógico de Atividades, cada um dos quais define as ações a executar nos dados contidos nos Conjuntos de Dados. Os serviços ligados são utilizados para definir a informação necessária para que a Data Factory se conectem aos recursos de dados.

Com a ADF, os serviços de processamento de dados existentes podem ser compostos em pipelines de dados altamente disponíveis e geridos na nuvem. Estes pipelines de dados podem ser programados para ingerir, preparar, transformar, analisar e publicar dados, e a ADF gere e orquestra os dados complexos e as dependências de processamento. As soluções podem ser rapidamente construídas e implantadas na nuvem, ligando um número crescente de fontes de dados no local e na nuvem.

Considere usar a ADF:

* quando os dados precisam ser continuamente migrados em um cenário híbrido que acede tanto no local como nos recursos da nuvem
* quando os dados precisam de transformações ou têm a lógica do negócio adicionada a ele quando são migrados.

A ADF permite o agendamento e monitorização de trabalhos utilizando scripts JSON simples que gerem o movimento de dados numa base periódica. A ADF também tem outras capacidades, como apoio a operações complexas. Para obter mais informações sobre a ADF, consulte a documentação na [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="the-scenario"></a><a name="scenario"></a>O Cenário
Estabelecemos um oleoduto ADF que compõe duas atividades de migração de dados. Juntos movem dados diariamente entre uma base de dados SQL no local e uma Base de Dados Azure SQL na nuvem. As duas atividades são:

* copiar dados de uma base de dados do SQL Server no local para uma conta de Armazenamento Azure Blob
* copiar dados da conta de armazenamento De Blob Azure para uma base de dados Azure SQL.

> [!NOTE]
> Os passos aqui apresentados foram adaptados a partir do tutorial mais detalhado fornecido pela equipa DaDF: [Copiar dados de uma base de dados do SQL Server no local para armazenamento Azure Blob](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) As referências às secções relevantes desse tópico são fornecidas quando apropriado.
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Pré-requisitos
Este tutorial assume que tem:

* Uma **subscrição Azure.** Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento Azure.** Utiliza uma conta de armazenamento Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md). Depois de ter criado a conta de armazenamento, tem de obter a chave de conta utilizada para aceder ao armazenamento. Ver [Gerir as chaves](../../storage/common/storage-account-keys-manage.md)de acesso à conta de armazenamento .
* Acesso a uma Base de **Dados SQL Azure**. Se tiver de configurar uma Base de Dados Azure SQL, o tópico Iniciar-se com a Base de [Dados SQL](../../sql-database/sql-database-get-started.md) do Microsoft Azure fornece informações sobre como fornecer uma nova instância de uma Base de Dados Azure SQL.
* Instalado e configurado **Azure PowerShell** localmente. Para obter instruções, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Este procedimento utiliza o [portal Azure.](https://portal.azure.com/)
>
>

## <a name="upload-the-data-to-your-on-premises-sql-server"></a><a name="upload-data"></a>Faça upload dos dados para o seu Servidor SQL no local
Usamos o conjunto de dados do [táxi de NYC](https://chriswhong.com/open-data/foil_nyc_taxi/) para demonstrar o processo de migração. O conjunto de dados do TÁXI NYC está disponível, como se nota nesse post, no armazenamento de blob Azure [NYC Taxi Data](https://www.andresmh.com/nyctaxitrips/). Os dados têm dois ficheiros, o ficheiro trip_data.csv, que contém detalhes da viagem, e o ficheiro trip_far.csv, que contém detalhes da tarifa paga por cada viagem. Uma amostra e descrição destes ficheiros são fornecidas na [Descrição](sql-walkthrough.md#dataset)do conjunto de dados de viagens de táxi de NYC .

Pode adaptar o procedimento aqui fornecido a um conjunto de dados próprios ou seguir os passos descritos através do conjunto de dados do Táxi de NYC. Para fazer o upload do conjunto de dados do Táxi NYC para a sua base de dados do SQL Server no local, siga o procedimento descrito nos Dados de Importação a granel para a Base de Dados do [Servidor SQL](sql-walkthrough.md#dbload). Estas instruções são para um Servidor SQL numa máquina virtual Azure, mas o procedimento para o upload para o Servidor SQL no local é o mesmo.

## <a name="create-an-azure-data-factory"></a><a name="create-adf"></a>Criar uma fábrica de dados azure
As instruções para a criação de uma nova Fábrica de Dados Azure e um grupo de recursos no [portal Azure](https://portal.azure.com/) são fornecidas Criar uma Fábrica de [Dados Azure.](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory) Nomeie o novo *adfdsp* de instância ADF e nomeie o grupo de recursos criado *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Instalar e configurar o tempo de integração da fábrica de dados Azure
O Integration Runtime é uma infraestrutura de integração de dados gerida pelo cliente utilizada pela Azure Data Factory para fornecer capacidades de integração de dados em diferentes ambientes de rede. Este tempo de execução era anteriormente chamado de "Data Management Gateway".

Para configurar, [siga as instruções para a criação](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline) de um oleoduto

## <a name="create-linked-services-to-connect-to-the-data-resources"></a><a name="adflinkedservices"></a>Criar serviços ligados para se conectar aos recursos de dados
Um serviço ligado define a informação necessária para que a Azure Data Factory se conectem a um recurso de dados. Dispomos de três recursos neste cenário para os quais são necessários serviços ligados:

1. No local, O Servidor SQL
2. Armazenamento de Blobs do Azure
3. Base de Dados SQL do Azure

O procedimento passo a passo para a criação de serviços ligados é fornecido na [Create linked services.](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)


## <a name="define-and-create-tables-to-specify-how-to-access-the-datasets"></a><a name="adf-tables"></a>Defina e crie tabelas para especificar como aceder aos conjuntos de dados
Crie tabelas que especifiquem a estrutura, localização e disponibilidade dos conjuntos de dados com os seguintes procedimentos baseados em scripts. Os ficheiros JSON são usados para definir as tabelas. Para obter mais informações sobre a estrutura destes ficheiros, consulte [datasets](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> Deve executar `Add-AzureAccount` o cmdlet antes de executar o cmdlet [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) para confirmar que a subscrição azure certa é selecionada para a execução do comando. Para obter documentação deste cmdlet, consulte [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

As definições baseadas em JSON nas tabelas utilizam os seguintes nomes:

* o nome da **tabela** no servidor SQL no local é *nyctaxi_data*
* o **nome** do contentor na conta de armazenamento de Blob Azure é o nome *do contentor*

São necessárias três definições de tabela para este gasoduto ADF:

1. [Tabela sql no local](#adf-table-onprem-sql)
2. [Mesa blob](#adf-table-blob-store)
3. [Mesa Azul SQL](#adf-table-azure-sql)

> [!NOTE]
> Estes procedimentos utilizam o Azure PowerShell para definir e criar as atividades da ADF. Mas estas tarefas também podem ser realizadas através do portal Azure. Para mais detalhes, consulte [Criar conjuntos de dados](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="sql-on-premises-table"></a><a name="adf-table-onprem-sql"></a>Tabela sql no local
A definição de tabela para o Servidor SQL no local é especificada no seguinte ficheiro JSON:

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

Os nomes das colunas não foram incluídos aqui. Pode subseleccionar os nomes das colunas, incluindo-os aqui (para obter detalhes, verifique o tópico [de documentação DaDF.](../../data-factory/copy-activity-overview.md)

Copie a definição JSON da tabela num ficheiro chamado ficheiro *onpremtabledef.json* e guarde-o para um local conhecido (aqui se presume ser *C:\temp\onpremtabledef.json*). Crie a tabela em ADF com o seguinte cmdlet Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="blob-table"></a><a name="adf-table-blob-store"></a>Mesa blob
A definição para a tabela para a localização da bolha de saída está na seguinte (isto mapeia os dados ingeridos de instalações para a blob Azure):

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

Copie a definição JSON da tabela num ficheiro chamado *ficheiro bloboutputtabledef.json* e guarde-a para um local conhecido (aqui se supõe que é *C:\temp\bloboutputtabledef.json*). Crie a tabela em ADF com o seguinte cmdlet Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="sql-azure-table"></a><a name="adf-table-azure-sql"></a>Mesa Azul SQL
A definição para a tabela para a saída Do SQL Azure está na seguinte (este esquema mapeia os dados provenientes da bolha):

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

Copie a definição JSON da tabela num ficheiro chamado ficheiro *AzureSqlTable.json* e guarde-a para um local conhecido (aqui se supõe que seja *C:\temp\AzureSqlTable.json*). Crie a tabela em ADF com o seguinte cmdlet Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="define-and-create-the-pipeline"></a><a name="adf-pipeline"></a>Defina e crie o oleoduto
Especifique as atividades que pertencem ao gasoduto e crie o gasoduto com os seguintes procedimentos baseados em scripts. Um ficheiro JSON é usado para definir as propriedades do gasoduto.

* O script pressupõe que o nome do **pipeline** é *AMLDSProcessPipeline*.
* Note também que definimos a periodicidade do gasoduto a ser executado diariamente e utilizamos o tempo de execução padrão para o trabalho (12 h UTC).

> [!NOTE]
> Os seguintes procedimentos utilizam o Azure PowerShell para definir e criar o gasoduto ADF. Mas esta tarefa também pode ser concretizada através do portal Azure. Para mais detalhes, consulte [Criar o oleoduto](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

Utilizando as definições de tabela fornecidas anteriormente, a definição do gasoduto para a ADF é especificada da seguinte forma:

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

Copie esta definição JSON do oleoduto num ficheiro chamado *ficheiro pipelinedef.json* e guarde-o para um local conhecido (aqui se supõe que seja *C:\temp\pipelinedef.json*). Crie o gasoduto em ADF com o seguinte cmdlet Azure PowerShell:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="start-the-pipeline"></a><a name="adf-pipeline-start"></a>Inicie o oleoduto
O gasoduto pode agora ser executado utilizando o seguinte comando:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Os valores do parâmetro de data de *arranque* e de data *limite* devem ser substituídos pelas datas reais entre as quais pretende que o gasoduto seja executado.

Uma vez executada a conduta, deverá poder ver os dados aparecerem no recipiente selecionado para a bolha, um ficheiro por dia.

Não aproveitamos a funcionalidade fornecida pela ADF para canalizar dados de forma incremental. Para obter mais informações sobre como fazê-lo e outras capacidades fornecidas pela ADF, consulte a [documentação da ADF.](https://azure.microsoft.com/services/data-factory/)
