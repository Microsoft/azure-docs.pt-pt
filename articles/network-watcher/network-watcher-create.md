---
title: Criar um exemplo de Observador de Redes Azure ! Microsoft Docs
description: Saiba como criar um Observador de Rede Azure numa região do Azure utilizando o portal Azure ou outras tecnologias, e como eliminar um Observador de Redes.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: c308824afdcae5f5c04a316c199bad71ad84a429
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962013"
---
# <a name="create-an-azure-network-watcher-instance"></a>Criar uma instância do Observador de Rede do Azure

O Network Watcher é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de cenário de rede dentro, e a partir de Azure. A monitorização do nível do cenário permite diagnosticar problemas no final da vista do nível da rede. As ferramentas de diagnóstico e visualização da rede disponíveis com o Network Watcher ajudam-no a compreender, diagnosticar e a obter informações sobre a sua rede em Azure. O Network Watcher é ativado através da criação de um recurso Network Watcher. Este recurso permite-lhe utilizar as capacidades do Observador de Redes.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>O Observador de Rede está automaticamente ativado
Quando criar ou atualizar uma rede virtual na subscrição, o Observador de Rede será ativado automaticamente na região da Rede Virtual. Não existe impacto sobre os seus recursos ou custos associados à ativação automática do Observador de Rede.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Opt-out do dispositivo automático do Network Watcher
Se pretender excluir a ativação automática do Network Watcher, pode fazê-lo executando os seguintes comandos:

> [!WARNING]
> A exclusão da ativação automática do Network Watcher é uma alteração permanente. Uma vez que você opt-out não pode optar sem [contatar suporte](https://azure.microsoft.com/support/options/)

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Criar um Observador de Rede no portal

Navegue para O Observador de Rede de Todos **os Serviços.**  >  **Networking**  >  **Network Watcher** Pode selecionar todas as subscrições para as quais pretende ativar o Observador de Redes. Esta ação cria um Observador de Rede em todas as regiões que estão disponíveis.

![criar um observador de rede](./media/network-watcher-create/figure1.png)

Quando ativa o Network Watcher utilizando o portal, o nome da instância Do Observador de Rede é automaticamente definido para *NetworkWatcher_region_name* onde *region_name* corresponde à região Azure onde a instância está ativada. Por exemplo, um Observador de Rede ativado na região centro-americana ocidental é nomeado *NetworkWatcher_westcentralus*.

A instância do Observador de Rede é criada automaticamente num grupo de recursos chamado *NetworkWatcherRG*. O grupo de recursos é criado se já não existir.

Se desejar personalizar o nome de uma instância do Observador de Rede e do grupo de recursos em que é colocado, pode utilizar o PowerShell, o Azure CLI, o REST API ou os métodos ARMClient descritos nas secções que se seguem. Em cada opção, o grupo de recursos deve existir antes de criar um Observador de Rede nele.  

## <a name="create-a-network-watcher-with-powershell"></a>Criar um Observador de Rede com PowerShell

Para criar um exemplo de Network Watcher, corra o seguinte exemplo:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Criar um Observador de Rede com o Azure CLI

Para criar um exemplo de Network Watcher, corra o seguinte exemplo:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Criar um Observador de Rede com a API REST

O ARMclient é utilizado para chamar a API REST usando PowerShell. O ARMClient é encontrado em chocolate na [ARMClient em Chocolatey](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Iniciar sessão com ARMClient

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

## <a name="create-a-network-watcher-using-azure-quickstart-template"></a>Criar um Observador de Rede usando o modelo de arranque rápido do Azure

Para criar uma instância de Observador de Rede consulte este [Modelo quickstart](https://azure.microsoft.com/resources/templates/101-networkwatcher-create/)

## <a name="delete-a-network-watcher-in-the-portal"></a>Excluir um Observador de Rede no portal

Navegue para O Observador de Rede de Todos **os Serviços.**  >  **Networking**  >  **Network Watcher**

Selecione o separador visão geral, se ainda não estiver lá. Utilize o dropdown para selecionar a subscrição em que pretende desativar o observador de rede.
Expanda a lista de regiões para a sua subscrição escolhida clicando na seta. Para qualquer um dado, use os 3 pontos no direito de aceder ao menu de contexto.
Clique em "Desativar o observador de rede" para começar a desativar. Pedir-lhe-á que confirme este passo. Clique em Sim para continuar.
No portal, terá de o fazer individualmente para cada região em cada subscrição.


## <a name="delete-a-network-watcher-with-powershell"></a>Excluir um Observador de Rede com PowerShell

Para eliminar uma instância do Observador de Rede, corra o seguinte exemplo:

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>Passos seguintes

Agora que tem um exemplo de Network Watcher, conheça as funcionalidades disponíveis:

* [Topologia](./view-network-topology.md)
* [Captura de pacotes](network-watcher-packet-capture-overview.md)
* [Verificação do fluxo de IP](network-watcher-ip-flow-verify-overview.md)
* [Próximo salto](network-watcher-next-hop-overview.md)
* [Vista do grupo de segurança](network-watcher-security-group-view-overview.md)
* [Registo de fluxo nSG](network-watcher-nsg-flow-logging-overview.md)
* [Resolução de problemas do Gateway de Rede Virtual](network-watcher-troubleshoot-overview.md)