---
title: Criar uma instância de Vigilante da Rede Azure  Microsoft Docs
description: Saiba como ativar o Network Watcher numa região do Azure.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: b091c501fb565fb267c40f686dc037e41f3c90ee
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845118"
---
# <a name="create-an-azure-network-watcher-instance"></a>Criar uma instância de Observador de Rede Azure

O Network Watcher é um serviço regional que lhe permite monitorizar e diagnosticar condições ao nível do cenário da rede, de e para o Azure. A monitorização do nível de cenário permite diagnosticar problemas no final da visão do nível da rede. As ferramentas de diagnóstico e visualização de rede disponíveis com o observador de rede ajudam você a entender, diagnosticar e obter informações sobre sua rede no Azure. O Network Watcher está ativado através da criação de um recurso Do Observador de Rede. Este recurso permite-lhe utilizar as capacidades do Observador de Rede.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>O Observador de Rede está ativado automaticamente
Quando criar ou atualizar uma rede virtual na sua subscrição, o Observador de Rede será ativado automaticamente na região da rede Virtual. Não há impacto nos seus recursos ou cobrança associada para ativar automaticamente o Observador da Rede.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Opt-out da ativação automática do Observador de Rede
Se quiser optar por não ativar o Observador de Rede, pode fazê-lo executando os seguintes comandos:

> [!WARNING]
> A exclusão da ativação automática do Observador de Rede é uma alteração permanente. Uma vez que opte por não optar, não pode optar sem [contactar o suporte](https://azure.microsoft.com/support/options/)

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Criar um Observador de Rede no portal

Navegue para **todos os serviços** > **Networking** > **Network Watcher**. Pode selecionar todas as subscrições que pretende ativar o Network Watcher. Esta ação cria um Observador de Rede em todas as regiões disponíveis.

![Criar um observador de rede](./media/network-watcher-create/figure1.png)

Quando ativa o Observador de Rede utilizando o portal, o nome da instância Do Observador da Rede é automaticamente definido para *NetworkWatcher_region_name* onde *region_name* corresponde à região do Azure onde a instância está ativada. Por exemplo, um Observador de Rede ativado na região centro-oeste dos EUA é nomeado *NetworkWatcher_westcentralus*.

A instância Do Observador de Rede é criada automaticamente num grupo de recursos chamado *NetworkWatcherRG*. O grupo de recursos é criado se ainda não existir.

Se desejar personalizar o nome de uma instância do Observador de Rede e o grupo de recursos em que é colocado, pode utilizar o Powershell, o Azure CLI, o REST API ou os métodos ARMClient descritos nas secções que se seguem. Em cada opção, o grupo de recursos deve existir antes de criar um Observador de Rede nele.  

## <a name="create-a-network-watcher-with-powershell"></a>Criar um Observador de Rede com PowerShell

Para criar uma instância de Observador de Rede, corra o seguinte exemplo:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Criar um Observador de Rede com o Azure CLI

Para criar uma instância de Observador de Rede, corra o seguinte exemplo:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Criar um Observador de Rede com a API REST

O ARMclient é usado para chamar a API REST usando powerShell. O ARMClient é encontrado em chocolate na [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Fazer logon com ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Criar o observador de rede

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="delete-a-network-watcher-in-the-portal"></a>Eliminar um Observador de Rede no portal

Navegue para **todos os serviços** > **Networking** > **Network Watcher**.

Selecione o separador de visão geral, se ainda não estiver lá. Utilize o dropdown para selecionar a subscrição em que pretende desativar o observador da rede.
Expanda a lista de regiões para a sua subscrição escolhida clicando na seta. Para qualquer dado, utilize os 3 pontos no direito de aceder ao menu de contexto.
Clique em "Disable network watcher" para começar a desativar. Será-lhe pedido que confirme este passo. Clique sim para continuar.
No portal, terá de o fazer individualmente para cada região em cada subscrição.


## <a name="delete-a-network-watcher-with-powershell"></a>Eliminar um Observador de Rede com PowerShell

Para eliminar uma instância do Observador de Rede, execute o seguinte exemplo:

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma instância de Network Watcher, saiba mais sobre as funcionalidades disponíveis:

* [Topologia](network-watcher-topology-overview.md)
* [Captura de pacote](network-watcher-packet-capture-overview.md)
* [Verificação do fluxo de IP](network-watcher-ip-flow-verify-overview.md)
* [Salto seguinte](network-watcher-next-hop-overview.md)
* [Vista do grupo de segurança](network-watcher-security-group-view-overview.md)
* [Exploração de fluxo saneado do NSG](network-watcher-nsg-flow-logging-overview.md)
* [Resolução de problemas da Rede Virtual Gateway](network-watcher-troubleshoot-overview.md)

Uma vez que uma instância do Observador de Rede é, pode ativar a captura de pacotes dentro de máquinas virtuais. Para saber como, ver [Criar um alerta desencadeado captura de pacote](network-watcher-alert-triggered-packet-capture.md)
