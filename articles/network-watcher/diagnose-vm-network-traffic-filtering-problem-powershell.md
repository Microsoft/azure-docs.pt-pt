---
title: 'Quickstart: Diagnosticar um problema de filtro de tráfego de rede VM - Azure PowerShell'
titleSuffix: Azure Network Watcher
description: Aprenda a utilizar o Azure PowerShell para diagnosticar um problema de filtro de tráfego de rede de máquinas virtuais utilizando a capacidade de verificação do fluxo IP do Observador de Redes Azure.
services: network-watcher
documentationcenter: network-watcher
author: damendo
ms.author: damendo
editor: ''
ms.date: 01/07/2021
ms.assetid: ''
ms.topic: quickstart
ms.service: network-watcher
ms.workload: infrastructure
ms.tgt_pltfrm: network-watcher
ms.devlang: na
tags:
- azure-resource-manager
ms.custom:
- mvc
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: fafe090d72d53b33ddb3e9863b7e62deba1055c5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535771"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-powershell"></a>Guia de Início Rápido: Diagnosticar um problema de filtro de tráfego de rede na máquina virtual – Azure PowerShell

Neste guia de início rápido, implemente uma máquina virtual (VM) e, em seguida, verifique as comunicações para um endereço IP e URL e de um endereço IP. Vai determinar a causa de uma falha de comunicação e aprender a resolvê-la.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este arranque rápido requer o módulo Azure `Az` PowerShell. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.



## <a name="create-a-vm"></a>Criar uma VM

Antes de criar uma VM, tem de criar um grupo de recursos para conter a VM. Criar um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Crie o VM com [New-AzVM](/powershell/module/az.compute/new-azvm). Ao executar este passo, serão pedidas credenciais. Os valores que introduzir são configurados, como o nome de utilizador e a palavra-passe para a VM.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

A criação da VM demora alguns minutos. Não continue com os restantes passos até que a VM seja criada e o PowerShell devolva um resultado.

## <a name="test-network-communication"></a>Testar a comunicação de rede

Para testar a comunicação de rede com o Observador de Rede, tem primeiro ativar um observador de rede na região onde se encontra a VM que pretende testar e, em seguida, utilizar a função de verificação do fluxo IP do Observador de Rede para testar a comunicação.

### <a name="enable-network-watcher"></a>Ativar o observador de rede

Se já tiver um observador de rede ativado na região leste dos EUA, utilize o [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) para recuperar o observador de rede. O seguinte exemplo obtém um observador de rede existente com o nome *NetworkWatcher_eastus* que se encontra no grupo de recursos *NetworkWatcherRG*:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Se ainda não tiver um observador de rede ativado na região leste dos EUA, utilize o [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) para criar um observador de rede na região leste dos EUA:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-ip-flow-verify"></a>Utilizar a verificação do fluxo IP

Quando cria uma VM, o Azure permite e recusa o tráfego de rede de e para a VM, por predefinição. Mais tarde, poderá substituir as predefinições do Azure, ao permitir ou recusar tipos adicionais de tráfego. Para testar se o tráfego é permitido ou negado a diferentes destinos e a partir de um endereço IP de origem, utilize o comando [Test-AzNetworkWatcherIPFlow.](/powershell/module/az.network/test-aznetworkwatcheripflow)

Teste a comunicação de saída a partir da VM para um dos endereços IP para www.bing.com:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 13.107.21.200 `
  -RemotePort 80
```

Após alguns segundos, o resultado devolvido informa-o de que é permitido o acesso devido a uma regra de segurança denominada **AllowInternetOutbound**.

Teste a comunicação de saída da VM para 172.31.0.100:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 80
```

O resultado devolvido informa-o de que o acesso foi recusado devido a uma regra de segurança denominada **DefaultOutboundDenyAll**.

Teste a comunicação de entrada para a VM a partir de 172.31.0.100:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Inbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 80 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 60000
```

O resultado devolvido informa-o de que o acesso foi recusado devido a uma regra de segurança denominada **DefaultInboundDenyAll**. Agora que sabe quais as regras de segurança que estão a permitir ou a recusar tráfego de ou para uma VM, pode determinar como resolver os problemas.

## <a name="view-details-of-a-security-rule"></a>Ver detalhes de uma regra de segurança

Para determinar por que as regras da [comunicação](#test-network-communication) da rede de teste estão a permitir ou impedir a comunicação, reveja as regras de segurança eficazes para a interface de rede com [o Get-AzEffectiveNetworkSecurityGroup:](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup
```

O resultado devolvido inclui o seguinte texto para a regra **AllowInternetOutbound** que permitiu o acesso de saída para www.bing.com em [Utilizar a verificação do fluxo IP](#use-ip-flow-verify):

```powershell
{
  "Name":
"defaultSecurityRules/AllowInternetOutBound",
  "Protocol": "All",
  "SourcePortRange": [
    "0-65535"
  ],
  "DestinationPortRange": [
    "0-65535"
  ],
  "SourceAddressPrefix": [
    "0.0.0.0/0"
  ],
  "DestinationAddressPrefix": [
    "Internet"
  ],
  "ExpandedSourceAddressPrefix": [],
  "ExpandedDestinationAddressPrefix": [
    "1.0.0.0/8",
    "2.0.0.0/7",
    "4.0.0.0/6",
    "8.0.0.0/7",
    "11.0.0.0/8",
    "12.0.0.0/6",
    ...
    ],
    "Access": "Allow",
    "Priority": 65001,
    "Direction": "Outbound"
  },
```

Pode ver no resultado que **DestinationAddressPrefix** é **Internet**. No entanto, não é claro como 13.107.21.200, o endereço testado em [Utilizar a verificação do fluxo IP](#use-ip-flow-verify), está relacionado com **Internet**. Verá vários prefixos de endereço numa lista, em **ExpandedDestinationAddressPrefix**. Um dos prefixos na lista é **12.0.0.0/6**, que abrange o intervalo 12.0.0.1-15.255.255.254 de endereços IP. Uma vez que 13.107.21.200 está dentro desse intervalo de endereços, a regra **AllowInternetOutBound** permite o tráfego de saída. Além disso, não existem regras com **prioridade** superior (número inferior) apresentadas no resultado devolvido por `Get-AzEffectiveNetworkSecurityGroup` que substituam esta regra. Para recusar comunicações de saída para 13.107.21.200, pode adicionar uma regra de segurança com uma prioridade mais elevada, que recusa a saída da porta 80 para o endereço IP.

Quando executou o comando `Test-AzNetworkWatcherIPFlow` para testar a comunicação de saída para 172.131.0.100 em [Utilizar a verificação do fluxo IP](#use-ip-flow-verify), o resultado informou-o de que a regra **DefaultOutboundDenyAll** recusou a comunicação. A regra **DefaultOutboundDenyAll** equivale à regra **DenyAllOutBound** apresentada no resultado seguinte do comando `Get-AzEffectiveNetworkSecurityGroup`:

```powershell
{
"Name": "defaultSecurityRules/DenyAllOutBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Outbound"
}
```

A regra apresenta **0.0.0.0/0** como o **DestinationAddressPrefix**. A regra recusa a comunicação de saída para 172.131.0.100, porque o endereço não se encontra dentro do **DestinationAddressPrefix** de qualquer uma das outras regras de saída no resultado do comando `Get-AzEffectiveNetworkSecurityGroup`. Para permitir a comunicação de saída, pode adicionar uma regra de segurança com uma prioridade mais elevada, que permite o tráfego de saída para a porta 80 em 172.131.0.100.

Quando executou o comando `Test-AzNetworkWatcherIPFlow` para testar a comunicação de entrada de 172.131.0.100 em [Utilizar a verificação do fluxo IP](#use-ip-flow-verify), o resultado informou-o de que a regra **DefaultInboundDenyAll** recusou a comunicação. A regra **DefaultInboundDenyAll** equivale à regra **DenyAllInBound** apresentada no resultado seguinte do comando `Get-AzEffectiveNetworkSecurityGroup`:

```powershell
{
"Name": "defaultSecurityRules/DenyAllInBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Inbound"
},
```

A regra **DenyAllInBound** é aplicada porque, conforme apresentado no resultado, não existe outra regra de prioridade superior no resultado do comando `Get-AzEffectiveNetworkSecurityGroup` que permita a entrada da porta 80 para a VM de 172.131.0.100. Para permitir a comunicação de entrada, pode adicionar uma regra de segurança com uma prioridade superior que permita a entrada da porta 80 de 172.131.0.100.

As verificações neste guia de início rápido testaram a configuração do Azure. Se as verificações devolverem os resultados esperados e continuar a ter problemas de rede, certifique-se de que não tem uma firewall entre a VM e o ponto final com o qual está a comunicar e que o sistema operativo na VM não tem uma firewall que esteja a permitir ou a recusar a comunicação.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que contém:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, criou uma VM e diagnosticou filtros de tráfego de rede de entrada e saída. Aprendeu que as regras do grupo de segurança de rede permitem ou recusam tráfego de e para uma VM. Saiba mais sobre [regras de segurança](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e como [criar regras de segurança](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Mesmo com os filtros de tráfego de rede adequados ativados, a comunicação para uma VM ainda pode falhar, devido à configuração de encaminhamento. Para saber como diagnosticar problemas de encaminhamento de rede de VM, veja [Diagnosticar problemas de encaminhamento de VM](diagnose-vm-network-routing-problem-powershell.md) ou, para diagnosticar problemas de encaminhamento de saída, latência e filtro de tráfego, com uma ferramenta, veja [Resolver problemas de ligação](network-watcher-connectivity-powershell.md).
