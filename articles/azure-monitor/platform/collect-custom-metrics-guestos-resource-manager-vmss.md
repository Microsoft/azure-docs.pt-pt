---
title: Coletar métricas do conjunto de dimensionamento do Windows no Azure Monitor com o modelo
description: Enviar métricas do sistema operacional convidado para o repositório de métrica Azure Monitor usando um modelo do Resource Manager para um conjunto de dimensionamento de máquinas virtuais do Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: ec9f7ecf218b635588065c14bd4d11283d027c11
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75364089"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Enviar métricas do sistema operacional convidado para o repositório de métrica Azure Monitor usando um modelo de Azure Resource Manager para um conjunto de dimensionamento de máquinas virtuais do Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Usando a extensão Azure Monitor [Windows diagnóstico do Azure (wad)](diagnostics-extension-overview.md), você pode coletar métricas e logs do sistema operacional convidado (SO convidado) que é executado como parte de uma máquina virtual, serviço de nuvem ou cluster de Service Fabric do Azure. A extensão pode enviar telemetria para vários locais diferentes listados no artigo vinculado anteriormente.  

Este artigo descreve o processo para enviar métricas de desempenho do SO convidado para um conjunto de dimensionamento de máquinas virtuais do Windows para o armazenamento de dados do Azure Monitor. A partir do Windows Diagnóstico do Azure versão 1,11, você pode gravar métricas diretamente no repositório de métricas de Azure Monitor, em que as métricas de plataforma padrão já são coletadas. Ao armazená-los nesse local, você pode acessar as mesmas ações que estão disponíveis para métricas de plataforma. As ações incluem alertas em tempo real, criação de gráficos, roteamento, acesso da API REST e muito mais. No passado, a extensão de Diagnóstico do Azure do Windows gravou no armazenamento do Azure, mas não no armazenamento de dados Azure Monitor.  

Se você for novo nos modelos do Resource Manager, saiba mais sobre [implantações de modelo](../../azure-resource-manager/management/overview.md) e sua estrutura e sintaxe.  

## <a name="prerequisites"></a>Pré-requisitos

- Sua assinatura deve ser registrada com [Microsoft. insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Você precisa ter [Azure PowerShell](/powershell/azure) instalado ou pode usar [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). 

- O recurso da VM deve estar em uma [região que ofereça suporte a métricas personalizadas](metrics-custom-overview.md#supported-regions).

## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurar Azure Monitor como um coletor de dados 
A extensão Diagnóstico do Azure usa um recurso chamado **coletores de dados** para rotear métricas e logs para locais diferentes. As etapas a seguir mostram como usar um modelo do Resource Manager e o PowerShell para implantar uma VM usando o novo coletor de dados Azure Monitor. 

## <a name="author-a-resource-manager-template"></a>Criar um modelo do Resource Manager 
Para este exemplo, você pode usar um modelo de [exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale)disponível publicamente:  

- **Azuredeploy. JSON** é um modelo pré-configurado do Resource Manager para a implantação de um conjunto de dimensionamento de máquinas virtuais.

- **Azuredeploy. Parameters. JSON** é um arquivo de parâmetros que armazena informações como o nome de usuário e a senha que você deseja definir para sua VM. Durante a implantação, o modelo do Resource Manager usa os parâmetros definidos neste arquivo. 

Baixe e salve os dois arquivos localmente. 

###  <a name="modify-azuredeployparametersjson"></a>Modificar azuredeploy. Parameters. JSON
Abra o arquivo **azuredeploy. Parameters. JSON** :  
 
- Forneça um **vmSKU** que você deseja implantar. Recomendamos Standard_D2_v3. 
- Especifique um **windowsOSVersion** que você deseja para o conjunto de dimensionamento de máquinas virtuais. Recomendamos 2016-datacenter. 
- Nomeie o recurso de conjunto de dimensionamento de máquinas virtuais a ser implantado usando uma propriedade **vmssName** . Um exemplo é **VMSS-wad-Test**.    
- Especifique o número de VMs que você deseja executar no conjunto de dimensionamento de máquinas virtuais usando a propriedade **instanceCount** .
- Insira valores para **adminUsername** e **adminPassword** para o conjunto de dimensionamento de máquinas virtuais. Esses parâmetros são usados para acesso remoto às VMs no conjunto de dimensionamento. Para evitar que sua VM seja seqüestrada **, não** use aquelas neste modelo. Os bots examinam a Internet em busca de nomes de acesso e senhas em repositórios GitHub públicos. É provável que eles estejam testando VMs com esses padrões. 


###  <a name="modify-azuredeployjson"></a>Modify azuredeploy.json
Abra o arquivo **azuredeploy. JSON** . 

Adicione uma variável para conter as informações da conta de armazenamento no modelo do Resource Manager. Todos os logs ou contadores de desempenho especificados no arquivo de configuração de diagnóstico são gravados no repositório de métrica Azure Monitor e na conta de armazenamento que você especificar aqui: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Localize a definição do conjunto de dimensionamento de máquinas virtuais na seção recursos e adicione a seção **identidade** à configuração. Essa adição garante que o Azure atribua uma identidade do sistema a ele. Essa etapa também garante que as VMs no conjunto de dimensionamento possam emitir métricas de convidado sobre si mesmos para Azure Monitor:  

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

No recurso do conjunto de dimensionamento de máquinas virtuais, localize a seção **virtualMachineProfile** . Adicione um novo perfil chamado **extensionsProfile** para gerenciar extensões.  


No **extensionProfile**, adicione uma nova extensão ao modelo, conforme mostrado na seção **VMSS-wad-Extension** .  Esta seção são as identidades gerenciadas para a extensão de recursos do Azure que garante que as métricas que estão sendo emitidas sejam aceitas pelo Azure Monitor. O campo **nome** pode conter qualquer nome. 

O código a seguir da extensão MSI também adiciona a extensão de diagnóstico e a configuração como um recurso de extensão ao recurso de conjunto de dimensionamento de máquinas virtuais. Sinta-se à vontade para adicionar ou remover contadores de desempenho conforme necessário: 

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


Adicione uma **dependência** para a conta de armazenamento para garantir que ela seja criada na ordem correta: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Crie uma conta de armazenamento se uma já não estiver criada no modelo: 

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

Salve e feche os dois arquivos. 

## <a name="deploy-the-resource-manager-template"></a>Implantar o modelo do Resource Manager 

> [!NOTE]  
> Você deve estar executando a extensão de Diagnóstico do Azure versão 1,5 ou superior **e** ter a propriedade **autoUpgradeMinorVersion:** definida como **true** em seu modelo do Resource Manager. Em seguida, o Azure carrega a extensão apropriada quando inicia a VM. Se você não tiver essas configurações em seu modelo, altere-as e reimplante o modelo. 


Para implantar o modelo do Resource Manager, use Azure PowerShell:  

1. Inicie o PowerShell. 
1. Entre no Azure usando `Login-AzAccount`.
1. Obtenha sua lista de assinaturas usando `Get-AzSubscription`.
1. Defina a assinatura que você criará ou atualize a máquina virtual: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Crie um novo grupo de recursos para a VM que está sendo implantada. Execute o seguinte comando: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Lembre-se de usar uma região do Azure que está habilitada para métricas personalizadas. Lembre-se de usar uma [região do Azure que está habilitada para métricas personalizadas](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions).
 
1. Execute os seguintes comandos para implantar a VM:  

   > [!NOTE]  
   > Se você quiser atualizar um conjunto de dimensionamento existente, adicione o **modo incremental** ao final do comando. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Depois que a implantação for realizada com sucesso, você deverá encontrar o conjunto de dimensionamento de máquinas virtuais na portal do Azure. Ele deve emitir métricas para Azure Monitor. 

   > [!NOTE]  
   > Você pode encontrar erros em volta do **vmSkuSize**selecionado. Nesse caso, volte para o arquivo **azuredeploy. JSON** e atualize o valor padrão do parâmetro **vmSkuSize** . Recomendamos que você tente **Standard_DS1_v2**. 


## <a name="chart-your-metrics"></a>Gráfico de suas métricas 

1. Inicie sessão no Portal do Azure. 

1. No menu à esquerda, selecione **Monitor**. 

1. Na página **monitorar** , selecione **métricas**. 

   ![Monitor – página métricas](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Altere o período de agregação para os **últimos 30 minutos**.  

1. No menu suspenso de recursos, selecione o conjunto de dimensionamento de máquinas virtuais que você criou.  

1. No menu suspenso namespaces, selecione **Azure. VM. Windows. Guest**. 

1. No menu suspenso métricas, selecione **memória\%bytes confirmados em uso**.  

Você também pode optar por usar as dimensões nessa métrica para criar um gráfico dela para uma VM específica ou para plotar cada VM no conjunto de dimensionamento. 



## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).


