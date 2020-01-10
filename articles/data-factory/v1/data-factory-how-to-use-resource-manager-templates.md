---
title: Usar modelos do Resource Manager no Data Factory
description: Saiba como criar e usar modelos de Azure Resource Manager para criar Data Factory entidades.
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
ms.openlocfilehash: 96adcfa4bbfc4ed0066ccfbdbe57f9840e8b07f8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438813"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Usar modelos para criar entidades de Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

## <a name="overview"></a>Visão geral
Ao usar Azure Data Factory para suas necessidades de integração de dados, você pode se deparar reutilizar o mesmo padrão em diferentes ambientes ou implementar a mesma tarefa repetidamente dentro da mesma solução. Os modelos ajudam você a implementar e gerenciar esses cenários de maneira fácil. Os modelos no Azure Data Factory são ideais para cenários que envolvem reutilização e repetição.

Considere a situação em que uma organização tem 10 plantas de fabricação em todo o mundo. Os logs de cada fábrica são armazenados em um banco de dados SQL Server local separado. A empresa deseja criar uma única data warehouse na nuvem para análise ad hoc. Ele também quer ter a mesma lógica, mas configurações diferentes para ambientes de desenvolvimento, teste e produção.

Nesse caso, uma tarefa precisa ser repetida dentro do mesmo ambiente, mas com valores diferentes entre as 10 fábricas de dados para cada planta de fabricação. Na verdade, a **repetição** está presente. Templating permite a abstração desse fluxo genérico (ou seja, pipelines com as mesmas atividades em cada data factory), mas usa um arquivo de parâmetro separado para cada planta de fabricação.

Além disso, como a organização deseja implantar essas 10 fábricas de dados várias vezes em ambientes diferentes, os modelos podem usar essa **reusabilidade** utilizando arquivos de parâmetros separados para ambientes de desenvolvimento, teste e produção.

## <a name="templating-with-azure-resource-manager"></a>Templating com Azure Resource Manager
Os [modelos de Azure Resource Manager](../../azure-resource-manager/template-deployment-overview.md) são uma ótima maneira de obter templating em Azure data Factory. Os modelos do Resource Manager definem a infraestrutura e a configuração de sua solução do Azure por meio de um arquivo JSON. Como os modelos de Azure Resource Manager funcionam com todos/a maioria dos serviços do Azure, eles podem ser amplamente usados para gerenciar facilmente todos os recursos de seus ativos do Azure. Consulte [criando modelos de Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) para saber mais sobre os modelos do Resource Manager em geral.

## <a name="tutorials"></a>Tutoriais
Consulte os seguintes tutoriais para obter instruções passo a passo para criar Data Factory entidades usando modelos do Resource Manager:

* [Tutorial: criar um pipeline para copiar dados usando Azure Resource Manager modelo](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Tutorial: criar um pipeline para processar dados usando Azure Resource Manager modelo](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Modelos de Data Factory no GitHub
Confira os seguintes modelos de início rápido do Azure no GitHub:

* [Criar um data Factory para copiar dados do armazenamento de BLOBs do Azure para o banco de dados SQL do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Criar um data Factory com atividade de Hive no cluster do Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Criar um data Factory para copiar dados do Salesforce para BLOBs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Criar um data Factory que encadeia as atividades: copia os dados de um servidor FTP para BLOBs do Azure, invoca um script do hive em um cluster HDInsight sob demanda para transformar os dados e copia o resultado no Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Sinta-se à vontade para compartilhar seus modelos de Azure Data Factory no [início rápido do Azure](https://azure.microsoft.com/documentation/templates/). Consulte o [Guia de contribuição](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) ao desenvolver modelos que podem ser compartilhados por meio desse repositório.

As seções a seguir fornecem detalhes sobre como definir Data Factory recursos em um modelo do Resource Manager.

## <a name="defining-data-factory-resources-in-templates"></a>Definindo Data Factory recursos em modelos
O modelo de nível superior para definir um data factory é:

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
O datafactoryname é definido em "Variables" como:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Definir serviços vinculados

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Consulte [serviço vinculado de armazenamento](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [Serviços vinculados de computação](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes sobre as propriedades JSON para o serviço vinculado específico que você deseja implantar. O parâmetro "depends" especifica o nome do data factory correspondente. Um exemplo de definição de um serviço vinculado para o armazenamento do Azure é mostrado na seguinte definição de JSON:

### <a name="define-datasets"></a>Definir conjuntos de os

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
Consulte [armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para obter detalhes sobre as propriedades JSON para o tipo de conjunto de dados específico que você deseja implantar. Observe que o parâmetro "depending" especifica o nome do data factory e do serviço vinculado de armazenamento correspondente. Um exemplo de definição do tipo de conjunto de texto do armazenamento de BLOBs do Azure é mostrado na seguinte definição de JSON:

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

### <a name="define-pipelines"></a>Definir pipelines

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

Consulte [definindo pipelines](data-factory-create-pipelines.md#pipeline-json) para obter detalhes sobre as propriedades JSON para definir o pipeline específico e as atividades que você deseja implantar. Observe que o parâmetro "depending" especifica o nome do data factory e quaisquer serviços vinculados ou conjuntos de valores correspondentes. Um exemplo de um pipeline que copia dados do armazenamento de BLOBs do Azure para o Azure SQL Database é mostrado no seguinte trecho de JSON:

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
## <a name="parameterizing-data-factory-template"></a>Parametrizando Data Factory modelo
Para obter as práticas recomendadas de parametrização, consulte [práticas recomendadas para criar modelos de Azure Resource Manager](../../azure-resource-manager/resource-manager-template-best-practices.md). Em geral, o uso do parâmetro deve ser minimizado, especialmente se variáveis podem ser usadas em seu lugar. Forneça apenas parâmetros nos seguintes cenários:

* As configurações variam de acordo com o ambiente (exemplo: desenvolvimento, teste e produção)
* Segredos (como senhas)

Se você precisar extrair segredos de [Azure Key Vault](../../key-vault/key-vault-overview.md) ao implantar Azure data Factory entidades usando modelos, especifique o cofre de **chaves** e o **nome do segredo** , conforme mostrado no exemplo a seguir:

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
> Embora a exportação de modelos para data factories existentes ainda não tenha suporte, ela está em funcionamento no momento.
>
>
