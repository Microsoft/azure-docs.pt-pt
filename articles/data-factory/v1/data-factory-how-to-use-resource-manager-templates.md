---
title: Use modelos de gestor de recursos na fábrica de dados
description: Saiba como criar e utilizar modelos de Gestor de Recursos Azure para criar entidades da Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 8ade57fc4cf96e3638fffc918899385d8935c95f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782899"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Use modelos para criar entidades da Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

## <a name="overview"></a>Descrição Geral
Ao utilizar a Azure Data Factory para as suas necessidades de integração de dados, poderá encontrar-se a reutilizar o mesmo padrão em diferentes ambientes ou a implementar a mesma tarefa repetidamente dentro da mesma solução. Os modelos ajudam-no a implementar e gerir estes cenários de forma fácil. Os modelos na Azure Data Factory são ideais para cenários que envolvem reutilização e repetição.

Considere a situação em que uma organização tem 10 fábricas em todo o mundo. Os registos de cada planta são armazenados numa base de dados separada do SQL Server. A empresa quer construir um único armazém de dados na nuvem para análise ad hoc. Também quer ter a mesma lógica, mas diferentes configurações para ambientes de desenvolvimento, teste e produção.

Neste caso, uma tarefa deve ser repetida no mesmo ambiente, mas com valores diferentes em todas as 10 fábricas de dados para cada fábrica. Com efeito, **a repetição** está presente. A templário permite a abstração deste fluxo genérico (isto é, os oleodutos têm as mesmas atividades em cada fábrica de dados), mas utiliza um arquivo de parâmetros separado para cada fábrica.

Além disso, como a organização quer implementar estas 10 fábricas de dados várias vezes em diferentes ambientes, os modelos podem usar esta reutilização utilizando ficheiros de **parâmetros separados** para ambientes de desenvolvimento, teste e produção.

## <a name="templating-with-azure-resource-manager"></a>Templating com Azure Resource Manager
[Os modelos Azure Resource Manager](../../azure-resource-manager/templates/overview.md) são uma ótima maneira de alcançar o templating na Azure Data Factory. Os modelos de Gestor de Recursos definem a infraestrutura e configuração da sua solução Azure através de um ficheiro JSON. Como os modelos do Azure Resource Manager funcionam com todos/a maioria dos serviços Azure, pode ser amplamente utilizado para gerir facilmente todos os recursos dos seus ativos Azure. Consulte os modelos do [Gestor de Recursos Azure](../../azure-resource-manager/templates/template-syntax.md) para saber mais sobre os modelos do Gestor de Recursos em geral.

## <a name="tutorials"></a>Tutoriais
Consulte os seguintes tutoriais para instruções passo a passo para criar entidades da Data Factory utilizando modelos de Gestor de Recursos:

* [Tutorial: Criar um pipeline para copiar dados utilizando o modelo do Gestor de Recursos Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Tutorial: Criar um pipeline para processar dados utilizando o modelo do Gestor de Recursos Azure](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Modelos de fábrica de dados no GitHub
Confira os seguintes modelos de arranque rápido do Azure no GitHub:

* [Criar uma fábrica de dados para copiar dados do Azure Blob Storage para a Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Criar uma fábrica de dados com atividade de Colmeia no cluster Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Criar uma fábrica de dados para copiar dados da Salesforce para a Azure Blobs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Criar uma fábrica de dados que acorrenta atividades: copia dados de um servidor FTP para Azure Blobs, invoca um script de colmeia num cluster HDInsight a pedido para transformar os dados, e cópias resultam em Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Sinta-se livre para partilhar os seus modelos de Fábrica de Dados [Azure no Azure quickstart](https://azure.microsoft.com/documentation/templates/). Consulte o [guia de contribuição](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) enquanto desenvolve modelos que podem ser partilhados através deste repositório.

As secções seguintes fornecem detalhes sobre a definição dos recursos da Data Factory num modelo de Gestor de Recursos.

## <a name="defining-data-factory-resources-in-templates"></a>Definição de recursos da Fábrica de Dados em modelos
O modelo de nível superior para definir uma fábrica de dados é:

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

Consulte [o Serviço ligado ao Armazenamento](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [Serviços Ligados a Compute](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter mais informações sobre as propriedades JSON para o serviço específico ligado que pretende implementar. O parâmetro "dependOn" especifica o nome da fábrica de dados correspondente. Um exemplo de definição de um serviço ligado para o Armazenamento Azure é mostrado na seguinte definição JSON:

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
Consulte [as lojas de dados suportadas](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para obter mais informações sobre as propriedades JSON para o tipo de conjunto de dados específico que pretende implementar. Note que o parâmetro "dependOn" especifica o nome da fábrica de dados correspondente e o serviço ligado ao armazenamento. Um exemplo de definição do tipo de conjunto de dados de armazenamento de bolhas Azure é mostrado na seguinte definição JSON:

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

Consulte a [definição de oleodutos](data-factory-create-pipelines.md#pipeline-json) para mais detalhes sobre as propriedades JSON para definir o oleoduto específico e as atividades que deseja implementar. Note que o parâmetro "dependOn" especifica o nome da fábrica de dados e quaisquer serviços ou conjuntos de dados ligados correspondentes. Um exemplo de um oleoduto que copia dados do Azure Blob Storage para a Azure SQL Database é mostrado no seguinte snippet JSON:

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
## <a name="parameterizing-data-factory-template"></a>Modelo de fábrica de dados parametrizante
Para obter as melhores práticas sobre a parametrização, consulte [as melhores práticas para criar modelos do Gestor de Recursos Azure](../../azure-resource-manager/templates/template-best-practices.md). Em geral, a utilização dos parâmetros deve ser minimizada, especialmente se as variáveis puderem ser utilizadas. Apenas forneça parâmetros nos seguintes cenários:

* As configurações variam consoante o ambiente (exemplo: desenvolvimento, teste e produção)
* Segredos (como palavras-passe)

Se precisar de obter segredos do [Azure Key Vault](../../key-vault/general/overview.md) ao implementar entidades da Azure Data Factory utilizando modelos, especifique o **cofre-chave** e o **nome secreto** como mostrado no seguinte exemplo:

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
> Embora os modelos de exportação para as fábricas de dados existentes ainda não tenham sido suportados, está em obras.
>
>