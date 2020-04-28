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
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: b9ef084e530faa3bcea2cfcf1554a84f543bbf59
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186089"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Adicionar, alterar ou eliminar uma sub-rede de rede virtual

Aprenda a adicionar, alterar ou eliminar uma subnet de rede virtual. Todos os recursos do Azure implementados numa rede virtual são implementados numa sub-rede dentro dessa rede virtual. Se você é novo em redes virtuais, você pode aprender mais sobre eles na visão geral da [rede Virtual](virtual-networks-overview.md) ou completando um [quickstart](quick-create-portal.md). Para saber mais sobre a gestão de uma rede virtual, consulte [Criar, alterar ou eliminar uma rede virtual](manage-virtual-network.md).

## <a name="before-you-begin"></a>Antes de começar

Se não tiver uma, instale uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Em seguida, complete uma destas tarefas antes de iniciar etapas em qualquer secção deste artigo: 

- **Utilizadores**do portal : Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta Azure.

- **Utilizadores powerShell**: Ou executa os comandos na [Casca de Nuvem Azure,](https://shell.azure.com/powershell)ou executa powerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. No separador de navegador Azure Cloud Shell, encontre a lista de abandono ambiental **Select** e escolha **powerShell** se ainda não estiver selecionado.

    Se estiver a executar o PowerShell localmente, utilize a versão 1.0.0 do módulo PowerShell Do Mato Azure. Execute `Get-Module -ListAvailable Az.Network` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Também `Connect-AzAccount` corra para criar uma ligação com Azure.

- Utilizadores da **interface da linha de comando Azure (CLI):** Ou executa os comandos na Membrana Nuvem [Azure,](https://shell.azure.com/bash)ou executa o CLI a partir do seu computador. Utilize a versão Azure CLI 2.0.31 ou mais tarde se estiver a executar o Azure CLI localmente. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). Também `az login` corra para criar uma ligação com Azure.

A conta a que se inscreveu, ou se liga ao Azure, deve ser atribuída ao papel de [colaborador da Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenha atribuído as ações apropriadas listadas em [Permissões](#permissions).

## <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Vá ao [portal Azure](https://portal.azure.com) para ver as suas redes virtuais. Procure e selecione **redes Virtuais.**

2. Selecione o nome da rede virtual a que pretende adicionar uma sub-rede.

3. A partir de **Definições,** selecione **Subnet Sub-redes sub-redes** > **Subnet**.

4. Na caixa de diálogo **da sub-rede Adicionar,** introduza valores para as seguintes definições:

    | Definição | Descrição |
    | --- | --- |
    | **Nome** | O nome tem de ser exclusivo dentro da rede virtual. Para uma compatibilidade máxima com outros serviços Azure, recomendamos a utilização de uma letra como primeiro personagem do nome. Por exemplo, o Azure Application Gateway não se implanta numa sub-rede que tenha um nome que comece com um número. |
    | **Intervalo de endereços** | <p>A gama deve ser única dentro do espaço de endereço para a rede virtual. A gama não pode sobrepor-se a outras gamas de endereços de sub-rede dentro da rede virtual. O espaço de endereço deve ser especificado utilizando a notação de encaminhamento inter-domínio sem classe (CIDR).</p><p>Por exemplo, numa rede virtual com espaço de endereço *10.0.0.0.0/16,* pode definir um espaço de endereço de sub-rede de *10.0.0.0.0/22*. A gama mais pequena que pode especificar é */29,* que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e último endereço em cada subnet para a conformidade do protocolo. Três endereços adicionais estão reservados para a utilização do serviço Azure. Como resultado, a definição de uma sub-rede com uma gama de endereços */29* resulta em três endereços IP utilizáveis na sub-rede.</p><p>Se planeia ligar uma rede virtual a um gateway VPN, tem de criar uma sub-rede de gateway. Saiba mais sobre [considerações específicas de gama de endereços para subredes gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Pode alterar o intervalo de endereços após a adição da sub-rede, em condições específicas. Para aprender a alterar uma gama de endereços de sub-rede, consulte [as definições da sub-rede .](#change-subnet-settings)</p> |
    | **Grupo de segurança da rede** | Para filtrar o tráfego de rede de entrada e saída da subnet, pode associar um grupo de segurança de rede existente a uma subrede. O grupo de segurança da rede deve existir na mesma subscrição e localização que a rede virtual. Saiba mais sobre [grupos](security-overview.md) de segurança de rede e [como criar um grupo](tutorial-filter-network-traffic.md)de segurança de rede . |
    | **Tabela de rota** | Para controlar o encaminhamento de tráfego da rede para outras redes, pode associar opcionalmente uma tabela de rotas existente a uma subnet. A tabela de rotas deve existir na mesma subscrição e localização que a rede virtual. Saiba mais sobre [o encaminhamento do Azure](virtual-networks-udr-overview.md) e como criar uma tabela de [rotas.](tutorial-create-route-table-portal.md) |
    | **Pontos finais de serviço** | <p>Uma sub-rede pode opcionalmente ter um ou mais pontos finais de serviço habilitados para o mesmo. Para ativar um ponto final de serviço para um serviço, selecione o serviço ou serviços que pretende ativar pontos finais de serviço da lista de **Serviços.** O Azure configura automaticamente a localização para um ponto final. Por padrão, o Azure configura os pontos finais de serviço para a região da rede virtual. Para apoiar cenários regionais de failover, o Azure configura automaticamente os pontos finais para [as regiões emparelhadas](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) do Azure para o Armazenamento Azure.</p><p>Para remover um ponto final de serviço, desmarque o serviço para o que pretende remover o ponto final do serviço. Para saber mais sobre os pontos finais do serviço e sobre os serviços para os quais podem ser ativados, consulte [os pontos finais do serviço](virtual-network-service-endpoints-overview.md)de rede Virtual. Uma vez que você ativa um ponto final de serviço para um serviço, você também deve ativar o acesso da rede para a subnet para um recurso criado com o serviço. Por exemplo, se ativar o ponto final do serviço para **microsoft.Storage,** também deve ativar o acesso à rede a todas as contas do Armazenamento Azure a que pretende conceder acesso à rede. Para permitir o acesso à rede às subredes para as quais um ponto final de serviço está habilitado, consulte a documentação para o serviço individual para o qual ativou o ponto final do serviço.</p><p>Para validar que um ponto final de serviço está ativado para uma sub-rede, veja as [rotas eficazes](diagnose-network-routing-problem.md) para qualquer interface de rede na subrede. Quando configura um ponto final, vê uma rota *predefinida* com os prefixos de endereço do serviço e um próximo tipo de lúpulo de **VirtualNetworkServiceEndpoint**. Para saber mais sobre o encaminhamento, consulte o [encaminhamento](virtual-networks-udr-overview.md)de tráfego da rede virtual.</p> |
    | **Delegação de sub-rede** | Uma sub-rede pode facultariamente ter uma ou mais delegações habilitadas para a sua vida. A delegação da Subnet dá permissões explícitas ao serviço para criar recursos específicos do serviço na subrede utilizando um identificador único durante a implantação do serviço. Para delegar um serviço, selecione o serviço a que pretende delegar na lista **de Serviços.** |

5. Para adicionar a sub-rede à rede virtual que selecionou, selecione **OK**.

### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Alterar as definições de sub-rede

1. Vá ao [portal Azure](https://portal.azure.com) para ver as suas redes virtuais. Procure e selecione **redes Virtuais.**

2. Selecione o nome da rede virtual que contém a sub-rede que pretende alterar.

3. A partir de **Definições,** selecione **Subnets**.

4. Na lista de subredes, selecione a sub-rede para a que pretende alterar as definições.

5. Na página da sub-rede, altere qualquer uma das seguintes definições:

    | Definição | Descrição |
    | --- | --- |
    | **Intervalo de endereços** | Se não forem mobilizados recursos dentro da sub-rede, pode alterar o intervalo de endereços. Se existirem recursos na sub-rede, deve transferir os recursos para outra sub-rede ou eliminá-los primeiro da sub-rede. Os passos que toma para mover ou apagar um recurso variam consoante o recurso. Para aprender a mover ou apagar recursos que se encontram em subnets, leia a documentação para cada um desses tipos de recursos. Consulte as restrições para **a gama Endereço** no passo 4 de Adicionar uma [sub-rede](#add-a-subnet). |
    | **Utilizadores** | Pode controlar o acesso à subrede utilizando papéis incorporados ou as suas próprias funções personalizadas. Para saber mais sobre atribuir funções e utilizadores para aceder à subnet, consulte Adicionar uma atribuição de [funções](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment). |
    | **Grupo de segurança de rede** e **Tabela de rota** | Consulte o passo 4 de [Adicionar uma sub-rede](#add-a-subnet). |
    | **Pontos finais de serviço** | <p>Consulte os pontos finais do serviço no passo 4 de [Adicionar uma sub-rede](#add-a-subnet). Ao ativar um ponto final de serviço para uma sub-rede existente, certifique-se de que não estão a decorrer tarefas críticas em qualquer recurso da sub-rede. Os pontos finais do serviço mudam as rotas em todas as interfaces de rede da subnet. Os pontos finais do serviço passam da utilização da rota padrão com o prefixo de *endereço 0.0.0.0/0* e o próximo tipo de *Internet*hop, até utilizar uma nova rota com os prefixos de endereço do serviço e um próximo tipo de lúpulo de *VirtualNetworkServiceEndpoint*.</p><p>Durante o interruptor, quaisquer ligações TCP abertas podem ser terminadas. O ponto final do serviço não está ativado até que o tráfego flua para o serviço para todas as interfaces de rede sejam atualizados com a nova rota. Para saber mais sobre o encaminhamento, consulte o [encaminhamento](virtual-networks-udr-overview.md)de tráfego da rede virtual.</p> |
    | **Delegação de sub-rede** | Consulte os pontos finais do serviço no passo 4 de [Adicionar uma sub-rede](#add-a-subnet). A delegação da sub-rede pode ser modificada para zero ou várias delegações habilitadas para a sua utilização. Se um recurso para um serviço já estiver implantado na subnet, a delegação da sub-rede não pode ser adicionada ou removida até que todos os recursos para o serviço sejam removidos. Para delegar um serviço diferente, selecione o serviço a que pretende delegar na lista **de Serviços.** |

6. Selecione **Guardar**.

### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Eliminar uma sub-rede

Só é possível eliminar uma sub-rede se não houver recursos na sub-rede. Se os recursos estiverem na sub-rede, deve eliminar esses recursos antes de poder eliminar a sub-rede. Os passos que toma para eliminar um recurso variam consoante o recurso. Para aprender a apagar os recursos que estão em subredes, leia a documentação para cada um desses tipos de recursos.

1. Vá ao [portal Azure](https://portal.azure.com) para ver as suas redes virtuais. Procure e selecione **redes Virtuais.**

2. Selecione o nome da rede virtual que contém a sub-rede que pretende eliminar.

3. A partir de **Definições,** selecione **Subnets**.

4. Na lista de subredes, selecione a sub-rede que pretende eliminar.

5. **Selecione Eliminar**e, em seguida, selecione **Sim** na caixa de diálogo de confirmação.

### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede vnet subnet eliminar](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Remover-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Permissões

Para realizar tarefas em subredes, a sua conta deve ser atribuída ao papel de [colaborador da Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenha sido atribuída as ações apropriadas na tabela seguinte:

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
- Criar e atribuir definições de [política azure](policy-samples.md) para redes virtuais
