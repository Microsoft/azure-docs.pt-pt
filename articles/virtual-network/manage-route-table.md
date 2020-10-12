---
title: Criar, alterar ou eliminar uma tabela de rotas Azure
titlesuffix: Azure Virtual Network
description: Saiba onde encontrar informações sobre o encaminhamento de tráfego de rede virtual e como criar, alterar ou apagar uma tabela de rotas.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: 664e3851370be812cd7a0e58cf9beb1fddb5d991
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87291004"
---
# <a name="create-change-or-delete-a-route-table"></a>Criar, alterar ou eliminar uma tabela de rotas

O Azure liga automaticamente o tráfego entre sub-redes Azure, redes virtuais e redes no local. Se quiser alterar qualquer um dos roteamentos padrão do Azure, fá-lo criando uma tabela de rotas. Se você é novo em encaminhamento em redes virtuais, você pode aprender mais sobre isso em [encaminhamento de tráfego de rede virtual](virtual-networks-udr-overview.md) ou completando um [tutorial](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Antes de começar

Se não tiver uma, crie uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Em seguida, complete uma destas tarefas antes de iniciar etapas em qualquer secção deste artigo:

- **Utilizadores do Portal**: Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta Azure.

- **Utilizadores powerShell**: Ou executam os comandos na [Azure Cloud Shell,](https://shell.azure.com/powershell)ou executam o PowerShell a partir do computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. No separador de navegador Azure Cloud Shell, encontre a lista de dropdown **de ambiente Select** e, em seguida, escolha **PowerShell** se ainda não estiver selecionado.

    Se estiver a executar o PowerShell localmente, utilize a versão 1.0.0 ou mais tarde do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az.Network` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Também corra `Connect-AzAccount` para criar uma ligação com Azure.

- **Os utilizadores da interface de linha de comando azure (CLI):** Ou executam os comandos na [Azure Cloud Shell,](https://shell.azure.com/bash)ou executam o CLI a partir do computador. Utilize a versão 2.0.31 do Azure CLI ou mais tarde se estiver a executar o Azure CLI localmente. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). Também corra `az login` para criar uma ligação com Azure.

A conta em que inicia sessão ou liga-se ao Azure deve ser atribuída à [função de contribuinte da Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenha atribuído as ações [apropriadas listadas](#permissions)nas Permissões .

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Há um limite para quantas tabelas de rotas pode criar por localização e subscrição de Azure. Para mais informações, consulte [os limites de rede - Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. No menu do [portal do Azure](https://portal.azure.com) ou a partir da **Home page**, selecione **Criar um recurso**.

1. Na caixa de pesquisa, introduza a *tabela Rota*. Quando **a tabela Rota** aparecer nos resultados da pesquisa, selecione-a.

1. Na página da **tabela Rota,** selecione **Criar**.

1. Na caixa de diálogo de **tabela de rotas Create:**

    1. Insira um **Nome** para a tabela de rotas.
    1. Escolha a sua **Subscrição**.
    1. Escolha um **grupo** de Recursos existente ou selecione **Criar novo** para criar um novo grupo de recursos.
    1. Escolha uma **localização**.
    1. Se pretende associar a tabela de rotas a uma sub-rede numa rede virtual que está ligada à sua rede no local através de um gateway VPN, e não pretende propagar as suas rotas no local para as interfaces de rede na sub-rede, deteta a **propagação** da rota de gateway de rede virtual para **Desativado.**

1. Selecione **Criar** para criar a sua nova tabela de rotas.

### <a name="create-route-table---commands"></a>Criar tabela de rotas - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Ver tabelas de rotas

Vá ao [portal Azure](https://portal.azure.com) para gerir a sua rede virtual. Procure e selecione **tabelas de rotas**. As tabelas de rotas existentes na sua subscrição estão listadas.

### <a name="view-route-table---commands"></a>Ver tabela de rota - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [lista de tabelas de rota de rede az](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Ver detalhes de uma tabela de rotas

1. Vá ao [portal Azure](https://portal.azure.com) para gerir a sua rede virtual. Procure e selecione **tabelas de rotas**.

1. Na lista de tabelas de rotas, escolha a tabela de rotas para a qual deseja ver detalhes.

1. Na página do quadro de rotas, em **Definições,** veja as **Rotas** na tabela de rotas ou as **sub-redes** a que a tabela de rotas está associada.

Para saber mais sobre as configurações comuns do Azure, consulte as seguintes informações:

- [Registo de atividades](../azure-monitor/platform/platform-logs-overview.md)
- [Controlo de acesso (IAM)](../role-based-access-control/overview.md)
- [Etiquetas](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Bloqueios](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Script de automatização](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Ver detalhes da tabela de rotas - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede rota-table show](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Alterar uma tabela de rotas

1. Vá ao [portal Azure](https://portal.azure.com) para gerir a sua rede virtual. Procure e selecione **tabelas de rotas**.

1. Na lista de tabelas de rotas, escolha a tabela de rotas que pretende alterar.

As alterações mais comuns são [adicionar](#create-a-route) rotas, [remover](#delete-a-route) rotas, [associar](#associate-a-route-table-to-a-subnet) tabelas de rotas a sub-redes ou [dissociar](#dissociate-a-route-table-from-a-subnet) tabelas de rotas a partir de sub-redes.

### <a name="change-a-route-table---commands"></a>Alterar uma tabela de rotas - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede rota-table atualização](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Pode associar opcionalmente uma tabela de rota a uma sub-rede. Uma tabela de rotas pode ser associada a zero ou mais sub-redes. Como as tabelas de rotas não estão associadas a redes virtuais, deve associar uma tabela de rotas a cada sub-rede a que pretende a tabela de rotas associada. A Azure encaminha todo o tráfego que sai da sub-rede com base nas rotas que criou dentro das tabelas de rotas, [rotas padrão](virtual-networks-udr-overview.md#default)e rotas propagadas a partir de uma rede no local, se a rede virtual estiver ligada a um gateway de rede virtual Azure (ExpressRoute ou VPN). Só é possível associar uma tabela de rotas a sub-redes em redes virtuais que existam na mesma localização Estruz e subscrição do Azure que a tabela de rotas.

1. Vá ao [portal Azure](https://portal.azure.com) para gerir a sua rede virtual. Procure e selecione **redes Virtuais.**

1. Na lista de rede virtual, escolha a rede virtual que contém a sub-rede a que pretende associar uma tabela de rotas.

1. Na barra de menus de rede virtual, escolha **Subnets**.

1. Selecione a sub-rede a que pretende associar a tabela de rotas.

1. Na **tabela Rota,** escolha a tabela de rotas que pretende associar à sub-rede.

1. Selecione **Guardar**.

Se a sua rede virtual estiver ligada a um gateway Azure VPN, não associe uma tabela de rota à [sub-rede gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) que inclui uma rota com um destino de *0.0.0.0/0*. Se o fizer, poderá impedir que o gateway funcione corretamente. Para obter mais informações sobre a utilização *de 0.0.0.0/0* numa rota, consulte [o encaminhamento de tráfego de rede virtual](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Associar uma tabela de rotas - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Dissociem uma tabela de rotas a partir de uma sub-rede

Quando se dissocia uma tabela de rotas a partir de uma sub-rede, a Azure encaminha o tráfego com base nas suas [rotas predefinidas.](virtual-networks-udr-overview.md#default)

1. Vá ao [portal Azure](https://portal.azure.com) para gerir a sua rede virtual. Procure e selecione **redes Virtuais.**

1. Na lista de rede virtual, escolha a rede virtual que contém a sub-rede a partir da qual pretende dissociar uma tabela de rotas.

1. Na barra de menus de rede virtual, escolha **Subnets**.

1. Selecione a sub-rede de onde pretende dissociar a tabela de rotas.

1. Na **tabela Rota,** escolha **Nenhum**.

1. Selecione **Guardar**.

### <a name="dissociate-a-route-table---commands"></a>Dissociar uma tabela de rotas - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Excluir uma tabela de rotas

Não é possível apagar uma tabela de rotas associada a quaisquer sub-redes. [Dissociem](#dissociate-a-route-table-from-a-subnet) uma tabela de rotas de todas as sub-redes antes de tentar eliminá-la.

1. Vá ao [portal Azure](https://portal.azure.com) para gerir as suas tabelas de rotas. Procure e selecione **tabelas de rotas**.

1. Na lista de tabelas de rotas, escolha a tabela de rotas que pretende eliminar.

1. Selecione **Eliminar**e, em seguida, selecione **Sim** na caixa de diálogo de confirmação.

### <a name="delete-a-route-table---commands"></a>Excluir uma tabela de rotas - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [tabela de rota de rede az eliminar](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Criar uma rota

Há um limite para quantas rotas por tabela de rotas pode criar por localização e subscrição de Azure. Para mais informações, consulte [os limites de rede - Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Vá ao [portal Azure](https://portal.azure.com) para gerir as suas tabelas de rotas. Procure e selecione **tabelas de rotas**.

1. Na lista de tabelas de rotas, escolha a tabela de rotas a que pretende adicionar uma rota.

1. A partir da barra de menu de mesa de rota, escolha **Rotas**  >  **Adicionar.**

1. Introduza um **nome de rota** único para o percurso dentro da tabela de rotas.

1. Introduza o **prefixo address**, na notação de encaminhamento de Inter-Domain (CIDR) sem classe, para a quais pretende encaminhar o tráfego para. O prefixo não pode ser duplicado em mais de uma rota dentro da tabela de rotas, embora o prefixo possa estar dentro de outro prefixo. Por exemplo, se definiu *10.0.0.0/16* como prefixo numa rota, ainda pode definir outra rota com o prefixo de endereço *10.0.0.0/22.* O Azure seleciona uma rota para o tráfego com base na correspondência prefixo mais longa. Para saber mais, veja [como o Azure seleciona uma rota.](virtual-networks-udr-overview.md#how-azure-selects-a-route)

1. Escolha um **próximo tipo de lúpulo**. Para saber mais sobre os próximos tipos de lúpulo, consulte [o encaminhamento de tráfego de rede virtual.](virtual-networks-udr-overview.md)

1. Se escolher um tipo de **aparelho virtual**de **próximo lúpulo,** insira um endereço IP para **o próximo endereço de lúpulo**.

1. Selecione **OK**.

### <a name="create-a-route---commands"></a>Criar uma rota - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Ver rotas

Uma tabela de rotas contém zero ou mais rotas. Para saber mais sobre as informações listadas ao visualizar as rotas, consulte [o encaminhamento de tráfego de rede virtual.](virtual-networks-udr-overview.md)

1. Vá ao [portal Azure](https://portal.azure.com) para gerir as suas tabelas de rotas. Procure e selecione **tabelas de rotas**.

1. Na lista de tabelas de rotas, escolha a tabela de rotas para a qual deseja ver as rotas.

1. Na barra de menu de mesa de rota, escolha **Rotas** para ver a lista de rotas.

### <a name="view-routes---commands"></a>Ver rotas - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [lista de rotas de rota de rede az](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Ver detalhes de uma rota

1. Vá ao [portal Azure](https://portal.azure.com) para gerir as suas tabelas de rotas. Procure e selecione **tabelas de rotas**.

1. Na lista de tabelas de rotas, escolha a tabela de rotas que contém o percurso para o qual pretende ver detalhes.

1. Na barra de menu de mesa de rota, escolha **Rotas** para ver a lista de rotas.

1. Selecione a rota que deseja ver detalhes.

### <a name="view-details-of-a-route---commands"></a>Ver detalhes de uma rota - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede rota-tabela show](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Mudar uma rota

1. Vá ao [portal Azure](https://portal.azure.com) para gerir as suas tabelas de rotas. Procure e selecione **tabelas de rotas**.

1. Na lista de tabelas de rotas, escolha a tabela de rotas que contém o percurso que pretende alterar.

1. Na barra de menu de mesa de rota, escolha **Rotas** para ver a lista de rotas.

1. Escolha o percurso que pretende alterar.

1. Altere as definições existentes para as suas novas definições e, em seguida, **selecione Guardar**.

### <a name="change-a-route---commands"></a>Alterar uma rota - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede rota-tabela de rota atualização](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Excluir uma rota

1. Vá ao [portal Azure](https://portal.azure.com) para gerir as suas tabelas de rotas. Procure e selecione **tabelas de rotas**.

1. Na lista de tabelas de rotas, escolha a tabela de rotas que contém a rota que pretende eliminar.

1. Na barra de menu de mesa de rota, escolha **Rotas** para ver a lista de rotas.

1. Escolha o percurso que pretende eliminar.

1. Selecione **Eliminar**e, em seguida, selecione **Sim** na caixa de diálogo de confirmação.

### <a name="delete-a-route---commands"></a>Excluir uma rota - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [rota de rota de rede az eliminar](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Remover-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Ver rotas eficazes

As rotas eficazes para cada interface de rede anexada a VM são uma combinação de tabelas de rotas que você criou, as rotas padrão de Azure, e quaisquer rotas propagadas a partir de redes no local através do Border Gateway Protocol (BGP) através de um gateway de rede virtual Azure. Compreender as rotas eficazes para uma interface de rede é útil quando se trata de problemas de resolução de problemas de encaminhamento. Pode ver as rotas eficazes para qualquer interface de rede que esteja ligada a um VM em execução.

1. Vá ao [portal Azure](https://portal.azure.com) para gerir os seus VMs. Procure e selecione **máquinas Virtuais.**

1. Na lista de máquinas virtuais, escolha o VM para o qual pretende visualizar rotas eficazes.

1. Na barra de menus VM, escolha **Networking**.

1. Selecione o nome de uma interface de rede.

1. Na barra de menu de interface de rede, selecione **Rotas Eficazes**.

1. Reveja a lista de rotas eficazes para ver se existe a rota correta para onde pretende encaminhar o tráfego. Saiba mais sobre os próximos tipos de lúpulo que vê nesta lista no [encaminhamento de tráfego de rede virtual.](virtual-networks-udr-overview.md)

### <a name="view-effective-routes---commands"></a>Ver rotas eficazes - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Validar o encaminhamento entre dois pontos finais

Pode determinar o próximo tipo de lúpulo entre uma máquina virtual e o endereço IP de outro recurso Azure, um recurso no local ou um recurso na Internet. Determinar o encaminhamento do Azure é útil quando se resolução de problemas de encaminhamento. Para completar esta tarefa, tem de ter um observador de rede existente. Se não tiver um observador de rede existente, crie um, completando os passos na [instância Criar um Observador de Rede](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Vá ao [portal Azure](https://portal.azure.com) para gerir os seus observadores de rede. Procure e selecione **Network Watcher**.

1. Na barra de menu do observador de rede, escolha **Next hop**.

1. No **Observador de Rede ! Próxima página de lúpulo:**

    1. Escolha a sua **Subscrição** e o grupo de **Recursos** da fonte VM a partir da qual pretende validar o encaminhamento.

    1. Escolha a **máquina Virtual** e a interface **de Rede** que está ligada ao VM.
    
    1. Introduza um **endereço IP de Origem** atribuído à interface de rede a partir do quais pretende validar o encaminhamento.

    1. Insira um **endereço IP de destino** para o que pretende validar o encaminhamento para.

1. Selecione **Next hop**.

Depois de uma curta espera, a Azure diz-lhe o próximo tipo de lúpulo e o ID da rota que encaminhou o tráfego. Saiba mais sobre os próximos tipos de lúpulo que vê devolvidos no [encaminhamento de tráfego de rede virtual.](virtual-networks-udr-overview.md)

### <a name="validate-routing-between-two-endpoints---commands"></a>Validar o encaminhamento entre dois pontos finais - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network watcher show-next-hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Permissões

Para realizar tarefas em tabelas e rotas de rota, a sua conta deve ser atribuída à [função de contribuinte da Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenha atribuído as ações apropriadas listadas no quadro seguinte:

| Ação                                                          |   Nome                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Leia uma tabela de rotas                                    |
| Microsoft.Network/routeTables/write                             |   Criar ou atualizar uma tabela de rotas                        |
| Microsoft.Network/routeTables/delete                            |   Excluir uma tabela de rotas                                  |
| Microsoft.Network/routeTables/join/action                       |   Associar uma tabela de rotas a uma sub-rede                   |
| Microsoft.Network/routeTables/routes/read                       |   Leia uma rota                                          |
| Microsoft.Network/routeTables/routes/write                      |   Criar ou atualizar uma rota                              |
| Microsoft.Network/routeTables/routes/delete                     |   Excluir uma rota                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Obtenha a tabela de rotas eficaz para uma interface de rede |
| Microsoft.Network/networkWatchers/nextHop/action                |   Recebe o próximo salto de um VM                           |

## <a name="next-steps"></a>Passos seguintes

- Crie uma tabela de rotas utilizando scripts de [amostras powerShell](powershell-samples.md) ou [Azure CLI](cli-samples.md) ou [modelos](template-samples.md) de Gestor de Recursos Azure
- Criar e atribuir [definições de Política Azure](policy-samples.md) para redes virtuais
