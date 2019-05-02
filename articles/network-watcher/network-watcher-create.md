---
title: Criar uma instância do observador de rede do Azure | Documentos da Microsoft
description: Saiba como ativar o observador de rede numa região do Azure.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: c97f6dff17896b8a58c17aed9063e0b2b5733503
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64681565"
---
# <a name="create-an-azure-network-watcher-instance"></a>Criar uma instância do observador de rede do Azure

Observador de rede é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de cenário rede, para e do Azure. A monitorização ao nível do cenário permite-lhe diagnosticar problemas numa vista de nível de rede ponto a ponto. Diagnóstico de rede e ferramentas de visualização disponíveis com o observador de rede ajudam a compreender, diagnosticar e obter informações da sua rede no Azure. Observador de rede está ativado por meio da criação de um recurso de observador de rede. Este recurso permite-lhe utilizar as capacidades do observador de rede.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>Observador de rede é ativado automaticamente
Quando cria ou atualiza uma rede virtual na sua subscrição, o observador de rede será ativado automaticamente na região da sua rede Virtual. Não há impacto nos seus recursos ou cobrança associada para ativar automaticamente o Observador da Rede.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Sair da ativação automática de observador de rede
Se gostaria de opção de desativar ativação automática de observador de rede, pode fazê-lo ao executar os comandos seguintes:

> [!WARNING]
> Optando por-out de ativação automática de observador de rede é uma alteração permanente. Assim que sair não pode participar sem [contactar o suporte](https://azure.microsoft.com/support/options/)

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Criar um observador de rede no portal

Navegue para **todos os serviços** > **rede** > **observador de rede**. Pode selecionar todas as subscrições que pretende ativar o observador de rede para. Esta ação cria um observador de rede em cada região que está disponível.

![Criar um observador de rede](./media/network-watcher-create/figure1.png)

Quando ativar o observador de rede com o portal, o nome da instância do observador de rede é automaticamente definido como *NetworkWatcher_region_name* onde *region_name* corresponde à região do Azure em que a instância está ativada. Por exemplo, é denominado um observador de rede ativado na região e.u.a. Centro-Oeste *NetworkWatcher_westcentralus*.

A instância do observador de rede é criada automaticamente no grupo de recursos chamado *NetworkWatcherRG*. O grupo de recursos é criado caso ainda não exista.

Se desejar personalizar o nome de uma instância de observador de rede e o grupo de recursos é colocada no, pode utilizar o Powershell, a CLI do Azure, a REST API ou métodos de ARMClient descritos nas secções que se seguem. Cada opção, o grupo de recursos tem de existir antes de criar um observador de rede no mesmo.  

## <a name="create-a-network-watcher-with-powershell"></a>Criar um observador de rede com o PowerShell

Para criar uma instância do observador de rede, execute o exemplo seguinte:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Criar um observador de rede com a CLI do Azure

Para criar uma instância do observador de rede, execute o exemplo seguinte:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Criar um observador de rede com a API REST

O ARMclient é utilizada para chamar a API de REST com o PowerShell. O ARMClient encontra-se no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Inicie sessão com ARMClient

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

## <a name="next-steps"></a>Passos Seguintes

Agora que tem uma instância do observador de rede, saiba mais sobre as funcionalidades disponíveis:

* [Topologia](network-watcher-topology-overview.md)
* [Captura de pacotes](network-watcher-packet-capture-overview.md)
* [Verificação do fluxo de IP](network-watcher-ip-flow-verify-overview.md)
* [Salto seguinte](network-watcher-next-hop-overview.md)
* [Vista do grupo de segurança](network-watcher-security-group-view-overview.md)
* [Registo do fluxo do NSG](network-watcher-nsg-flow-logging-overview.md)
* [Resolução de problemas do Gateway de rede virtual](network-watcher-troubleshoot-overview.md)

Assim que uma instância do observador de rede, pode ativar a captura de pacotes nas máquinas virtuais. Para saber como, veja [criar uma captura de pacotes acionadas alerta](network-watcher-alert-triggered-packet-capture.md)
