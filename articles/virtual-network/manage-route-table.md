---
title: Criar, alterar ou excluir uma tabela de rotas do Azure
titlesuffix: Azure Virtual Network
description: Saiba como criar, alterar ou excluir uma tabela de rotas.
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
ms.openlocfilehash: c57a1f7a676e5766dc7c022ae44e08750cccaf8b
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750598"
---
# <a name="create-change-or-delete-a-route-table"></a>Criar, alterar ou excluir uma tabela de rotas

O Azure roteia automaticamente o tráfego entre as sub-redes do Azure, as redes virtuais e as redes locais. Se você quiser alterar qualquer roteamento padrão do Azure, faça isso criando uma tabela de rotas. Se você for novo no roteamento em redes virtuais, poderá aprender mais sobre ela na [visão geral do roteamento](virtual-networks-udr-overview.md) ou concluindo um [tutorial](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

* Se você ainda não tiver uma conta do Azure, Inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).<br>
* Se estiver usando o portal, abra https://portal.azure.com e entre com sua conta do Azure.<br>
* Se estiver usando comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/powershell)ou executando o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o módulo Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.<br>
* Se você estiver usando comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/bash)ou executando a CLI do seu computador. Este tutorial requer o CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se você estiver executando o CLI do Azure localmente, também precisará executar `az login` para criar uma conexão com o Azure.

A conta na qual você entra ou se conecta ao Azure com, deve ser atribuída à função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas em [permissões](#permissions).

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Há um limite para quantas tabelas de rotas você pode criar por assinatura e local do Azure. Para obter mais detalhes, veja [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. No canto superior esquerdo do portal, selecione **+ criar um recurso**.
1. Selecione **rede**e, em seguida, selecione **tabela de rotas**.
1. Insira um **nome** para a tabela de rotas, selecione sua **assinatura**, crie um novo **grupo de recursos**ou selecione um grupo de recursos existente, selecione um **local**e, em seguida, selecione **criar**. Se você planeja associar a tabela de rotas a uma sub-rede em uma rede virtual conectada à sua rede local por meio de um gateway de VPN e desabilitar a **propagação de rota de gateway de rede virtual**, suas rotas locais não são propagadas para as interfaces de rede na sub-rede.

### <a name="create-route-table---commands"></a>Criar tabela de rotas-comandos

* CLI do Azure: [AZ Network rota-tabela Create](/cli/azure/network/route-table/route)<br>
* PowerShell: [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)

## <a name="view-route-tables"></a>Exibir tabelas de rotas

Na caixa de pesquisa na parte superior do portal, insira *tabelas de rotas* na caixa de pesquisa. Quando as **tabelas de rotas** aparecem nos resultados da pesquisa, selecione-as. As tabelas de rotas que existem na sua assinatura são listadas.

### <a name="view-route-table---commands"></a>Exibir tabela de rotas-comandos

* CLI do Azure: [AZ Network Route-tabela List](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="view-details-of-a-route-table"></a>Exibir detalhes de uma tabela de rotas

1. Na caixa de pesquisa na parte superior do portal, insira *tabelas de rotas* na caixa de pesquisa. Quando as **tabelas de rotas** aparecem nos resultados da pesquisa, selecione-as.
1. Selecione a tabela de rotas na lista para a qual você deseja exibir detalhes. Em **configurações**, você pode exibir as **rotas** na tabela de rotas e as **sub-redes** às quais a tabela de rotas está associada.
1. Para saber mais sobre as configurações comuns do Azure, consulte as seguintes informações:

    * [Registo de atividades](../azure-monitor/platform/platform-logs-overview.md)<br>
    * [Controle de acesso (IAM)](../role-based-access-control/overview.md)<br>
    * [Etiquetas](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Bloquea](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Script de automação](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Exibir detalhes da tabela de rotas – comandos

* CLI do Azure: [AZ Network Route-tabela show](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="change-a-route-table"></a>Alterar uma tabela de rotas

1. Na caixa de pesquisa na parte superior do portal, insira *tabelas de rotas* na caixa de pesquisa. Quando as **tabelas de rotas** aparecem nos resultados da pesquisa, selecione-as.
1. Selecione a tabela de rotas que você deseja alterar. As alterações mais comuns são [Adicionar](#create-a-route) ou [remover](#delete-a-route) rotas e [associar](#associate-a-route-table-to-a-subnet) tabelas de rotas para ou [dissociar](#dissociate-a-route-table-from-a-subnet) tabelas de rotas de sub-redes.

### <a name="change-a-route-table---commands"></a>Alterar uma tabela de rotas – comandos

* CLI do Azure: [AZ Network Route-Table Update](/cli/azure/network/route-table/route)<br>
* PowerShell: [set-AzRouteTable](/powershell/module/az.network/set-azroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Uma sub-rede pode ter zero ou uma tabela de rotas associada a ela. Uma tabela de rotas pode ser associada a zero ou várias sub-redes. Como as tabelas de rotas não estão associadas a redes virtuais, você deve associar uma tabela de rotas a cada sub-rede à qual deseja que a tabela de rotas seja associada. Todo o tráfego que sai da sub-rede é roteado com base nas rotas que você criou nas tabelas de rota, [rotas padrão](virtual-networks-udr-overview.md#default)e rotas propagadas de uma rede local, se a rede virtual estiver conectada a um gateway de rede virtual do Azure (EXPRESSROUTE ou VPN). Você só pode associar uma tabela de rotas a sub-redes em redes virtuais que existem no mesmo local e assinatura do Azure que a tabela de rotas.

1. Na caixa de pesquisa na parte superior do portal, insira *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecem nos resultados da pesquisa, selecione-as.
1. Selecione a rede virtual na lista que contém a sub-rede à qual você deseja associar uma tabela de rotas.
1. Selecione **sub-redes** em **configurações**.
1. Selecione a sub-rede à qual você deseja associar a tabela de rotas.
1. Selecione **tabela de rotas**, selecione a tabela de rotas que você deseja associar à sub-rede e, em seguida, selecione **salvar**.

Se a rede virtual esiver ligada a um gateway VPN do Azure, não associe nenhuma tabela de rota à [sub-rede do gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) que incluia uma rota com um destino 0.0.0.0/0. Se o fizer, poderá impedir que o gateway funcione corretamente. Para obter mais informações sobre como usar 0.0.0.0/0 em uma rota, consulte [Roteamento de tráfego de rede virtual](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Associar uma tabela de rotas – comandos

* CLI do Azure: [AZ Network vnet subnet Update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Dissociar uma tabela de rotas de uma sub-rede

Quando você dissocia uma tabela de rotas de uma sub-rede, o Azure roteia o tráfego com base em suas [rotas padrão](virtual-networks-udr-overview.md#default).

1. Na caixa de pesquisa na parte superior do portal, insira *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecem nos resultados da pesquisa, selecione-as.
1. Selecione a rede virtual que contém a sub-rede da qual você deseja dissociar uma tabela de rotas.
1. Selecione **sub-redes** em **configurações**.
1. Selecione a sub-rede da qual você deseja dissociar a tabela de rotas.
1. Selecione **tabela de rotas**, selecione **nenhum**e, em seguida, selecione **salvar**.

### <a name="dissociate-a-route-table---commands"></a>Dissociar uma tabela de rotas – comandos

* CLI do Azure: [AZ Network vnet subnet Update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-route-table"></a>Excluir uma tabela de rotas

Se uma tabela de rotas estiver associada a qualquer sub-rede, ela não poderá ser excluída. [Dissociar](#dissociate-a-route-table-from-a-subnet) uma tabela de rotas de todas as sub-redes antes de tentar excluí-la.

1. Na caixa de pesquisa na parte superior do portal, insira *tabelas de rotas* na caixa de pesquisa. Quando as **tabelas de rotas** aparecem nos resultados da pesquisa, selecione-as.
1. Selecione **...** no lado direito da tabela de rotas que você deseja excluir.
1. Selecione **Eliminar** e, em seguida, **Sim**.

### <a name="delete-a-route-table---commands"></a>Excluir uma tabela de rotas – comandos

* CLI do Azure: [AZ rede rota-tabela Delete](/cli/azure/network/route-table/route)<br>
* PowerShell: [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable)

## <a name="create-a-route"></a>Criar uma rota

Há um limite para quantas rotas por tabela de rotas podem ser criadas por assinatura e local do Azure. Para obter mais detalhes, veja [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Na caixa de pesquisa na parte superior do portal, insira *tabelas de rotas* na caixa de pesquisa. Quando as **tabelas de rotas** aparecem nos resultados da pesquisa, selecione-as.
1. Selecione a tabela de rotas na lista à qual você deseja adicionar uma rota.
1. Selecione **rotas**, em **configurações**.
1. Selecione **+ Adicionar**.
1. Insira um **nome** exclusivo para a rota dentro da tabela de rotas.
1. Insira o **prefixo de endereço**, em notação CIDR, para o qual você deseja rotear o tráfego. O prefixo não pode ser duplicado em mais de uma rota dentro da tabela de rotas, embora o prefixo possa estar dentro de outro prefixo. Por exemplo, se você definiu 10.0.0.0/16 como um prefixo em uma rota, ainda poderá definir outra rota com o prefixo de endereço 10.0.0.0/24. O Azure seleciona uma rota para o tráfego com base na correspondência de prefixo mais longa. Para saber mais sobre como o Azure seleciona rotas, consulte [visão geral de roteamento](virtual-networks-udr-overview.md#how-azure-selects-a-route).
1. Selecione um **tipo de próximo salto**. Para obter uma descrição detalhada de todos os tipos do próximo salto, consulte [visão geral de roteamento](virtual-networks-udr-overview.md).
1. Insira um endereço IP para o **endereço do próximo salto**. Você só poderá inserir um endereço se tiver selecionado *dispositivo virtual* para o **tipo do próximo salto**.
1. Selecione **OK**.

### <a name="create-a-route---commands"></a>Criar um comando de rota

* CLI do Azure: [AZ Network Route-tabela Route Create](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)

## <a name="view-routes"></a>Exibir rotas

Uma tabela de rotas contém zero ou várias rotas. Para saber mais sobre as informações listadas ao exibir rotas, consulte [visão geral de roteamento](virtual-networks-udr-overview.md).

1. Na caixa de pesquisa na parte superior do portal, insira *tabelas de rotas* na caixa de pesquisa. Quando as **tabelas de rotas** aparecem nos resultados da pesquisa, selecione-as.
1. Selecione a tabela de rotas da lista para a qual você deseja exibir rotas.
1. Selecione **rotas** em **configurações**.

### <a name="view-routes---commands"></a>Exibir rotas-comandos

* CLI do Azure: [AZ Network Route-tabela Route List](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="view-details-of-a-route"></a>Ver detalhes de uma rota

1. Na caixa de pesquisa na parte superior do portal, insira *tabelas de rotas* na caixa de pesquisa. Quando as **tabelas de rotas** aparecem nos resultados da pesquisa, selecione-as.
1. Selecione a tabela de rotas para a qual você deseja exibir detalhes de uma rota.
1. Selecione **rotas**.
1. Selecione a rota para a qual você deseja exibir detalhes.

### <a name="view-details-of-a-route---commands"></a>Exibir detalhes de um comando de rota

* CLI do Azure: [AZ Network Route-tabela Route show](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="change-a-route"></a>Alterar uma rota

1. Na caixa de pesquisa na parte superior do portal, insira *tabelas de rotas* na caixa de pesquisa. Quando as **tabelas de rotas** aparecem nos resultados da pesquisa, selecione-as.
1. Selecione a tabela de rotas para a qual você deseja alterar uma rota.
1. Selecione **rotas**.
1. Selecione a rota que você deseja alterar.
1. Altere as configurações existentes para suas novas configurações e, em seguida, selecione **salvar**.

### <a name="change-a-route---commands"></a>Alterar um comando de rota

* CLI do Azure: [AZ Network Route-tabela Route Update](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig)

## <a name="delete-a-route"></a>Excluir uma rota

1. Na caixa de pesquisa na parte superior do portal, insira *tabelas de rotas* na caixa de pesquisa. Quando as **tabelas de rotas** aparecem nos resultados da pesquisa, selecione-as.
1. Selecione a tabela de rotas para a qual você deseja excluir uma rota.
1. Selecione **rotas**.
1. Na lista de rotas, selecione **...** no lado direito da rota que você deseja excluir.
1. Selecione **excluir**e, em seguida, selecione **Sim**.

### <a name="delete-a-route---commands"></a>Excluir um comando de rota

* CLI do Azure: [AZ Network Route-tabela Route Delete](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig)

## <a name="view-effective-routes"></a>Exibir rotas efetivas

As rotas efetivas para cada interface de rede anexada a uma máquina virtual são uma combinação de tabelas de rotas que você criou, as rotas padrão do Azure e todas as rotas propagadas de redes locais via BGP por meio de um gateway de rede virtual do Azure. Entender as rotas efetivas para uma interface de rede é útil ao solucionar problemas de roteamento. Você pode exibir as rotas em vigor para qualquer interface de rede conectada a uma máquina virtual em execução.

1. Na caixa de pesquisa na parte superior do portal, insira o nome de uma máquina virtual para a qual você deseja exibir rotas efetivas. Se você não souber o nome de uma máquina virtual, insira *máquinas virtuais* na caixa de pesquisa. Quando as **máquinas virtuais** aparecem nos resultados da pesquisa, selecione-a e selecione uma máquina virtual na lista.
1. Selecione **rede** em **configurações**.
1. Selecione o nome de uma interface de rede.
1. Selecione **rotas efetivas** em **suporte + solução de problemas**.
1. Examine a lista de rotas efetivas para determinar se existe a rota correta para onde você deseja rotear o tráfego. Saiba mais sobre os tipos de próximo salto que você vê nesta lista na [visão geral de roteamento](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Exibir rotas efetivas-comandos

* CLI do Azure: [AZ Network NIC show-efetivo-Route-Table](/cli/azure/network/nic?view=azure-cli-latest)<br>
* PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="validate-routing-between-two-endpoints"></a>Validar o roteamento entre dois pontos de extremidade

Você pode determinar o tipo do próximo salto entre uma máquina virtual e o endereço IP de outro recurso do Azure, um recurso local ou um recurso na Internet. Determinar o roteamento do Azure é útil ao solucionar problemas de roteamento. Para concluir essa tarefa, você deve ter um observador de rede existente. Se você não tiver um observador de rede existente, crie um, concluindo as etapas em [criar uma instância do observador de rede](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Na caixa de pesquisa na parte superior do portal, digite *observador de rede* na caixa de pesquisa. Quando o **Observador de Rede** aparecer nos resultados da pesquisa, selecione-o.
1. Selecione **próximo salto** em **ferramentas de diagnóstico de rede**.
1. Selecione sua **assinatura** e o **grupo de recursos** da máquina virtual de origem da qual você deseja validar o roteamento.
1. Selecione a **máquina virtual**, a **interface de rede** conectada à máquina virtual e o **endereço IP de origem** atribuído à interface de rede da qual você deseja validar o roteamento.
1. Insira o **endereço IP de destino** para o qual você deseja validar o roteamento.
1. Selecione o **próximo salto**.
1. Após uma breve espera, são retornadas informações que indicam o tipo do próximo salto e a ID da rota que direcionou o tráfego. Saiba mais sobre os tipos de próximo salto que você vê retornados na [visão geral de roteamento](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Validar o roteamento entre dois pontos de extremidade-comandos

* CLI do Azure: [AZ Network vigia show-próximo salto](/cli/azure/network/watcher?view=azure-cli-latest)<br>
* PowerShell: [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop)

## <a name="permissions"></a>Permissões

Para executar tarefas em tabelas de rota e rotas, sua conta deve ser atribuída à função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas na tabela a seguir:

| Ação                                                          |   Nome                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Ler uma tabela de rotas                                    |
| Microsoft.Network/routeTables/write                             |   Criar ou atualizar uma tabela de rotas                        |
| Microsoft.Network/routeTables/delete                            |   Excluir uma tabela de rotas                                  |
| Microsoft.Network/routeTables/join/action                       |   Associar uma tabela de rotas a uma sub-rede                   |
| Microsoft.Network/routeTables/routes/read                       |   Ler uma rota                                          |
| Microsoft.Network/routeTables/routes/write                      |   Criar ou atualizar uma rota                              |
| Microsoft.Network/routeTables/routes/delete                     |   Excluir uma rota                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Obter a tabela de rotas efetiva para uma interface de rede |
| Microsoft.Network/networkWatchers/nextHop/action                |   Obtém o próximo salto de uma VM                           |

## <a name="next-steps"></a>Passos seguintes

* Criar uma tabela de rotas usando o [PowerShell](powershell-samples.md) ou [CLI do Azure](cli-samples.md) scripts de exemplo ou usando modelos do Azure [Resource Manager](template-samples.md)<br>
* Criar e aplicar a [política do Azure](policy-samples.md) para redes virtuais
