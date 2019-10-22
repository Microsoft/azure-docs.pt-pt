---
title: Criar uma instância do observador de rede do Azure | Microsoft Docs
description: Saiba como habilitar o observador de rede em uma região do Azure.
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
ms.openlocfilehash: fd293c2815721295715c5e02846c55d4cdb74a32
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693475"
---
# <a name="create-an-azure-network-watcher-instance"></a>Criar uma instância do observador de rede do Azure

O observador de rede é um serviço regional que permite monitorar e diagnosticar condições em um nível de cenário de rede em, para e do Azure. O monitoramento de nível de cenário permite que você diagnostique problemas em uma exibição de nível de rede de ponta a ponta. As ferramentas de diagnóstico e visualização de rede disponíveis com o observador de rede ajudam você a entender, diagnosticar e obter informações sobre sua rede no Azure. O observador de rede está habilitado por meio da criação de um recurso do observador de rede. Esse recurso permite que você utilize os recursos do observador de rede.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>O observador de rede é habilitado automaticamente
Quando você cria ou atualiza uma rede virtual em sua assinatura, o observador de rede será habilitado automaticamente na região da sua rede virtual. Não há impacto nos seus recursos ou cobrança associada para ativar automaticamente o Observador da Rede.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Recusa da habilitação automática do observador de rede
Se você quiser recusar a habilitação automática do observador de rede, poderá fazer isso executando os seguintes comandos:

> [!WARNING]
> A recusa da habilitação automática do observador de rede é uma alteração permanente. Depois de recusar, você não pode aceitar sem [entrar em contato com o suporte](https://azure.microsoft.com/support/options/)

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Criar um observador de rede no portal

Navegue até **todos os serviços**  > **rede**  > **observador de rede**. Você pode selecionar todas as assinaturas para as quais deseja habilitar o observador de rede. Essa ação cria um observador de rede em todas as regiões disponíveis.

![criar um observador de rede](./media/network-watcher-create/figure1.png)

Quando você habilita o observador de rede usando o portal, o nome da instância do observador de rede é definido automaticamente como *NetworkWatcher_region_name* , em que *REGION_NAME* corresponde à região do Azure onde a instância está habilitada. Por exemplo, um observador de rede habilitado na região Oeste EUA Central é denominado *NetworkWatcher_westcentralus*.

A instância do observador de rede é criada automaticamente em um grupo de recursos chamado *NetworkWatcherRG*. O grupo de recursos será criado se ele ainda não existir.

Se desejar personalizar o nome de uma instância do observador de rede e o grupo de recursos no qual ele é colocado, você poderá usar o PowerShell, o CLI do Azure, a API REST ou os métodos ARMClient descritos nas seções a seguir. Em cada opção, o grupo de recursos deve existir antes de você criar um observador de rede nele.  

## <a name="create-a-network-watcher-with-powershell"></a>Criar um observador de rede com o PowerShell

Para criar uma instância do observador de rede, execute o seguinte exemplo:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Criar um observador de rede com o CLI do Azure

Para criar uma instância do observador de rede, execute o seguinte exemplo:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Criar um observador de rede com a API REST

O ARMclient é usado para chamar a API REST usando o PowerShell. O ARMClient é encontrado no Chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

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

## <a name="delete-a-network-watcher-in-the-portal"></a>Excluir um observador de rede no portal

Navegue até **todos os serviços**  > **rede**  > **observador de rede**.

Selecione a guia Visão geral, se você ainda não estiver lá. Use a lista suspensa para selecionar a assinatura na qual você deseja desabilitar o observador de rede.
Expanda a lista de regiões para sua assinatura escolhida clicando na seta. Para qualquer dado, use os 3 pontos à direita para acessar o menu de contexto.
Clique em "desabilitar observador de rede" para iniciar a desabilitação. Você será solicitado a confirmar esta etapa. Clique em Sim para continuar.
No portal, você precisará fazer isso individualmente para cada região em cada assinatura.


## <a name="delete-a-network-watcher-with-powershell"></a>Excluir um observador de rede com o PowerShell

Para excluir uma instância do observador de rede, execute o seguinte exemplo:

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>Passos seguintes

Agora que você tem uma instância do observador de rede, saiba mais sobre os recursos disponíveis:

* [Topologia](network-watcher-topology-overview.md)
* [Captura de pacote](network-watcher-packet-capture-overview.md)
* [Verificação do fluxo de IP](network-watcher-ip-flow-verify-overview.md)
* [Salto seguinte](network-watcher-next-hop-overview.md)
* [Vista do grupo de segurança](network-watcher-security-group-view-overview.md)
* [Log de fluxo do NSG](network-watcher-nsg-flow-logging-overview.md)
* [Solução de problemas do gateway de rede virtual](network-watcher-troubleshoot-overview.md)

Depois de uma instância do observador de rede, você pode habilitar a captura de pacotes em máquinas virtuais. Para saber como, consulte [criar uma captura de pacote disparada por alerta](network-watcher-alert-triggered-packet-capture.md)
