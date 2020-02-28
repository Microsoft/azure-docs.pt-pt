---
title: Colete métricas de conjunto de escala do Windows no Monitor Azure com modelo
description: Envie métricas de SO para a loja métrica Do Monitor Azure utilizando um modelo de Gestor de Recursos para um conjunto de escala de máquina virtual do Windows
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 24f83e4f6285d045e67bdaef431ebcff2345ef84
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663901"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Envie métricas de SO para a loja métrica Do Monitor Azure utilizando um modelo de Gestor de Recursos Azure para um conjunto de escala de máquina virtual windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Utilizando a extensão De diagnóstico do Azure Monitor [Windows Azure (WAD),](diagnostics-extension-overview.md)pode recolher métricas e registos do sistema operativo convidado (OS convidado) que funciona como parte de uma máquina virtual, serviço na nuvem ou cluster Azure Service Fabric. A extensão pode enviar telemetria para muitos locais diferentes listados no artigo anteriormente ligado.  

Este artigo descreve o processo de envio de métricas de desempenho do OS dos hóspedes para uma escala virtual do Windows definida para a loja de dados Azure Monitor. A partir da versão 1.11 do Windows Azure Diagnostics, pode escrever métricas diretamente na loja de métricas Do Monitor Do Azure, onde as métricas padrão da plataforma já são recolhidas. Ao armazená-los neste local, pode aceder às mesmas ações que estão disponíveis para métricas da plataforma. As ações incluem alerta, gráfico, encaminhamento, acesso da API REST, e muito mais. No passado, a extensão do Windows Azure Diagnostics escreveu ao Azure Storage, mas não à loja de dados Azure Monitor.  

Se você é novo em modelos de Gestor de Recursos, aprenda sobre implementações de [modelos](../../azure-resource-manager/management/overview.md) e sua estrutura e sintaxe.  

## <a name="prerequisites"></a>Pré-requisitos

- A sua subscrição deve ser registada na [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Precisa de ter o [Azure PowerShell](/powershell/azure) instalado, ou pode utilizar a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). 

- O seu recurso VM deve estar numa [região que suporte métricas personalizadas.](metrics-custom-overview.md#supported-regions)

## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurar o Monitor Azure como um sumidouro de dados 
A extensão Azure Diagnostics utiliza uma funcionalidade chamada **data sinks** para encaminhar métricas e registos para diferentes locais. Os seguintes passos mostram como usar um modelo de Gestor de Recursos e PowerShell para implementar um VM utilizando o novo sumidouro de dados do Monitor Azure. 

## <a name="author-a-resource-manager-template"></a>Autor um modelo de Gestor de Recursos 
Para este exemplo, pode utilizar um modelo de [amostra](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale)disponível ao público:  

- **Azuredeploy.json** é um modelo de Gestor de Recursos reconfigurado para implementação de um conjunto de escala de máquina virtual.

- **Azuredeploy.parímetros.json** é um ficheiro de parâmetros que armazena informações como o nome de utilizador e senha que pretende definir para o seu VM. Durante a implementação, o modelo do Gestor de Recursos utiliza os parâmetros definidos neste ficheiro. 

Descarregue e guarde ambos os ficheiros localmente. 

###  <a name="modify-azuredeployparametersjson"></a>Modificar azuredeploy.parameters.json
Abra o ficheiro **azuredeploy.parâmetros.json:**  
 
- Forneça um **vmSKU** que pretende implementar. Recomendamos Standard_D2_v3. 
- Especifique um **windowsOSVersão** que deseja para o seu conjunto de escala de máquina virtual. Recomendamos 2016-Datacenter. 
- Nomeie o recurso conjunto de conjunto de escala de máquina virtual a ser implantado utilizando uma propriedade **vmssName.** Um exemplo é **VMSS-WAD-TEST**.    
- Especifique o número de VMs que pretende executar na escala de máquina virtual definida utilizando a propriedade **do Count.**
- Introduza valores para **administradorUsername** e **administradorPassword** para o conjunto de escala de máquina virtual. Estes parâmetros são utilizados para o acesso remoto aos VMs no conjunto de escala. Para evitar que o seu VM seja sequestrado, **não** utilize os deste modelo. Os bots digitalizam a internet para ver nomes de utilizadores e senhas em repositórios públicos do GitHub. É provável que estejam a testar VMs com estes incumprimentos. 


###  <a name="modify-azuredeployjson"></a>Modify azuredeploy.json
Abra o ficheiro **azuredeploy.json.** 

Adicione uma variável para manter a informação da conta de armazenamento no modelo de Gestor de Recursos. Quaisquer registos ou contadores de desempenho especificados no ficheiro de diagnóstico config são escritos tanto para a loja métrica Do Monitor Azure como para a conta de armazenamento que especifica aqui: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Encontre a definição de conjunto de conjunto de máquinavirtual na secção de recursos e adicione a secção **de identidade** à configuração. Esta adição garante que o Azure lhe atribui uma identidade do sistema. Este passo também garante que os VMs no conjunto de escala podem emitir métricas de hóspedes sobre si mesmos para o Monitor Azure:  

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

No recurso conjunto de conjunto de máquinas virtuais, encontre a secção **virtualMachineProfile.** Adicione um novo perfil chamado **extensõesPerfil** para gerir extensões.  


Na **extensãoPerfil,** adicione uma nova extensão ao modelo, como mostrado na secção **de extensão VMSS-WAD.**  Esta secção é a identidade gerida para a extensão de recursos do Azure que garante que as métricas emitidas são aceites pelo Azure Monitor. O campo de **nomepode** conter qualquer nome. 

O seguinte código da extensão MSI também adiciona a extensão de diagnóstico e a configuração como um recurso de extensão ao recurso conjunto de escala de máquina virtual. Sinta-se à vontade para adicionar ou remover contadores de desempenho conforme necessário: 

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


Adicione um **dependente** para a conta de armazenamento para garantir que é criada na ordem correta: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Crie uma conta de armazenamento se ainda não for criada no modelo: 

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
> Você deve estar executando a versão de extensão de Diagnóstico Azure 1.5 ou superior **e** tem o **autoUpgradeMinorVersion:** propriedade definida para **verdade** no seu modelo De Gestor de Recursos. Azure então carrega a extensão adequada quando inicia o VM. Se não tiver estas definições no seu modelo, altere-as e recoloque o modelo. 


Para implementar o modelo de Gestor de Recursos, utilize o Azure PowerShell:  

1. Lançar PowerShell. 
1. Inscreva-se no Azure usando `Login-AzAccount`.
1. Obtenha a sua lista de subscrições utilizando `Get-AzSubscription`.
1. Detete a subscrição que irá criar, ou atualize a máquina virtual: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Criar um novo grupo de recursos para o VM que está a ser implantado. Execute o seguinte comando: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Lembre-se de usar uma região azure que está ativada para métricas personalizadas. Lembre-se de usar uma [região azure que está ativada para métricas personalizadas](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions).
 
1. Executar os seguintes comandos para implantar o VM:  

   > [!NOTE]  
   > Se pretender atualizar um conjunto de escala existente, adicione **-Modo Incremental** na extremidade do comando. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Após o sucesso da sua implementação, deverá encontrar a escala virtual da máquina definida no portal Azure. Deve emitir métricas para o Monitor Azure. 

   > [!NOTE]  
   > Pode ter erros em torno do **vmSkuSize**selecionado . Nesse caso, volte ao seu ficheiro **azuredeploy.json** e atualize o valor padrão do parâmetro **vmSkuSize.** Recomendamos que tente **Standard_DS1_v2.** 


## <a name="chart-your-metrics"></a>Mapeie as suas métricas 

1. Inicie sessão no Portal do Azure. 

1. No menu à esquerda, selecione **Monitor**. 

1. Na página **Monitor,** selecione **Métricas**. 

   ![Página de Monitor - Métricas](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Mude o período de agregação para **30 minutos**.  

1. No menu de desistência de recursos, selecione o conjunto de escala de máquina virtual que criou.  

1. No menu de lançamento dos espaços de nome, **selecione azure.vm.windows.guest**. 

1. No menu de entrega de métricas, selecione **Memory\%Committed Bytes in Use**.  

Em seguida, também pode optar por utilizar as dimensões desta métrica para a traçar para um Determinado VM ou para traçar cada VM no conjunto de escala. 



## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [métricas personalizadas.](metrics-custom-overview.md)


