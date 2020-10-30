---
title: 'Quickstart: Configurar registos de fluxo NSG utilizando o modelo do Gestor de Recursos Azure'
description: Saiba como ativar os registos de fluxo NSG programáticamente utilizando um modelo de Gestor de Recursos Azure (modelo ARM) e Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG flow logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 96f30c05527754cbce3b7593c8d62fb56844d41e
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042761"
---
# <a name="quickstart-configure-nsg-flow-logs-using-an-arm-template"></a>Quickstart: Configurar registos de fluxo NSG usando um modelo ARM

Neste arranque rápido, ativa [os registos de fluxo NSG](network-watcher-nsg-flow-logging-overview.md) utilizando um modelo [de Gestor de Recursos Azure](../azure-resource-manager/management/overview.md) (modelo ARM) e Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Começamos por fornecer uma visão geral das propriedades do objeto NSG Flow Log, seguido de alguns modelos de amostra. Em seguida, nós o modelo de implementação usando uma instância local PowerShell.

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure** . O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Vários recursos são definidos no modelo:

- [Microsoft.Storage/storageAcontas](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/implementações](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>Objeto de registos de fluxo NSG

O objeto de registos de fluxo NSG com todos os parâmetros é mostrado abaixo. Para obter uma visão geral completa das propriedades, consulte [os Microsoft.NetworkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

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

Para criar um `Microsoft.Network/networkWatchers/flowLogs` recurso, adicione o JSON acima na secção de recursos do seu modelo.

## <a name="creating-your-template"></a>Criando o seu modelo

Se estiver a utilizar modelos ARM pela primeira vez, pode saber mais sobre eles usando os links abaixo.

- [Implementar recursos com modelos ARM e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [Tutorial: Crie e implemente o seu primeiro modelo ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

Abaixo exemplo de modelo completo é a versão mais simples com parâmetros mínimos passados para configurar registos de fluxo NSG. Para mais exemplos aceda a este [guia De como fazer.](network-watcher-nsg-flow-logging-azure-resource-manager.md)

**Exemplo** : O modelo abaixo permite registos de fluxo NSG num NSG alvo e armazena-os numa determinada conta de armazenamento.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> - O nome do recurso tem o recurso _Resource_Child de formação Parent_ . Aqui, o recurso principal é a instância regional do Observador de Rede (Formato: NetworkWatcher_RegionName. Exemplo: NetworkWatcher_centraluseuap)
> - `targetResourceId` é o ID de recursos do NSG alvo.
> - `storageId` é o ID de recursos da conta de armazenamento de destino.

## <a name="deploy-the-template"></a>Implementar o modelo

Este tutorial pressupõe que tem um grupo de Recursos existente e um NSG que pode ativar o fluxo de iniciar sessão.
Pode guardar qualquer um dos modelos de exemplo acima localmente como `azuredeploy.json` . Atualize os valores da propriedade para que apia recursos válidos na sua subscrição.

Para implementar o modelo, executar o seguinte comando em PowerShell.

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Os comandos acima estão a implantar um recurso para o grupo de recursos NetworkWatcherRG e não para o grupo de recursos que contém o NSG

## <a name="validate-the-deployment"></a>Validar a implementação

Há algumas maneiras de verificar se a sua implantação foi bem sucedida. A sua consola PowerShell deve mostrar `ProvisioningState` como `Succeeded` . Além disso, pode visitar a página do [portal de registos de fluxo NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) para confirmar as suas alterações. Se houver problemas com a implementação, consulte [os erros comuns de implementação do Azure com o Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Limpar recursos

O Azure permite a eliminação de recursos através do `Complete` modo de implantação. Para eliminar um recurso de registos de fluxo, especifique uma implementação no `Complete` modo sem incluir o recurso que pretende eliminar. Leia mais sobre o [modo de implementação Completo](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

Em alternativa, pode desativar um registo de fluxo NSG do portal Azure de acordo com os passos abaixo:

1. Iniciar sessão no portal do Azure
1. No canto superior esquerdo do portal, selecione **Todos os serviços** . Na **caixa Filtro** , escreva _Observador de Rede_ . Quando o **Observador de Rede** aparecer nos resultados de pesquisa, selecione-o.
1. Em **LOGS** , selecione **registos de fluxo NSG** .
1. Na lista de NSGs, selecione o NSG para o qual pretende desativar os registos de fluxo.
1. Nas **definições de registos de fluxos** , definir o estado do registo dos fluxos como **Desligado** .
1. Desloque-se para baixo e **selecione Guardar** .

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, ativou os registos de fluxo NSG. Agora, você deve aprender a visualizar os seus dados de fluxo NSG usando:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure Traffic Analytics](traffic-analytics.md)
