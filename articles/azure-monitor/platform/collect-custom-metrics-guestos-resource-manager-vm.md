---
title: Enviar métricas de SO convidado para a métrica do Azure Monitor armazenam utilizando um modelo do Resource Manager para uma máquina virtual do Windows
description: Enviar métricas de SO convidado para a métrica do Azure Monitor armazenam utilizando um modelo do Resource Manager para uma máquina virtual do Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 63b134ab9bfdac3617c845da7a14ee6b9234c84d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57782025"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Enviar métricas de SO convidado para a métrica do Azure Monitor armazenam utilizando um modelo do Resource Manager para uma máquina virtual do Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ao utilizar o Azure Monitor [extensão de diagnóstico](diagnostics-extension-overview.md), pode coletar métricas e registos do sistema de operativo convidado (SO convidado) que está a ser executado como parte de uma máquina virtual, um serviço em nuvem ou um cluster do Service Fabric. A extensão pode enviar telemetria para [vários locais diferentes.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Este artigo descreve o processo para envio de métricas de desempenho do SO convidado para uma máquina virtual do Windows para o arquivo de dados do Azure Monitor. A partir do diagnóstico versão 1.11, pode escrever métricas diretamente para o Azure Monitor armazenam métricas, onde as métricas de plataforma padrão já são recolhidas.

Armazená-los nesta localização permite que acesse as mesmas ações para métricas de plataforma. Ações incluem quase em tempo real alerta, a criação de gráficos, encaminhamento e acederem a partir de uma API REST e muito mais. No passado, a extensão de diagnóstico escreveu para o armazenamento do Azure, mas não para o arquivo de dados do Azure Monitor.

Se estiver familiarizado com modelos do Resource Manager, saiba mais sobre [implementações de modelo](../../azure-resource-manager/resource-group-overview.md) e sua estrutura e a sintaxe.

## <a name="prerequisites"></a>Pré-requisitos

- A sua subscrição tem de estar registrada com [Microsoft. insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- Tem de ter [do Azure PowerShell](/powershell/azure) ou [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) instalado.


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurar o Azure Monitor como um sink de dados
A extensão de diagnóstico do Azure utiliza um recurso chamado "Coletores de dados" para a métrica das rotas e registos para localizações diferentes. Os passos seguintes mostram como utilizar um modelo do Resource Manager e o PowerShell para implementar uma VM com o novo sink de dados de "Do Azure Monitor".

## <a name="author-resource-manager-template"></a>Modelo de Gestor de recursos do autor
Neste exemplo, pode utilizar um modelo de exemplo disponíveis publicamente. Os modelos de partida correm https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows.

- **Azuredeploy. JSON** é um modelo do Resource Manager pré-configurada para a implementação de uma máquina virtual.

- **Azuredeploy** é um ficheiro de parâmetros que armazena informações como o nome de utilizador e palavra-passe que pretende definir para a sua VM. Durante a implementação, o modelo do Resource Manager utiliza os parâmetros que estão definidos neste ficheiro.

Transfira e guarde ambos os ficheiros localmente.

### <a name="modify-azuredeployparametersjson"></a>Modify azuredeploy.parameters.json
Abra o *azuredeploy* ficheiro

1. Introduza os valores para **adminUsername** e **adminPassword** para a VM. Esses parâmetros são usados para acesso remoto à VM. Para evitar que a sua VM seqüestrada, não use os valores neste modelo. A internet para nomes de utilizador e palavras-passe em repositórios de GitHub públicos de análise de bots. É provável que realizar testes VMs com esses padrões.

1. Crie um dnsname exclusivo para a VM.

### <a name="modify-azuredeployjson"></a>Modify azuredeploy.json

Abra o *azuredeploy. JSON* ficheiro

Adicionar um ID de conta de armazenamento para o **variáveis** secção do modelo após a entrada para **storageAccountName.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Adicione esta extensão de identidade de serviço gerida (MSI) para o modelo na parte superior a **recursos** secção. A extensão garante que o Azure Monitor aceita as métricas que estão a ser emitidas.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "WADExtensionSetup",
        "apiVersion": "2015-05-01-preview",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    },
```

Adicionar a **identidade** configuração para o recurso VM para se certificar de que o Azure atribui uma identidade de sistema para a extensão MSI. Este passo garante que a VM pode emitir as métricas de convidado sobre si próprio para o Azure Monitor.

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    // add these 3 lines below
    "identity": {
    "type": "SystemAssigned"
    },
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {
    ...
```

Adicione a seguinte configuração para ativar a extensão de diagnóstico numa máquina virtual do Windows. Para uma simple baseado no Resource Manager máquina virtual, podemos adicionar a configuração da extensão para a matriz de recursos para a máquina virtual. A linha "Coletores de"&mdash; "AzMonSink" e o "SinksConfig" correspondente mais tarde na seção&mdash;ativar a extensão emitir métricas diretamente para o Azure Monitor. Fique à vontade adicionar ou remover contadores de desempenho, conforme necessário.


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]"
    }
}
},
//Start of section to add
"resources": [
{
            "type": "extensions",
            "name": "Microsoft.Insights.VMDiagnosticsSettings",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.12",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "WadCfg": {
                "DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
        },
                    "Directories": {
                    "scheduledTransferPeriod": "PT1M",
    "IISLogs": {
                        "containerName": "wad-iis-logfiles"
                    },
                    "FailedRequestLogs": {
                        "containerName": "wad-failedrequestlogs"
                    }
                    },
                    "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "sinks": "AzMonSink",
                    "PerformanceCounterConfiguration": [
                        {
                        "counterSpecifier": "\\Memory\\Available Bytes",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\Committed Bytes",
                        "sampleRate": "PT15S"
                        }
                    ]
                    },
                    "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                        {
                        "name": "Application!*"
                        }
                    ]
                    },
                    "Logs": {
                    "scheduledTransferPeriod": "PT1M",
                    "scheduledTransferLogLevelFilter": "Error"
                    }
                },
                "SinksConfig": {
                    "Sink": [
                    {
                        "name" : "AzMonSink",
                        "AzureMonitor" : {}
                    }
                    ]
                }
                },
                "StorageAccount": "[variables('storageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[variables('storageAccountName')]",
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]",
                "storageAccountEndPoint": "https://core.windows.net/"
            }
            }
        }
        ]
//End of section to add
```


Guarde e feche ambos os ficheiros.


## <a name="deploy-the-resource-manager-template"></a>Implementar o modelo do Resource Manager

> [!NOTE]
> Tem de estar a executar a versão da extensão de diagnóstico do Azure versão 1.5 ou posterior e tenha as **autoUpgradeMinorVersion**: propriedade definida como 'true' no modelo do Resource Manager. Azure, em seguida, carrega a extensão correta quando iniciar a VM. Se não tiver estas definições no seu modelo, alterá-los e Reimplementar o modelo.


Para implementar o modelo do Resource Manager, podemos tirar partido do Azure PowerShell.

1. Inicie o PowerShell.
1. Inicie sessão no Azure com `Login-AzAccount`.
1. Obter a lista de subscrições com `Get-AzSubscription`.
1. Defina a subscrição que está a utilizar para criar ou atualizar a máquina virtual em:

   ```PowerShell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Para criar um novo grupo de recursos para a VM que está a ser implementada, execute o seguinte comando:

   ```PowerShell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Lembre-se [utilizam uma região do Azure que está ativada para métricas personalizadas](metrics-custom-overview.md).

1. Execute os seguintes comandos para implementar a VM com o modelo do Resource Manager.
   > [!NOTE]
   > Se desejar atualizar de uma VM existente, basta adicionar *-modo Incremental* ao final do comando seguinte.

   ```PowerShell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Depois de sua implementação com êxito, deve ser a VM no portal do Azure, emitindo métricas para o Azure Monitor.

   > [!NOTE]
   > Podem ocorrer erros em todo o vmSkuSize selecionado. Se isto acontecer, regresse ao seu ficheiro azuredeploy JSON e atualize o valor predefinido do parâmetro vmSkuSize. Neste caso, é recomendável tentar "Standard_DS1_v2").

## <a name="chart-your-metrics"></a>As suas métricas do gráfico

1. Inicie sessão no portal do Azure.

2. No menu da esquerda, selecione **Monitor**.

3. Na página do Monitor, selecione **métricas**.

   ![Página de métricas](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Alterar o período de agregação para **últimos 30 minutos**.

5. No menu de lista pendente de recursos, selecione a VM que criou. Se não alterar o nome do modelo, deve ser *SimpleWinVM2*.

6. No menu de lista pendente de espaços de nomes, selecione **azure.vm.windows.guest**

7. No menu pendente métricas, selecione **memória\%Bytes dedicados em utilização**.


## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).

