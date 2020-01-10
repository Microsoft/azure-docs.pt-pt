---
title: Diagnosticar um problema de roteamento de máquina virtual do Azure | Microsoft Docs
description: Saiba como diagnosticar um problema de roteamento de máquina virtual exibindo as rotas efetivas para uma máquina virtual.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: kumud
ms.openlocfilehash: 13d74fbb4a7c133ca2365fd2cbfce4b3d2bea72e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75350625"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnosticar um problema de roteamento de máquina virtual

Neste artigo, você aprende a diagnosticar um problema de roteamento exibindo as rotas que são efetivas para um adaptador de rede em uma VM (máquina virtual). O Azure cria várias rotas padrão para cada sub-rede de rede virtual. Você pode substituir as rotas padrão do Azure definindo rotas em uma tabela de rotas e, em seguida, associando a tabela de rotas a uma sub-rede. A combinação de rotas que você cria, as rotas padrão do Azure e todas as rotas propagadas da sua rede local por meio de um gateway de VPN do Azure (se sua rede virtual está conectada à sua rede local) por meio do BGP (Border Gateway Protocol), são as rotas efetivas para todas as interfaces de rede em uma sub-rede. Se você não estiver familiarizado com a rede virtual, a interface de rede ou os conceitos de roteamento, consulte [visão geral da rede virtual](virtual-networks-overview.md), [interface de rede](virtual-network-network-interface.md)e [visão geral do roteamento](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Cenário

Você tenta se conectar a uma VM, mas a conexão falha. Para determinar por que você não pode se conectar à VM, você pode exibir as rotas em vigor para uma interface de rede usando o [portal](#diagnose-using-azure-portal)do Azure, o [PowerShell](#diagnose-using-powershell)ou o [CLI do Azure](#diagnose-using-azure-cli).

As etapas a seguir pressupõem que você tenha uma VM existente para exibir as rotas efetivas para o. Se você não tiver uma VM existente, primeiro implante uma VM [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para concluir as tarefas neste artigo com. Os exemplos neste artigo são para uma VM chamada *myVM* com uma interface de rede chamada *myVMNic1*. A VM e a interface de rede estão em um grupo de recursos chamado *MyResource*Group e estão na região *leste dos EUA* . Altere os valores nas etapas, conforme apropriado, para a VM para a qual você está diagnosticando o problema.

## <a name="diagnose-using-azure-portal"></a>Diagnosticar usando o portal do Azure

1. Faça logon no [portal](https://portal.azure.com) do Azure com uma conta do Azure que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions).
2. Na parte superior da portal do Azure, insira o nome de uma VM que está no estado executando, na caixa de pesquisa. Quando o nome da VM aparecer nos resultados da pesquisa, selecione-o.
3. Em **configurações** à esquerda, selecione **rede**e navegue até o recurso de interface de rede selecionando seu nome.
     ![exibir as interfaces de rede](./media/diagnose-network-routing-problem/view-nics.png)
4. À esquerda, selecione **rotas efetivas**. As rotas efetivas para uma interface de rede denominada **myVMNic1** são mostradas na figura a seguir: ![Exibir rotas efetivas](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Se houver várias interfaces de rede anexadas à VM, você poderá exibir as rotas efetivas para qualquer interface de rede selecionando-a. Como cada interface de rede pode estar em uma sub-rede diferente, cada interface de rede pode ter rotas efetivas diferentes.

    No exemplo mostrado na figura anterior, as rotas listadas são rotas padrão que o Azure cria para cada sub-rede. Sua lista tem pelo menos essas rotas, mas pode ter rotas adicionais, dependendo dos recursos que você pode ter habilitado para sua rede virtual, como ela está emparelhada com outra rede virtual ou conectada à sua rede local por meio de um gateway de VPN do Azure. Para saber mais sobre cada uma das rotas e outras rotas que você pode ver para sua interface de rede, consulte [Roteamento de tráfego de rede virtual](virtual-networks-udr-overview.md). Se sua lista tiver um grande número de rotas, talvez você ache mais fácil selecionar **baixar**para baixar um arquivo. csv com a lista de rotas.

Embora as rotas efetivas tenham sido exibidas por meio da VM nas etapas anteriores, você também pode exibir rotas efetivas por meio de um:
- **Interface de rede individual**: saiba como [exibir uma interface de rede](virtual-network-network-interface.md#view-network-interface-settings).
- **Tabela de rotas individuais**: saiba como [exibir uma tabela de rotas](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnosticar usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode executar os comandos a seguir no [Azure cloud Shell](https://shell.azure.com/powershell)ou executando o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se você executar o PowerShell do seu computador, precisará do módulo Azure PowerShell, versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` em seu computador para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar `Connect-AzAccount` para fazer logon no Azure com uma conta que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions).

Obtenha as rotas efetivas para uma interface de rede com [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable). O exemplo a seguir obtém as rotas efetivas para uma interface de rede chamada *myVMNic1*, que está em um grupo de recursos chamado *MyResource*Group:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Para entender as informações retornadas na saída, consulte [visão geral de roteamento](virtual-networks-udr-overview.md). A saída só será retornada se a VM estiver no estado executando. Se houver várias interfaces de rede anexadas à VM, você poderá examinar as rotas efetivas para cada interface de rede. Como cada interface de rede pode estar em uma sub-rede diferente, cada interface de rede pode ter rotas efetivas diferentes. Se você ainda tiver um problema de comunicação, consulte [diagnóstico](#additional-diagnosis) e [considerações](#considerations)adicionais.

Se você não souber o nome de um adaptador de rede, mas souber o nome da VM à qual o adaptador de rede está conectado, os seguintes comandos retornarão as IDs de todas as interfaces de rede anexadas a uma VM:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Você recebe uma saída semelhante ao exemplo a seguir:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

Na saída anterior, o nome da interface de rede é *myVMNic1*.

## <a name="diagnose-using-azure-cli"></a>Diagnosticar usando o CLI do Azure

Você pode executar os comandos a seguir no [Azure cloud Shell](https://shell.azure.com/bash)ou executando a CLI do seu computador. Este artigo requer o CLI do Azure versão 2.0.32 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se você estiver executando o CLI do Azure localmente, também precisará executar `az login` e fazer logon no Azure com uma conta que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions).

Obtenha as rotas efetivas para uma interface de rede com [AZ Network NIC show-efetivo-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table). O exemplo a seguir obtém as rotas efetivas para uma interface de rede chamada *myVMNic1* que está em um grupo de recursos chamado *MyResource*Group:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

Para entender as informações retornadas na saída, consulte [visão geral de roteamento](virtual-networks-udr-overview.md). A saída só será retornada se a VM estiver no estado executando. Se houver várias interfaces de rede anexadas à VM, você poderá examinar as rotas efetivas para cada interface de rede. Como cada interface de rede pode estar em uma sub-rede diferente, cada interface de rede pode ter rotas efetivas diferentes. Se você ainda tiver um problema de comunicação, consulte [diagnóstico](#additional-diagnosis) e [considerações](#considerations)adicionais.

Se você não souber o nome de um adaptador de rede, mas souber o nome da VM à qual o adaptador de rede está conectado, os seguintes comandos retornarão as IDs de todas as interfaces de rede anexadas a uma VM:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Resolver um problema

Resolver problemas de roteamento normalmente consiste em:

- Adicionar uma rota personalizada para substituir uma das rotas padrão do Azure. Saiba como [Adicionar uma rota personalizada](manage-route-table.md#create-a-route).
- Alterar ou remover uma rota personalizada que pode causar o roteamento para um local indesejado. Saiba como [alterar](manage-route-table.md#change-a-route) ou [excluir](manage-route-table.md#delete-a-route) uma rota personalizada.
- Garantir que a tabela de rotas que contém as rotas personalizadas que você definiu está associada à sub-rede em que o adaptador de rede está. Saiba como [associar uma tabela de rotas a uma sub-rede](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Garantir que dispositivos como o gateway de VPN do Azure ou dispositivos de virtualização de rede implantados sejam operáveis. Use o recurso de [diagnóstico de VPN](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do observador de rede para determinar quaisquer problemas com um gateway de VPN do Azure.

Se você ainda estiver tendo problemas de comunicação, consulte [considerações](#considerations) e diagnóstico adicional.

## <a name="considerations"></a>Considerações

Considere os seguintes pontos ao solucionar problemas de comunicação:

- O roteamento é baseado na LPM (correspondência de prefixo mais longa) entre as rotas que você definiu, BGP (Border Gateway Protocol) e rotas do sistema. Se houver mais de uma rota com a mesma correspondência de LPM, uma rota será selecionada com base em sua origem na ordem listada em [Roteamento visão geral](virtual-networks-udr-overview.md#how-azure-selects-a-route). Com as rotas efetivas, você só pode ver rotas efetivas que são uma correspondência de LPM, com base em todas as rotas disponíveis. Ver como as rotas são avaliadas para uma interface de rede torna muito mais fácil solucionar problemas de rotas específicas que podem estar afetando a comunicação da VM.
- Se você tiver definido rotas personalizadas para uma solução de virtualização de rede (NVA), com o *dispositivo virtual* como o tipo do próximo salto, verifique se o encaminhamento de IP está habilitado no NVA recebendo o tráfego ou se os pacotes foram descartados. Saiba mais sobre como [habilitar o encaminhamento de IP para uma interface de rede](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Além disso, o sistema operacional ou aplicativo dentro do NVA também deve ser capaz de encaminhar o tráfego de rede e ser configurado para fazer isso.
- Se você tiver criado uma rota para 0.0.0.0/0, todo o tráfego de Internet de saída será roteado para o próximo salto especificado, como para um gateway de NVA ou de VPN. A criação de uma rota desse tipo é geralmente conhecida como túnel forçado. Conexões remotas que usam protocolos RDP ou SSH da Internet para sua VM podem não funcionar com essa rota, dependendo de como o próximo salto manipula o tráfego. O túnel forçado pode ser habilitado:
    - Ao usar a VPN site a site, criando uma rota com um tipo do próximo salto de *Gateway de VPN*. Saiba mais sobre como [Configurar o túnel forçado](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Se 0.0.0.0/0 (rota padrão) for anunciado por meio de BGP por meio de um gateway de rede virtual ao usar uma VPN site a site ou um circuito de ExpressRoute. Saiba mais sobre como usar o BGP com uma [VPN site a site](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- Para que o tráfego de emparelhamento de rede virtual funcione corretamente, uma rota do sistema com um tipo do próximo salto de *emparelhamento VNet* deve existir para o intervalo de prefixo da rede virtual emparelhada. Se essa rota não existir e o link de emparelhamento de rede virtual estiver **conectado**:
    - Aguarde alguns segundos e tente novamente. Se for um link de emparelhamento recentemente estabelecido, ocasionalmente levará mais tempo para propagar rotas para todas as interfaces de rede em uma sub-rede. Para saber mais sobre o emparelhamento de rede virtual, consulte [visão geral de emparelhamento de rede virtual](virtual-network-peering-overview.md) e [gerenciar o emparelhamento de rede virtual](virtual-network-manage-peering.md).
    - As regras do grupo de segurança de rede podem estar afetando a comunicação. Para obter mais informações, consulte [diagnosticar um problema de filtro de tráfego de rede de máquina virtual](diagnose-network-traffic-filter-problem.md).
- Embora o Azure atribua rotas padrão para cada interface de rede do Azure, se você tiver várias interfaces de rede anexadas à VM, somente o adaptador de rede primário será atribuído a uma rota padrão (0.0.0.0/0) ou gateway no sistema operacional da VM. Saiba como criar uma rota padrão para interfaces de rede secundárias anexadas a uma VM [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) . Saiba mais sobre as [interfaces de rede primária e secundária](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Diagnóstico adicional

* Para executar um teste rápido para determinar o tipo do próximo salto para o tráfego destinado a um local, use o recurso de [próximo salto](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do observador de rede do Azure. O próximo salto informa qual é o tipo do próximo salto para o tráfego destinado a um local especificado.
* Se não houver nenhuma rota fazendo com que a comunicação de rede de uma VM falhe, o problema pode ser devido ao software de firewall em execução no sistema operacional da VM
* Se você estiver [forçando](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o tráfego de túnel para um dispositivo local por meio de um gateway de VPN, ou NVA, talvez você não consiga se conectar a uma VM da Internet, dependendo de como você configurou o roteamento para os dispositivos. Confirme se o roteamento que você configurou para o dispositivo roteia o tráfego para um endereço IP público ou privado para a VM.
* Use a funcionalidade de [solução de problemas de conexão](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do observador de rede para determinar as causas de roteamento, filtragem e no sistema operacional dos problemas de comunicação de saída.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre todas as tarefas, propriedades e configurações de uma [tabela e rotas de rota](manage-route-table.md).
- Saiba mais sobre todos os [tipos de próximo salto, rotas do sistema e como o Azure seleciona uma rota](virtual-networks-udr-overview.md).
