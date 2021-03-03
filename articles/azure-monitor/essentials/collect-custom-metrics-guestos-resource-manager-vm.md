---
title: Colete métricas de VM do Windows no Monitor Azure com modelo
description: Envie métricas de SO para a loja de base de dados métrica do Azure Monitor utilizando um modelo de Gestor de Recursos para uma máquina virtual windows
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: bwren
ms.subservice: metrics
ms.openlocfilehash: 0051ec15cafc60af6a0523631f77a9b52294e69c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731719"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine"></a>Envie métricas de SO para a loja métrica do Azure Monitor utilizando um modelo de Gestor de Recursos Azure para uma máquina virtual Windows
Os dados de desempenho do os hóspedes AS das máquinas virtuais Azure não são recolhidos automaticamente como [outras métricas da plataforma.](./monitor-azure-resource.md#monitoring-data) Instale a [extensão](../agents/diagnostics-extension-overview.md) de diagnóstico do Azure Monitor para recolher as métricas de SO dos hóspedes na base de dados de métricas para que possa ser utilizada com todas as funcionalidades de Azure Monitor Metrics, incluindo alerta em tempo real, gráfico, encaminhamento e acesso a partir de uma API REST. Este artigo descreve o processo de envio de métricas de desempenho do Guest OS para uma máquina virtual do Windows para a base de dados de métricas usando um modelo de Gestor de Recursos. 

> [!NOTE]
> Para obter mais informações sobre a configuração da extensão de diagnóstico para recolher as métricas de SO dos hóspedes utilizando o portal Azure, consulte [instalar e configurar a extensão de diagnóstico do Windows Azure (WAD)](../agents/diagnostics-extension-windows-install.md).


Se você é novo em modelos de Gestor de Recursos, aprenda sobre [implementações de modelos](../../azure-resource-manager/management/overview.md) e sua estrutura e sintaxe.

## <a name="prerequisites"></a>Pré-requisitos

- A sua subscrição deve estar registada no [Microsoft.Insights](../../azure-resource-manager/management/resource-providers-and-types.md).

- Precisa de ter [a Azure PowerShell](/powershell/azure) ou [a Azure Cloud Shell](../../cloud-shell/overview.md) instaladas.

- O seu recurso VM deve estar numa [região que suporte métricas personalizadas.](./metrics-custom-overview.md#supported-regions) 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurar o Azure Monitor como um sumidouro de dados
A extensão Azure Diagnostics utiliza uma funcionalidade chamada "sumidouros de dados" para encaminhar métricas e registos para diferentes locais. Os passos a seguir mostram como utilizar um modelo de Gestor de Recursos e PowerShell para implantar um VM utilizando o novo lava-dados "Azure Monitor".

## <a name="author-resource-manager-template"></a>Modelo de gestor de recursos de autor
Para este exemplo, pode utilizar um modelo de amostra disponível ao público. Os modelos de partida estão em https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows .

- **Azuredeploy.jsé** um modelo pré-configurado de Gestor de Recursos para a implementação de uma máquina virtual.

- **Azuredeploy.parameters.jsé** um ficheiro de parâmetros que armazena informações como o nome de utilizador e a palavra-passe que gostaria de definir para o seu VM. Durante a implementação, o modelo de Gestor de Recursos utiliza os parâmetros definidos neste ficheiro.

Faça o download e guarde ambos os ficheiros localmente.

### <a name="modify-azuredeployparametersjson"></a>Modificar azuredeploy.parameters.jsem
Abra a *azuredeploy.parameters.jsno* arquivo

1. Introduza valores para **administradorUsername** e **adminPassword** para o VM. Estes parâmetros são utilizados para o acesso remoto ao VM. Para evitar que o seu VM seja sequestrado, NÃO utilize os valores deste modelo. Os bots digitalizam a internet para obter nomes de utilizadores e palavras-passe em repositórios públicos do GitHub. É provável que estejam a testar VMs com estes padrão.

1. Crie um nome de dnsname único para o VM.

### <a name="modify-azuredeployjson"></a>Modificar azuredeploy.jsem

Abra a *azuredeploy.jsno* arquivo

Adicione um ID de conta de armazenamento à secção de **variáveis** do modelo após a entrada para **armazenamentoName.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Adicione esta extensão de Identidade de Serviço Gerido (MSI) ao modelo no topo da secção de **recursos.** A extensão garante que o Azure Monitor aceita as métricas que estão a ser emitidas.

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

Adicione a configuração **de identidade** ao recurso VM para garantir que o Azure atribui uma identidade do sistema à extensão MSI. Este passo garante que o VM pode emitir métricas de hóspedes sobre si mesmo para o Azure Monitor.

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

Adicione a seguinte configuração para ativar a extensão de Diagnóstico numa máquina virtual Do Windows. Para uma simples máquina virtual baseada em Gestor de Recursos, podemos adicionar a configuração de extensão à matriz de recursos para a máquina virtual. A linha "afunda" &mdash; "AzMonSink" e o correspondente "SinksConfig" mais tarde na secção &mdash; permitem que a extensão emita métricas diretamente ao Monitor Azure. Sinta-se livre para adicionar ou remover contadores de desempenho conforme necessário.


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
> Tem de estar a executar a versão de extensão Azure Diagnostics 1.5 ou superior e tem a **versão autoUpgradeMinorVersion**: propriedade definida como 'verdadeira' no seu modelo de Gestor de Recursos. Azure carrega então a extensão adequada quando inicia o VM. Se não tiver estas definições no seu modelo, altere-as e reimplante o modelo.


Para implementar o modelo de Gestor de Recursos, aproveitamos o Azure PowerShell.

1. Inicie o PowerShell.
1. Faça login no Azure utilizando `Login-AzAccount` .
1. Obtenha a sua lista de subscrições `Get-AzSubscription` utilizando.
1. Desave a subscrição que está a utilizar para criar/atualizar a máquina virtual em:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Para criar um novo grupo de recursos para o VM que está a ser implantado, executar o seguinte comando:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Lembre-se de [utilizar uma região Azure que está ativada para métricas personalizadas](./metrics-custom-overview.md).

1. Executar os seguintes comandos para implantar o VM utilizando o modelo de Gestor de Recursos.
   > [!NOTE]
   > Se desejar atualizar um VM existente, basta adicionar *-Modo Incremental* ao fim do seguinte comando.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Após a sua implantação ter sucesso, o VM deve estar no portal Azure, emitindo métricas para o Azure Monitor.

   > [!NOTE]
   > Pode encontrar erros em torno do vmSkuSize selecionado. Se isso acontecer, volte ao seu azuredeploy.jsno ficheiro e atualize o valor padrão do parâmetro vmSkuSize. Neste caso, recomendamos tentar "Standard_DS1_v2").

## <a name="chart-your-metrics"></a>Mapear as suas métricas

1. Inicie sessão no portal do Azure.

2. No menu esquerdo, selecione **Monitor**.

3. Na página Monitor, selecione **Métricas.**

   ![Página de métricas](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Altere o período de agregação para **durar 30 minutos**.

5. No menu suspenso de recursos, selecione o VM que criou. Se não alterou o nome no modelo, deve ser *SimpleWinVM2*.

6. No menu drop-down dos espaços de nome, selecione **azure.vm.windows.guest**

7. No menu de entrega de métricas, selecione **Memory \% Committed Bytes in Use**.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [métricas personalizadas.](./metrics-custom-overview.md)