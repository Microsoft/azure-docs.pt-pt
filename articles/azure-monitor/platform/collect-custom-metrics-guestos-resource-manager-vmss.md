---
title: Enviar métricas de SO convidado para o arquivo de métrica de Monitor do Azure com um modelo Azure Resource Manager para um conjunto de dimensionamento de máquinas virtuais do Windows
description: Enviar métricas de SO convidado para o arquivo de métrica de Monitor do Azure com um modelo do Resource Manager para um conjunto de dimensionamento de máquinas virtuais do Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 64fb3acf9b134b7188d316633bc663d7dd9b14b8
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760227"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Enviar métricas de SO convidado para o arquivo de métrica de Monitor do Azure com um modelo Azure Resource Manager para um conjunto de dimensionamento de máquinas virtuais do Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ao utilizar o Azure Monitor [extensão do Windows Azure Diagnostics (WAD)](diagnostics-extension-overview.md), pode coletar métricas e registos do sistema operativo convidado (SO convidado) que é executado como parte de uma máquina virtual, um serviço em nuvem ou um cluster do Azure Service Fabric. A extensão pode enviar telemetria para vários locais diferentes listados no artigo ligado anteriormente.  

Este artigo descreve o processo de envio métricas de desempenho do SO de convidado para um dimensionamento de máquinas virtuais do Windows definido para o arquivo de dados do Azure Monitor. A partir do Windows Azure Diagnostics versão 1.11, pode escrever armazenam métricas diretamente para as métricas do Azure Monitor, onde as métricas de plataforma padrão já são recolhidas. Armazenando-os nesta localização, pode acessar as mesmas ações que estão disponíveis para as métricas de plataforma. As ações incluem perto de alertas em tempo real, criação de gráficos, encaminhamento, aceder a partir da API REST e muito mais. No passado, a extensão de diagnóstico do Windows Azure escreveu para o armazenamento do Azure, mas não o arquivo de dados do Azure Monitor.  

Se estiver familiarizado com modelos do Resource Manager, saiba mais sobre [implementações de modelo](../../azure-resource-manager/resource-group-overview.md) e sua estrutura e a sintaxe.  

## <a name="prerequisites"></a>Pré-requisitos

- A sua subscrição tem de estar registrada com [Microsoft. insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Tem de ter [do Azure PowerShell](/powershell/azure) instalado, ou pode utilizar [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurar o Azure Monitor como um sink de dados 
A extensão de diagnóstico do Azure utiliza um recurso chamado **sinks de dados** rota métricas e registos para localizações diferentes. Os passos seguintes mostram como utilizar um modelo do Resource Manager e o PowerShell para implementar uma VM com o novo sink de dados do Azure Monitor. 

## <a name="author-a-resource-manager-template"></a>Criar um modelo do Resource Manager 
Neste exemplo, pode usar um disponíveis publicamente [modelo de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale):  

- **Azuredeploy. JSON** é um modelo do Resource Manager pré-configurada para a implementação de um conjunto de dimensionamento de máquina virtual.

- **Azuredeploy** é um ficheiro de parâmetros que armazena informações como o nome de utilizador e palavra-passe que pretende definir para a sua VM. Durante a implementação, o modelo do Resource Manager utiliza os parâmetros definidos neste ficheiro. 

Transfira e guarde ambos os ficheiros localmente. 

###  <a name="modify-azuredeployparametersjson"></a>Modify azuredeploy.parameters.json
Abra o **azuredeploy** ficheiro:  
 
- Fornecer um **vmSKU** que pretende implementar. Recomendamos que Standard_D2_v3. 
- Especifique um **windowsOSVersion** pretende para o conjunto de dimensionamento de máquina virtual. Recomendamos que 2016 Datacenter. 
- Nome do dimensionamento da máquina virtual definido recurso a ser implantado usando um **vmssName** propriedade. Um exemplo é **VMSS-WAD-teste**.    
- Especifique o número de VMs que pretende executar no conjunto de dimensionamento ao utilizar o **instanceCount** propriedade.
- Introduza os valores para **adminUsername** e **adminPassword** para o dimensionamento de máquinas virtuais definido. Esses parâmetros são usados para acesso remoto para as VMs no conjunto de dimensionamento. Para evitar que a sua VM seqüestrada, **não** utilizar a este modelo. A internet para nomes de utilizador e palavras-passe em repositórios de GitHub públicos de análise de bots. Eles provavelmente testar VMs com esses padrões. 


###  <a name="modify-azuredeployjson"></a>Modify azuredeploy.json
Abra o **azuredeploy. JSON** ficheiro. 

Adicione uma variável para conter as informações de conta de armazenamento no modelo do Resource Manager. Quaisquer contadores de desempenho especificados no ficheiro de configuração de diagnóstico ou de registos são escritos para o armazenamento de métrico do Azure Monitor e a conta de armazenamento que especificar aqui: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Localize a definição do conjunto de dimensionamento da máquina virtual na seção de recursos e adicione a **identidade** secção na configuração. Esta adição garante que o Azure atribui-lo uma identidade de sistema. Este passo também garante que as VMs no conjunto de dimensionamento podem emitir as métricas de convidado sobre si para o Azure Monitor:  

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

Na máquina virtual recurso de conjunto de dimensionamento, localize a **virtualMachineProfile** secção. Adicionar um novo perfil chamado **extensionsProfile** para gerir extensões.  


Na **extensionProfile**, adicione uma nova extensão para o modelo, conforme mostrado na **VMSS-WAD-extension** secção.  Esta secção é de identidades geridas para a extensão de recursos do Azure que garante que as métricas que está a ser emitidas são aceites pelo Azure Monitor. O **nome** campo pode conter qualquer nome. 

O código a seguir a partir da extensão do MSI também adiciona a extensão de diagnóstico e a configuração como um recurso de extensão para o recurso de conjunto de dimensionamento de máquina virtual. Fique à vontade adicionar ou remover contadores de desempenho, conforme necessário: 

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


Adicionar uma **dependsOn** para a conta de armazenamento garantir que ele é criado na ordem correta: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Crie uma conta de armazenamento, se uma já não está a ser criada no modelo: 

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

Guarde e feche ambos os ficheiros. 

## <a name="deploy-the-resource-manager-template"></a>Implementar o modelo do Resource Manager 

> [!NOTE]  
> Tem de executar a versão da extensão de diagnóstico do Azure versão 1.5 ou superior **e** tem o **autoUpgradeMinorVersion:** propriedade definida como **verdadeiro** no seu Gerenciador de recursos modelo. Azure, em seguida, carrega a extensão correta quando iniciar a VM. Se não tiver estas definições no seu modelo, alterá-los e Reimplementar o modelo. 


Para implementar o modelo do Resource Manager, utilize o Azure PowerShell:  

1. Inicie o PowerShell. 
1. Inicie sessão no Azure com `Login-AzAccount`.
1. Obter a lista de subscrições com `Get-AzSubscription`.
1. Defina a subscrição irá criar ou atualizar a máquina virtual: 

   ```PowerShell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Crie um novo grupo de recursos para a VM que está sendo implementada. Execute o seguinte comando: 

   ```PowerShell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Lembre-se utilizar uma região do Azure que está ativada para métricas personalizadas. Não se esqueça de utilizar um [região do Azure que está ativada para métricas personalizadas](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions).
 
1. Execute os seguintes comandos para implementar a VM:  

   > [!NOTE]  
   > Se quiser atualizar um conjunto de dimensionamento existente, adicione **-modo Incremental** ao final do comando. 
 
   ```PowerShell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Depois de sua implementação com êxito, deve encontrar o conjunto de dimensionamento no portal do Azure. Ele deve emitir métricas para o Azure Monitor. 

   > [!NOTE]  
   > Podem ocorrer erros em todo o selecionado **vmSkuSize**. Nesse caso, voltar para sua **azuredeploy. JSON** de ficheiros e atualizar o valor predefinido da **vmSkuSize** parâmetro. Recomendamos que tente **Standard_DS1_v2**. 


## <a name="chart-your-metrics"></a>As suas métricas do gráfico 

1. Inicie sessão no Portal do Azure. 

1. No menu da esquerda, selecione **Monitor**. 

1. Sobre o **Monitor** página, selecione **métricas**. 

   ![Monitor de-página de métricas](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Alterar o período de agregação para **últimos 30 minutos**.  

1. No menu de lista pendente de recursos, selecione o conjunto de dimensionamento de máquina virtual que criou.  

1. No menu de lista pendente de espaços de nomes, selecione **azure.vm.windows.guest**. 

1. No menu de lista pendente de métricas, selecione **memória\%Bytes dedicados em utilização**.  

Em seguida, também é possível usar as dimensões nesta métrica num gráfico-lo para uma VM específica ou para desenhar cada VM no conjunto de dimensionamento. 



## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).


