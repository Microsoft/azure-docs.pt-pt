---
title: Use modelos de Gestor de Recursos na Fábrica de Dados
description: Aprenda a criar e utilizar modelos do Gestor de Recursos Azure para criar entidades da Fábrica de Dados.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 73498b3537f4cf9313fc9e2464785f63c2af0d5a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460737"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Utilize modelos para criar entidades da Fábrica de Dados Azure
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

## <a name="overview"></a>Descrição geral
Ao utilizar a Azure Data Factory para as suas necessidades de integração de dados, poderá reutilizar o mesmo padrão em diferentes ambientes ou implementar a mesma tarefa repetidamente dentro da mesma solução. Os modelos ajudam-no a implementar e gerir estes cenários de forma fácil. Os modelos na Azure Data Factory são ideais para cenários que envolvam reutilização e repetição.

Considere a situação em que uma organização tem 10 fábricas em todo o mundo. Os registos de cada planta são armazenados numa base de dados separada no local SQL Server. A empresa quer construir um único armazém de dados na nuvem para análise sílsódia. Também quer ter a mesma lógica, mas diferentes configurações para ambientes de desenvolvimento, teste e produção.

Neste caso, uma tarefa deve ser repetida dentro do mesmo ambiente, mas com valores diferentes em todas as 10 fábricas de dados para cada fábrica. Com efeito, a **repetição** está presente. A Templating permite a abstração deste fluxo genérico (isto é, os oleodutos que têm as mesmas atividades em cada fábrica de dados), mas utiliza um ficheiro de parâmetro separado para cada fábrica.

Além disso, como a organização quer implementar estas 10 fábricas de dados várias vezes em diferentes ambientes, os modelos podem usar esta **reutilização** utilizando ficheiros de parâmetros separados para ambientes de desenvolvimento, teste e produção.

## <a name="templating-with-azure-resource-manager"></a>Templating com Gestor de Recursos Azure
Os [modelos do Gestor de Recursos Azure](../../azure-resource-manager/templates/overview.md) são uma ótima maneira de alcançar o templating na Azure Data Factory. Os modelos do Gestor de Recursos definem a infraestrutura e configuração da sua solução Azure através de um ficheiro JSON. Como os modelos do Gestor de Recursos Azure funcionam com todos/a maioria dos serviços Azure, pode ser amplamente utilizado para gerir facilmente todos os recursos dos seus ativos Azure. Consulte [os modelos de Gestor de Recursos Azure](../../azure-resource-manager/templates/template-syntax.md) para saber mais sobre os modelos de Gestor de Recursos em geral.

## <a name="tutorials"></a>Tutoriais
Consulte os seguintes tutoriais para obter instruções passo a passo para criar entidades da Fábrica de Dados utilizando modelos de Gestor de Recursos:

* [Tutorial: Criar um pipeline para copiar dados utilizando o modelo do Gestor de Recursos Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Tutorial: Criar um pipeline para processar dados utilizando o modelo do Gestor de Recursos Azure](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Modelos de fábrica de dados no GitHub
Confira os seguintes modelos de arranque rápido do Azure no GitHub:

* [Criar uma fábrica de dados para copiar dados do Armazenamento De Blob Azure para a Base de Dados Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Criar uma fábrica de dados com atividade da Colmeia no cluster Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Criar uma fábrica de dados para copiar dados da Salesforce para a Azure Blobs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Criar uma fábrica de dados que agrupa atividades: copia dados de um servidor FTP para O Blobs, invoca um script de colmeia num cluster HDInsight a pedido para transformar os dados, e cópias resultam em Base de Dados Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Sinta-se à vontade para partilhar os seus modelos de Fábrica de Dados Azure no [Arranque Rápido do Azure](https://azure.microsoft.com/documentation/templates/). Consulte o guia de [contribuição](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) enquanto desenvolve modelos que podem ser partilhados através deste repositório.

As seguintes secções fornecem detalhes sobre a definição de recursos da Fábrica de Dados num modelo de Gestor de Recursos.

## <a name="defining-data-factory-resources-in-templates"></a>Definição de recursos da fábrica de dados em modelos
O modelo de alto nível para definir uma fábrica de dados é:

```JSON
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": { ...
},
"variables": { ...
},
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
    "resources": [
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>Definir fábrica de dados
Defina uma fábrica de dados no modelo do Resource Manager, conforme mostrado no exemplo seguinte:

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
O dataFactoryName é definido em "variáveis" como:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Definir serviços ligados

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Consulte o [Serviço Ligado](data-factory-azure-blob-connector.md#azure-storage-linked-service) ao Armazenamento ou [serviços computacionais ligados](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes sobre as propriedades JSON para o serviço específico ligado que deseja implementar. O parâmetro "dependeon" especifica o nome da fábrica de dados correspondente. Um exemplo de definição de um serviço ligado ao Armazenamento Azure é mostrado na seguinte definição JSON:

### <a name="define-datasets"></a>Definir conjuntos de dados

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
Consulte as lojas de [dados suportadas](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para obter detalhes sobre as propriedades JSON para o tipo específico de conjunto de dados que pretende implementar. Note que o parâmetro "dependeon" especifica o nome da fábrica de dados correspondente e do serviço ligado ao armazenamento. Um exemplo de definição do tipo de conjunto de dados de armazenamento de bolhas Azure é mostrado na seguinte definição JSON:

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>Definir oleodutos

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

Consulte [a definição](data-factory-create-pipelines.md#pipeline-json) de oleodutos para mais detalhes sobre as propriedades JSON para definir o pipeline específico e atividades que deseja implementar. Note que o parâmetro "dependeon" especifica o nome da fábrica de dados e quaisquer serviços ou conjuntos de dados correspondentes. Um exemplo de um pipeline que copia dados do Armazenamento De Azure Blob para a Base de Dados Azure SQL é mostrado no seguinte corte JSON:

```JSON
"type": "datapipelines",
"name": "[variables('pipelineName')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('azureSqlLinkedServiceName')]",
    "[variables('blobInputDatasetName')]",
    "[variables('sqlOutputDatasetName')]"
],
"apiVersion": "2015-10-01",
"properties": {
    "activities": [
    {
        "name": "CopyFromAzureBlobToAzureSQL",
        "description": "Copy data frm Azure blob to Azure SQL",
        "type": "Copy",
        "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
        ],
        "outputs": [
            {
                "name": "[variables('sqlOutputDatasetName')]"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
            },
            "translator": {
                "type": "TabularTranslator",
                "columnMappings": "Column0:FirstName,Column1:LastName"
            }
        },
        "Policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 3,
            "timeout": "01:00:00"
        }
    }
    ],
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>Modelo de fábrica de dados parametrizador
Para obter as melhores práticas de parametrização, consulte [as melhores práticas para criar modelos](../../azure-resource-manager/resource-manager-template-best-practices.md)de Gestor de Recursos Azure . Em geral, o uso do parâmetro deve ser minimizado, especialmente se as variáveis puderem ser utilizadas. Apenas fornecer parâmetros nos seguintes cenários:

* As definições variam consoante o ambiente (exemplo: desenvolvimento, teste e produção)
* Segredos (como senhas)

Se precisar de retirar segredos do [Cofre de Chaves Azure](../../key-vault/general/overview.md) ao implementar entidades da Fábrica de Dados Azure utilizando modelos, especifique o **cofre chave** e o **nome secreto,** como mostra o seguinte exemplo:

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> Embora os modelos de exportação para as fábricas de dados existentes ainda não sejam suportados, está em obras.
>
>
