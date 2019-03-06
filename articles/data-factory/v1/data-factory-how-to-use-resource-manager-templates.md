---
title: Utilize o Gestor de recursos de modelos no Data Factory | Documentos da Microsoft
description: Saiba como criar e utilizar os modelos Azure Resource Manager para criar entidades do Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 37724021-f55f-4e85-9206-6d4a48bda3d8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 904b5db35281ed07745b33c364c03e3f4c4bfd2b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449211"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Utilizar modelos para criar entidades do Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

## <a name="overview"></a>Descrição geral
Ao utilizar o Azure Data Factory para as suas necessidades de integração de dados, talvez tenha reutilizar o mesmo padrão em ambientes diferentes ou implementar a mesma tarefa repetidamente dentro da mesma solução. Os modelos ajudam-na implementar e gerir estes cenários de uma forma fácil. Modelos no Azure Data Factory são ideais para cenários que envolvem a capacidade de reutilização e repetição.

Considere a situação em que uma organização tiver as fábricas a 10 em todo o mundo. Os registos de cada fábrica são armazenados numa base de dados do SQL Server no local independente. A empresa quer criar um armazém de dados única na cloud para análise ad hoc. Também quer ter a mesma lógica, mas diferentes configurações para ambientes de desenvolvimento, teste e produção.

Neste caso, uma tarefa tem de ser repetido no mesmo ambiente, mas com valores diferentes entre as fábricas de dados de 10 para cada fábrica de produção. Na verdade, **repetição** está presente. Para modelos permite a abstração deste fluxo genérico (ou seja, pipelines ter iguais às atividades em cada fábrica de dados), mas utiliza um ficheiro de parâmetro separado para cada fábrica de produção.

Além disso, como a empresa pretende implementar estas fábricas de dados de 10 várias vezes entre ambientes diferentes, modelos podem utilizá-lo **reutilização** através da utilização de ficheiros de parâmetro separado para o desenvolvimento, teste, e ambientes de produção.

## <a name="templating-with-azure-resource-manager"></a>Modelos com o Azure Resource Manager
[Modelos Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md#template-deployment) são uma excelente forma de alcançar para modelos no Azure Data Factory. Modelos do Resource Manager definem a infraestrutura e a configuração da sua solução do Azure através de um ficheiro JSON. Como modelos Azure Resource Manager funcionam com serviços do Azure todos os/maioria, ele pode amplamente utilizado para gerir facilmente todos os recursos dos seus recursos do Azure. Ver [modelos Authoring Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) para saber mais sobre os modelos do Resource Manager em geral.

## <a name="tutorials"></a>Tutoriais
Veja os tutoriais seguintes para obter instruções passo a passo Criar entidades do Data Factory com modelos do Resource Manager:

* [Tutorial: Criar um pipeline para copiar dados utilizando o modelo Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Tutorial: Criar um pipeline para processar dados com o modelo Azure Resource Manager](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Modelos de fábrica de dados no GitHub
Veja os seguintes modelos de início rápido do Azure no GitHub:

* [Criar uma fábrica de dados para copiar dados do armazenamento de Blobs do Azure para a base de dados do Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Criar uma fábrica de dados com a atividade de Hive num cluster HDInsight do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Criar uma fábrica de dados para copiar dados do Salesforce para Blobs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Criar uma fábrica de dados que está ligado a atividades: copia dados de um servidor FTP para Blobs do Azure, invoca um script do hive num cluster do HDInsight a pedido para transformar os dados e copia o resultado na base de dados do Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Sinta-se à vontade para compartilhar seus modelos do Azure Data Factory em [início rápido do Azure](https://azure.microsoft.com/documentation/templates/). Consulte a [guia de contribuições](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) durante o desenvolvimento de modelos que podem ser partilhados através deste repositório.

As secções seguintes fornecem detalhes sobre como definir recursos do Data Factory num modelo do Resource Manager.

## <a name="defining-data-factory-resources-in-templates"></a>Definir recursos do Data Factory em modelos
O modelo de nível superior para definir uma fábrica de dados é:

```JSON
"$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
O dataFactoryName é definido em "variables" como:

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

Ver [serviço ligado do Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [serviços ligados de computação](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes sobre as propriedades JSON para o serviço ligado específico que pretende implementar. O parâmetro de "dependsOn" Especifica o nome da fábrica de dados correspondente. Um exemplo da definição de um serviço ligado do armazenamento do Azure é mostrado na seguinte definição de JSON:

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
Consulte a [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para obter detalhes sobre as propriedades JSON para o tipo de conjunto de dados específico que pretende implementar. Tenha em atenção que o parâmetro de "dependsOn" Especifica o nome da fábrica de dados correspondente e armazenamento de serviço ligado. Um exemplo da definição de tipo de conjunto de dados de armazenamento de Blobs do Azure é mostrado na seguinte definição de JSON:

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

Consulte a [definindo pipelines](data-factory-create-pipelines.md#pipeline-json) para obter detalhes sobre as propriedades JSON para definir o pipeline específico e as atividades que pretende implementar. Tenha em atenção o parâmetro de "dependsOn" Especifica o nome da fábrica de dados, e qualquer correspondente associado serviços ou conjuntos de dados. Um exemplo de um pipeline que copia dados de armazenamento de Blobs do Azure para a base de dados do Azure SQL é mostrado no fragmento JSON seguinte:

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
## <a name="parameterizing-data-factory-template"></a>Parametrização de modelo de fábrica de dados
Para melhores práticas em parametrização, consulte [melhores práticas para a criação de modelos Azure Resource Manager](../../azure-resource-manager/resource-manager-template-best-practices.md). Em geral, utilização do parâmetro deve ser minimizada, especialmente se variáveis podem ser utilizadas em vez disso. Apenas fornece parâmetros nos seguintes cenários:

* As definições variam consoante o ambiente (exemplo: desenvolvimento, teste e produção)
* Segredos (tal como palavras-passe)

Se precisar de obter segredos a partir [Azure Key Vault](../../key-vault/key-vault-overview.md) quando implementar entidades do Azure Data Factory através de modelos, especifique a **Cofre de chaves** e **nome secreto** como mostra a exemplo a seguir:

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
> Embora a exportar modelos para fábricas de dados existentes atualmente ainda não é suportada, está em produção.
>
>
