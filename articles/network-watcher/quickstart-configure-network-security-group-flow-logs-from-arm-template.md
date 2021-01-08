---
title: 'Quickstart: Configurar registos de fluxo de grupo de segurança de rede utilizando um modelo de Gestor de Recursos Azure (modelo ARM)'
description: Saiba como ativar os registos de fluxo do grupo de segurança da rede (NSG) programáticamente utilizando um modelo de Gestor de Recursos Azure (modelo ARM) e Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the network security group flow logs by using an Azure Resource Manager template.
ms.service: network-watcher
ms.topic: quickstart
ms.date: 01/07/2021
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: ded7b24461fdcdbc3d020a487cafc20620633097
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019725"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>Quickstart: Configurar registos de fluxo de grupo de segurança de rede usando um modelo ARM

Neste arranque rápido, aprende-se a ativar [os registos de fluxo do grupo de segurança da rede (NSG)](network-watcher-nsg-flow-logging-overview.md) utilizando um modelo [de Gestor de Recursos Azure](../azure-resource-manager/management/overview.md) (modelo ARM) e Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Começamos com uma visão geral das propriedades do objeto de log de fluxo NSG. Fornecemos modelos de amostra. Em seguida, usamos uma instância Azure PowerShell local para implementar o modelo.

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo abre-se no portal Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo que usamos neste quickstart é de [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Estes recursos são definidos no modelo:

- [Microsoft.Storage/storageAcontas](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/implementações](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>Objeto de registos de fluxo NSG

O código que se segue mostra um objeto de registos de fluxo NSG e os seus parâmetros. Para criar um `Microsoft.Network/networkWatchers/flowLogs` recurso, adicione este código à secção de recursos do seu modelo:

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

Para obter uma visão geral completa das propriedades dos objetos de fluxo NSG, consulte [os Microsoft.NetworkWatchers/FlowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

## <a name="create-your-template"></a>Crie o seu modelo

Se estiver a usar modelos ARM pela primeira vez, consulte os seguintes artigos para saber mais sobre os modelos ARM:

- [Implementar recursos com modelos ARM e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [Tutorial: Crie e implemente o seu primeiro modelo ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

O exemplo a seguir é um modelo completo. É também a versão mais simples do modelo. O exemplo contém os parâmetros mínimos que são passados para configurar os registos de fluxo NSG. Para mais exemplos, consulte o artigo visão geral [Configurar registos de fluxo NSG a partir de um modelo de Gestor de Recursos Azure](network-watcher-nsg-flow-logging-azure-resource-manager.md).

### <a name="example"></a>Exemplo

O modelo a seguir permite registos de fluxo para um NSG e, em seguida, armazena os registos numa conta de armazenamento específica:

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
> - O nome do recurso utiliza o formato _ParentResource_ChildResource_. No nosso exemplo, o recurso principal é o exemplo regional do Observador da Rede Azure:
>    - **Formato**: NetworkWatcher_RegionName
>    - **Exemplo:** NetworkWatcher_centraluseuap
> - `targetResourceId` é o ID de recursos do NSG alvo.
> - `storageId` é o ID de recursos da conta de armazenamento de destino.

## <a name="deploy-the-template"></a>Implementar o modelo

Este tutorial pressupõe que tem um grupo de recursos existente e um NSG que pode ativar o fluxo de login.

Pode guardar qualquer um dos modelos de exemplo que são mostrados neste artigo localmente como *azuredeploy.jsem*. Atualize os valores da propriedade para que eles apiem recursos válidos na sua subscrição.

Para implementar o modelo, executar o seguinte comando em Azure PowerShell:

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Estes comandos implantam um recurso para o exemplo do grupo de recursos NetworkWatcherRG, e não para o grupo de recursos que contém o NSG.

## <a name="validate-the-deployment"></a>Validar a implementação

Tem duas opções para ver se a sua implantação foi bem sucedida:

- A sua consola PowerShell mostra `ProvisioningState` como `Succeeded` .
- Aceda à página do [portal de registos de fluxos NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) para confirmar as suas alterações. 

Se houver problemas com a implementação, consulte [os erros comuns de implementação do Azure com o Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Pode eliminar os recursos do Azure utilizando o modo de implementação completo. Para eliminar um recurso de registos de fluxo, especifique uma implementação em modo completo sem incluir o recurso que pretende eliminar. Leia mais sobre [o modo de implementação completo](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

Também pode desativar um registo de fluxo NSG no portal Azure:

1. Inicie sessão no portal do Azure.
1. Selecione **Todos os serviços**. Na caixa **Filtro,** introduza o **observador de rede**. Nos resultados da pesquisa, selecione **Network Watcher**.
1. Em **Registos**, selecione **registos de fluxo NSG**.
1. Na lista de NSGs, selecione o NSG para o qual pretende desativar os registos de fluxo.
1. Nas **definições de registos de fluxo**, selecione **Off**.
1. Selecione **Save** (Guardar).

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, aprendeu a ativar os registos de fluxo NSG utilizando um modelo ARM. Em seguida, aprenda a visualizar os seus dados de fluxo NSG utilizando uma destas opções:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure Traffic Analytics](traffic-analytics.md)
