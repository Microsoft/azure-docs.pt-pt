---
title: 'Quickstart: Configurar registos de fluxo NSG utilizando o modelo do Gestor de Recursos Azure'
description: Saiba como ativar os Registos de Fluxo NSG programáticamente utilizando um modelo de Gestor de Recursos Azure e Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG Flow Logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 7d8cb89b1187bb15e7b361e1b6b9505400c612b5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87986322"
---
# <a name="quickstart-configure-nsg-flow-logs-from-arm-template"></a>Quickstart: Configurar registos de fluxo NSG do modelo ARM

Neste arranque rápido, ativa os [Registos de Fluxo NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) programáticamente utilizando um modelo [de Gestor de Recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) (modelo ARM) e Azure PowerShell. 

Começamos por fornecer uma visão geral das propriedades do objeto NSG Flow Log, seguido de alguns modelos de amostra. Em seguida, nós o modelo de implementação usando uma instância local PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="nsg-flow-logs-object"></a>Objeto de registos de fluxo NSG

O objeto NSG Flow Logs com todos os parâmetros é mostrado abaixo.
Para uma visão geral completa das propriedades, pode ler a referência do [modelo NSG Flow Logs](https://docs.microsoft.com/azure/templates/microsoft.network/networkwatchers/flowlogs).

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Para criar um recurso Microsoft.Network/networkWatchers/flowLogs, adicione o JSON acima na secção de recursos do seu modelo.


## <a name="creating-your-template"></a>Criando o seu modelo

Se estiver a utilizar os modelos do Azure Resource Manager pela primeira vez, pode saber mais sobre eles utilizando os links abaixo.

* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Tutorial: Criar e implementar o seu primeiro modelo de Gestor de Recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

Abaixo exemplo de modelo completo é a versão mais simples com parâmetros mínimos passados para configurar registos de fluxo NSG. Para mais exemplos, aceda a este [link.](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager)

**Exemplo**: O modelo abaixo permite registos de fluxo NSG num NSG alvo e armazena-os numa determinada conta de armazenamento.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * O nome do recurso tem o formato "Recurso Resource_Child dos pais". Aqui, o recurso principal é a instância regional do Observador de Rede (Formato: NetworkWatcher_RegionName. Exemplo: NetworkWatcher_centraluseuap)
> * targetResourceId é o ID de recurso do NSG alvo
> * storageId é o ID de recursos da conta de armazenamento de destino


## <a name="deploying-your-azure-resource-manager-template"></a>Implementar o seu modelo do Azure Resource Manager

Este tutorial pressupõe que tem um grupo de Recursos existente e um NSG que pode ativar o fluxo de iniciar sessão.
Pode guardar qualquer um dos modelos de exemplo acima localmente como `azuredeploy.json` . Atualize os valores da propriedade para que apia recursos válidos na sua subscrição.

Para implementar o modelo, executar o seguinte comando em PowerShell.
```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Os comandos acima estão a implantar um recurso para o grupo de recursos NetworkWatcherRG e não para o grupo de recursos que contém o NSG


## <a name="validate-the-deployment"></a>Validar a implementação

Há algumas maneiras de verificar se a sua implantação foi bem sucedida. A sua consola PowerShell deve mostrar "ProvisioningState" como "Bem sucedido". Além disso, pode visitar a página do [portal NSG Flow Logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) para confirmar as suas alterações. Se houver problemas com a implantação, dê uma olhada nos [erros comuns de implementação do Azure com o Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).

## <a name="deleting-your-resource"></a>Apagar o seu recurso
O Azure permite a eliminação de recursos através do modo de implementação "Completo". Para eliminar um recurso Flow Logs, especifique uma implementação no modo Completo sem incluir o recurso que pretende eliminar. Leia mais sobre o [modo de implementação completo](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)

Em alternativa, pode desativar um registo de fluxo NSG do portal Azure de acordo com os passos abaixo:
1. Iniciar sessão no portal do Azure
2. No canto superior esquerdo do portal, selecione **Todos os serviços**. Na **caixa Filtro**, escreva *Observador de Rede*. Quando o **Observador de Rede** aparecer nos resultados de pesquisa, selecione-o.
3. Em **LOGS**, selecione **registos de fluxo NSG**
4. A partir da lista de NSGs, selecione o NSG para o qual pretende desativar os registos de fluxo
5. Nas **definições de registos de fluxos**, definir o estado do registo dos fluxos como **Desligado**
6. Desloque-se para baixo e selecione **guardar**

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, ativou os registos NSG Flow. Agora, tem de aprender a visualizar os seus dados do NSG Flow utilizando: 

* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
