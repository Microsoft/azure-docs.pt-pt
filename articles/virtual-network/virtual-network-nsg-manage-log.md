---
title: Evento do grupo de segurança de rede e a regra de contador de registos de diagnóstico do Azure
titlesuffix: Azure Virtual Network
description: Saiba como ativar o evento e a regra de contador os registos de diagnóstico para um grupo de segurança de rede do Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: jdial
ms.openlocfilehash: 6cc75a70dfc7022efa8577234cc67659d53b2f88
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194422"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Registo de diagnósticos para um grupo de segurança de rede

Um grupo de segurança de rede (NSG) inclui regras que permitem ou negam o tráfego para uma sub-rede de rede virtual, interface de rede ou ambos. Quando ativar o diagnóstico de registo para um NSG, podem iniciar as seguintes categorias de informações:

* **Evento:** As entradas são registadas para o qual NSG regras são aplicadas a VMs, com base no endereço MAC. O estado para estas regras é recolhido a cada 60 segundos.
* **Contador de regra:** Contém entradas para quantas vezes cada regra NSG é aplicada a negar ou permitir tráfego.

Os registos de diagnóstico só estão disponíveis para NSGs implementados por meio do modelo de implementação Azure Resource Manager. Não é possível ativar o registo de diagnósticos para NSGs implementadas através do modelo de implementação clássica. Para uma melhor compreensão dos dois modelos, consulte [modelos de implementação de compreender o Azure](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Registo de diagnósticos está ativado em separado para *cada* NSG que pretende recolher dados de diagnóstico. Se estiver interessado em operacional, ou registos de atividades, em vez disso, veja o Azure [registo de atividade](../azure-monitor/platform/activity-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enable-logging"></a>Ativar registo

Pode utilizar o [Portal do Azure](#azure-portal), [PowerShell](#powershell), ou o [da CLI do Azure](#azure-cli) para ativar o registo de diagnóstico.

### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal](https://portal.azure.com).
2. Selecione **todos os serviços**, em seguida, escreva *grupos de segurança de rede*. Quando **grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione-o.
3. Selecione o NSG que pretende ativar o registo de.
4. Sob **monitorização**, selecione **os registos de diagnóstico**e, em seguida, selecione **ativar diagnósticos**, conforme mostrado na imagem seguinte:

   ![Ativar os diagnósticos](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. Sob **as definições de diagnóstico**, introduza, ou selecione as seguintes informações e, em seguida, selecione **guardar**:

    | Definição                                                                                     | Valor                                                          |
    | ---------                                                                                   |---------                                                       |
    | Name                                                                                        | Um nome à sua escolha.  Por exemplo: *myNsgDiagnostics*      |
    | **Arquivo para uma conta de armazenamento**, **Stream para um hub de eventos**, e **enviar para o Log Analytics** | Pode selecionar destinos, conforme escolher. Para saber mais sobre cada uma, veja [destinos de registo](#log-destinations).                                                                                                                                           |
    | REGISTO                                                                                         | Selecione um ou ambos os categorias de registo. Para saber mais sobre os dados registados para cada categoria, veja [categorias de registo](#log-categories).                                                                                                                                             |
6. Ver e analisar registos. Para obter mais informações, consulte [ver e analisar registos](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode executar os comandos que se seguem na [Azure Cloud Shell](https://shell.azure.com/powershell), ou ao executar o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se executar o PowerShell a partir do seu computador, terá do módulo Azure PowerShell, versão 1.0.0 ou posterior. Executar `Get-Module -ListAvailable Az` no seu computador, para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver executando o PowerShell localmente, terá também de ser executado `Connect-AzAccount` para iniciar sessão no Azure com uma conta que tenha o [as permissões necessárias](virtual-network-network-interface.md#permissions).

Para ativar o registo de diagnósticos, precisa do Id de um NSG existente. Se não tiver um NSG existente, pode criar um com [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

Obter o grupo de segurança de rede que pretende ativar o diagnóstico para o registo com [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Por exemplo, para obter um NSG chamada *myNsg* que existe num grupo de recursos com o nome *myResourceGroup*, introduza o seguinte comando:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Pode escrever os registos de diagnóstico a três tipos de destino. Para obter mais informações, consulte [destinos de registo](#log-destinations). Neste artigo, os registos são enviados para o *do Log Analytics* destino, por exemplo. Recuperar uma área de trabalho do Log Analytics existente com [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Por exemplo recuperar uma área de trabalho existente com o nome *myWorkspace* num grupo de recursos com o nome *myWorkspaces*, introduza o seguinte comando:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Se não tiver uma área de trabalho existente, pode criar um com [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Existem duas categorias de registo, que pode ativar os registos para. Para obter mais informações, consulte [categorias de registo](#log-categories). Ativar o registo de diagnóstico para o NSG com [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). O exemplo seguinte registos de dados de categoria de evento e o contador para a área de trabalho para um NSG, utilizando os IDs para o NSG e a área de trabalho que obteve anteriormente:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Se pretender apenas registar dados para uma categoria ou a outra, em vez de ambos, adicione a `-Categories` opção para o comando anterior, seguido *NetworkSecurityGroupEvent* ou *NetworkSecurityGroupRuleCounter*. Se quiser iniciar a outro [destino](#log-destinations) que uma área de trabalho do Log Analytics, utilizar os parâmetros adequados para um Azure [conta de armazenamento](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Hub de eventos](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Ver e analisar registos. Para obter mais informações, consulte [ver e analisar registos](#view-and-analyze-logs).

### <a name="azure-cli"></a>CLI do Azure

Pode executar os comandos que se seguem na [Azure Cloud Shell](https://shell.azure.com/bash), ou ao executar a CLI do Azure a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se executar a CLI do seu computador, terá de versão 2.0.38 ou posterior. Executar `az --version` no seu computador, para localizar a versão instalada. Se precisar de atualizar, veja [instalar a CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Se estiver a executar a CLI localmente, terá também de ser executado `az login` para iniciar sessão no Azure com uma conta que tenha o [as permissões necessárias](virtual-network-network-interface.md#permissions).

Para ativar o registo de diagnósticos, precisa do Id de um NSG existente. Se não tiver um NSG existente, pode criar um com [nsg de rede de az criar](/cli/azure/network/nsg#az-network-nsg-create).

Obter o grupo de segurança de rede que pretende ativar o diagnóstico para o registo com [show de nsg de rede de az](/cli/azure/network/nsg#az-network-nsg-show). Por exemplo, para obter um NSG chamada *myNsg* que existe num grupo de recursos com o nome *myResourceGroup*, introduza o seguinte comando:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Pode escrever os registos de diagnóstico a três tipos de destino. Para obter mais informações, consulte [destinos de registo](#log-destinations). Neste artigo, os registos são enviados para o *do Log Analytics* destino, por exemplo. Para obter mais informações, consulte [categorias de registo](#log-categories).

Ativar o registo de diagnóstico para o NSG com [az monitor diagnostic-settings criar](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). O exemplo seguinte regista dados de categoria de evento e o contador numa área de trabalho existente com o nome *myWorkspace*, que existe num grupo de recursos com o nome *myWorkspaces*e o ID do NSG que obteve anteriormente:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Se não tiver uma área de trabalho existente, pode criar uma com o [portal do Azure](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [PowerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace). Existem duas categorias de registo, que pode ativar os registos para. 

Se pretender apenas registar dados para uma categoria ou outro, remova a categoria que não pretende registar dados para no comando anterior. Se quiser iniciar a outro [destino](#log-destinations) que uma área de trabalho do Log Analytics, utilizar os parâmetros adequados para um Azure [conta de armazenamento](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Hub de eventos](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Ver e analisar registos. Para obter mais informações, consulte [ver e analisar registos](#view-and-analyze-logs).

## <a name="log-destinations"></a>Destinos de log

Dados de diagnóstico podem ser:
- [Escrito para uma conta de armazenamento do Azure](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), para inspeção de auditoria ou manual. Pode especificar o período de retenção (em dias) com as definições de diagnóstico de recursos.
- [Transmissão em fluxo para um hub de eventos](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ingestão por um serviço de terceiros, ou uma solução de análise personalizada, como o Power BI.
- [Escrito para o Azure Log Analytics](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-diagnostics-direct-to-log-analytics).

## <a name="log-categories"></a>Categorias de registo

Dados formatados por JSON são escritos para as categorias de registo seguinte:

### <a name="event"></a>Evento

O registo de eventos contém informações sobre os quais as regras do NSG são aplicadas a VMs, com base no endereço MAC. Os seguintes dados são registados para cada evento. No exemplo a seguir, os dados são registados para uma máquina virtual com o endereço IP 192.168.1.4 e um endereço MAC de 00-0D-3A-92-6A-7C:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "priority":[PRIORITY-SPECIFIED-IN-RULE],
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "conditions":{
            "protocols":"[PROTOCOLS-SPECIFIED-IN-RULE]",
            "destinationPortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourcePortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourceIP":"[SOURCE-IP-OR-RANGE-SPECIFIED-IN-RULE]",
            "destinationIP":"[DESTINATION-IP-OR-RANGE-SPECIFIED-IN-RULE]"
            }
        }
}
```

### <a name="rule-counter"></a>Contador de regra

O registo do contador de regra contém informações sobre cada regra aplicada aos recursos. Os dados de exemplo seguinte são registados sempre que uma regra é aplicada. No exemplo a seguir, os dados são registados para uma máquina virtual com o endereço IP 192.168.1.4 e um endereço MAC de 00-0D-3A-92-6A-7C:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> O endereço IP de origem para a comunicação não é registado. Pode habilitar [registo do fluxo do NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md) para um NSG no entanto, que regista todas as informações do contador de regra, bem como o endereço IP de origem que iniciou a comunicação. Os dados de registo de fluxo do NSG são escritos numa conta de Armazenamento do Microsoft Azure. Pode analisar os dados com o [análise de tráfego](../network-watcher/traffic-analytics.md) capacidade do observador de rede do Azure.

## <a name="view-and-analyze-logs"></a>Ver e analisar registos

Para saber como ver dados de registo de diagnóstico, veja [descrição geral de registos de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se tiver de enviar dados de diagnóstico para:
- **Log Analytics**: Pode utilizar o [análise do grupo de segurança de rede](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-log-analytics
) solução de informações melhoradas. A solução fornece visualizações para as regras do NSG que permitem ou negam o tráfego, por endereço MAC, da interface de rede numa máquina virtual.
- **Conta de armazenamento do Azure**: Dados são escritos num ficheiro PT1H.json. Pode encontrar o:
  - Registo de eventos no seguinte caminho: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Log de contador de regra no seguinte caminho: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [registo de atividade](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), anteriormente conhecido como auditoria ou registos operacionais. Registo de atividade está ativado por predefinição para NSGs criados por meio de qualquer modelo de implementação do Azure. Para determinar quais operações foram concluídas em NSGs no registo de atividades, procure entradas que contêm os seguintes tipos de recurso:
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- Para saber como registar informações de diagnóstico, para incluir o endereço IP de origem para cada fluxo, veja [registo do fluxo do NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).