---
title: Amostras de modelo de gestor de recursos para monitor Azure para VMs
description: Experimente os modelos do Gestor de Recursos Azure para implementar e configurar o Monitor deAzure para VMs.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: 91c8396795a85cd63fa502c4b091b7ca9a42865b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100627957"
---
# <a name="resource-manager-template-samples-for-azure-monitor-for-vms"></a>Amostras de modelo de gestor de recursos para monitor Azure para VMs
Este artigo inclui [modelos de gestor de recursos Azure](../../azure-resource-manager/templates/template-syntax.md) para permitir o Monitor Azure para VMs em máquinas virtuais. Cada amostra inclui um ficheiro de modelo e um ficheiro de parâmetros com valores de amostra para fornecer ao modelo.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="configure-workspace"></a>Configurar a área de trabalho
A amostra seguinte permite o Azure Monitor para VMs para um espaço de trabalho Log Analytics.


### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "String",
            "metadata": {
                "description": "Resource ID of the workspace."
            }
        },
        "workspaceLocation": {
            "type": "String",
            "metadata": {
                "description": "Location of the workspace."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "name": "VMISolutionDeployment",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceLocation')]",
                            "name": "[concat('VMInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('WorkspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[concat('VMInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[concat('OMSGallery/', 'VMInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                }
            },
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]"
        }
    ]
}
```

### <a name="parameter-file"></a>Arquivo de parâmetros

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
        },
        "workspaceLocation": {
            "value": "eastus"
        }
    }
}
```


## <a name="onboard-an-azure-virtual-machine"></a>A bordo de uma máquina virtual Azure
A amostra seguinte adiciona uma máquina virtual Azure ao Azure Monitor para VMs.


### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "VmResourceId": {
            "type": "String",
            "metadata": {
                "description": "VM Resource ID."
            }
        },
        "VmLocation": {
            "type": "String",
            "metadata": {
                "description": "The Virtual Machine Location."
            }
        },
        "osType": {
            "type": "String",
            "metadata": {
                "description": "OS Type, Example: Linux / Windows"
            }
        },
        "WorkspaceResourceId": {
            "type": "String",
            "metadata": {
                "description": "Workspace Resource ID."
            }
        }
    },
    "variables": {
        "VmName": "[split(parameters('VmResourceId'),'/')[8]]",
        "DaExtensionName": "[if(equals(toLower(parameters('osType')), 'windows'), 'DependencyAgentWindows', 'DependencyAgentLinux')]",
        "DaExtensionType": "[if(equals(toLower(parameters('osType')), 'windows'), 'DependencyAgentWindows', 'DependencyAgentLinux')]",
        "DaExtensionVersion": "9.5",
        "MmaExtensionName": "[if(equals(toLower(parameters('osType')), 'windows'), 'MMAExtension', 'OMSExtension')]",
        "MmaExtensionType": "[if(equals(toLower(parameters('osType')), 'windows'), 'MicrosoftMonitoringAgent', 'OmsAgentForLinux')]",
        "MmaExtensionVersion": "[if(equals(toLower(parameters('osType')), 'windows'), '1.0', '1.4')]"
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2018-10-01",
            "name": "[variables('VmName')]",
            "location": "[parameters('VmLocation')]",
            "resources": [
                {
                    "type": "extensions",
                    "apiVersion": "2018-10-01",
                    "name": "[variables('DaExtensionName')]",
                    "location": "[parameters('VmLocation')]",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/VirtualMachines/', variables('VmName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                        "type": "[variables('DaExtensionType')]",
                        "typeHandlerVersion": "[variables('DaExtensionVersion')]",
                        "autoUpgradeMinorVersion": true
                    }
                },
                {
                    "type": "extensions",
                    "apiVersion": "2018-10-01",
                    "name": "[variables('MmaExtensionName')]",
                    "location": "[parameters('VmLocation')]",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/VirtualMachines/', variables('VmName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "[variables('MmaExtensionType')]",
                        "typeHandlerVersion": "[variables('MmaExtensionVersion')]",
                        "autoUpgradeMinorVersion": "true",
                        "settings": {
                            "workspaceId": "[reference(parameters('WorkspaceResourceId'), '2015-03-20').customerId]",
                            "azureResourceId": "[parameters('VmResourceId')]",
                            "stopOnMultipleConnections": "true"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(parameters('WorkspaceResourceId'), '2015-03-20').primarySharedKey]"
                        }
                    }
                }
            ]
        }
    ]
}
```

### <a name="parameter-file"></a>Arquivo de parâmetros

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-linux-vm"
        },
        "vmLocation": {
            "value": "westus"
        },
        "osType": {
            "value": "linux"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
        }
    }
}

```


## <a name="onboard-an-azure-virtual-machine-scale-set"></a>A bordo de um conjunto de escala de máquina virtual Azure
A amostra seguinte adiciona uma balança de máquina virtual Azure definida ao Azure Monitor para VMs.


### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "VmssResourceId": {
            "type": "String",
            "metadata": {
                "description": "VM Resource ID."
            }
        },
        "VmssLocation": {
            "type": "String",
            "metadata": {
                "description": "The Virtual Machine Location."
            }
        },
        "osType": {
            "type": "String",
            "metadata": {
                "description": "OS Type, Example: Linux / Windows"
            }
        },
        "WorkspaceResourceId": {
            "type": "String",
            "metadata": {
                "description": "Workspace Resource ID."
            }
        }
    },
    "variables": {
        "VmssName": "[split(parameters('VmssResourceId'),'/')[8]]",
        "DaExtensionName": "[if(equals(toLower(parameters('osType')), 'windows'), 'DependencyAgentWindows', 'DependencyAgentLinux')]",
        "DaExtensionType": "[if(equals(toLower(parameters('osType')), 'windows'), 'DependencyAgentWindows', 'DependencyAgentLinux')]",
        "DaExtensionVersion": "9.5",
        "MmaExtensionName": "[if(equals(toLower(parameters('osType')), 'windows'), 'MMAExtension', 'OMSExtension')]",
        "MmaExtensionType": "[if(equals(toLower(parameters('osType')), 'windows'), 'MicrosoftMonitoringAgent', 'OmsAgentForLinux')]",
        "MmaExtensionVersion": "[if(equals(toLower(parameters('osType')), 'windows'), '1.0', '1.4')]"
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "apiVersion": "2018-10-01",
            "name": "[variables('VmssName')]",
            "location": "[parameters('VmssLocation')]",
            "resources": [
                {
                    "type": "extensions",
                    "apiVersion": "2018-10-01",
                    "name": "[variables('DaExtensionName')]",
                    "location": "[parameters('VmssLocation')]",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachineScaleSets/', variables('VmssName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                        "type": "[variables('DaExtensionType')]",
                        "typeHandlerVersion": "[variables('DaExtensionVersion')]",
                        "autoUpgradeMinorVersion": true
                    }
                },
                {
                    "type": "extensions",
                    "apiVersion": "2018-10-01",
                    "name": "[variables('MmaExtensionName')]",
                    "location": "[parameters('VmssLocation')]",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachineScaleSets/', variables('VmssName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "[variables('MmaExtensionType')]",
                        "typeHandlerVersion": "[variables('MmaExtensionVersion')]",
                        "autoUpgradeMinorVersion": "true",
                        "settings": {
                            "workspaceId": "[reference(parameters('WorkspaceResourceId'), '2015-03-20').customerId]",
                            "azureResourceId": "[parameters('VmssResourceId')]",
                            "stopOnMultipleConnections": "true"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(parameters('WorkspaceResourceId'), '2015-03-20').primarySharedKey]"
                        }
                    }
                }
            ]
        }
    ]
}
```

### <a name="parameter-file"></a>Arquivo de parâmetros

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "VmssResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-windows-vmss"
        },
        "VmssLocation": {
            "value": "westus"
        },
        "OsType": {
            "value": "windows"
        },
        "WorkspaceResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
        }
    }
}
```


## <a name="next-steps"></a>Passos seguintes

* [Obtenha outros modelos de amostra para Azure Monitor](../samples/resource-manager-samples.md).
* [Saiba mais sobre o Azure Monitor para VMs](../vm/vminsights-overview.md).
