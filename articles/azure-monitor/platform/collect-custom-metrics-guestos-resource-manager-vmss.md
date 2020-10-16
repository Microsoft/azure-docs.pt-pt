---
title: Colete métricas definidas de escala do Windows no Monitor Azure com o modelo
description: Envie métricas de SO para a loja métrica do Azure Monitor utilizando um modelo de Gestor de Recursos para um conjunto de escala de máquina virtual do Windows
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 76f73df01b34cb20be48aefa3b5b3a6392a35b8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87045196"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Envie métricas de SO para a loja métrica do Azure Monitor utilizando um modelo de Gestor de Recursos Azure para um conjunto de escala de máquina virtual Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ao utilizar a extensão Azure Monitor [Windows Azure Diagnostics (WAD),](diagnostics-extension-overview.md)pode recolher métricas e registos do sistema operativo convidado (OS convidado) que funciona como parte de uma máquina virtual, serviço em nuvem ou cluster Azure Service Fabric. A extensão pode enviar telemetria para vários locais diferentes listados no artigo anteriormente ligado.  

Este artigo descreve o processo de envio de métricas de desempenho do SO para um conjunto de escala de máquina virtual do Windows para a loja de dados do Azure Monitor. A partir da versão 1.11 do Windows Azure Diagnostics, é possível escrever métricas diretamente na loja de métricas do Azure Monitor, onde já são recolhidas métricas padrão da plataforma. Ao guardá-las neste local, pode aceder às mesmas ações que estão disponíveis para métricas da plataforma. As ações incluem alertas em tempo real, gráficos, encaminhamento, acesso a partir da API REST, e muito mais. No passado, a extensão do Windows Azure Diagnostics escreveu para o Azure Storage, mas não para a loja de dados do Azure Monitor.  

Se você é novo em modelos de Gestor de Recursos, aprenda sobre [implementações de modelos](../../azure-resource-manager/management/overview.md) e sua estrutura e sintaxe.  

## <a name="prerequisites"></a>Pré-requisitos

- A sua subscrição deve estar registada no [Microsoft.Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- Você precisa ter [Azure PowerShell](/powershell/azure) instalado, ou você pode usar [Azure Cloud Shell](../../cloud-shell/overview.md). 

- O seu recurso VM deve estar numa [região que suporte métricas personalizadas.](metrics-custom-overview.md#supported-regions)

## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurar o Azure Monitor como um sumidouro de dados 
A extensão Azure Diagnostics utiliza uma funcionalidade chamada **sumidouros de dados** para encaminhar métricas e registos para diferentes locais. Os passos a seguir mostram como utilizar um modelo de Gestor de Recursos e PowerShell para implantar um VM utilizando o novo lava-dados do Azure Monitor. 

## <a name="author-a-resource-manager-template"></a>Autor de um modelo de gestor de recursos 
Para este exemplo, pode utilizar um [modelo de amostra](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale)disponível ao público:  

- **Azuredeploy.jsé** um modelo pré-configurado de Gestor de Recursos para a implementação de um conjunto de escala de máquina virtual.

- **Azuredeploy.parameters.jsé** um ficheiro de parâmetros que armazena informações como o nome de utilizador e a palavra-passe que pretende definir para o seu VM. Durante a implementação, o modelo de Gestor de Recursos utiliza os parâmetros definidos neste ficheiro. 

Faça o download e guarde ambos os ficheiros localmente. 

###  <a name="modify-azuredeployparametersjson"></a>Modificar azuredeploy.parameters.jsem
Abra a **azuredeploy.parameters.jsno** ficheiro:  
 
- Forneça um **vmSKU** que pretende implementar. Recomendamos Standard_D2_v3. 
- Especifique uma **versão do windowsOS** Que deseja para o seu conjunto de escala de máquina virtual. Recomendamos o Centro de Dados de 2016. 
- Nomeie o recurso de conjunto de escala de máquina virtual a ser implantado utilizando uma propriedade **vmssName.** Um exemplo é **VMSS-WAD-TEST**.    
- Especifique o número de VMs que pretende executar na escala de máquina virtual definida utilizando a propriedade **instanceCount.**
- Introduza valores para **administradorUsername** e **adminPassword** para o conjunto de escala de máquina virtual. Estes parâmetros são utilizados para o acesso remoto aos VMs no conjunto de escala. Para evitar que o seu VM seja sequestrado, **não** utilize os deste modelo. Os bots digitalizam a internet para nomes de utilizadores e senhas em repositórios públicos do GitHub. É provável que estejam a testar VMs com estes defeitos. 


###  <a name="modify-azuredeployjson"></a>Modificar azuredeploy.jsem
Abra a **azuredeploy.jsarquivada.** 

Adicione uma variável para manter a informação da conta de armazenamento no modelo de Gestor de Recursos. Quaisquer registos ou contadores de desempenho especificados no ficheiro de configuração de diagnósticos são escritos tanto para a loja métrica do Azure Monitor como para a conta de armazenamento que especifica aqui: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Encontre a definição de escala de máquina virtual na secção de recursos e adicione a secção **de identidade** à configuração. Esta adição garante que a Azure lhe atribui uma identidade do sistema. Este passo também garante que os VMs no conjunto de escala podem emitir métricas de hóspedes sobre si mesmos para Azure Monitor:  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

No recurso de conjunto de escala de máquina virtual, encontre a secção **virtualMachineProfile.** Adicione um novo perfil chamado **extensõesProfile** para gerir extensões.  


Na **extensãoProfile,** adicione uma nova extensão ao modelo, como mostrado na secção **de extensão VMSS-WAD.**  Esta secção é a identidade gerida para a extensão de recursos Azure que garante que as métricas que estão a ser emitidas são aceites pelo Azure Monitor. O campo **de nomes** pode conter qualquer nome. 

O código a seguir da extensão MSI também adiciona a extensão e configuração de diagnóstico como um recurso de extensão ao recurso conjunto de escala de máquina virtual. Sinta-se livre para adicionar ou remover contadores de desempenho conforme necessário: 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Adicione um **dependsOn** para a conta de armazenamento para garantir que é criado na ordem correta: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Crie uma conta de armazenamento se não for já criada no modelo: 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Guarde e feche os dois ficheiros. 

## <a name="deploy-the-resource-manager-template"></a>Implementar o modelo de Gestor de Recursos 

> [!NOTE]  
> Tem de estar a executar a versão de extensão Azure Diagnostics 1.5 ou superior **e** ter a **versão autoUpgradeMinorVersion:** propriedade definida como **verdadeira** no seu modelo de Gestor de Recursos. Azure carrega então a extensão adequada quando inicia o VM. Se não tiver estas definições no seu modelo, altere-as e reimplante o modelo. 


Para implementar o modelo de Gestor de Recursos, utilize a Azure PowerShell:  

1. Inicie o PowerShell. 
1. Inscreva-se no Azure usando `Login-AzAccount` .
1. Obtenha a sua lista de subscrições `Get-AzSubscription` utilizando.
1. Desave a subscrição que irá criar ou atualize a máquina virtual: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Criar um novo grupo de recursos para o VM ser implantado. Execute o seguinte comando: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Lembre-se de usar uma região Azure que está habilitada para métricas personalizadas. Lembre-se de usar uma [região Azure que está habilitada para métricas personalizadas.](./metrics-custom-overview.md#supported-regions)
 
1. Executar os seguintes comandos para implantar o VM:  

   > [!NOTE]  
   > Se pretender atualizar um conjunto de escala existente, adicione **-Modo Incremental** ao fim do comando. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Depois de a sua implementação ter sucesso, deverá encontrar a escala de máquina virtual definida no portal Azure. Deve emitir métricas ao Monitor Azure. 

   > [!NOTE]  
   > Pode encontrar erros em torno do **vmSkuSize**selecionado. Nesse caso, volte ao seu **azuredeploy.jsno** ficheiro e atualize o valor padrão do parâmetro **vmSkuSize.** Recomendamos que tente **Standard_DS1_v2**. 


## <a name="chart-your-metrics"></a>Mapear as suas métricas 

1. Inicie sessão no Portal do Azure. 

1. No menu da esquerda, selecione **Monitor**. 

1. Na página **Monitor,** selecione **Métricas.** 

   ![Monitor - Página métrica](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Altere o período de agregação para **durar 30 minutos**.  

1. No menu suspenso de recursos, selecione o conjunto de escala de máquina virtual que criou.  

1. No menu drop-down dos espaços de nome, selecione **azure.vm.windows.guest**. 

1. No menu suspenso de métricas, selecione **Memory \% Committed Bytes in Use**.  

Em seguida, também pode optar por utilizar as dimensões desta métrica para cartografá-la para um determinado VM ou para traçar cada VM no conjunto de escala. 



## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [métricas personalizadas.](metrics-custom-overview.md)
