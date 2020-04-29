---
title: Recolher métricas vM do Windows no Monitor Azure com modelo
description: Envie métricas de SO para a loja métrica Do Monitor Azure usando um modelo de Gestor de Recursos para uma máquina virtual windows
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e747ca89912c36538bfb9d02986629fe57c5adcb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77657372"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Envie métricas de OS do Hóspede para a loja métrica Do Monitor Azure usando um modelo de Gestor de Recursos para uma máquina virtual do Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Utilizando a [extensão](diagnostics-extension-overview.md)de Diagnóstico do Monitor Azure, pode recolher métricas e registos do sistema operativo convidado (Guest OS) que está a funcionar como parte de uma máquina virtual, serviço de nuvem ou cluster de Tecido de Serviço. A extensão pode enviar telemetria para [muitos locais diferentes.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Este artigo descreve o processo de envio de métricas de desempenho do Guest OS para uma máquina virtual do Windows para a loja de dados Azure Monitor. A partir da versão 1.11 do Diagnostics, pode escrever métricas diretamente na loja de métricas Do Monitor Do Azure, onde as métricas padrão da plataforma já são recolhidas.

Armazená-las neste local permite-lhe aceder às mesmas ações para métricas da plataforma. As ações incluem alerta em tempo quase real, gráficos, encaminhamento e acesso a partir de uma API REST e muito mais. No passado, a extensão de Diagnóstico supor ao Armazenamento Azure, mas não à loja de dados Azure Monitor.

Se você é novo em modelos de Gestor de Recursos, aprenda sobre implementações de [modelos](../../azure-resource-manager/management/overview.md) e sua estrutura e sintaxe.

## <a name="prerequisites"></a>Pré-requisitos

- A sua subscrição deve ser registada na [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- É necessário instalar o [Azure PowerShell](/powershell/azure) ou o [Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview)

- O seu recurso VM deve estar numa [região que suporte métricas personalizadas.](metrics-custom-overview.md#supported-regions) 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurar o Monitor Azure como um sumidouro de dados
A extensão Azure Diagnostics utiliza uma funcionalidade chamada "data sinks" para encaminhar métricas e registos para diferentes locais. Os seguintes passos mostram como usar um modelo de Gestor de Recursos e PowerShell para implementar um VM utilizando o novo sumidouro de dados "Azure Monitor".

## <a name="author-resource-manager-template"></a>Modelo de Gestor de Recursos de Autor
Para este exemplo, pode utilizar um modelo de amostra disponível ao público. Os modelos de https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windowspartida estão em .

- **Azuredeploy.json** é um modelo de Gestor de Recursos reconfigurado para a implantação de uma máquina virtual.

- **Azuredeploy.parímetros.json** é um ficheiro de parâmetros que armazena informações como o nome de utilizador e a palavra-passe que gostaria de definir para o seu VM. Durante a implementação, o modelo do Gestor de Recursos utiliza os parâmetros definidos neste ficheiro.

Descarregue e guarde ambos os ficheiros localmente.

### <a name="modify-azuredeployparametersjson"></a>Modificar azuredeploy.parameters.json
Abra o ficheiro *azuredeploy.parameters.json*

1. Introduza valores para **administradorUsername** e **administradorPassword** para o VM. Estes parâmetros são utilizados para o acesso remoto ao VM. Para evitar que o seu VM seja sequestrado, NÃO utilize os valores deste modelo. Os bots digitalizam a internet para saber nomes de utilizadores e palavras-passe em repositórios públicos do GitHub. É provável que estejam a testar VMs com estes incumprimentos.

1. Crie um nome único para o VM.

### <a name="modify-azuredeployjson"></a>Modificar azuredeploy.json

Abra o ficheiro *azuredeploy.json*

Adicione um ID de conta de armazenamento à secção **variáveis** do modelo após a entrada para **armazenamentoAccountName.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Adicione esta extensão de Identidade de Serviço Gerida (MSI) ao modelo no topo da secção de **recursos.** A extensão garante que o Monitor Azure aceita as métricas que estão a ser emitidas.

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

Adicione a configuração **de identidade** ao recurso VM para garantir que o Azure atribui uma identidade de sistema à extensão MSI. Este passo garante que o VM pode emitir métricas de hóspedes sobre si mesmo para o Monitor Azure.

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

Adicione a seguinte configuração para ativar a extensão de Diagnóstico numa máquina virtual do Windows. Para uma simples máquina virtual baseada em Recursos, podemos adicionar a configuração de extensão à matriz de recursos para a máquina virtual. A linha "afunda"&mdash; "AzMonSink" e a correspondente "SinksConfig" mais tarde na secção&mdash;permitem a extensão de emitir métricas diretamente para o Monitor Azure. Sinta-se à vontade para adicionar ou remover os contadores de desempenho conforme necessário.


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


Guarde e feche os dois ficheiros.


## <a name="deploy-the-resource-manager-template"></a>Implementar o modelo de Gestor de Recursos

> [!NOTE]
> Deve estar a executar a versão de extensão de Diagnóstico Sinuoso Azure 1.5 ou superior e ter a **versão automáticaUpgradeMinorVersion**: propriedade definida para 'verdadeira' no seu modelo de Gestor de Recursos. Azure então carrega a extensão adequada quando inicia o VM. Se não tiver estas definições no seu modelo, altere-as e recoloque o modelo.


Para implementar o modelo de Gestor de Recursos, aproveitamos o Azure PowerShell.

1. Inicie o PowerShell.
1. Faça login no `Login-AzAccount`Azure utilizando .
1. Obtenha a sua lista `Get-AzSubscription`de subscrições utilizando .
1. Detete a subscrição que está a usar para criar/atualizar a máquina virtual em:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Para criar um novo grupo de recursos para o VM que está a ser implantado, executar o seguinte comando:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Lembre-se de [utilizar uma região azure que esteja ativada para métricas personalizadas](metrics-custom-overview.md).

1. Executar os seguintes comandos para implantar o VM utilizando o modelo de Gestor de Recursos.
   > [!NOTE]
   > Se desejar atualizar um VM existente, basta adicionar *-Modo Incremental* ao fim do seguinte comando.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Após o sucesso da sua implantação, o VM deve estar no portal Azure, emitindo métricas ao Monitor Azure.

   > [!NOTE]
   > Pode ter erros em torno do vmSkuSize selecionado. Se isto acontecer, volte ao ficheiro azuredeploy.json e atualize o valor padrão do parâmetro vmSkuSize. Neste caso, recomendamos tentar "Standard_DS1_v2").

## <a name="chart-your-metrics"></a>Mapeie as suas métricas

1. Inicie sessão no portal do Azure.

2. No menu esquerdo, selecione **Monitor**.

3. Na página Monitor, selecione **Métricas**.

   ![Página de métricas](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Mude o período de agregação para **30 minutos**.

5. No menu de desistência de recursos, selecione o VM que criou. Se não alterou o nome no modelo, deve ser *SimpleWinVM2*.

6. No menu de lançamento dos espaços de nome, **selecione azure.vm.windows.windows.guest**

7. No menu de descida de métricas, selecione **Memory\%Committed Bytes in Use**.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [métricas personalizadas.](metrics-custom-overview.md)

