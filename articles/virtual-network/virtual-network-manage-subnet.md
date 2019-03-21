---
title: Adicionar, alterar ou eliminar uma sub-rede de rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba como adicionar, alterar ou eliminar uma sub-rede de rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: b4fd3d7cd1d25415ba5938586aee448bdc512aa7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58100467"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Adicionar, alterar ou eliminar uma sub-rede de rede virtual

Saiba como adicionar, alterar ou eliminar uma sub-rede de rede virtual. Todos os recursos do Azure, implementados numa rede virtual são implementados numa sub-rede numa rede virtual. Se estiver familiarizado com redes virtuais, pode saber mais sobre os mesmos no [descrição geral de rede Virtual](virtual-networks-overview.md) ou ao preencher um [tutorial](quick-create-portal.md). Para criar, alterar, ou eliminar uma rede virtual, veja [gerir uma rede virtual](manage-virtual-network.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir os passos em qualquer secção deste artigo:

- Se ainda não tiver uma conta do Azure, inscreva-se para uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se utilizar o portal, abra https://portal.azure.come inicie sessão com a sua conta do Azure.
- Se utilizar comandos do PowerShell para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/powershell), ou ao executar o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o Azure PowerShell versão 1.0.0 do módulo ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comandos (CLI) do Azure para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/bash), ou ao executar a CLI do seu computador. Este tutorial requer a versão 2.0.31 CLI do Azure ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar a CLI do Azure localmente, terá também de executar `az login` para criar uma ligação com o Azure.

A conta iniciar sessão no, ou ligar ao Azure, tem de ser atribuída para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atribuída as ações adequadas listadas na [permissões ](#permissions).

## <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Na caixa de pesquisa na parte superior do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecer nos resultados da pesquisa, selecione-o.
2. Na lista de redes virtuais, selecione a rede virtual que pretende adicionar uma sub-rede para.
3. Em **DEFINIÇÕES**, selecione **sub-redes**.
4. Selecione **+ sub-rede**.
5. Introduza valores para os seguintes parâmetros:
   - **Nome**: O nome tem de ser exclusivo dentro da rede virtual. Para compatibilidade máxima com outros serviços do Azure, recomendamos que utilize uma letra de como o primeiro caráter do nome. Por exemplo, o Gateway de aplicação do Azure só serão implementadas numa sub-rede que tem um nome que começa com um número.
   - **Intervalo de endereços**: O intervalo tem de ser exclusivo dentro do espaço de endereço para a rede virtual. O intervalo não pode sobrepor com outros intervalos de endereços da sub-rede na rede virtual. O espaço de endereços tem de ser especificado utilizando a notação de Classless entre domínios encaminhamento CIDR (). Por exemplo, numa rede virtual com endereço espaço 10.0.0.0/16, poderia definir um espaço de endereços da sub-rede de 10.0.0.0/24. O intervalo mais pequeno que pode especificar é /29, que fornece oito endereços IP para a sub-rede. O Azure reserva o endereço do primeiro e último em cada sub-rede para conformidade com o protocolo. Três endereços adicionais estão reservados para utilização do serviço do Azure. Como resultado, definindo uma sub-rede/29 resultados de intervalo em três utilizáveis endereços IP na sub-rede de endereços. Se planear ligar uma rede virtual a um gateway VPN, tem de criar uma sub-rede de gateway. Saiba mais sobre [considerações de intervalo de endereço específico para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). É possível alterar o intervalo de endereços, após a sub-rede é adicionada, em condições específicas. Para saber como alterar um intervalo de endereços da sub-rede, veja [alterar as definições de sub-rede](#change-subnet-settings).
   - **Grupo de segurança de rede**: Pode associar zero ou uma rede grupo de segurança existente a uma sub-rede para filtrar o tráfego de rede de entrada e saída para a sub-rede. O grupo de segurança de rede tem de existir na mesma subscrição e localização que a rede virtual. Saiba mais sobre [grupos de segurança de rede](security-overview.md) e [como criar um grupo de segurança de rede](tutorial-filter-network-traffic.md).
   - **Tabela de rotas**: Pode associar uma ou nenhuma rota tabela existente a uma sub-rede para controlar o encaminhamento de tráfego de rede a outras redes. A tabela de rotas tem de existir na mesma subscrição e localização que a rede virtual. Saiba mais sobre [encaminhamento do Azure](virtual-networks-udr-overview.md) e [como criar uma tabela de rotas](tutorial-create-route-table-portal.md)
   - **Pontos finais de serviço:** Uma sub-rede pode ter zero ou vários pontos finais de serviço ativados para o mesmo. Para ativar um ponto final de serviço para um serviço, selecione o serviço ou serviços que pretende ativar pontos finais de serviço para a partir da **serviços** lista. A localização é configurada automaticamente para um ponto de extremidade. Por predefinição, os pontos finais de serviço são configurados para a região da rede virtual. Armazenamento do Azure, para suportar cenários de ativação pós-falha regional, pontos finais são automaticamente configurados para [regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
   - **Delegação de sub-rede:** Uma sub-rede pode ter zero para vários delegações ativado para o mesmo. Delegação de sub-rede dá permissões explícitas para o serviço para criar recursos específicos do serviço na sub-rede com um identificador exclusivo quando implementar o serviço. Para delegar para um serviço, selecione o serviço que pretende delegar a partir da **serviços** lista.

       Para remover um ponto de extremidade de serviço, anule a seleção que pretende remover o ponto final de serviço para serviço. Para saber mais sobre pontos finais de serviço e os serviços podem ser ativadas para, veja [descrição geral de pontos finais de serviço de rede Virtual](virtual-network-service-endpoints-overview.md). Depois de ativar um ponto final de serviço para um serviço, deve habilitar acesso à rede para a sub-rede para um recurso criado com o serviço. Por exemplo, se ativar o ponto final de serviço para *Microsoft. Storage*, também tem de ativar o acesso de rede a todas as contas de armazenamento do Azure que pretende conceder acesso à rede. Para obter detalhes sobre como ativar o acesso à rede para sub-redes ativado para um ponto de extremidade de serviço, consulte a documentação para o serviço individual que ativou o ponto final de serviço para.

     Para validar que um ponto de extremidade de serviço está ativado para uma sub-rede, veja a [rotas efetivas](diagnose-network-routing-problem.md) para qualquer interface de rede na sub-rede. Quando um ponto de extremidade é configurado, verá uma *predefinição* rota com os prefixos de endereço do serviço e um nextHopType de **VirtualNetworkServiceEndpoint**. Para saber mais sobre o encaminhamento, consulte [descrição geral do encaminhamento](virtual-networks-udr-overview.md).
6. Para adicionar a sub-rede à rede virtual que selecionou, selecione **OK**.

**Comandos**

- CLI do Azure: [criar sub-rede de vnet de rede de az](/cli/azure/network/vnet/subnet)
- PowerShell: [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Alterar as definições de sub-rede

1. Na caixa de pesquisa na parte superior do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecer nos resultados da pesquisa, selecione-o.
2. Na lista de redes virtuais, selecione a rede virtual que contém a sub-rede que pretende alterar as definições.
3. Em **DEFINIÇÕES**, selecione **sub-redes**.
4. Na lista de sub-redes, selecione a sub-rede que pretende alterar as definições. Pode alterar as seguintes definições:

    - **Intervalo de endereços:** Se não existem recursos forem implementados na sub-rede, pode alterar o intervalo de endereços. Se existirem quaisquer recursos na sub-rede, deve mover os recursos para outra sub-rede ou eliminá-los primeiro a sub-rede. Os passos que efetuar para mover ou eliminar um recurso variam dependendo do recurso. Para saber como mover ou eliminar recursos que estão em sub-redes, leia a documentação para cada tipo de recurso que pretende mover ou eliminar. Consulte as restrições de **intervalo de endereços** no passo 5 da [adicionar uma sub-rede](#add-a-subnet).
    - **Os utilizadores**: Pode controlar o acesso para a sub-rede com funções incorporadas ou suas próprias funções personalizadas. Para saber mais sobre a atribuição de funções e os utilizadores acedam a sub-rede, veja [utilizar a atribuição de função para gerir o acesso aos recursos do Azure](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment).
    - **Grupo de segurança de rede** e **tabela de rotas**: Consulte o passo 5 de [adicionar uma sub-rede](#add-a-subnet).
    - **Pontos finais de serviço**: Ver pontos finais de serviço no passo 5 da [adicionar uma sub-rede](#add-a-subnet). Quando ativar um ponto final de serviço para uma sub-rede existente, certifique-se de que não existem tarefas críticas estão em execução em nenhum outro recurso na sub-rede. Pontos finais de serviço mudar as rotas em cada interface de rede na sub-rede do usando a rota padrão com o *0.0.0.0/0* abordar o prefixo e o tipo de próximo salto *Internet*, à utilização uma nova rota com o prefixos do serviço e um tipo de próximo salto de endereço *VirtualNetworkServiceEndpoint*. Durante a mudança, todas as ligações TCP abertas serão encerradas. O ponto final de serviço não está ativado até que os fluxos de tráfego para o serviço para todas as interfaces de rede são atualizados com a nova rota. Para saber mais sobre o encaminhamento, consulte [descrição geral do encaminhamento](virtual-networks-udr-overview.md).
    - **Delegação de sub-rede:** Ver pontos finais de serviço no passo 5 da [adicionar uma sub-rede](#add-a-subnet). Delegação de sub-rede pode ser modificada para zero ou vários delegações ativadas para o mesmo. Se um recurso para um serviço já está implementado na sub-rede, não é possível remover a delegação de sub-rede até que todos os recursos para o serviço são removidos. Para delegar para um serviço diferente, selecione o serviço que pretende delegar a partir da **serviços** lista.
5. Selecione **Guardar**.

**Comandos**

- CLI do Azure: [atualização de sub-rede de vnet de rede de az](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Eliminar uma sub-rede

Pode eliminar uma sub-rede apenas se não há nenhum recurso na sub-rede. Se existirem recursos na sub-rede, tem de eliminar os recursos que estão na sub-rede antes de poder eliminar a sub-rede. Os passos que efetuar para eliminar um recurso variam dependendo do recurso. Para saber como eliminar recursos que estão em sub-redes, leia a documentação para cada tipo de recurso que pretende eliminar.

1. Na caixa de pesquisa na parte superior do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecer nos resultados da pesquisa, selecione-o.
2. Na lista de redes virtuais, selecione a rede virtual que contém a sub-rede que pretende eliminar.
3. Em **DEFINIÇÕES**, selecione **sub-redes**.
4. Na lista de sub-redes, selecione **...** , à direita, para a sub-rede que pretende eliminar
5. Selecione **elimine**e, em seguida, selecione **Sim**.

**Comandos**

- CLI do Azure: [eliminar az rede vnet](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Permissões

Para executar tarefas em sub-redes, deve ser atribuída a sua conta para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) função que é atribuída as ações adequadas listadas na tabela a seguir:

|Ação                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Ler uma sub-rede de rede virtual              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Criar ou atualizar uma sub-rede de rede virtual  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Eliminar uma sub-rede de rede virtual            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Junte-se a uma rede virtual                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Ativar um ponto de final de serviço para uma sub-rede     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Obter as máquinas virtuais numa sub-rede       |

## <a name="next-steps"></a>Passos Seguintes

- Criar uma rede virtual e sub-redes usando [PowerShell](powershell-samples.md) ou [CLI do Azure](cli-samples.md) scripts ou utilizar o Azure de exemplo [modelos do Resource Manager](template-samples.md)
- Criar e aplicar [o Azure policy](policy-samples.md) para redes virtuais
