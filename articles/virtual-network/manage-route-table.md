---
title: Criar, alterar ou eliminar uma tabela de rota do Azure
titlesuffix: Azure Virtual Network
description: Saiba como criar, alterar ou eliminar uma tabela de rotas.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: a39d9f9c5a138ece5d40cc5afe1d1dcdd8e7a41a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65849792"
---
# <a name="create-change-or-delete-a-route-table"></a>Criar, alterar ou eliminar uma tabela de rotas

O Azure automaticamente encaminha o tráfego entre sub-redes do Azure, redes virtuais e redes no local. Se pretender alterar qualquer um dos padrão do Azure, encaminhamento, fazer isso criando uma tabela de rotas. Se estiver familiarizado com o encaminhamento em redes virtuais, pode saber mais sobre isso na [descrição geral do encaminhamento](virtual-networks-udr-overview.md) ou ao preencher um [tutorial](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir os passos em qualquer secção deste artigo:

* Se ainda não tiver uma conta do Azure, inscreva-se para uma [conta de avaliação gratuita](https://azure.microsoft.com/free).<br>
* Se utilizar o portal, abra https://portal.azure.com e inicie sessão com a sua conta do Azure.<br>
* Se utilizar comandos do PowerShell para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/powershell), ou ao executar o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o Azure PowerShell versão 1.0.0 do módulo ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.<br>
* Se utilizar comandos de interface de linha de comandos (CLI) do Azure para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/bash), ou ao executar a CLI do seu computador. Este tutorial requer a versão 2.0.31 CLI do Azure ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar a CLI do Azure localmente, terá também de executar `az login` para criar uma ligação com o Azure.

A conta iniciar sessão ou ligar ao Azure, tem de ser atribuída para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atribuída as ações adequadas listadas na [permissões ](#permissions).

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Existe um limite para quantas tabelas de rotas que pode criar por subscrição e localização do Azure. Para obter mais detalhes, veja [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. No canto superior esquerdo do portal, selecione **+ criar um recurso**.
1. Selecione **Networking**, em seguida, selecione **tabela de rotas**.
1. Introduza um **Name** para a tabela de rota, selecione seu **subscrição**, crie um novo **grupo de recursos**, ou selecione um grupo de recursos existente, selecione um **localização** , em seguida, selecione **criar**. Se pretende associar a tabela de rotas a uma sub-rede numa rede virtual que está ligada à sua rede no local através de um gateway VPN e desativar **propagação de rotas de gateway de rede Virtual**, não são as rotas no local propagadas para as interfaces de rede na sub-rede.

### <a name="create-route-table---commands"></a>Criar tabela de rotas - comandos

* CLI do Azure: [criar rota de rede de az-table](/cli/azure/network/route-table/route)<br>
* PowerShell: [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)

## <a name="view-route-tables"></a>Tabelas de rotas do Vista

Na caixa de pesquisa na parte superior do portal, introduza *tabelas de rotas* na caixa de pesquisa. Quando **tabelas de rotas** aparecer nos resultados da pesquisa, selecione-o. As tabelas de rota que existem na sua subscrição estão listadas.

### <a name="view-route-table---commands"></a>Tabela de rotas de vista – comandos

* CLI do Azure: [lista de tabela de rotas de rede de az](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="view-details-of-a-route-table"></a>Ver detalhes de uma tabela de rotas

1. Na caixa de pesquisa na parte superior do portal, introduza *tabelas de rotas* na caixa de pesquisa. Quando **tabelas de rotas** aparecer nos resultados da pesquisa, selecione-o.
1. Na lista que pretende ver os detalhes para, selecione a tabela de rotas. Sob **definições**, pode ver a **rotas** na tabela de rotas e o **sub-redes** a tabela de rotas é associada a.
1. Para saber mais sobre as definições do Azure comuns, consulte as seguintes informações:

    * [Registo de atividades](../azure-monitor/platform/activity-logs-overview.md)<br>
    * [Controlo de acesso (IAM)](../role-based-access-control/overview.md)<br>
    * [Etiquetas](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Bloqueios](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Script de automação](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

### <a name="view-details-of-route-table---commands"></a>Ver detalhes da tabela de rota - comandos

* CLI do Azure: [show de tabela de rotas de rede de az](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="change-a-route-table"></a>Alterar uma tabela de rotas

1. Na caixa de pesquisa na parte superior do portal, introduza *tabelas de rotas* na caixa de pesquisa. Quando **tabelas de rotas** aparecer nos resultados da pesquisa, selecione-o.
1. Selecione a tabela de rotas que pretende alterar. As alterações mais comuns são [adicionando](#create-a-route) ou [remover](#delete-a-route) rotas e [associar](#associate-a-route-table-to-a-subnet) tabelas para, de rotas ou [a desassociar a](#dissociate-a-route-table-from-a-subnet) tabelas a partir de rotas sub-redes.

### <a name="change-a-route-table---commands"></a>Alterar uma tabela de rotas - comandos

* CLI do Azure: [atualização de tabela de rotas de rede de az](/cli/azure/network/route-table/route)<br>
* PowerShell: [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Uma sub-rede pode ter zero ou uma tabela de rotas associada a ele. Uma tabela de rotas pode ser associada a zero ou várias sub-redes. Uma vez que as tabelas de rotas não foram associadas redes virtuais, tem de associar uma tabela de rotas para cada sub-rede que pretende que a tabela de rotas associada. Sai da sub-rede todo o tráfego é encaminhado com base nas rotas que criou em tabelas de rota [predefinido rotas](virtual-networks-udr-overview.md#default), e as rotas são propagadas de uma rede no local, se a rede virtual está ligada a um (de gateway de rede virtual do Azure ExpressRoute ou VPN). Só pode associar uma tabela de rotas às sub-redes nas redes virtuais existentes na mesma localização do Azure e subscrição como a tabela de rotas.

1. Na caixa de pesquisa na parte superior do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecer nos resultados da pesquisa, selecione-o.
1. Na lista que contém a sub-rede que pretende associar uma tabela de rotas para, selecione a rede virtual.
1. Selecione **sub-redes** sob **definições**.
1. Selecione a sub-rede que pretende associar a tabela de rotas para.
1. Selecione **tabela de rotas**, selecione a tabela de rotas que pretende associar à sub-rede, em seguida, selecione **guardar**.

Se a rede virtual esiver ligada a um gateway VPN do Azure, não associe nenhuma tabela de rota à [sub-rede do gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) que incluia uma rota com um destino 0.0.0.0/0. Se o fizer, poderá impedir que o gateway funcione corretamente. Para obter mais informações sobre como utilizar 0.0.0.0/0 numa rota, consulte [encaminhamento de tráfego de rede Virtual](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Associar uma tabela de rotas - comandos

* CLI do Azure: [atualização de sub-rede de vnet de rede de az](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Desassociar a uma tabela de rotas de sub-rede

Ao desassociar a uma tabela de rotas de sub-rede, o Azure encaminha o tráfego com base no respetivo [predefinido rotas](virtual-networks-udr-overview.md#default).

1. Na caixa de pesquisa na parte superior do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecer nos resultados da pesquisa, selecione-o.
1. Selecione a rede virtual que contém a sub-rede que pretende desassociar a partir de uma tabela de rotas.
1. Selecione **sub-redes** sob **definições**.
1. Selecione a sub-rede que pretende desassociar a tabela de rotas de.
1. Selecione **tabela de rotas**, selecione **None**, em seguida, selecione **guardar**.

### <a name="dissociate-a-route-table---commands"></a>Desassociar a uma tabela de rotas - comandos

* CLI do Azure: [atualização de sub-rede de vnet de rede de az](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-route-table"></a>Eliminar uma tabela de rotas

Se uma tabela de rotas associada a quaisquer sub-redes, não pode ser eliminada. [Desassociar](#dissociate-a-route-table-from-a-subnet) uma tabela de rotas de todas as sub-redes antes de tentar eliminá-lo.

1. Na caixa de pesquisa na parte superior do portal, introduza *tabelas de rotas* na caixa de pesquisa. Quando **tabelas de rotas** aparecer nos resultados da pesquisa, selecione-o.
1. Selecione **...**  no lado direito da tabela de rota que pretende eliminar.
1. Selecione **elimine**e, em seguida, selecione **Sim**.

### <a name="delete-a-route-table---commands"></a>Eliminar uma tabela de rotas - comandos

* CLI do Azure: [Eliminar tabela de rotas do rede az](/cli/azure/network/route-table/route)<br>
* PowerShell: [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable)

## <a name="create-a-route"></a>Criar uma rota

Existe um limite para quantas rotas por tabela de rotas podem criar por subscrição e localização do Azure. Para obter mais detalhes, veja [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Na caixa de pesquisa na parte superior do portal, introduza *tabelas de rotas* na caixa de pesquisa. Quando **tabelas de rotas** aparecer nos resultados da pesquisa, selecione-o.
1. Selecione na lista que pretende adicionar uma rota para a tabela de rotas.
1. Selecione **rotas**, em **definições**.
1. Selecione **+ Adicionar**.
1. Introduza uma única **nome** para a rota na tabela de rotas.
1. Introduza o **prefixo de endereço**, na notação CIDR, o que pretende encaminhar o tráfego para. O prefixo não pode ser duplicado em mais de uma rota na tabela de rotas, embora o prefixo pode ser dentro de outro prefixo. Por exemplo, se tiver definido 10.0.0.0/16 como um prefixo numa rota, pode definir outro rota com o prefixo de endereço 10.0.0.0/24. O Azure seleciona uma rota para o tráfego com base na correspondência de prefixo mais longa. Para saber mais sobre como o Azure seleciona as rotas, veja [descrição geral do encaminhamento](virtual-networks-udr-overview.md#how-azure-selects-a-route).
1. Selecione um **tipo de próximo salto**. Para obter uma descrição detalhada de todos os tipos de salto seguintes, consulte [descrição geral do encaminhamento](virtual-networks-udr-overview.md).
1. Introduza um endereço IP para **endereço do próximo salto**. Pode inserir apenas um endereço se tiver selecionado *aplicação Virtual* para **tipo de próximo salto**.
1. Selecione **OK**.

### <a name="create-a-route---commands"></a>Criar uma rota - comandos

* CLI do Azure: [criar rota de tabela de rotas de rede de az](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)

## <a name="view-routes"></a>Rotas de vista

Uma tabela de rotas contém rotas de zero ou vários. Para saber mais sobre as informações indicadas ao visualizar rotas, veja [descrição geral do encaminhamento](virtual-networks-udr-overview.md).

1. Na caixa de pesquisa na parte superior do portal, introduza *tabelas de rotas* na caixa de pesquisa. Quando **tabelas de rotas** aparecer nos resultados da pesquisa, selecione-o.
1. Selecione a tabela de rotas na lista que pretende ver as rotas para.
1. Selecione **rotas** sob **definições**.

### <a name="view-routes---commands"></a>Rotas de vista – comandos

* CLI do Azure: [lista de rota da tabela de rotas de rede de az](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="view-details-of-a-route"></a>Ver detalhes de uma rota

1. Na caixa de pesquisa na parte superior do portal, introduza *tabelas de rotas* na caixa de pesquisa. Quando **tabelas de rotas** aparecer nos resultados da pesquisa, selecione-o.
1. Selecione a tabela de rotas que pretende ver os detalhes de uma rota para.
1. Selecione **rotas**.
1. Selecione a rota que pretende ver os detalhes de.

### <a name="view-details-of-a-route---commands"></a>Ver detalhes de uma rota - comandos

* CLI do Azure: [show de rota de tabela de rotas de rede de az](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="change-a-route"></a>Alterar uma rota

1. Na caixa de pesquisa na parte superior do portal, introduza *tabelas de rotas* na caixa de pesquisa. Quando **tabelas de rotas** aparecer nos resultados da pesquisa, selecione-o.
1. Selecione a tabela de rotas para alterar uma rota para.
1. Selecione **rotas**.
1. Selecione a rota que pretende alterar.
1. Alterar as definições existentes para suas novas definições, em seguida, selecione **guardar**.

### <a name="change-a-route---commands"></a>Alterar uma rota - comandos

* CLI do Azure: [atualização de rota de tabela de rotas de rede de az](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig)

## <a name="delete-a-route"></a>Eliminar uma rota

1. Na caixa de pesquisa na parte superior do portal, introduza *tabelas de rotas* na caixa de pesquisa. Quando **tabelas de rotas** aparecer nos resultados da pesquisa, selecione-o.
1. Selecione a tabela de rotas que pretende eliminar uma rota para.
1. Selecione **rotas**.
1. Na lista de rotas, selecione **...**  no lado direito da rota que pretende eliminar.
1. Selecione **elimine**, em seguida, selecione **Sim**.

### <a name="delete-a-route---commands"></a>Eliminar uma rota - comandos

* CLI do Azure: [delete de rota de tabela de rotas de rede de az](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig)

## <a name="view-effective-routes"></a>Ver as rotas efetivas

As rotas efetivas para cada interface de rede ligado a uma máquina virtual são uma combinação de tabelas de rotas que criou, as rotas predefinidas do Azure e quaisquer rotas propagadas a partir de redes no local através do BGP através de um gateway de rede virtual do Azure. Noções básicas sobre as rotas efetivas para uma interface de rede é útil quando resolver problemas de encaminhamento. Pode ver as rotas efetivas para qualquer interface de rede que está ligado a uma máquina virtual em execução.

1. Na caixa de pesquisa na parte superior do portal, introduza o nome de uma máquina virtual que pretende visualizar as rotas efetivas para. Se não souber o nome de uma máquina virtual, introduza *máquinas virtuais* na caixa de pesquisa. Quando **máquinas virtuais** aparecer nos resultados da pesquisa, selecione-o e selecione uma máquina virtual a partir da lista.
1. Selecione **Networking** sob **definições**.
1. Selecione o nome de uma interface de rede.
1. Selecione **rotas efetivas** sob **suporte + resolução de problemas**.
1. Reveja a lista de rotas efetivas para determinar se a rota correta existe para onde pretende encaminhar o tráfego para. Saiba mais sobre os tipos de salto seguinte que vê nesta lista na [descrição geral do encaminhamento](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Ver as rotas efetivas - comandos

* CLI do Azure: [az network nic show-em vigor route-table](/cli/azure/network/nic?view=azure-cli-latest)<br>
* PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="validate-routing-between-two-endpoints"></a>Validar o encaminhamento entre dois pontos de extremidade

Pode determinar o tipo de próximo salto entre uma máquina virtual e o endereço IP do outro recurso do Azure, um recurso no local ou um recurso na Internet. Encaminhamento do Azure determinante é útil quando resolver problemas de encaminhamento. Para concluir essa tarefa, tem de ter um observador de rede existente. Se não tiver um observador de rede existente, crie uma, concluindo os passos em [criar uma instância do observador de rede](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Na caixa de pesquisa na parte superior do portal, introduza *observador de rede* na caixa de pesquisa. Quando a opção **Observador de Rede** aparecer nos resultados de pesquisa, selecione-a.
1. Selecione **do próximo salto** sob **ferramentas de diagnóstico de rede**.
1. Selecione seu **subscrição** e o **grupo de recursos** de máquina virtual de origem que pretende validar o encaminhamento de.
1. Selecione o **Máquina Virtual**, **interface de rede** anexado à máquina virtual, e **endereço IP de origem** atribuído à interface de rede que pretende validar encaminhamento de.
1. Introduza o **endereço IP de destino** que pretende validar o encaminhamento para.
1. Selecione **do próximo salto**.
1. Após uma breve espera, as informações são devolvidas que indica o tipo de próximo salto e o ID da rota que o tráfego de encaminhados. Saiba mais sobre tipos de salto seguinte que vê devolvidos na [descrição geral do encaminhamento](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Validar o encaminhamento entre dois pontos de extremidade - comandos

* CLI do Azure: [az show-next-hop do observador de rede](/cli/azure/network/watcher?view=azure-cli-latest)<br>
* PowerShell: [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop)

## <a name="permissions"></a>Permissões

Para executar tarefas em tabelas de rotas e rotas, deve ser atribuída a sua conta para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) função que é atribuída as ações adequadas listadas na tabela a seguir:

| Ação                                                          |   Name                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Ler uma tabela de rotas                                    |
| Microsoft.Network/routeTables/write                             |   Criar ou atualizar uma tabela de rotas                        |
| Microsoft.Network/routeTables/delete                            |   Eliminar uma tabela de rotas                                  |
| Microsoft.Network/routeTables/join/action                       |   Associar uma tabela de rotas a uma sub-rede                   |
| Microsoft.Network/routeTables/routes/read                       |   Ler uma rota                                          |
| Microsoft.Network/routeTables/routes/write                      |   Criar ou atualizar uma rota                              |
| Microsoft.Network/routeTables/routes/delete                     |   Eliminar uma rota                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Obtenha a tabela de rotas efetivas para uma interface de rede |
| Microsoft.Network/networkWatchers/nextHop/action                |   Obtém o salto seguinte a partir de uma VM                           |

## <a name="next-steps"></a>Passos Seguintes

* Criar uma tabela de rota com [PowerShell](powershell-samples.md) ou [CLI do Azure](cli-samples.md) scripts ou utilizar o Azure de exemplo [modelos do Resource Manager](template-samples.md)<br>
* Criar e aplicar [o Azure policy](policy-samples.md) para redes virtuais
