---
title: Criar, alterar ou excluir uma tabela de rotas Azure
titlesuffix: Azure Virtual Network
description: Aprenda a criar, alterar ou apagar uma tabela de rotas.
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
ms.openlocfilehash: fe8ea4dfb4de45a1e09648ac51fe8d74f93a6b9e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280277"
---
# <a name="create-change-or-delete-a-route-table"></a>Criar, alterar ou excluir uma tabela de rotas

O Azure encaminha automaticamente o tráfego entre as redes Azure, redes virtuais e no local. Se quiser alterar algum dos encaminhamentos padrão do Azure, fá-lo criando uma tabela de rotas. Se você é novo em encaminhamento em redes virtuais, você pode aprender mais sobre isso na visão geral do [encaminhamento](virtual-networks-udr-overview.md) ou completando um [tutorial](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Complete as seguintes tarefas antes de completar os passos em qualquer secção deste artigo:

* Se ainda não tem uma conta Azure, inscreva-se numa [conta de teste gratuita.](https://azure.microsoft.com/free)<br>
* Se utilizar o portal, abra https://portal.azure.come inscreva-se na sua conta Azure.<br>
* Se utilizar os comandos PowerShell para completar tarefas neste artigo, execute os comandos na Casca de [Nuvem Azure,](https://shell.azure.com/powershell)ou executando powerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer a versão 1.0.0 ou posterior do módulo PowerShell Azure. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.<br>
* Se utilizar comandos de interface de linha de comando Azure (CLI) para completar tarefas neste artigo, execute os comandos na [Casca de Nuvem Azure,](https://shell.azure.com/bash)ou executando o CLI a partir do seu computador. Este tutorial requer a versão Azure CLI 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI localmente, também precisa de correr `az login` para criar uma ligação com o Azure.

A conta em que assina, ou se liga ao Azure, deve ser atribuída à função de contribuinte da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que lhe seja atribuída as ações adequadas enumeradas nas [Permissões](#permissions).

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Há um limite para quantas tabelas de rotas pode criar por localização e subscrição do Azure. Para obter mais detalhes, veja [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. No canto superior esquerdo do portal, selecione **+ Crie um recurso**.
1. **Selecione Networking**e, em seguida, selecione **tabela Rota**.
1. Introduza um **Nome** para a tabela de rotas, selecione a sua **Subscrição,** crie um novo **grupo de Recursos,** ou selecione um grupo de recursos existente, selecione um **Local,** e selecione **Criar**. Se planeia associar a tabela de rotas a uma subneta numa rede virtual que esteja ligada à sua rede no local através de um gateway VPN, e desativar a propagação da rota de gateway da **rede virtual,** as suas rotas no local não são propagadas às interfaces de rede na subnet.

### <a name="create-route-table---commands"></a>Criar tabela de rotas - comandos

* Azure CLI: [az network route-table criar](/cli/azure/network/route-table/route)<br>
* PowerShell: [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)

## <a name="view-route-tables"></a>Ver tabelas de rotas

Na caixa de pesquisa no topo do portal, introduza *tabelas* de rota na caixa de pesquisa. Quando as **tabelas De rota** aparecerem nos resultados da pesquisa, selecione-as. As tabelas de rotas que existem na sua subscrição estão listadas.

### <a name="view-route-table---commands"></a>Ver tabela de rotas - comandos

* Azure CLI: [lista de tabelas de rotas da rede az](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="view-details-of-a-route-table"></a>Ver detalhes de uma tabela de rotas

1. Na caixa de pesquisa no topo do portal, introduza *tabelas* de rota na caixa de pesquisa. Quando as **tabelas De rota** aparecerem nos resultados da pesquisa, selecione-as.
1. Selecione a tabela de rotas na lista para a sua visualização de detalhes. Em **DEFINIÇÕES,** pode ver as **Rotas** na tabela de rotas e as **Subnets** a que a tabela de rotas está associada.
1. Para saber mais sobre as configurações comuns do Azure, consulte as seguintes informações:

    * [Registo de atividades](../azure-monitor/platform/platform-logs-overview.md)<br>
    * [Controlo de acesso (IAM)](../role-based-access-control/overview.md)<br>
    * [Etiquetas](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Fechaduras](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Roteiro de automação](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Ver detalhes da tabela de rotas - comandos

* Azure CLI: [show de tabela de rotas da rede az](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="change-a-route-table"></a>Alterar uma tabela de rotas

1. Na caixa de pesquisa no topo do portal, introduza *tabelas* de rota na caixa de pesquisa. Quando as **tabelas De rota** aparecerem nos resultados da pesquisa, selecione-as.
1. Selecione a tabela de rotas que pretende alterar. As alterações mais comuns são [a adição](#create-a-route) ou [remoção](#delete-a-route) de rotas e [a associação](#associate-a-route-table-to-a-subnet) de tabelas de rotas ou [a dissociação](#dissociate-a-route-table-from-a-subnet) das tabelas de rotas das subredes.

### <a name="change-a-route-table---commands"></a>Alterar uma tabela de rotas - comandos

* Azure CLI: [atualização da tabela de rotas da rede az](/cli/azure/network/route-table/route)<br>
* PowerShell: [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Uma sub-rede pode ter zero ou uma mesa de rota associada a ela. Uma tabela de rotas pode ser associada a zero ou várias subredes. Uma vez que as tabelas de rotas não estão associadas a redes virtuais, deve associar uma tabela de rotas a cada subnet a que pretende a tabela de rotas associada. Todo o tráfego que sai da subnet é encaminhado com base em rotas que criou dentro de tabelas de rotas, [rotas padrão,](virtual-networks-udr-overview.md#default)e rotas propagadas a partir de uma rede no local, se a rede virtual estiver ligada a um portal de rede virtual Azure (ExpressRoute ou VPN). Só é possível associar uma tabela de rotas a subredes em redes virtuais que existam na mesma localização e subscrição do Azure que a tabela de rotas.

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando as **redes Virtuais** aparecerem nos resultados da pesquisa, selecione-as.
1. Selecione a rede virtual na lista que contém a sub-rede a que pretende associar uma tabela de rotas.
1. Selecione **Subredes** em **DEFINIÇÕES**.
1. Selecione a sub-rede a que pretende associar a tabela de rotas.
1. Selecione **tabela Rota,** selecione a tabela de rota que pretende associar à sub-rede e, em seguida, selecione **Guardar**.

Se a rede virtual esiver ligada a um gateway VPN do Azure, não associe nenhuma tabela de rota à [sub-rede do gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) que incluia uma rota com um destino 0.0.0.0/0. Se o fizer, poderá impedir que o gateway funcione corretamente. Para mais informações sobre a utilização de 0.0.0.0/0 numa rota, consulte o [encaminhamento](virtual-networks-udr-overview.md#default-route)de tráfego da rede virtual .

### <a name="associate-a-route-table---commands"></a>Associar uma tabela de rotas - comandos

* Azure CLI: [az rede vnet subnet atualização](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Dissociar uma mesa de rota de uma subneta

Quando dissocia uma tabela de rotas de uma subneta, o Azure encaminha o tráfego com base nas suas [rotas padrão](virtual-networks-udr-overview.md#default).

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando as **redes Virtuais** aparecerem nos resultados da pesquisa, selecione-as.
1. Selecione a rede virtual que contém a sub-rede de que pretende dissociar uma tabela de rotas.
1. Selecione **Subredes** em **DEFINIÇÕES**.
1. Selecione a sub-rede de que pretende dissociar a tabela de rotas.
1. Selecione **tabela Rota,** selecione **Nenhuma**e, em seguida, selecione **Guardar**.

### <a name="dissociate-a-route-table---commands"></a>Dissociar uma mesa de rota - comandos

* Azure CLI: [az rede vnet subnet atualização](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-route-table"></a>Eliminar uma tabela de rotas

Se uma tabela de rotas estiver associada a quaisquer subredes, não pode ser eliminada. [Dissociar](#dissociate-a-route-table-from-a-subnet) uma tabela de rotas de todas as subredes antes de tentar eliminá-la.

1. Na caixa de pesquisa no topo do portal, introduza *tabelas* de rota na caixa de pesquisa. Quando as **tabelas De rota** aparecerem nos resultados da pesquisa, selecione-as.
1. Selecione **...** no lado direito da tabela de rotas que pretende eliminar.
1. **Selecione Eliminar**e, em seguida, selecione **Sim**.

### <a name="delete-a-route-table---commands"></a>Eliminar uma tabela de rotas - comandos

* Azure CLI: [az tabela de rotas da rede](/cli/azure/network/route-table/route)<br>
* PowerShell: [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable)

## <a name="create-a-route"></a>Criar uma rota

Há um limite para quantas rotas por tabela de rotas podem criar por localização e subscrição do Azure. Para obter mais detalhes, veja [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Na caixa de pesquisa no topo do portal, introduza *tabelas* de rota na caixa de pesquisa. Quando as **tabelas De rota** aparecerem nos resultados da pesquisa, selecione-as.
1. Selecione a tabela de rotas da lista a que pretende adicionar uma rota.
1. Selecione **Rotas,** em **DEFINIÇÕES**.
1. Selecione **+ Adicionar**.
1. Introduza um **nome** único para a rota dentro da tabela de rotas.
1. Introduza o **prefixo do Endereço,** na notação CIDR, para o sentido de encaminhar o tráfego para. O prefixo não pode ser duplicado em mais de uma rota dentro da tabela de rotas, embora o prefixo possa estar dentro de outro prefixo. Por exemplo, se definiu 10.0.0.0.0/16 como prefixo numa rota, ainda pode definir outra rota com o prefixo de endereço 10.0.0.0/24. O Azure seleciona uma rota para o tráfego com base na mais longa partida de prefixo. Para saber mais sobre como o Azure seleciona rotas, consulte a [visão geral do Routing.](virtual-networks-udr-overview.md#how-azure-selects-a-route)
1. Selecione um **tipo de próximo lúpulo**. Para uma descrição detalhada de todos os tipos de lúpulo seguinte, consulte a visão geral do [Routing](virtual-networks-udr-overview.md).
1. Introduza um endereço IP para **o próximo endereço de lúpulo**. Só pode introduzir um endereço se selecionar *o aparelho Virtual* para o próximo tipo de **lúpulo**.
1. Selecione **OK**.

### <a name="create-a-route---commands"></a>Criar uma rota - comandos

* Azure CLI: [rota de rota da rede az cria](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)

## <a name="view-routes"></a>Ver rotas

Uma tabela de rotas contém zero ou múltiplas rotas. Para saber mais sobre as informações listadas ao visualizar rotas, consulte a [visão geral do Routing](virtual-networks-udr-overview.md).

1. Na caixa de pesquisa no topo do portal, introduza *tabelas* de rota na caixa de pesquisa. Quando as **tabelas De rota** aparecerem nos resultados da pesquisa, selecione-as.
1. Selecione a tabela de rotas da lista para a sua visualização de rotas.
1. Selecione **Rotas** em **DEFINIÇÕES**.

### <a name="view-routes---commands"></a>Ver rotas - comandos

* Azure CLI: [lista de rotas de rota da rede az](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="view-details-of-a-route"></a>Ver detalhes de uma rota

1. Na caixa de pesquisa no topo do portal, introduza *tabelas* de rota na caixa de pesquisa. Quando as **tabelas De rota** aparecerem nos resultados da pesquisa, selecione-as.
1. Selecione a tabela de rotas para a sua escolha.
1. Selecione **Rotas**.
1. Selecione o percurso que pretende ver detalhes.

### <a name="view-details-of-a-route---commands"></a>Ver detalhes de uma rota - comandos

* Azure CLI: [az network route-table show](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="change-a-route"></a>Alterar uma rota

1. Na caixa de pesquisa no topo do portal, introduza *tabelas* de rota na caixa de pesquisa. Quando as **tabelas De rota** aparecerem nos resultados da pesquisa, selecione-as.
1. Selecione a tabela de rotas para a que pretende alterar uma rota.
1. Selecione **Rotas**.
1. Selecione o percurso que pretende alterar.
1. Altere as definições existentes para as suas novas definições e, em seguida, selecione **Guardar**.

### <a name="change-a-route---commands"></a>Alterar uma rota - comandos

* Azure CLI: [atualização da rota da rota da rede az](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig)

## <a name="delete-a-route"></a>Apagar uma rota

1. Na caixa de pesquisa no topo do portal, introduza *tabelas* de rota na caixa de pesquisa. Quando as **tabelas De rota** aparecerem nos resultados da pesquisa, selecione-as.
1. Selecione a tabela de rotas para a a sua deseja eliminar uma rota.
1. Selecione **Rotas**.
1. A partir da lista de rotas, selecione... no lado direito da rota que pretende apagar.
1. **Selecione Eliminar**e, em seguida, selecione **Sim**.

### <a name="delete-a-route---commands"></a>Apagar uma rota - comandos

* Azure CLI: [az rota de rota de rota eliminação](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig)

## <a name="view-effective-routes"></a>Ver rotas eficazes

As rotas eficazes para cada interface de rede anexada a uma máquina virtual são uma combinação de tabelas de rotas que criou, as rotas padrão do Azure e quaisquer rotas propagadas a partir de redes no local através do BGP através de um portal de rede virtual Azure. Compreender as rotas eficazes para uma interface de rede é útil para resolver problemas de encaminhamento. Pode visualizar as rotas eficazes para qualquer interface de rede que esteja ligada a uma máquina virtual em execução.

1. Na caixa de pesquisa no topo do portal, introduza o nome de uma máquina virtual para a sua necessidade. Se não sabe o nome de uma máquina virtual, introduza *máquinas virtuais* na caixa de pesquisa. Quando **as máquinas Virtuais** aparecerem nos resultados da pesquisa, selecione-as e selecione uma máquina virtual da lista.
1. **Selecione a rede** em **DEFINIÇÕES**.
1. Selecione o nome de uma interface de rede.
1. Selecione **Rotas eficazes** em **SUPORTE + RESOLUÇÃO DE PROBLEMAS**.
1. Reveja a lista de rotas eficazes para determinar se existe a rota correta para onde pretende encaminhar o tráfego. Saiba mais sobre os próximos tipos de lúpulo que vê nesta lista na visão geral do [Routing](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Ver rotas eficazes - comandos

* Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest)<br>
* PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="validate-routing-between-two-endpoints"></a>Validar o encaminhamento entre dois pontos finais

Você pode determinar o próximo tipo de lúpulo entre uma máquina virtual e o endereço IP de outro recurso Azure, um recurso no local, ou um recurso na Internet. Determinar o encaminhamento do Azure é útil para resolver problemas de encaminhamento. Para completar esta tarefa, deve ter um Observador de Rede existente. Se não tiver um Observador de Rede existente, crie um, completando os passos em [Create a Network Watcher .](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

1. Na caixa de pesquisa no topo do portal, introduza o observador de *rede* na caixa de pesquisa. Quando a opção **Observador de Rede** aparecer nos resultados de pesquisa, selecione-a.
1. Selecione **O próximo salto** em **ferramentas**de DIAGNÓSTICO DE REDE .
1. Selecione a sua **Subscrição** e o **grupo Derecursos** da máquina virtual de origem a que pretende validar o encaminhamento.
1. Selecione a **máquina Virtual,** interface de **rede** ligada à máquina virtual e endereço **IP fonte** atribuído à interface de rede a que pretende validar o encaminhamento.
1. Introduza o **endereço IP** destino a que pretende validar o encaminhamento.
1. Selecione **Next hop**.
1. Após uma breve espera, a informação é devolvida que lhe diz o próximo tipo de lúpulo e a identificação da rota que encaminhava o tráfego. Saiba mais sobre os próximos tipos de lúpulo que vê devolvidos na visão geral de [Routing](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Validar o encaminhamento entre dois pontos finais - comandos

* Azure CLI: [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest)<br>
* PowerShell: [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop)

## <a name="permissions"></a>Permissões

Para executar tarefas em tabelas e rotas de rotas, a sua conta deve ser atribuída à função de contribuinte da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que lhe seja atribuída as ações adequadas enumeradas na tabela seguinte:

| Ação                                                          |   Nome                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Leia uma mesa de rota                                    |
| Microsoft.Network/routeTables/write                             |   Criar ou atualizar uma tabela de rotas                        |
| Microsoft.Network/routeTables/delete                            |   Eliminar uma tabela de rotas                                  |
| Microsoft.Network/routeTables/join/action                       |   Associar uma tabela de rotas a uma sub-rede                   |
| Microsoft.Network/routeTables/routes/read                       |   Leia uma rota                                          |
| Microsoft.Network/routeTables/routes/write                      |   Criar ou atualizar uma rota                              |
| Microsoft.Network/routeTables/routes/delete                     |   Apagar uma rota                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Obtenha a tabela de rota eficaz para uma interface de rede |
| Microsoft.Network/networkWatchers/nextHop/action                |   Recebe o próximo salto de um VM                           |

## <a name="next-steps"></a>Passos Seguintes

* Crie uma tabela de rotas utilizando scripts de amostra [PowerShell](powershell-samples.md) ou [Azure CLI,](cli-samples.md) ou utilizando [modelos](template-samples.md) de Gestor de Recursos Azure<br>
* Criar e aplicar [a política azure](policy-samples.md) para redes virtuais
