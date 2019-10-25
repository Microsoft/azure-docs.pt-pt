---
author: lugoldbemicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 10/23/2019
ms.author: lugoldbe
ms.openlocfilehash: 1b5887fdc7cb610381b73b5bf7f6fd6dbc86afd4
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809582"
---
## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

Neste artigo, um modelo de Azure Resource Manager é usado para criar um grupo de recursos, uma conta de armazenamento e um contêiner, um hub de eventos e um cluster de Data Explorer do Azure e um banco de dados. Salve o conteúdo a seguir em um arquivo com o nome `template.json`, que será usado para executar o exemplo de código.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventHubNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Specifies a the event hub Namespace name."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Specifies a event hub name."
            }
        },
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": ["Standard_LRS", "Standard_GRS", "Standard_ZRS", "Premium_LRS"],
            "metadata": {
                "description": "Storage Account type"
            }
        },
        "storageAccountName": {
            "type": "string",
            "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Name of the storage account to create"
            }
        },
        "containerName": {
            "type": "string",
            "defaultValue": "[concat('storagecontainer', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Name of the container in storage account to create"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Specifies the Azure location for all resources."
            }
        },
        "eventHubSku": {
            "type": "string",
            "allowedValues": ["Basic", "Standard"],
            "defaultValue": "Standard",
            "metadata": {
                "description": "Specifies the messaging tier for service Bus namespace."
            }
        },
        "kustoClusterName": {
            "type": "string",
            "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Name of the cluster to create"
            }
        },
        "kustoDatabaseName": {
            "type": "string",
            "defaultValue": "kustodb",
            "metadata": {
                "description": "Name of the database to create"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {
    },
    "resources": [{
            "apiVersion": "2017-04-01",
            "type": "Microsoft.EventHub/namespaces",
            "name": "[parameters('eventHubNamespaceName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('eventHubSku')]",
                "tier": "[parameters('eventHubSku')]",
                "capacity": 1
            },
            "properties": {
                "isAutoInflateEnabled": false,
                "maximumThroughputUnits": 0
            }
        }, {
            "apiVersion": "2017-04-01",
            "type": "Microsoft.EventHub/namespaces/eventhubs",
            "name": "[concat(parameters('eventHubNamespaceName'), '/', parameters('eventHubName'))]",
            "location": "[parameters('location')]",
            "dependsOn": ["[resourceId('Microsoft.EventHub/namespaces', parameters('eventHubNamespaceName'))]"],
            "properties": {
                "messageRetentionInDays": 7,
                "partitionCount": 1
            }
        }, {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-07-01",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "resources": [
                {
                    "name": "[concat('default/', parameters('containerName'))]",
                    "type": "blobServices/containers",
                    "apiVersion": "2018-07-01",
                    "dependsOn": [
                        "[parameters('storageAccountName')]"
                    ],
                    "properties": {
                        "publicAccess": "None"
                    }
                }
            ],
            "properties": {}
        }, {
            "name": "[parameters('kustoClusterName')]",
            "type": "Microsoft.Kusto/clusters",
            "sku": {
                "name": "Standard_D13_v2",
                "tier": "Standard",
                "capacity": 2
            },
            "apiVersion": "2019-05-15",
            "location": "[parameters('location')]",
            "tags": {
                "Created By": "GitHub quickstart template"
            }
        }, {
            "name": "[concat(parameters('kustoClusterName'), '/', parameters('kustoDatabaseName'))]",
            "type": "Microsoft.Kusto/clusters/databases",
            "apiVersion": "2019-05-15",
            "location": "[parameters('location')]",
            "dependsOn": ["[resourceId('Microsoft.Kusto/clusters', parameters('kustoClusterName'))]"],
            "properties": {
                "softDeletePeriodInDays": 365,
                "hotCachePeriodInDays": 31
            }
        }
    ]
}

```