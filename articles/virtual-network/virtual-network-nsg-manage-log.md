---
title: Contador de regras e eventos do grupo de segurança de rede logs de diagnóstico do Azure
titlesuffix: Azure Virtual Network
description: Saiba como habilitar logs de diagnóstico do contador de regras e eventos para um grupo de segurança de rede do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 047c92f1c50409e6a1716f0ef2f774464bd12a0a
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972770"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Log de diagnóstico para um grupo de segurança de rede

Um NSG (grupo de segurança de rede) inclui regras que permitem ou negam o tráfego para uma sub-rede de rede virtual, interface de rede ou ambos. Ao habilitar o log de diagnóstico para um NSG, você pode registrar em log as seguintes categorias de informações:

* **Circunstância** As entradas são registradas para as quais as regras NSG são aplicadas às VMs, com base no endereço MAC.
* **Contador de regras:** Contém entradas para quantas vezes cada regra NSG é aplicada para negar ou permitir o tráfego. O status dessas regras é coletado a cada 60 segundos.

Os logs de diagnóstico estão disponíveis somente para NSGs implantados por meio do modelo de implantação Azure Resource Manager. Não é possível habilitar o log de diagnóstico para o NSGs implantado por meio do modelo de implantação clássico. Para uma melhor compreensão dos dois modelos, consulte [noções básicas sobre modelos de implantação do Azure](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

O log de diagnóstico é habilitado separadamente para *cada* NSG para a qual você deseja coletar dados de diagnóstico. Se você estiver interessado em logs operacionais ou de atividade, consulte [registro em log de atividades](../azure-monitor/platform/activity-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)do Azure.

## <a name="enable-logging"></a>Ativar registo

Você pode usar o [portal do Azure](#azure-portal), o [PowerShell](#powershell)ou o [CLI do Azure](#azure-cli) para habilitar o log de diagnóstico.

### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal](https://portal.azure.com).
2. Selecione **todos os serviços**e digite *grupos de segurança de rede*. Quando os **grupos de segurança de rede** aparecerem nos resultados da pesquisa, selecione-os.
3. Selecione o NSG para o qual você deseja habilitar o registro em log.
4. Em **monitoramento**, selecione **logs de diagnóstico**e, em seguida, selecione **Ativar diagnóstico**, conforme mostrado na figura a seguir:

   ![Ativar diagnósticos](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. Em **configurações de diagnóstico**, insira ou selecione as informações a seguir e, em seguida, selecione **salvar**:

    | Definição                                                                                     | Valor                                                          |
    | ---------                                                                                   |---------                                                       |
    | Name                                                                                        | Um nome de sua escolha.  Por exemplo: *myNsgDiagnostics*      |
    | **Arquivar em uma conta de armazenamento**, **transmitir para um hub de eventos**e **Enviar para log Analytics** | Você pode selecionar quantos destinos quiser. Para saber mais sobre cada um, consulte [destinos de log](#log-destinations).                                                                                                                                           |
    | LOG                                                                                         | Selecione uma ou ambas as categorias de log. Para saber mais sobre os dados registrados para cada categoria, consulte [categorias de log](#log-categories).                                                                                                                                             |
6. Exibir e analisar logs. Para obter mais informações, consulte [Exibir e analisar logs](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode executar os comandos a seguir no [Azure cloud Shell](https://shell.azure.com/powershell)ou executando o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se você executar o PowerShell do seu computador, precisará do módulo Azure PowerShell, versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` em seu computador para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar `Connect-AzAccount` para entrar no Azure com uma conta que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions).

Para habilitar o log de diagnóstico, você precisa da ID de um NSG existente. Se você não tiver um NSG existente, poderá criar um com [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

Recupere o grupo de segurança de rede para o qual você deseja habilitar o log de diagnóstico com [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Por exemplo, para recuperar um NSG chamado *myNsg* que existe em um grupo de recursos chamado *MyResource*Group, digite o seguinte comando:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Você pode gravar logs de diagnóstico em três tipos de destino. Para obter mais informações, consulte [destinos de log](#log-destinations). Neste artigo, os logs são enviados para o destino de *log Analytics* , como um exemplo. Recupere um espaço de trabalho Log Analytics existente com [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Por exemplo, para recuperar um espaço de trabalho existente chamado *MyWorkspace* em um grupo de recursos chamado *myworkspaces*, digite o seguinte comando:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Se você não tiver um espaço de trabalho existente, poderá criar um com [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Há duas categorias de registro em log para os quais você pode habilitar logs. Para obter mais informações, consulte [categorias de log](#log-categories). Habilite o log de diagnóstico para o NSG com [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). O exemplo a seguir registra dados de categoria de evento e de contador no espaço de trabalho para um NSG, usando as IDs para o NSG e o espaço de trabalho que você recuperou anteriormente:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Se você quiser apenas registrar dados para uma categoria ou outro, em vez de ambos, adicione a opção `-Categories` ao comando anterior, seguida por *NetworkSecurityGroupEvent* ou *NetworkSecurityGroupRuleCounter*. Se você quiser fazer logon em um [destino](#log-destinations) diferente de um espaço de trabalho log Analytics, use os parâmetros apropriados para uma [conta de armazenamento](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure ou Hub de [eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Exibir e analisar logs. Para obter mais informações, consulte [Exibir e analisar logs](#view-and-analyze-logs).

### <a name="azure-cli"></a>CLI do Azure

Você pode executar os comandos a seguir no [Azure cloud Shell](https://shell.azure.com/bash)ou executando o CLI do Azure do seu computador. O Azure Cloud Shell é um shell interativo gratuito. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se você executar a CLI do seu computador, precisará da versão 2.0.38 ou posterior. Execute `az --version` em seu computador para localizar a versão instalada. Se você precisar atualizar, consulte [instalar CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Se você estiver executando a CLI localmente, também precisará executar `az login` para entrar no Azure com uma conta que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions).

Para habilitar o log de diagnóstico, você precisa da ID de um NSG existente. Se você não tiver um NSG existente, poderá criar um com [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create).

Recupere o grupo de segurança de rede para o qual você deseja habilitar o log de diagnósticos com [AZ Network NSG show](/cli/azure/network/nsg#az-network-nsg-show). Por exemplo, para recuperar um NSG chamado *myNsg* que existe em um grupo de recursos chamado *MyResource*Group, digite o seguinte comando:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Você pode gravar logs de diagnóstico em três tipos de destino. Para obter mais informações, consulte [destinos de log](#log-destinations). Neste artigo, os logs são enviados para o destino de *log Analytics* , como um exemplo. Para obter mais informações, consulte [categorias de log](#log-categories).

Habilite o log de diagnóstico para o NSG com [AZ monitor Diagnostics-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). O exemplo a seguir registra dados de categoria de evento e de contador em um espaço de trabalho existente chamado *MyWorkspace*, que existe em um grupo de recursos chamado *myworkspaces*e a ID do NSG que você recuperou anteriormente:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Se você não tiver um espaço de trabalho existente, poderá criar um usando o [portal do Azure](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou o [PowerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace). Há duas categorias de registro em log para os quais você pode habilitar logs.

Se você quiser apenas registrar dados para uma categoria ou outra, remova a categoria para a qual você não deseja registrar dados no comando anterior. Se você quiser fazer logon em um [destino](#log-destinations) diferente de um espaço de trabalho log Analytics, use os parâmetros apropriados para uma [conta de armazenamento](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure ou Hub de [eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Exibir e analisar logs. Para obter mais informações, consulte [Exibir e analisar logs](#view-and-analyze-logs).

## <a name="log-destinations"></a>Destinos de log

Os dados de diagnóstico podem ser:
- [Gravado em uma conta de armazenamento do Azure](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), para auditoria ou inspeção manual. Você pode especificar o tempo de retenção (em dias) usando as configurações de diagnóstico de recurso.
- [Transmitido para um hub de eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ingestão por um serviço de terceiros, ou solução de análise personalizada, como o PowerBI.
- [Gravados em logs de Azure monitor](../azure-monitor/platform/resource-logs-collect-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-categories"></a>Categorias de registo

Os dados formatados em JSON são gravados para as seguintes categorias de log:

### <a name="event"></a>Evento

O log de eventos contém informações sobre quais regras do NSG são aplicadas às VMs, com base no endereço MAC. Os dados a seguir são registrados para cada evento. No exemplo a seguir, os dados são registrados em log para uma máquina virtual com o endereço IP 192.168.1.4 e um endereço MAC de 00-0D-3A-92-6A-7C:

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

O log do contador de regras contém informações sobre cada regra aplicada aos recursos. Os dados de exemplo a seguir são registrados toda vez que uma regra é aplicada. No exemplo a seguir, os dados são registrados em log para uma máquina virtual com o endereço IP 192.168.1.4 e um endereço MAC de 00-0D-3A-92-6A-7C:

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
> O endereço IP de origem para a comunicação não está registrado. Você pode habilitar o [log de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md) para um NSG no entanto, que registra todas as informações do contador de regras, bem como o endereço IP de origem que iniciou a comunicação. Os dados de registo de fluxo do NSG são escritos numa conta de Armazenamento do Microsoft Azure. Você pode analisar os dados com o recurso de [análise de tráfego](../network-watcher/traffic-analytics.md) do observador de rede do Azure.

## <a name="view-and-analyze-logs"></a>Exibir e analisar logs

Para saber como exibir dados de log de diagnóstico, consulte [visão geral dos logs de diagnóstico do Azure](../azure-monitor/platform/resource-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se você enviar dados de diagnóstico para:
- **Logs de Azure monitor**: Você pode usar a solução do grupo de segurança [network Analytics @ no__t-1 para obter informações avançadas. A solução fornece visualizações para regras NSG que permitem ou negam o tráfego, por endereço MAC, da interface de rede em uma máquina virtual.
- **Conta de armazenamento do Azure**: Os dados são gravados em um arquivo PT1H. JSON. Você pode encontrar:
  - Log de eventos no seguinte caminho: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Log do contador de regras no seguinte caminho: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [log de atividades](../azure-monitor/platform/resource-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), anteriormente conhecido como logs de auditoria ou operacionais. O log de atividades é habilitado por padrão para o NSGs criado por meio do modelo de implantação do Azure. Para determinar quais operações foram concluídas em NSGs no log de atividades, procure entradas que contenham os seguintes tipos de recursos:
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- Para saber como registrar em log as informações de diagnóstico, para incluir o endereço IP de origem para cada fluxo, consulte [log de fluxo do NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
