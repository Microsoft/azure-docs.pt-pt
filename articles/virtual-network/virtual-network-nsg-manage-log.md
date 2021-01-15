---
title: Registo de recursos de diagnóstico para um grupo de segurança de rede
titlesuffix: Azure Virtual Network
description: Saiba como ativar os registos de recursos de diagnóstico de eventos e regras para um grupo de segurança da rede Azure.
services: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 5d06c251ce16aff56a3645f5032cce4e27d5fc9e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216908"
---
# <a name="resource-logging-for-a-network-security-group"></a>Registo de recursos para um grupo de segurança de rede

Um grupo de segurança de rede (NSG) inclui regras que permitem ou negam o tráfego a uma sub-rede de rede virtual, interface de rede ou ambas. 

Quando ativa a sessão de registo de um NSG, pode recolher os seguintes tipos de informações de registo de recursos:

* **Evento:** As entradas são registadas para as quais as regras NSG são aplicadas a VMs, com base no endereço MAC.
* **Contador de regras:** Contém entradas para quantas vezes cada regra NSG é aplicada para negar ou permitir o tráfego. O estatuto destas regras é recolhido a cada 300 segundos.

Os registos de recursos só estão disponíveis para os NSGs implementados através do modelo de implementação do Gestor de Recursos Azure. Não é possível ativar a registo de recursos para NSGs implementadas através do modelo clássico de implementação. Para uma melhor compreensão dos dois modelos, consulte os [modelos de implementação Understanding Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

A sessão de registo de recursos é ativada separadamente para *cada* NSG para a qual pretende recolher dados de diagnóstico. Se estiver interessado em registos de atividade (operacionais), consulte o [registo de atividades](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)do Azure .

## <a name="enable-logging"></a>Ativar registo

Pode utilizar o [Portal Azure,](#azure-portal) [o PowerShell](#powershell)ou o [Azure CLI](#azure-cli) para permitir a registo de recursos.

### <a name="azure-portal"></a>Portal do Azure

1. Inscreva-se no [portal.](https://portal.azure.com)
2. Selecione **Todos os serviços** e, em seguida, escreva *grupos de segurança de rede*. Quando **os grupos de segurança da Rede** aparecerem nos resultados da pesquisa, selecione-os.
3. Selecione o NSG que pretende ativar a sessão.
4. Em **MONITORIZAÇÃO**, selecione **registos de diagnósticos** e, em seguida, selecione Ligue os **diagnósticos**, como mostra a seguinte imagem:

   ![Ativar os diagnósticos](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. Nas **definições de Diagnóstico,** introduza ou selecione as seguintes informações e, em seguida, selecione **Guardar**:

    | Definição                                                                                     | Valor                                                          |
    | ---------                                                                                   |---------                                                       |
    | Nome                                                                                        | Um nome à sua escolha.  Por exemplo: *myNsgDiagnostics*      |
    | **Arquivar para uma conta de armazenamento,** **transmitir para um centro de eventos,** e enviar para registar **analytics** | Pode selecionar quantos destinos escolher. Para saber mais sobre cada um, consulte [os destinos Log.](#log-destinations)                                                                                                                                           |
    | LOG                                                                                         | Selecione ambas as categorias de registo. Para saber mais sobre os dados registados para cada categoria, consulte [as categorias 'Registar'.](#log-categories)                                                                                                                                             |
6. Ver e analisar registos. Para obter mais informações, consulte [Ver e analisar registos.](#view-and-analyze-logs)

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode executar os comandos que seguem na [Azure Cloud Shell,](https://shell.azure.com/powershell)ou executando o PowerShell a partir do seu computador. O Azure Cloud Shell é uma concha interativa gratuita. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se executar o PowerShell a partir do computador, precisa do módulo Azure PowerShell, versão 1.0.0 ou posterior. Corra `Get-Module -ListAvailable Az` no seu computador, para encontrar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar o PowerShell localmente, também tem de correr `Connect-AzAccount` para iniciar seduca no Azure com uma conta que tenha as [permissões necessárias.](virtual-network-network-interface.md#permissions)

Para ativar a extração de recursos, precisa do ID de um NSG existente. Se não tiver um NSG existente, pode criar um com [o New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

Recupere o grupo de segurança de rede para o que pretende ativar a sessão de registo de recursos com [o Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Por exemplo, para recuperar um NSG chamado *myNsg* que existe num grupo de recursos chamado *myResourceGroup,* insira o seguinte comando:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Pode escrever registos de recursos em três tipos de destino. Para mais informações, consulte [os destinos Log](#log-destinations). Neste artigo, os registos são enviados para o destino *Log Analytics,* como exemplo. Recupere um espaço de trabalho log analytics existente com [o Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Por exemplo, para recuperar um espaço de trabalho existente chamado *myWorkspace* num grupo de recursos chamado *myWorkspaces, insira* o seguinte comando:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Se não tiver um espaço de trabalho existente, pode criar um com [o New-AzOperationalInsightsWorkspace.](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace)

Existem duas categorias de registos para os quais pode ativar os registos. Para obter mais informações, consulte [as categorias de Registo.](#log-categories) Ativar a sessão de registo de recursos para o NSG com [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). O exemplo a seguir regista dados de eventos e categorias de contador para o espaço de trabalho para um NSG, utilizando os IDs para o NSG e espaço de trabalho que recuperou anteriormente:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Se pretender registar apenas dados para uma categoria ou outra, em vez de ambos, adicione a `-Categories` opção ao comando anterior, seguido do *NetworkSecurityGroupEvent* ou *do NetworkSecurityGroupRuleCounter*. Se pretender registar-se num [destino](#log-destinations) diferente de um espaço de trabalho Log Analytics, utilize os parâmetros apropriados para uma [conta de Armazenamento](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) Azure ou Para o Centro de [Eventos](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).

Ver e analisar registos. Para obter mais informações, consulte [Ver e analisar registos.](#view-and-analyze-logs)

### <a name="azure-cli"></a>CLI do Azure

Pode executar os comandos que seguem na [Azure Cloud Shell,](https://shell.azure.com/bash)ou executando o CLI Azure a partir do seu computador. O Azure Cloud Shell é uma concha interativa gratuita. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se executar o CLI a partir do computador, precisa da versão 2.0.38 ou posterior. Corra `az --version` no seu computador, para encontrar a versão instalada. Se precisar de atualizar, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Se estiver a executar o CLI localmente, também tem de correr `az login` para entrar no Azure com uma conta que tenha as [permissões necessárias.](virtual-network-network-interface.md#permissions)

Para ativar a extração de recursos, precisa do ID de um NSG existente. Se não tiver um NSG existente, pode criar um com [a az network nsg criar](/cli/azure/network/nsg#az-network-nsg-create).

Recupere o grupo de segurança da rede para o que pretende permitir a registo de recursos com [o programa nsg da rede Az](/cli/azure/network/nsg#az-network-nsg-show). Por exemplo, para recuperar um NSG chamado *myNsg* que existe num grupo de recursos chamado *myResourceGroup,* insira o seguinte comando:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Pode escrever registos de recursos em três tipos de destino. Para mais informações, consulte [os destinos Log](#log-destinations). Neste artigo, os registos são enviados para o destino *Log Analytics,* como exemplo. Para obter mais informações, consulte [as categorias de Registo.](#log-categories)

Ativar a marcação de recursos para o NSG com [as definições de diagnóstico do monitor Az criar](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). O exemplo a seguir regista dados de eventos e categorias de contador para um espaço de trabalho existente chamado *myWorkspace*, que existe num grupo de recursos chamado *myWorkspaces*, e o ID do NSG que recuperou anteriormente:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Se não tiver um espaço de trabalho existente, pode criar um utilizando o [portal Azure](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [o PowerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace). Existem duas categorias de registos para os quais pode ativar os registos.

Se pretender registar apenas dados para uma categoria ou outra, remova a categoria para a qual não pretende registar dados no comando anterior. Se pretender registar-se num [destino](#log-destinations) diferente de um espaço de trabalho Log Analytics, utilize os parâmetros apropriados para uma [conta de Armazenamento](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) Azure ou Para o Centro de [Eventos](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).

Ver e analisar registos. Para obter mais informações, consulte [Ver e analisar registos.](#view-and-analyze-logs)

## <a name="log-destinations"></a>Destinos de registo

Os dados de diagnóstico podem ser:
- [Escrito numa conta de Armazenamento Azure,](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)para auditoria ou inspeção manual. Pode especificar o tempo de retenção (em dias) utilizando as definições de diagnóstico de recursos.
- [Transmitido para um centro de eventos](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs) para ingestão por um serviço de terceiros, ou solução de análise personalizada, como o PowerBI.
- [Registos escritos para Azure Monitor](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).

## <a name="log-categories"></a>Categorias de registo

Os dados formatados pelo JSON são escritos para as seguintes categorias de registo:

### <a name="event"></a>Evento

O registo do evento contém informações sobre as regras NSG aplicadas aos VMs, com base no endereço MAC. Os seguintes dados são registados para cada evento. No exemplo seguinte, os dados são registados para uma máquina virtual com o endereço IP 192.168.1.4 e um endereço MAC de 00-0D-3A-92-6A-7C:

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
        "priority":"[PRIORITY-SPECIFIED-IN-RULE]",
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

### <a name="rule-counter"></a>Contador de regras

O registo de contador de regras contém informações sobre cada regra aplicada aos recursos. Os dados de exemplo a seguir são registados sempre que uma regra é aplicada. No exemplo seguinte, os dados são registados para uma máquina virtual com o endereço IP 192.168.1.4 e um endereço MAC de 00-0D-3A-92-6A-7C:

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
> O endereço IP de origem para a comunicação não está registado. No entanto, pode ativar a registo de [fluxos NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md) para um NSG, que regista todas as informações de contador de regras, bem como o endereço IP de origem que iniciou a comunicação. Os dados de registo de fluxo do NSG são escritos numa conta de Armazenamento do Microsoft Azure. Pode analisar os dados com a capacidade de análise de [tráfego](../network-watcher/traffic-analytics.md) do Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Ver e analisar registos

Para saber como ver os dados do registo de recursos, consulte [a visão geral dos registos da plataforma Azure](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se enviar dados de diagnóstico para:
- **Registos do Monitor Azure**: Pode utilizar a solução de análise do [grupo de segurança](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) da rede para obter informações melhoradas. A solução fornece visualizações para regras NSG que permitem ou negam o tráfego, por endereço MAC, da interface de rede numa máquina virtual.
- **Conta de Armazenamento Azure**: Os dados são escritos para um PT1H.jsficheiro. Pode encontrar o:
  - Registo de eventos no seguinte caminho: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Registo de contador de regras no seguinte caminho: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o registo de atividades.](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) O registo de atividade é ativado por padrão para NSGs criados através de um modelo de implementação Azure. Para determinar quais as operações que foram concluídas em NSGs no registo de atividade, procure entradas que contenham os seguintes tipos de recursos:
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- Para aprender a registar informações de diagnóstico, para incluir o endereço IP de origem para cada fluxo, consulte [a registo de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).