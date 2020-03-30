---
title: Registo de diagnóstico para um grupo de segurança de rede
titlesuffix: Azure Virtual Network
description: Aprenda a ativar registos de diagnóstico de eventos e regras contra diagnósticos para um grupo de segurança de rede Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 9829e713f19ab9755e9dc79d676446c8048e09b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751177"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Registo de diagnóstico para um grupo de segurança de rede

Um grupo de segurança de rede (NSG) inclui regras que permitem ou negam o tráfego a uma subnet de rede virtual, interface de rede, ou ambos. Quando ativa o registo de diagnóstico para um NSG, pode registar as seguintes categorias de informação:

* **Evento:** As inscrições são registadas para as quais as regras de NSG são aplicadas aos VMs, com base no endereço MAC.
* **Contador de regras:** Contém entradas para quantas vezes cada regra do NSG é aplicada para negar ou permitir o tráfego. O estatuto destas regras é recolhido a cada 60 segundos.

Os registos de diagnóstico só estão disponíveis para NSGs implantados através do modelo de implementação do Gestor de Recursos Azure. Não é possível permitir o registo de diagnóstico para NSGs implantado através do modelo de implementação clássico. Para uma melhor compreensão dos dois modelos, consulte os modelos de [implementação Understanding Azure.](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

O registo de diagnóstico está ativado separadamente para *cada* NSG para o que pretende recolher dados de diagnóstico. Se estiver interessado em operações operacionais, ou atividade, faça login, consulte a [exploração madeireira](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)da atividade do Azure.

## <a name="enable-logging"></a>Ativar registo

Pode utilizar o [Portal Azure,](#azure-portal) [powerShell](#powershell)ou o [Azure CLI](#azure-cli) para permitir o registo de diagnóstico.

### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal](https://portal.azure.com).
2. Selecione **todos os serviços,** em seguida, digite *grupos de segurança de rede*. Quando os **grupos** de segurança da Rede aparecerem nos resultados da pesquisa, selecione-os.
3. Selecione o NSG para o que pretende ativar a exploração de madeira.
4. Em **monitorização,** selecione **registos**de diagnósticos, e, em seguida, selecione **ligar os diagnósticos,** como mostra a seguinte imagem:

   ![Ativar os diagnósticos](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. Sob **as definições de Diagnóstico,** introduza ou selecione as seguintes informações e, em seguida, selecione **Guardar:**

    | Definição                                                                                     | Valor                                                          |
    | ---------                                                                                   |---------                                                       |
    | Nome                                                                                        | Um nome à sua escolha.  Por exemplo: *myNsgDiagnostics*      |
    | **Arquivar para uma conta**de armazenamento , **Stream para um centro de eventos**, e **Enviar para Log Analytics** | Pode selecionar quantos destinos quiser. Para saber mais sobre cada um, consulte [os destinos de Registo.](#log-destinations)                                                                                                                                           |
    | LOG                                                                                         | Selecione qualquer uma, ou ambas as categorias de registo. Para saber mais sobre os dados registados para cada categoria, consulte [as categorias de Registo](#log-categories).                                                                                                                                             |
6. Ver e analisar registos. Para mais informações, consulte [Ver e analisar registos](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode executar os comandos que se seguem na Casca de [Nuvem Azure,](https://shell.azure.com/powershell)ou executando a PowerShell a partir do seu computador. A Casca de Nuvem Azure é uma concha interativa gratuita. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se executar o PowerShell a partir do seu computador, precisa do módulo Azure PowerShell, versão 1.0.0 ou posterior. Navere `Get-Module -ListAvailable Az` no seu computador, para encontrar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar a PowerShell `Connect-AzAccount` localmente, também precisa de correr para iniciar sessão no Azure com uma conta que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions).

Para permitir o registo de diagnóstico, precisa da identificação de um NSG existente. Se não tiver um NSG existente, pode criar um com [o New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

Recupere o grupo de segurança da rede que pretende ativar o registo de diagnóstico com [o Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Por exemplo, para recuperar um NSG chamado *myNsg* que existe num grupo de recursos chamado *myResourceGroup,* insira o seguinte comando:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Pode escrever registos de diagnóstico para três tipos de destino. Para mais informações, consulte os [destinos de Registo](#log-destinations). Neste artigo, os registos são enviados para o destino *Log Analytics,* como exemplo. Recupere um espaço de trabalho existente no Log Analytics com [o Get-AzOperationalInsightsWorkspacespace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Por exemplo, para recuperar um espaço de trabalho existente chamado *myWorkspace* num grupo de recursos chamado *myWorkspaces,* insira o seguinte comando:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Se não tiver um espaço de trabalho existente, pode criar um com [o Espaço New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Existem duas categorias de loga para as quais pode ativar registos. Para mais informações, consulte [as categorias de Registo](#log-categories). Ative o registo de diagnóstico para o NSG com [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). O exemplo seguinte regista dados de categoria seleção e contra-categoria para o espaço de trabalho de um NSG, utilizando os IDs para o NSG e espaço de trabalho que recuperou anteriormente:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Se pretender apenas registar dados para uma categoria ou para `-Categories` outra, em vez de ambas, adicione a opção ao comando anterior, seguido do *NetworkSecurityGroupEvent* ou *do NetworkSecurityGroupRuleCounter*. Se pretender iniciar sessão num [destino](#log-destinations) diferente de um espaço de trabalho log Analytics, utilize os parâmetros apropriados para uma conta de [Armazenamento](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure ou Hub de [Eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Ver e analisar registos. Para mais informações, consulte [Ver e analisar registos](#view-and-analyze-logs).

### <a name="azure-cli"></a>CLI do Azure

Pode executar os comandos que se seguem na Casca de [Nuvem Azure,](https://shell.azure.com/bash)ou executando o Azure CLI a partir do seu computador. A Casca de Nuvem Azure é uma concha interativa gratuita. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se executar o CLI a partir do seu computador, precisa da versão 2.0.38 ou posterior. Navere `az --version` no seu computador, para encontrar a versão instalada. Se precisar de fazer o upgrade, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Se estiver a executar o CLI localmente, também precisa de correr `az login` para iniciar sessão no Azure com uma conta que tenha as [permissões necessárias.](virtual-network-network-interface.md#permissions)

Para permitir o registo de diagnóstico, precisa da identificação de um NSG existente. Se não tiver um NSG existente, pode criar um com a [rede az nsg criar](/cli/azure/network/nsg#az-network-nsg-create).

Recupere o grupo de segurança da rede que pretende ativar o registo de diagnóstico com [o programa nsg da rede Az](/cli/azure/network/nsg#az-network-nsg-show). Por exemplo, para recuperar um NSG chamado *myNsg* que existe num grupo de recursos chamado *myResourceGroup,* insira o seguinte comando:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Pode escrever registos de diagnóstico para três tipos de destino. Para mais informações, consulte os [destinos de Registo](#log-destinations). Neste artigo, os registos são enviados para o destino *Log Analytics,* como exemplo. Para mais informações, consulte [as categorias de Registo](#log-categories).

Ative o registo de diagnóstico para o NSG com [a criação de definições de diagnóstico az monitor .](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) O exemplo seguinte regista dados de eventos e contracategorias para um espaço de trabalho existente chamado *myWorkspace*, que existe num grupo de recursos chamado *myWorkspaces*, e o ID do NSG que recuperou anteriormente:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Se não tiver um espaço de trabalho existente, pode criar um utilizando o [portal Azure](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [powerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace). Existem duas categorias de loga para as quais pode ativar registos.

Se pretender registar dados para uma categoria ou outra, remova a categoria para a qual não pretende registar dados no comando anterior. Se pretender iniciar sessão num [destino](#log-destinations) diferente de um espaço de trabalho log Analytics, utilize os parâmetros apropriados para uma conta de [Armazenamento](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure ou Hub de [Eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Ver e analisar registos. Para mais informações, consulte [Ver e analisar registos](#view-and-analyze-logs).

## <a name="log-destinations"></a>Destinos de log

Os dados de diagnóstico podem ser:
- [Escrito numa conta de Armazenamento Azure,](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)para auditoria ou inspeção manual. Pode especificar o tempo de retenção (em dias) utilizando as definições de diagnóstico de recursos.
- [Transmitido para um hub de evento](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ingestão por um serviço de terceiros, ou solução de análise personalizada, como o PowerBI.
- [Escrito para os registos do Monitor Azure](../azure-monitor/platform/resource-logs-collect-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-categories"></a>Categorias de registo

Os dados formados pela JSON são escritos para as seguintes categorias de registo:

### <a name="event"></a>Evento

O registo do evento contém informações sobre as regras de NSG aplicadas aos VMs, com base no endereço MAC. Os seguintes dados são registados para cada evento. No exemplo seguinte, os dados são registados para uma máquina virtual com o endereço IP 192.168.1.4 e um endereço MAC de 00-0D-3A-92-6A-7C:

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

O contra-registo de regras contém informações sobre cada regra aplicada aos recursos. Os seguintes dados de exemplo são registados cada vez que uma regra é aplicada. No exemplo seguinte, os dados são registados para uma máquina virtual com o endereço IP 192.168.1.4 e um endereço MAC de 00-0D-3A-92-6A-7C:

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
> O endereço IP de origem para a comunicação não está registado. No entanto, pode ativar o registo de [fluxo sancionatório de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md) para um NSG, que regista todas as informações do contador de regras, bem como o endereço IP de origem que iniciou a comunicação. Os dados de registo de fluxo do NSG são escritos numa conta de Armazenamento do Microsoft Azure. Pode analisar os dados com a capacidade de análise de [tráfego](../network-watcher/traffic-analytics.md) do Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Ver e analisar registos

Para aprender a visualizar os dados de registo de diagnóstico, consulte a visão geral dos [Registos de Diagnóstico do Azure](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se enviar dados de diagnóstico para:
- **Registos do Monitor Azure:** Pode utilizar a solução de análise do grupo de segurança da [rede](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) para obter informações melhoradas. A solução fornece visualizações para regras de NSG que permitem ou negam o tráfego, por endereço MAC, da interface de rede numa máquina virtual.
- **Conta de Armazenamento Azure**: Os dados são escritos num ficheiro PT1H.json. Pode encontrar o:
  - Registo do evento no seguinte caminho:`insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Registo do contador de regras no seguinte caminho:`insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o registo de atividades](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), anteriormente conhecidos como registos de auditoria ou operacionais. O registo de atividades é ativado por padrão para NSGs criados através de qualquer modelo de implementação Azure. Para determinar quais as operações concluídas nos NSGs no registo de atividade, procure entradas que contenham os seguintes tipos de recursos:
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- Para aprender a registar informações de diagnóstico, para incluir o endereço IP de origem para cada fluxo, consulte o [registo de fluxo sancionatório de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
