---
title: Coletar métricas de VM do Windows em Azure Monitor com modelo
description: Enviar métricas do sistema operacional convidado para o armazenamento de métrica Azure Monitor usando um modelo do Resource Manager para uma máquina virtual do Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 05a05c46a49c0262bd5db33390bb995ebe849fd7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75364123"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Enviar métricas do sistema operacional convidado para o armazenamento de métrica Azure Monitor usando um modelo do Resource Manager para uma máquina virtual do Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Usando a extensão de [diagnóstico](diagnostics-extension-overview.md)de Azure monitor, você pode coletar métricas e logs do sistema operacional convidado (SO convidado) que está sendo executado como parte de uma máquina virtual, serviço de nuvem ou Cluster Service Fabric. A extensão pode enviar telemetria para [vários locais diferentes.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Este artigo descreve o processo de envio de métricas de desempenho do sistema operacional convidado para uma máquina virtual do Windows para o armazenamento de dados Azure Monitor. A partir da versão de diagnóstico 1,11, você pode gravar métricas diretamente no repositório de métricas de Azure Monitor, em que as métricas de plataforma padrão já são coletadas.

Armazená-los nesse local permite que você acesse as mesmas ações para métricas de plataforma. As ações incluem alertas quase em tempo real, criação de gráficos, roteamento e acesso de uma API REST e muito mais. No passado, a extensão de diagnóstico gravou no armazenamento do Azure, mas não no armazenamento de dados Azure Monitor.

Se você for novo nos modelos do Resource Manager, saiba mais sobre [implantações de modelo](../../azure-resource-manager/management/overview.md) e sua estrutura e sintaxe.

## <a name="prerequisites"></a>Pré-requisitos

- Sua assinatura deve ser registrada com [Microsoft. insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- Você precisa ter o [Azure PowerShell](/powershell/azure) ou [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) instalado.

- O recurso da VM deve estar em uma [região que ofereça suporte a métricas personalizadas](metrics-custom-overview.md#supported-regions). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurar Azure Monitor como um coletor de dados
A extensão Diagnóstico do Azure usa um recurso chamado "coletores de dados" para rotear métricas e logs para locais diferentes. As etapas a seguir mostram como usar um modelo do Resource Manager e o PowerShell para implantar uma VM usando o novo coletor de dados "Azure Monitor".

## <a name="author-resource-manager-template"></a>Criar modelo do Resource Manager
Para este exemplo, você pode usar um modelo de exemplo disponível publicamente. Os modelos de inicialização estão em https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows.

- **Azuredeploy. JSON** é um modelo pré-configurado do Resource Manager para a implantação de uma máquina virtual.

- **Azuredeploy. Parameters. JSON** é um arquivo de parâmetros que armazena informações como o nome de usuário e a senha que você deseja definir para a VM. Durante a implantação, o modelo do Resource Manager usa os parâmetros definidos neste arquivo.

Baixe e salve os dois arquivos localmente.

### <a name="modify-azuredeployparametersjson"></a>Modificar azuredeploy. Parameters. JSON
Abra o arquivo *azuredeploy. Parameters. JSON*

1. Insira valores para **adminUsername** e **ADMINPASSWORD** para a VM. Esses parâmetros são usados para acesso remoto à VM. Para evitar que sua VM seja seqüestrada, não use os valores neste modelo. Os bots examinam a Internet em busca de nomes de usuário e senhas em repositórios GitHub públicos. É provável que eles estejam testando VMs com esses padrões.

1. Crie um DnsName exclusivo para a VM.

### <a name="modify-azuredeployjson"></a>Modify azuredeploy.json

Abra o arquivo *azuredeploy. JSON*

Adicione uma ID de conta de armazenamento à seção de **variáveis** do modelo após a entrada para **storageAccountName.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Adicione essa extensão de Identidade de Serviço Gerenciada (MSI) ao modelo na parte superior da seção de **recursos** . A extensão garante que Azure Monitor aceita as métricas que estão sendo emitidas.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'), '/', 'WADExtensionSetup')]",
        "apiVersion": "2017-12-01",
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

Adicione a configuração de **identidade** ao recurso da VM para garantir que o Azure atribua uma identidade do sistema à extensão do MSI. Essa etapa garante que a VM possa emitir métricas de convidado sobre si mesma para Azure Monitor.

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

Adicione a configuração a seguir para habilitar a extensão de diagnóstico em uma máquina virtual do Windows. Para uma máquina virtual baseada no Gerenciador de recursos simples, podemos adicionar a configuração de extensão à matriz de recursos para a máquina virtual. A linha "Sinks"&mdash; "AzMonSink" e o "SinksConfig" correspondente posteriormente na seção&mdash;habilitar a extensão para emitir métricas diretamente para Azure Monitor. Sinta-se à vontade para adicionar ou remover contadores de desempenho conforme necessário.


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
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'), '/', 'Microsoft.Insights.VMDiagnosticsSettings')]",
            "apiVersion": "2017-12-01",
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


Salve e feche os dois arquivos.


## <a name="deploy-the-resource-manager-template"></a>Implantar o modelo do Resource Manager

> [!NOTE]
> Você deve estar executando a extensão de Diagnóstico do Azure versão 1,5 ou superior e ter a propriedade **autoUpgradeMinorVersion**: definida como ' true ' em seu modelo do Resource Manager. Em seguida, o Azure carrega a extensão apropriada quando inicia a VM. Se você não tiver essas configurações em seu modelo, altere-as e reimplante o modelo.


Para implantar o modelo do Resource Manager, aproveitamos Azure PowerShell.

1. Inicie o PowerShell.
1. Faça logon no Azure usando `Login-AzAccount`.
1. Obtenha sua lista de assinaturas usando `Get-AzSubscription`.
1. Defina a assinatura que você está usando para criar/atualizar a máquina virtual em:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Para criar um novo grupo de recursos para a VM que está sendo implantada, execute o seguinte comando:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Lembre-se de [usar uma região do Azure que está habilitada para métricas personalizadas](metrics-custom-overview.md).

1. Execute os comandos a seguir para implantar a VM usando o modelo do Resource Manager.
   > [!NOTE]
   > Se você quiser atualizar uma VM existente, basta adicionar o *modo incremental* ao final do comando a seguir.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Após a implantação ser realizada com sucesso, a VM deve estar na portal do Azure, emitindo métricas para Azure Monitor.

   > [!NOTE]
   > Você pode encontrar erros em volta do vmSkuSize selecionado. Se isso acontecer, volte para o arquivo azuredeploy. JSON e atualize o valor padrão do parâmetro vmSkuSize. Nesse caso, é recomendável tentar "Standard_DS1_v2").

## <a name="chart-your-metrics"></a>Gráfico de suas métricas

1. Inicie sessão no portal do Azure.

2. No menu à esquerda, selecione **Monitor**.

3. Na página monitorar, selecione **métricas**.

   ![Página de métricas](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Altere o período de agregação para os **últimos 30 minutos**.

5. No menu suspenso de recursos, selecione a VM que você criou. Se você não alterou o nome no modelo, ele deve ser *SimpleWinVM2*.

6. No menu suspenso namespaces, selecione **Azure. VM. Windows. Guest**

7. No menu suspenso métricas, selecione **memória\%bytes confirmados em uso**.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).

