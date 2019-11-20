---
title: Adicionar, alterar ou excluir uma sub-rede de rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba como adicionar, alterar ou excluir uma sub-rede de rede virtual no Azure.
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
ms.openlocfilehash: e8717d10f61dfd50b9cdfa20a91203a5842d4c7d
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185386"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Adicionar, alterar ou excluir uma sub-rede de rede virtual

Saiba como adicionar, alterar ou excluir uma sub-rede de rede virtual. Todos os recursos do Azure implantados em uma rede virtual são implantados em uma sub-rede em uma rede virtual. Se você for novo em redes virtuais, poderá aprender mais sobre elas na [visão geral da rede virtual](virtual-networks-overview.md) ou concluindo um [tutorial](quick-create-portal.md). Para criar, alterar ou excluir uma rede virtual, consulte [gerenciar uma rede virtual](manage-virtual-network.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Se você ainda não tiver uma conta do Azure, Inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o portal, abra https://portal.azure.come faça logon com sua conta do Azure.
- Se estiver usando comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/powershell)ou executando o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o módulo Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se você estiver usando comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/bash)ou executando a CLI do seu computador. Este tutorial requer o CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se você estiver executando o CLI do Azure localmente, também precisará executar `az login` para criar uma conexão com o Azure.

A conta que você faz logon ou conecta-se ao Azure com o, deve ser atribuída à função [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas em [permissões](#permissions).

## <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Na caixa de pesquisa na parte superior do portal, insira *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecem nos resultados da pesquisa, selecione-as.
2. Na lista de redes virtuais, selecione a rede virtual à qual você deseja adicionar uma sub-rede.
3. Em **DEFINIÇÕES**, selecione **sub-redes**.
4. Selecione **+ sub-rede**.
5. Insira valores para os seguintes parâmetros:
   - **Nome**: o nome deve ser exclusivo na rede virtual. Para obter a compatibilidade máxima com outros serviços do Azure, é recomendável usar uma letra como o primeiro caractere do nome. Por exemplo, Aplicativo Azure gateway não será implantado em uma sub-rede que tem um nome que começa com um número.
   - **Intervalo de endereços**: o intervalo deve ser exclusivo dentro do espaço de endereço para a rede virtual. O intervalo não pode se sobrepor a outros intervalos de endereços de sub-rede na rede virtual. O espaço de endereço deve ser especificado usando a notação CIDR (roteamento entre domínios sem classificação). Por exemplo, em uma rede virtual com espaço de endereço 10.0.0.0/16, você pode definir um espaço de endereço de sub-rede de 10.0.0.0/24. O menor intervalo que você pode especificar é/29, que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e o último endereço em cada sub-rede para conformidade com o protocolo. Três endereços adicionais são reservados para o uso do serviço do Azure. Como resultado, a definição de uma sub-rede com um intervalo de endereços/29 resulta em três endereços IP utilizáveis na sub-rede. Se você planeja conectar uma rede virtual a um gateway de VPN, você deve criar uma sub-rede de gateway. Saiba mais sobre [Considerações específicas de intervalo de endereços para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Você pode alterar o intervalo de endereços depois que a sub-rede é adicionada, sob condições específicas. Para saber como alterar um intervalo de endereços de sub-rede, consulte [alterar as configurações de sub-rede](#change-subnet-settings).
   - **Grupo de segurança de rede**: você pode associar zero ou um grupo de segurança de rede existente a uma sub-rede para filtrar o tráfego de rede de entrada e saída para a sub-rede. O grupo de segurança de rede deve existir na mesma assinatura e local que a rede virtual. Saiba mais sobre [grupos de segurança de rede](security-overview.md) e [como criar um grupo de segurança de rede](tutorial-filter-network-traffic.md).
   - **Tabela de rotas**: você pode associar zero ou uma tabela de rotas existente a uma sub-rede para controlar o roteamento de tráfego de rede para outras redes. A tabela de rotas deve existir na mesma assinatura e local que a rede virtual. Saiba mais sobre o [Roteamento do Azure](virtual-networks-udr-overview.md) e [como criar uma tabela de rotas](tutorial-create-route-table-portal.md)
   - **Pontos de extremidade de serviço:** Uma sub-rede pode ter zero ou vários pontos de extremidade de serviço habilitados para ela. Para habilitar um ponto de extremidade de serviço para um serviço, selecione o serviço ou serviços para os quais você deseja habilitar os pontos de extremidades de serviço na lista de **Serviços** . O local é configurado automaticamente para um ponto de extremidade. Por padrão, os pontos de extremidade de serviço são configurados para a região da rede virtual. Para o armazenamento do Azure, para dar suporte a cenários de failover regional, os pontos de extremidade são automaticamente configurados para [regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
   - **Delegação de sub-rede:** Uma sub-rede pode ter zero a várias delegações habilitadas para ela. A delegação de sub-rede fornece permissões explícitas ao serviço para criar recursos específicos do serviço na sub-rede usando um identificador exclusivo ao implantar o serviço. Para delegar um serviço, selecione o serviço que você deseja delegar na lista de **Serviços** .

       Para remover um ponto de extremidade de serviço, desmarque o serviço para o qual você deseja remover o ponto de extremidade de serviço. Para saber mais sobre os pontos de extremidade de serviço e os serviços para os quais eles podem ser habilitados, consulte [visão geral de pontos de extremidade de serviço de rede virtual](virtual-network-service-endpoints-overview.md). Depois de habilitar um ponto de extremidade de serviço para um serviço, você também deve habilitar o acesso à rede para a sub-rede para um recurso criado com o serviço. Por exemplo, se você habilitar o ponto de extremidade de serviço para *Microsoft. Storage*, também deverá habilitar o acesso à rede para todas as contas de armazenamento do Azure às quais você deseja conceder acesso à rede. Para obter detalhes sobre como habilitar o acesso à rede para sub-redes para as quais um ponto de extremidade de serviço está habilitado, consulte a documentação do serviço individual para o qual você habilitou o ponto de extremidade de serviço.

     Para validar que um ponto de extremidade de serviço está habilitado para uma sub-rede, exiba as [rotas efetivas](diagnose-network-routing-problem.md) para qualquer interface de rede na sub-rede. Quando um ponto de extremidade é configurado, você vê uma rota *padrão* com os prefixos de endereço do serviço e um NextHopType de **VirtualNetworkServiceEndpoint**. Para saber mais sobre roteamento, consulte [visão geral de roteamento](virtual-networks-udr-overview.md).
6. Para adicionar a sub-rede à rede virtual que você selecionou, selecione **OK**.

**Comandos**

- CLI do Azure: [AZ Network vnet subnet Create](/cli/azure/network/vnet/subnet)
- PowerShell: [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Alterar configurações de sub-rede

1. Na caixa de pesquisa na parte superior do portal, insira *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecem nos resultados da pesquisa, selecione-as.
2. Na lista de redes virtuais, selecione a rede virtual que contém a sub-rede para a qual você deseja alterar as configurações.
3. Em **DEFINIÇÕES**, selecione **sub-redes**.
4. Na lista de sub-redes, selecione a sub-rede para a qual você deseja alterar as configurações. Você pode alterar as seguintes configurações:

    - **Intervalo de endereços:** Se nenhum recurso for implantado na sub-rede, você poderá alterar o intervalo de endereços. Se existirem recursos na sub-rede, você deverá mover os recursos para outra sub-rede ou excluí-los da sub-rede primeiro. As etapas executadas para mover ou excluir um recurso variam de acordo com o recurso. Para saber como mover ou excluir recursos que estão em sub-redes, leia a documentação de cada tipo de recurso que você deseja mover ou excluir. Consulte as restrições para o **intervalo de endereços** na etapa 5 de [Adicionar uma sub-rede](#add-a-subnet).
    - **Usuários**: você pode controlar o acesso à sub-rede usando funções internas ou suas próprias funções personalizadas. Para saber mais sobre como atribuir funções e usuários para acessar a sub-rede, confira [usar a atribuição de função para gerenciar o acesso aos recursos do Azure](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment).
    - **Grupo de segurança de rede** e **tabela de rotas**: consulte a etapa 5 de [Adicionar uma sub-rede](#add-a-subnet).
    - **Pontos de extremidade de serviço**: consulte pontos de extremidade de serviço na etapa 5 de [Adicionar uma sub-rede](#add-a-subnet). Ao habilitar um ponto de extremidade de serviço para uma sub-rede existente, certifique-se de que nenhuma tarefa crítica esteja em execução em nenhum recurso na sub-rede. Os pontos de extremidade de serviço alternam as rotas em cada interface de rede na sub-rede usando a rota padrão com o prefixo de endereço *0.0.0.0/0* e o tipo do próximo salto da *Internet*, para usar uma nova rota com os prefixos de endereço do serviço e um tipo de próximo salto de *VirtualNetworkServiceEndpoint*. Durante a mudança, qualquer conexão TCP aberta pode ser encerrada. O ponto de extremidade de serviço não estará habilitado até que o tráfego flua para o serviço para todas as interfaces de rede sejam atualizados com a nova rota. Para saber mais sobre roteamento, consulte [visão geral de roteamento](virtual-networks-udr-overview.md).
    - **Delegação de sub-rede:** Consulte pontos de extremidade de serviço na etapa 5 de [Adicionar uma sub-rede](#add-a-subnet). A delegação de sub-rede pode ser modificada para zero ou várias delegações habilitadas para ela. Se um recurso para um serviço já estiver implantado na sub-rede, a delegação de sub-rede não poderá ser adicionada ou removida até que todos os recursos do serviço sejam removidos. Para delegar para um serviço diferente, selecione o serviço que você deseja delegar na lista de **Serviços** .
5. Selecione **Guardar**.

**Comandos**

- CLI do Azure: [AZ Network vnet subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Excluir uma sub-rede

Você só poderá excluir uma sub-rede se não houver recursos na sub-rede. Se houver recursos na sub-rede, você deverá excluir os recursos que estão na sub-rede antes de poder excluir a sub-rede. As etapas executadas para excluir um recurso variam de acordo com o recurso. Para saber como excluir recursos que estão em sub-redes, leia a documentação de cada tipo de recurso que você deseja excluir.

1. Na caixa de pesquisa na parte superior do portal, insira *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecem nos resultados da pesquisa, selecione-as.
2. Na lista de redes virtuais, selecione a rede virtual que contém a sub-rede que você deseja excluir.
3. Em **DEFINIÇÕES**, selecione **sub-redes**.
4. Na lista de sub-redes, selecione **...** , à direita, para a sub-rede que você deseja excluir
5. Selecione **excluir**e, em seguida, selecione **Sim**.

**Comandos**

- CLI do Azure: [AZ Network vnet subnet Delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Permissões

Para executar tarefas em sub-redes, sua conta deve ser atribuída à função [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas na tabela a seguir:

|Ação                                                                   |   Nome                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Ler uma sub-rede de rede virtual              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Criar ou atualizar uma sub-rede de rede virtual  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Excluir uma sub-rede de rede virtual            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Ingressar em uma rede virtual                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Habilitar um ponto de extremidade de serviço para uma sub-rede     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Obter as máquinas virtuais em uma sub-rede       |

## <a name="next-steps"></a>Passos seguintes

- Criar uma rede virtual e sub-redes usando o [PowerShell](powershell-samples.md) ou [CLI do Azure](cli-samples.md) scripts de exemplo ou usando modelos do Azure [Resource Manager](template-samples.md)
- Criar e aplicar a [política do Azure](policy-samples.md) para redes virtuais
