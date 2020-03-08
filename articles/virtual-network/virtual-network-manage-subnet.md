---
title: Adicionar, alterar ou eliminar uma subrede de rede virtual Azure
titlesuffix: Azure Virtual Network
description: Aprenda a adicionar, alterar ou eliminar uma subrede de rede virtual em Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: de80094c3fd2df7d2f8b32d1e968e9bebea847a1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356022"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Adicionar, alterar ou eliminar uma subnet de rede virtual

Aprenda a adicionar, alterar ou eliminar uma subnet de rede virtual. Todos os recursos do Azure implantados numa rede virtual são implantados numa subrede dentro de uma rede virtual. Se você é novo em redes virtuais, você pode aprender mais sobre eles na visão geral da [rede Virtual](virtual-networks-overview.md) ou completando um [tutorial](quick-create-portal.md). Para criar, alterar ou eliminar uma rede virtual, ver [Gerir uma rede virtual](manage-virtual-network.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Complete as seguintes tarefas antes de completar os passos em qualquer secção deste artigo:

- Se ainda não tem uma conta Azure, inscreva-se numa [conta de teste gratuita.](https://azure.microsoft.com/free)
- Se utilizar o portal, abra https://portal.azure.come faça login na sua conta Azure.
- Se utilizar os comandos PowerShell para completar tarefas neste artigo, execute os comandos na Casca de [Nuvem Azure,](https://shell.azure.com/powershell)ou executando powerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer a versão 1.0.0 ou posterior do módulo PowerShell Azure. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comando Azure (CLI) para completar tarefas neste artigo, execute os comandos na [Casca de Nuvem Azure,](https://shell.azure.com/bash)ou executando o CLI a partir do seu computador. Este tutorial requer a versão Azure CLI 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI localmente, também precisa de correr `az login` para criar uma ligação com o Azure.

A conta em que inicia sessão, ou liga-se ao Azure, deve ser atribuída à função de contribuinte da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que lhe seja atribuída as ações adequadas enumeradas nas [Permissões](#permissions).

## <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando as **redes Virtuais** aparecerem nos resultados da pesquisa, selecione-as.
2. A partir da lista de redes virtuais, selecione a rede virtual a que pretende adicionar uma subnet.
3. Em **DEFINIÇÕES**, selecione **sub-redes**.
4. **Selecione +Subnet**.
5. Introduza valores para os seguintes parâmetros:
   - **Nome**: O nome deve ser único dentro da rede virtual. Para uma compatibilidade máxima com outros serviços Azure, recomendamos a utilização de uma letra como primeiro personagem do nome. Por exemplo, o Azure Application Gateway não se implanta numa sub-rede que tenha um nome que comece com um número.
   - Intervalo de **endereços**: O intervalo deve ser único dentro do espaço de endereço para a rede virtual. A gama não pode sobrepor-se a outras gamas de endereços de sub-rede dentro da rede virtual. O espaço de endereço deve ser especificado utilizando a notação de encaminhamento inter-domínio sem classe (CIDR). Por exemplo, numa rede virtual com espaço de endereço 10.0.0.0.0/16, pode definir um espaço de endereço de sub-rede de 10.0.0.0/24. A gama mais pequena que pode especificar é /29, que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e último endereço em cada subnet para a conformidade do protocolo. Três endereços adicionais estão reservados para a utilização do serviço Azure. Como resultado, a definição de uma sub-rede com uma gama de endereços /29 resulta em três endereços IP utilizáveis na sub-rede. Se planeia ligar uma rede virtual a um gateway VPN, tem de criar uma sub-rede de gateway. Saiba mais sobre [considerações específicas de gama de endereços para subredes gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Pode alterar o intervalo de endereços após a adição da sub-rede, em condições específicas. Para aprender a alterar uma gama de endereços de sub-rede, consulte [as definições da sub-rede .](#change-subnet-settings)
   - **Grupo de segurança**da rede: Pode associar zero, ou um grupo de segurança de rede existente a uma subrede para filtrar o tráfego de rede de entrada e saída para a subrede. O grupo de segurança da rede deve existir na mesma subscrição e localização que a rede virtual. Saiba mais sobre [grupos](security-overview.md) de segurança de rede e [como criar um grupo](tutorial-filter-network-traffic.md)de segurança de rede .
   - **Tabela de rota**: Pode associar zero ou uma tabela de rota existente a uma subnet para controlar o encaminhamento de tráfego da rede para outras redes. A tabela de rotas deve existir na mesma subscrição e localização que a rede virtual. Saiba mais sobre [o encaminhamento do Azure](virtual-networks-udr-overview.md) e [como criar uma tabela](tutorial-create-route-table-portal.md) de rotas
   - **Pontos finais do serviço:** Uma subnet a nula pode ter zero ou vários pontos finais de serviço ativados para o mesmo. Para ativar um ponto final de serviço para um serviço, selecione o serviço ou serviços que pretende ativar pontos finais de serviço da lista de **Serviços.** A localização é configurada automaticamente para um ponto final. Por padrão, os pontos finais do serviço estão configurados para a região da rede virtual. Para o Armazenamento Azure, para apoiar cenários regionais de failover, os pontos finais são automaticamente configurados para [regiões emparelhadas azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
   
       Para remover um ponto final de serviço, desmarque o serviço para o que pretende remover o ponto final do serviço. Para saber mais sobre os pontos finais do serviço e sobre os serviços para os quais podem ser ativados, consulte a visão geral dos [pontos finais do serviço](virtual-network-service-endpoints-overview.md)de rede Virtual. Uma vez que você ativa um ponto final de serviço para um serviço, você também deve ativar o acesso da rede para a subnet para um recurso criado com o serviço. Por exemplo, se ativar o ponto final do serviço para *microsoft.Storage,* também deve ativar o acesso à rede a todas as contas do Armazenamento Azure a que pretende conceder acesso à rede. Para mais detalhes sobre como permitir o acesso da rede às redes que um ponto final de serviço está habilitado, consulte a documentação para o serviço individual para o qual ativou o ponto final do serviço.

     Para validar que um ponto final de serviço está ativado para uma sub-rede, veja as [rotas eficazes](diagnose-network-routing-problem.md) para qualquer interface de rede na subrede. Quando um ponto final é configurado, você vê uma rota *padrão* com os prefixos de endereço do serviço, e um próximo HopType de **VirtualNetworkServiceEndpoint**. Para saber mais sobre o encaminhamento, consulte a [visão geral do Routing.](virtual-networks-udr-overview.md)
   - **Delegação subnet:** Uma sub-rede pode ter zero a várias delegações habilitadas para ela. A delegação da Subnet dá permissões explícitas ao serviço para criar recursos específicos de serviço na subrede utilizando um identificador único ao implementar o serviço. Para delegar um serviço, selecione o serviço a que pretende delegar na lista **de Serviços.**

6. Para adicionar a sub-rede à rede virtual que selecionou, selecione **OK**.

**Comandos**

- Azure CLI: [az rede vnet subnet criar](/cli/azure/network/vnet/subnet)
- PowerShell: [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Alterar as definições de sub-rede

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando as **redes Virtuais** aparecerem nos resultados da pesquisa, selecione-as.
2. A partir da lista de redes virtuais, selecione a rede virtual que contém a sub-rede para a sua deseja alterar as definições.
3. Em **DEFINIÇÕES**, selecione **sub-redes**.
4. Na lista de subredes, selecione a sub-rede para a que pretende alterar as definições. Pode alterar as seguintes definições:

    - **Intervalo de endereços:** Se não forem mobilizados recursos dentro da sub-rede, pode alterar o intervalo de endereços. Se existirem recursos na sub-rede, deve transferir os recursos para outra sub-rede ou eliminá-los primeiro da sub-rede. Os passos que toma para mover ou apagar um recurso variam consoante o recurso. Para aprender a mover ou apagar os recursos que estão em subredes, leia a documentação para cada tipo de recurso que pretende mover ou apagar. Consulte as restrições para **o intervalo de endereço** no passo 5 de Adicionar uma [sub-rede](#add-a-subnet).
    - **Utilizadores**: Pode controlar o acesso à subrede utilizando funções incorporadas ou as suas próprias funções personalizadas. Para saber mais sobre atribuir funções e utilizadores para aceder à subnet, consulte a atribuição de [funções de utilização para gerir o acesso aos seus recursos Azure](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment).
    - **Grupo de segurança da rede** e tabela **Rota**: Ver passo 5 de Adicionar [uma sub-rede](#add-a-subnet).
    - **Pontos finais**de serviço : Consulte os pontos finais do serviço no passo 5 de [Adicionar uma sub-rede](#add-a-subnet). Ao ativar um ponto final de serviço para uma sub-rede existente, certifique-se de que não estão a decorrer tarefas críticas em qualquer recurso da sub-rede. Os pontos finais do serviço mudam as rotas em todas as interfaces de rede da subnet desde a utilização da rota predefinida com o prefixo de endereço *0.0.0.0/0* e o próximo tipo de *Internet*hop, até utilizar uma nova rota com os prefixos de endereço do serviço, e um próximo tipo de lúpulo de *VirtualNetworkServiceEndpoint*. Durante o interruptor, quaisquer ligações TCP abertas podem ser terminadas. O ponto final do serviço não está ativado até que o tráfego flua para o serviço para todas as interfaces de rede seja atualizado com a nova rota. Para saber mais sobre o encaminhamento, consulte a [visão geral do Routing.](virtual-networks-udr-overview.md)
    - **Delegação subnet:** Consulte os pontos finais do serviço no passo 5 de [Adicionar uma sub-rede](#add-a-subnet). A delegação da sub-rede pode ser modificada para zero ou várias delegações habilitadas para a sua utilização. Se um recurso para um serviço já estiver implantado na sub-rede, a delegação da sub-rede não pode ser adicionada ou removida até que todos os recursos para o serviço sejam removidos. Para delegar um serviço diferente, selecione o serviço a que pretende delegar na lista **de Serviços.**
5. Selecione **Guardar**.

**Comandos**

- Azure CLI: [az rede vnet subnet atualização](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Eliminar uma sub-rede

Só é possível eliminar uma sub-rede se não houver recursos na sub-rede. Se houver recursos na sub-rede, deve eliminar os recursos que estão na sub-rede antes de poder eliminar a sub-rede. Os passos que toma para eliminar um recurso variam consoante o recurso. Para aprender a eliminar os recursos que se encontram nas subredes, leia a documentação para cada tipo de recurso que pretende eliminar.

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando as **redes Virtuais** aparecerem nos resultados da pesquisa, selecione-as.
2. A partir da lista de redes virtuais, selecione a rede virtual que contém a sub-rede que pretende eliminar.
3. Em **DEFINIÇÕES**, selecione **sub-redes**.
4. Na lista de subnets, selecione... **à**direita, para a sub-rede que pretende eliminar
5. **Selecione Eliminar**e, em seguida, selecione **Sim**.

**Comandos**

- Azure CLI: [az rede vnet subnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Permissões

Para executar tarefas em subredes, a sua conta deve ser atribuída à função de contribuinte da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que lhe seja atribuída as ações adequadas enumeradas no quadro seguinte:

|Ação                                                                   |   Nome                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Leia uma subnet de rede virtual              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Criar ou atualizar uma subnet de rede virtual  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Eliminar uma subrede de rede virtual            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Junte-se a uma rede virtual                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Ativar um ponto final de serviço para uma subrede     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Obtenha as máquinas virtuais numa subneta       |

## <a name="next-steps"></a>Passos seguintes

- Criar uma rede virtual e subredes utilizando scripts de amostra [PowerShell](powershell-samples.md) ou [Azure CLI,](cli-samples.md) ou utilizando [modelos](template-samples.md) de Gestor de Recursos Azure
- Criar e aplicar [a política azure](policy-samples.md) para redes virtuais
