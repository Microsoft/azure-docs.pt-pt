---
title: Diagnosticar um problema de encaminhamento de máquinas virtuais Azure [ Microsoft Docs
description: Aprenda a diagnosticar um problema de encaminhamento de máquinavirtual visualizando as rotas eficazes para uma máquina virtual.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350625"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnosticar um problema de encaminhamento de máquina virtual

Neste artigo, aprende-se a diagnosticar um problema de encaminhamento visualizando as rotas que são eficazes para uma interface de rede numa máquina virtual (VM). O Azure cria várias rotas padrão para cada subnet de rede virtual. Pode substituir as rotas padrão do Azure definindo rotas numa tabela de rotas e, em seguida, associando a tabela de rotas a uma subnet. A combinação de rotas que cria, as rotas padrão do Azure e quaisquer rotas propagadas da sua rede no local através de um gateway Azure VPN (se a sua rede virtual estiver ligada à sua rede no local) através do protocolo de gateway fronteiriço (BGP), são as rotas eficazes para todas as interfaces de rede numa subnet. Se não estiver familiarizado com conceitos de rede virtual, interface de rede ou de encaminhamento, consulte [a visão geral da rede virtual,](virtual-networks-overview.md)interface de [rede](virtual-network-network-interface.md)e visão geral do [Encaminhamento](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Cenário

Tentas ligar-te a um VM, mas a ligação falha. Para determinar por que não consegue ligar-se ao VM, pode visualizar as rotas eficazes para uma interface de rede utilizando o [portal](#diagnose-using-azure-portal)Azure, [PowerShell](#diagnose-using-powershell)ou o [Azure CLI](#diagnose-using-azure-cli).

Os passos que se seguem assumem que tem um VM existente para ver as rotas efetivas para. Se não tiver um VM existente, implemente primeiro um [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM para completar as tarefas neste artigo com. Os exemplos deste artigo são para um VM chamado *myVM* com uma interface de rede chamada *myVMNic1*. O VM e a interface de rede estão num grupo de recursos chamado *myResourceGroup*, e estão na região *leste dos EUA.* Mude os valores nos passos, se for caso disso, para o VM para o que está a diagnosticar o problema.

## <a name="diagnose-using-azure-portal"></a>Diagnosticar usando o portal Azure

1. Inicie sessão no [portal](https://portal.azure.com) Azure com uma conta Azure que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions).
2. No topo do portal Azure, insira o nome de um VM que se encontra em estado de funcionamento, na caixa de pesquisa. Quando o nome do VM aparecer nos resultados da pesquisa, selecione-o.
3. Em **Definições** à esquerda, selecione **Networking**e navegue para o recurso de interface de rede selecionando o seu nome.
     ![Ver interfaces de rede](./media/diagnose-network-routing-problem/view-nics.png)
4. À esquerda, selecione **Rotas Eficazes**. As rotas eficazes para uma interface de rede chamada **myVMNic1** são mostradas, na imagem seguinte: ![Ver rotas eficazes](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Se existirem múltiplas interfaces de rede ligadas ao VM, pode visualizar as rotas eficazes para qualquer interface de rede selecionando-a. Uma vez que cada interface de rede pode estar numa subrede diferente, cada interface de rede pode ter diferentes rotas eficazes.

    No exemplo mostrado na imagem anterior, as rotas listadas são rotas padrão que o Azure cria para cada sub-rede. A sua lista tem pelo menos estas rotas, mas pode ter rotas adicionais, dependendo das capacidades que possa ter ativado para a sua rede virtual, como ser espreitado com outra rede virtual ou ligada à sua rede no local através de um gateway Azure VPN. Para saber mais sobre cada uma das rotas e outras rotas que poderá ver para a sua interface de rede, consulte o [encaminhamento](virtual-networks-udr-overview.md)de tráfego de rede virtual . Se a sua lista tiver um grande número de rotas, poderá ser mais fácil selecionar **Download**, para descarregar um ficheiro .csv com a lista de rotas.

Embora as rotas eficazes tenham sido vistas através do VM nos passos anteriores, você também pode ver rotas eficazes através de um:
- **Interface de rede individual**: Aprenda a [visualizar uma interface](virtual-network-network-interface.md#view-network-interface-settings)de rede .
- Tabela de **rotas individual:** Saiba como [ver uma tabela](manage-route-table.md#view-details-of-a-route-table)de rotas .

## <a name="diagnose-using-powershell"></a>Diagnosticar usando PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode executar os comandos que se seguem na Casca de [Nuvem Azure,](https://shell.azure.com/powershell)ou executando a PowerShell a partir do seu computador. A Casca de Nuvem Azure é uma concha interativa gratuita. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se executar o PowerShell a partir do seu computador, precisa do módulo Azure PowerShell, versão 1.0.0 ou posterior. Navere `Get-Module -ListAvailable Az` no seu computador, para encontrar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar a PowerShell `Connect-AzAccount` localmente, também precisa de correr para entrar no Azure com uma conta que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions).

Obtenha as rotas eficazes para uma interface de rede com [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable). O exemplo seguinte obtém as rotas eficazes para uma interface de rede chamada *myVMNic1,* que está num grupo de recursos chamado *myResourceGroup*:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Para compreender a informação devolvida na saída, consulte a visão geral do [Routing.](virtual-networks-udr-overview.md) A saída só é devolvida se o VM estiver em estado de funcionamento. Se existirem múltiplas interfaces de rede ligadas ao VM, pode rever as rotas eficazes para cada interface de rede. Uma vez que cada interface de rede pode estar numa subrede diferente, cada interface de rede pode ter diferentes rotas eficazes. Se ainda tiver um problema de comunicação, consulte [um diagnóstico](#additional-diagnosis) adicional e [considerações.](#considerations)

Se não sabe o nome de uma interface de rede, mas sabe o nome do VM a interface de rede a que a interface de rede está ligada, os seguintes comandos devolvem os IDs de todas as interfaces de rede anexadas a um VM:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Recebe uma saída semelhante ao seguinte exemplo:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

Na saída anterior, o nome da interface de rede é *myVMNic1*.

## <a name="diagnose-using-azure-cli"></a>Diagnosticar usando Azure CLI

Pode executar os comandos que se seguem na Casca de [Nuvem Azure,](https://shell.azure.com/bash)ou executando o CLI a partir do seu computador. Este artigo requer a versão Azure CLI 2.0.32 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI `az login` localmente, também precisa de correr e entrar no Azure com uma conta que tenha as [permissões necessárias.](virtual-network-network-interface.md#permissions)

Obtenha as rotas eficazes para uma interface de rede com a [z network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table). O exemplo seguinte obtém as rotas eficazes para uma interface de rede chamada *myVMNic1* que está num grupo de recursos chamado *myResourceGroup*:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

Para compreender a informação devolvida na saída, consulte a visão geral do [Routing.](virtual-networks-udr-overview.md) A saída só é devolvida se o VM estiver em estado de funcionamento. Se existirem múltiplas interfaces de rede ligadas ao VM, pode rever as rotas eficazes para cada interface de rede. Uma vez que cada interface de rede pode estar numa subrede diferente, cada interface de rede pode ter diferentes rotas eficazes. Se ainda tiver um problema de comunicação, consulte [um diagnóstico](#additional-diagnosis) adicional e [considerações.](#considerations)

Se não sabe o nome de uma interface de rede, mas sabe o nome do VM a interface de rede a que a interface de rede está ligada, os seguintes comandos devolvem os IDs de todas as interfaces de rede anexadas a um VM:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Resolver um problema

A resolução de problemas de encaminhamento consiste tipicamente em:

- Adicionando uma rota personalizada para anular uma das rotas padrão do Azure. Saiba como [adicionar uma rota personalizada.](manage-route-table.md#create-a-route)
- Altere ou remova uma rota personalizada que possa causar encaminhamento para um local indesejado. Aprenda a [alterar](manage-route-table.md#change-a-route) ou [apagar](manage-route-table.md#delete-a-route) uma rota personalizada.
- Garantir que a tabela de rotas que contém quaisquer rotas personalizadas que definiu está associada à subrede em que a interface de rede se encontra. Aprenda a associar uma tabela de [rota a uma subnet.](manage-route-table.md#associate-a-route-table-to-a-subnet)
- Garantir que dispositivos como o gateway Azure VPN ou os aparelhos virtuais de rede que implementou são operáveis. Utilize a capacidade de [diagnóstico VPN](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Observador de Rede para determinar quaisquer problemas com um gateway VPN Azure.

Se ainda tiver problemas de comunicação, consulte [considerações](#considerations) e diagnóstico adicional.

## <a name="considerations"></a>Considerações

Considere os seguintes pontos quando resolver problemas de comunicação:

- O encaminhamento baseia-se no mais longo jogo de prefixo (LPM) entre as rotas que definiu, o protocolo de gateway fronteiriço (BGP) e as rotas do sistema. Se houver mais de uma rota com a mesma correspondência LPM, então uma rota é selecionada com base na sua origem na ordem listada na visão geral do [Routing](virtual-networks-udr-overview.md#how-azure-selects-a-route). Com rotas eficazes, só é possível ver rotas eficazes que sejam compatíveis com LPM, com base em todas as rotas disponíveis. Ver como as rotas são avaliadas para uma interface de rede torna muito mais fácil resolver rotas específicas que podem estar a afetar a comunicação do seu VM.
- Se definiu rotas personalizadas para um aparelho virtual de rede (NVA), com *o Virtual Appliance* como o próximo tipo de lúpulo, certifique-se de que o encaminhamento ip está ativado na NVA que recebe o tráfego, ou que os pacotes são retirados. Saiba mais sobre [o encaminhamento ip para uma interface](virtual-network-network-interface.md#enable-or-disable-ip-forwarding)de rede . Além disso, o sistema operativo ou aplicação dentro da NVA também deve ser capaz de encaminhar o tráfego da rede e ser configurado para o fazer.
- Se criou uma rota para 0.0.0.0/0, todo o tráfego de internet de saída é encaminhado para o próximo salto que especificou, como por exemplo para um gateway NVA ou VPN. A criação de tal rota é muitas vezes referida como túnel forçado. As ligações remotas que utilizam os protocolos RDP ou SSH da internet para o seu VM podem não funcionar com esta rota, dependendo da forma como o próximo salto lida com o tráfego. A escavação forçada pode ser ativada:
    - Ao utilizar vpn site-to-site, criando uma rota com um próximo tipo de lúpulo de *Gateway VPN*. Saiba mais sobre [a configuração de túneis forçados](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Se for publicitada uma rota de 0.0.0.0/0 (rota padrão) através de um portal de rede virtual quando utilizar um circuito VPN de site-para-site ou ExpressRoute. Saiba mais sobre a utilização do BGP com uma VPN ou [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering) [site.](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Para que o tráfego de rede virtual funcione corretamente, deve existir uma rota do sistema com um próximo tipo de *vNet Peering* para a gama de pré-fixação da rede virtual. Se tal rota não existir, e o elo de peering da rede virtual estiver **ligado:**
    - Espere alguns segundos, e tente de novo. Se é uma ligação de pares recém-estabelecida, ocasionalmente demora mais tempo a propagar rotas para todas as interfaces de rede numa subnet. Para saber mais sobre o peering virtual da rede, consulte a [visão geral](virtual-network-peering-overview.md) da rede virtual e gere o [peering virtual da rede.](virtual-network-manage-peering.md)
    - As regras do grupo de segurança da rede podem estar a afetar a comunicação. Para obter mais informações, consulte Diagnosticar um problema de filtro de tráfego de [rede de máquinas virtuais](diagnose-network-traffic-filter-problem.md).
- Embora o Azure atribua rotas padrão para cada interface de rede Azure, se tiver várias interfaces de rede ligadas ao VM, apenas a interface de rede primária é atribuída a uma rota predefinida (0.0.0.0/0), ou gateway, dentro do sistema operativo do VM. Saiba como criar uma rota padrão para interfaces de rede secundárias anexadas a um [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) VM. Saiba mais sobre [interfaces de rede primária seleções e secundárias.](virtual-network-network-interface-vm.md#constraints)

## <a name="additional-diagnosis"></a>Diagnóstico adicional

* Para realizar um teste rápido para determinar o próximo tipo de lúpulo para o tráfego destinado a um local, use a capacidade [de lúpulo Seguinte](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure Network Watcher. O próximo salto diz-lhe qual é o próximo tipo de lúpulo para o tráfego destinado a um local especificado.
* Se não houver rotas que causem falhas na comunicação de rede de um VM, o problema pode dever-se ao software de firewall que funciona dentro do sistema operativo do VM
* Se estiver a forçar o tráfego de [túneis](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a um dispositivo no local através de um gateway VPN, ou NVA, poderá não conseguir ligar-se a um VM a partir da internet, dependendo de como configurao o encaminhamento para os dispositivos. Confirme que o encaminhamento configurado para as rotas do dispositivo detráfego para um endereço IP público ou privado para o VM.
* Utilize a capacidade de resolução de problemas de [ligação](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Observador de Rede para determinar as causas de encaminhamento, filtragem e in-OS de problemas de comunicação de saída.

## <a name="next-steps"></a>Passos seguintes

- Conheça todas as tarefas, propriedades e configurações para uma [tabela de rotas e rotas.](manage-route-table.md)
- Saiba mais sobre todos os próximos tipos de [lúpulo, rotas do sistema e como o Azure seleciona uma rota](virtual-networks-udr-overview.md).
