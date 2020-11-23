---
title: Ativar o Azure Monitor para a saúde dos hóspedes VMs (pré-visualização)
description: Descreve como ativar o Azure Monitor para a saúde dos hóspedes em VMs na sua subscrição e como embarcar em VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/16/2020
ms.openlocfilehash: 647256949d1f8f13439a0a5db87f3b02d697d32b
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95318138"
---
# <a name="enable-azure-monitor-for-vms-guest-health-preview"></a>Ativar o Azure Monitor para a saúde dos hóspedes VMs (pré-visualização)
O Azure Monitor para a saúde dos hóspedes em VMs permite-lhe visualizar a saúde de uma máquina virtual, tal como definida por um conjunto de medições de desempenho que são amostradas a intervalos regulares. Este artigo descreve como ativar esta funcionalidade na sua subscrição e como ativar a monitorização do hóspede para cada máquina virtual.

## <a name="current-limitations"></a>Limitações atuais
O Azure Monitor para a saúde dos hóspedes VMs tem as seguintes limitações na pré-visualização pública:

- Apenas máquinas virtuais Azure são suportadas atualmente. Azure Arc para servidores não está suportada atualmente.
- A Máquina Virtual deve executar um dos seguintes sistemas operativos: 
  - Ubuntu 16.04 LTS, Ubuntu 18.04 LTS
  - Windows Server 2012 ou posterior
- A máquina virtual deve estar localizada numa das seguintes regiões:
  - Leste da Austrália
  - Sudeste da Austrália
  - Índia Central
  - E.U.A. Central
  - E.U.A. Leste
  - E.U.A. Leste 2
  - Leste DOS EUA 2
  - Alemanha Centro-Oeste
  - Leste do Japão
  - E.U.A. Centro-Norte
  - Europa do Norte
  - E.U.A. Centro-Sul
  - Sudeste Asiático
  - Sul do Reino Unido
  - Europa Ocidental
  - E.U.A. Oeste
  - E.U.A. Oeste 2
- O espaço de trabalho log Analytics deve estar localizado numa das seguintes regiões:
  - E.U.A. Leste
  - Leste DOS EUA 2
  - Região da Europa Ocidental

## <a name="prerequisites"></a>Pré-requisitos

- A máquina virtual deve ser a bordo do Azure Monitor para VMs.
- A execução dos passos de embarque do utilizador deve ter um acesso mínimo ao nível do Contribuinte à subscrição onde está localizada a máquina virtual e a regra de recolha de dados.
- Os fornecedores de recursos Azure necessários devem estar registados como descrito na secção seguinte.


## <a name="register-required-azure-resource-providers"></a>Registar os fornecedores de recursos Azure necessários
Os seguintes fornecedores de recursos Azure precisam de se registar para a sua subscrição para permitir a Saúde do Hóspede do Azure Monitor para a saúde dos hóspedes. 

- Microsoft.WorkloadMonitor
- Microsoft.Insights

Pode utilizar qualquer um dos métodos disponíveis para registar um fornecedor de recursos, conforme descrito nos [fornecedores e tipos de recursos Azure](../../azure-resource-manager/management/resource-providers-and-types.md). Também pode utilizar o seguinte comando de amostra usando o braço, carteiro ou outro método para fazer uma chamada autenticada para O Gestor de Recursos Azure:

```
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.WorkloadMonitor/register?api-version=2019-10-01
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.Insights/register?api-version=2019-10-01
```


## <a name="enable-a-virtual-machine-using-the-azure-portal"></a>Ativar uma máquina virtual utilizando o portal Azure
Quando ativa a saúde dos hóspedes para uma máquina virtual no portal Azure, todas as configurações necessárias são realizadas para si. Isto inclui a criação da regra de recolha de dados requere, a instalação da extensão de saúde do hóspede na máquina virtual e a criação de uma associação com a regra de recolha de dados.

A partir da vista **Get Started** no Monitor Azure para VMs, clique no link ao lado da mensagem de atualização para uma máquina virtual e, em seguida, clique no botão **Deabos.** Também pode selecionar várias máquinas virtuais para as atualizar em conjunto.

![Ativar a funcionalidade de saúde na máquina virtual](media/vminsights-health-enable/enable-agent.png)


## <a name="enable-a-virtual-machine-using-resource-manager-template"></a>Ativar uma máquina virtual usando o modelo de Gestor de Recursos
São necessários três passos para permitir a utilização de máquinas virtuais utilizando o Gestor de Recursos Azure.

- Criar regra de recolha de dados.
- Instale a extensão de saúde do hóspede em cada máquina virtual
- Criar uma associação entre a máquina virtual e a regra de recolha de dados.

### <a name="create-data-collection-rule-dcr"></a>Criar regra de recolha de dados (DCR)

> [!NOTE]
> Se ativar uma máquina virtual utilizando o portal Azure, a regra de recolha de dados descrita aqui é criada para si. Neste caso, não precisa de executar este passo.

A configuração para os monitores no Azure Monitor para a saúde dos hóspedes VMs é armazenada nas [Regras de Recolha de Dados (DCR)](../platform/data-collection-rule-overview.md). Instale a regra de recolha de dados definida no modelo de Gestor de Recursos abaixo para permitir que todos os monitores para as máquinas virtuais com a extensão de saúde do hóspede. Cada máquina virtual com a extensão de saúde do hóspede necessitará de uma associação com esta regra.

> [!NOTE]
> Pode criar regras adicionais de recolha de dados para modificar a configuração padrão dos monitores, tal como descrito na [monitorização configure no Azure Monitor para a saúde dos hóspedes VMs (pré-visualização)](vminsights-health-configure.md).

O modelo requer valores para os seguintes parâmetros:

- **padrãoHealthDataCollectionRuleName**: Mantenha o nome predefinido definido no modelo.
- **destinationWorkspaceResourceId**: ID de recursos do espaço de trabalho Log Analytics utilizado para a recolha de dados de máquinas virtuais.
- **dataCollectionRuleLocation**: Região da regra de recolha de dados. Isto deve coincidir com a região do log analytics espaço de trabalho.


Implemente o modelo utilizando qualquer [método de implementação para modelos de Gestor de Recursos](../../azure-resource-manager/templates/deploy-powershell.md). Os seguintes comandos implementam o ficheiro de modelo e parâmetros utilizando o PowerShell ou o Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDataCollectionRule -ResourceGroupName my-resource-group -TemplateFile Health.DataCollectionRule.template.json -TemplateParameterFile Health.DataCollectionRule.template.parameters.json
```

# <a name="cli"></a>[CLI](#tab/cli)

```cli
az deployment group create --name GuestHealthDataCollectionRule --resource-group my-resource-group --template-file Health.DataCollectionRule.template.json --parameters Health.DataCollectionRule.template.parameters.json
```

---



```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data colleciton rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              }
            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

### <a name="sample-parameter-file"></a>Arquivo de parâmetros de amostra

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "dataCollectionRuleLocation": {
        "value": "eastus"
      }
  }
}
```



## <a name="install-guest-health-extension-and-associate-with-data-collection-rule"></a>Instale a extensão de saúde dos hóspedes e associe-se à regra da recolha de dados
Utilize o seguinte modelo de Gestor de Recursos para ativar uma máquina virtual para a saúde do hóspede. Isto instala a extensão de saúde do hóspede e cria a associação com a regra de recolha de dados. Pode implementar este modelo utilizando qualquer [método de implementação para modelos de Gestor de Recursos](../../azure-resource-manager/templates/deploy-powershell.md).


Por exemplo, utilize os seguintes comandos para implantar o ficheiro de modelo e parâmetros para um grupo de recursos utilizando o PowerShell ou o Azure CLI.


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

# <a name="cli"></a>[CLI](#tab/cli)

```cli
az deployment group create --name GuestHealthDeployment --resource-group my-resource-group --template-file Health.VirtualMachine.template.json --parameters Health.VirtualMachine.template.parameters.json
```

---

### <a name="template-file"></a>Arquivo de modelo

``` json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualMachineName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the virtual machine."
      }
    },
    "virtualMachineLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code of the virtual machine region (location). Examples: eastus, westeurope, etc"
      }
    },
    "virtualMachineOsType": {
      "type": "string",
      "metadata": {
        "description": "Specifies operating system type of the target virtual machine."
      },
      "allowedValues": ["windows", "linux"]
    },
    "dataCollectionRuleAssociationName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule association to create."
      },
      "defaultValue": "VM-Health-Dcr-Association"
    },
    "healthDataCollectionRuleResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies resource id of Azure Monitor Data Collection Rule for virtual machine health data."
      }
    }
  },
  "variables": {
    "healthExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthWindowsAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthLinuxAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      }
    },
    "azureMonitorAgentExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorWindowsAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": false 
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorLinuxAgent", 
        "typeHandlerVersion": "1.5", 
        "autoUpgradeMinorVersion": false 
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('virtualMachineName')]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2017-03-30",
      "identity": {
        "type": "SystemAssigned"
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
      "name": "[concat(parameters('virtualMachineName'), '/Microsoft.Insights/', parameters('dataCollectionRuleAssociationName'))]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Association of data collection rule for VM Insights Health.",
        "dataCollectionRuleId": "[parameters('healthDataCollectionRuleResourceId')]"
      }
    },
    { 
      "type": "Microsoft.Compute/virtualMachines/extensions", 
      "apiVersion": "2019-12-01", 
      "name": "[concat(parameters('virtualMachineName'), '/', variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ], 
      "properties": "[variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')]]"
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'), '/', variables('healthExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2018-06-01",
      "dependsOn": [ 
        "[resourceId('Microsoft.Compute/virtualMachines/extensions', parameters('virtualMachineName'), variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ],    
      "properties": "[variables('healthExtensionProperties')[parameters('virtualMachineOsType')]]"
    }               
  ]
}
```

### <a name="sample-parameter-file"></a>Arquivo de parâmetros de amostra

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualMachineName": {
        "value": "myvm"
      },
      "virtualMachineLocation": {
        "value": "eastus"
      },
      "virtualMachineOsType": {
        "value": "linux"
      },
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/dataCollectionRules/Microsoft-VMInsights-Health"
      },
      "healthExtensionVersion": {
        "value": "private-preview"
      }
  }
}
```

## <a name="next-steps"></a>Próximos passos

- [Personalizar monitores ativados pelo Azure Monitor para VMs](vminsights-health-configure.md)