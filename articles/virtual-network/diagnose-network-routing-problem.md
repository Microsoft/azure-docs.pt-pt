---
title: Diagnosticar um problema de encaminhamento de máquina virtual do Azure | Documentos da Microsoft
description: Saiba como diagnosticar um problema de encaminhamento de máquina virtual ao visualizar as rotas efetivas para uma máquina virtual.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: jdial
ms.openlocfilehash: 6864e282319bc5a0539c4c94f3062dcab7315970
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652251"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnosticar um problema de encaminhamento de máquina virtual

Neste artigo, saiba como diagnosticar um problema de encaminhamento ao visualizar as rotas que estão em vigor para uma interface de rede numa máquina virtual (VM). O Azure cria várias rotas padrão para cada sub-rede da rede virtual. Pode substituir as rotas predefinidas do Azure, definir rotas numa tabela de rotas e, em seguida, associar a tabela de rotas a uma sub-rede. A combinação de rotas que cria, as rotas predefinidas do Azure e quaisquer rotas propagadas a partir da rede no local através de um gateway de VPN do Azure (se a rede virtual está ligada à sua rede no local) através do protocolo de gateway do limite (BGP), são o rotas efetivas para todas as interfaces de rede numa sub-rede. Se não estiver familiarizado com a rede virtual, a interface de rede ou o roteamento de conceitos, veja [descrição geral de rede Virtual](virtual-networks-overview.md), [interface de rede](virtual-network-network-interface.md), e [descrição geral do encaminhamento](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Cenário

Está tentando se conectar a uma VM, mas a ligação falha. Para determinar por que não é possível ligar à VM, pode ver as rotas efetivas para interfaces de rede através do Azure [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), ou o [da CLI do Azure](#diagnose-using-azure-cli).

Os passos que se seguem partem do princípio de que tem uma VM existente para ver as rotas efetivas para. Se não tiver uma VM existente, primeiro implementar um [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM para concluir as tarefas neste artigo com. Os exemplos neste artigo são para uma VM com o nome *myVM* com uma interface de rede com o nome *myVMVMNic*. A interface de rede e a VM estiver num grupo de recursos com o nome *myResourceGroup*e o *E.U.A. Leste* região. Altere os valores nos passos, conforme apropriado, para diagnosticar o problema para a VM.

## <a name="diagnose-using-azure-portal"></a>Diagnosticar através do portal do Azure

1. Inicie sessão no Azure [portal](https://portal.azure.com) com uma conta do Azure com o [as permissões necessárias](virtual-network-network-interface.md#permissions).
2. Na parte superior do portal do Azure, introduza o nome de uma VM que está em execução, na caixa de pesquisa. Quando o nome da VM for apresentada nos resultados da pesquisa, selecione-o.
3. Selecione **diagnosticar e resolver problemas**e, em **etapas recomendadas**, selecione **rotas efetivas** no item 7, conforme mostrado na imagem seguinte:

    ![Ver as rotas efetivas](./media/diagnose-network-routing-problem/view-effective-routes.png)

4. As rotas a efetivas para uma interface de rede com o nome **myVMVMNic** são mostrados na imagem seguinte:

     ![Ver as rotas efetivas](./media/diagnose-network-routing-problem/effective-routes.png)

    Se existirem várias interfaces de rede ligados à VM, pode ver as rotas efetivas para qualquer interface de rede ao selecioná-la. Uma vez que cada interface de rede pode estar numa sub-rede diferente, cada interface de rede pode ter diferentes rotas efetivas.

    No exemplo mostrado na imagem anterior, as rotas listadas são as rotas predefinidas que o Azure cria para cada sub-rede. Sua lista tem, pelo menos, estas rotas, mas pode ter rotas adicionais, consoante as capacidades que pode ativar para a rede virtual, como ele que está a ser executado o peering com outra rede virtual ou ligado à sua rede no local através de um gateway de VPN do Azure. Para saber mais sobre cada uma das outras rotas de vir para a sua interface de rede e as rotas, veja [encaminhamento de tráfego de rede Virtual](virtual-networks-udr-overview.md). Se a sua lista tiver um grande número de rotas, talvez ache mais fácil selecionar **transferir**, para transferir um ficheiro. csv com a lista de rotas.

Embora as rotas efetivas foram visualizadas através da VM nos passos anteriores, também pode ver as rotas efetivas por meio de um:
- **Interface de rede individuais**: Saiba como [exibir uma interface de rede](virtual-network-network-interface.md#view-network-interface-settings).
- **Tabela de rotas individuais**: Saiba como [ver uma tabela de rotas](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnosticar a utilizar o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode executar os comandos que se seguem na [Azure Cloud Shell](https://shell.azure.com/powershell), ou ao executar o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se executar o PowerShell a partir do seu computador, terá do módulo Azure PowerShell, versão 1.0.0 ou posterior. Executar `Get-Module -ListAvailable Az` no seu computador, para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver executando o PowerShell localmente, terá também de ser executado `Connect-AzAccount` iniciar sessão no Azure com uma conta que tenha o [as permissões necessárias](virtual-network-network-interface.md#permissions).

Obter as rotas efetivas para uma interface de rede com [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable). O exemplo seguinte obtém as rotas efetivas para uma interface de rede com o nome *myVMVMNic*, que é um grupo de recursos chamado *myResourceGroup*:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Para compreender as informações devolvidas na saída, veja [descrição geral do encaminhamento](virtual-networks-udr-overview.md). Saída só é devolvida se a VM está em execução. Se existirem várias interfaces de rede ligados à VM, pode rever as rotas efetivas para cada interface de rede. Uma vez que cada interface de rede pode estar numa sub-rede diferente, cada interface de rede pode ter diferentes rotas efetivas. Se ainda estiver a ter um problema de comunicação, consulte [diagnóstico adicional](#additional-diagnosis) e [considerações](#considerations).

Se não souber o nome de uma interface de rede, mas souber o nome da VM a interface de rede está ligada a, os seguintes comandos devolvem os IDs de todas as interfaces de rede ligadas a uma VM:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Recebe um resultado semelhante ao seguinte exemplo:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

Na saída anterior, o nome de interface de rede está *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnosticar com a CLI do Azure

Pode executar os comandos que se seguem na [Azure Cloud Shell](https://shell.azure.com/bash), ou ao executar a CLI do seu computador. Este artigo requer a versão 2.0.32 CLI do Azure ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar a CLI do Azure localmente, terá também de ser executado `az login` e inicie sessão no Azure com uma conta que tenha o [as permissões necessárias](virtual-network-network-interface.md#permissions).

Obter as rotas efetivas para uma interface de rede com [az network nic show-em vigor route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table). O exemplo seguinte obtém as rotas efetivas para uma interface de rede com o nome *myVMVMNic* que está no grupo de recursos chamado *myResourceGroup*:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Para compreender as informações devolvidas na saída, veja [descrição geral do encaminhamento](virtual-networks-udr-overview.md). Saída só é devolvida se a VM está em execução. Se existirem várias interfaces de rede ligados à VM, pode rever as rotas efetivas para cada interface de rede. Uma vez que cada interface de rede pode estar numa sub-rede diferente, cada interface de rede pode ter diferentes rotas efetivas. Se ainda estiver a ter um problema de comunicação, consulte [diagnóstico adicional](#additional-diagnosis) e [considerações](#considerations).

Se não souber o nome de uma interface de rede, mas souber o nome da VM a interface de rede está ligada a, os seguintes comandos devolvem os IDs de todas as interfaces de rede ligadas a uma VM:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Resolver um problema

Resolver problemas de encaminhamento, normalmente, é composta por:

- Adicionar uma rota personalizada para substituir um das rotas predefinidas do Azure. Saiba como [adicionar uma rota personalizada](manage-route-table.md#create-a-route).
- Alterar ou remover uma rota personalizada que pode fazer com que o encaminhamento para uma localização indesejada. Saiba como [alterar](manage-route-table.md#change-a-route) ou [eliminar](manage-route-table.md#delete-a-route) uma rota personalizada.
- É associada à sub-rede que a interface de rede está em garantir que a tabela de rota que contém quaisquer rotas personalizadas que definiu. Saiba como [associar uma tabela de rotas a uma sub-rede](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Para garantir que os dispositivos, como de VPN do Azure ou rede de gateway de aplicações virtuais implementou sejam operável. Utilize o [diagnóstico de VPN](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) capacidade do observador de rede para determinar quaisquer problemas com um gateway de VPN do Azure.

Se ainda estiver a ter problemas de comunicação, consulte [considerações](#considerations) e diagnóstico adicional.

## <a name="considerations"></a>Considerações

Na solução de problemas de comunicação, considere os seguintes pontos:

- Encaminhamento se baseia na correspondência de prefixo mais longa (LPM) entre as rotas que definir, as rotas border gateway protocol (BGP) e do sistema. Se houver mais de uma rota com a mesma correspondência LPM, uma rota é selecionada com base na respetiva origem pela ordem listada na [descrição geral do encaminhamento](virtual-networks-udr-overview.md#how-azure-selects-a-route). Com as rotas efetivas, pode ver apenas as rotas efetivas que são uma correspondência LPM, com base em todas as rotas disponíveis. Ver como as rotas são avaliados quanto a uma interface de rede torna muito mais fácil de resolver problemas de rotas específicas que poderão estar a afetar a comunicação de sua VM.
- Se definiu rotas personalizadas para uma aplicação virtual de rede (NVA), com *aplicação Virtual* como o tipo de salto seguinte, certifique-se de que o reencaminhamento de IP está ativado na NVA a receber o tráfego, ou os pacotes são largados. Saiba mais sobre [ativar o reencaminhamento de IP para uma interface de rede](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Além disso, o sistema operativo ou aplicação dentro da NVA tem também de ser capaz de reencaminhar o tráfego de rede e ser configurado para fazê-lo.
- Se tiver criado uma rota para 0.0.0.0/0, todo o tráfego de saída da internet é encaminhado para o próximo salto especificado, como para um gateway de VPN ou da NVA. Criar uma rota tal é frequentemente referido como túnel forçado. Ligações remotas utilizando os protocolos RDP ou SSH da internet para a VM poderão não funcionar com esta rota, dependendo de como o salto seguinte processa o tráfego. Imposição de túnel pode ser ativado:
    - Ao utilizar a VPN de site a site, ao criar uma rota com um tipo de próximo salto *Gateway de VPN*. Saiba mais sobre [configurar o túnel forçado](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Se um 0.0.0.0/0 (rota predefinida) é anunciada através do BGP através de um gateway de rede virtual ao utilizar uma VPN de site a site, ou o circuito do ExpressRoute. Saiba mais sobre como utilizar o BGP com um [VPN site a site](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- Para tráfego peering de rede virtual funcionar corretamente, uma rota de sistema com um tipo de próximo salto *VNet Peering* tem de existir durante o intervalo de prefixos da rede virtual em modo de peering. Se não existe uma rota desse tipo, e a ligação de peering de rede virtual **ligado**:
    - Aguarde alguns segundos e tente novamente. Se for uma ligação de peering recém-estabelecido, ocasionalmente, leva mais tempo para propagar rotas para todas as interfaces de rede numa sub-rede. Para saber mais sobre o peering de rede virtual, veja [descrição geral o peering de rede Virtual](virtual-network-peering-overview.md) e [gerir o peering de rede virtual](virtual-network-manage-peering.md).
    - Regras do grupo de segurança de rede podem estar a afetar comunicação. Para obter mais informações, consulte [diagnosticar um problema de filtragem de tráfego de rede de máquina virtual](diagnose-network-traffic-filter-problem.md).
- Embora o Azure atribui as rotas predefinidas para cada interface de rede do Azure, se tiver várias interfaces de rede ligados à VM, apenas a interface de rede principal é atribuída uma rota predefinida (0.0.0.0/0) ou o gateway, no sistema de operativo da VM. Saiba como criar uma rota padrão para interfaces de rede secundárias ligadas a uma [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) VM. Saiba mais sobre [interfaces de rede primária e secundária](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Diagnóstico adicional

* Para executar um teste rápido para determinar o tipo de próximo salto para o tráfego destinado a um local, utilize o [próximo salto](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) capacidade do observador de rede do Azure. Salto seguinte indica o que é o tipo de próximo salto para o tráfego destinado a uma localização especificada.
* Se não existirem não existem rotas fazendo com que a comunicação de rede de uma VM falhar, o problema pode ser devido a software de firewall em execução no sistema de operativo da VM
* Se estiver [imposição de túnel](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tráfego para um dispositivo no local através de um gateway de VPN ou NVA, poderá não conseguir ligar a uma VM a partir da internet, dependendo de como tiver configurado o encaminhamento para os dispositivos. Confirme que o encaminhamento que configurou para o dispositivo encaminha o tráfego para a um endereço IP público ou privado para a VM.
* Utilize o [resolver problemas de ligação](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) capacidade do observador de rede para determinar as causas de encaminhamento, filtragem e no sistema operacional de problemas de comunicação de saída.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre todas as tarefas, propriedades e definições para um [rotas e tabela de rotas](manage-route-table.md).
- Saiba tudo sobre [do próximo salto tipos, rotas de sistema e como o Azure seleciona uma rota](virtual-networks-udr-overview.md).
