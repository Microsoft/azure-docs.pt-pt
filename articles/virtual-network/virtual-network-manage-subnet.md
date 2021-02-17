---
title: Adicionar, alterar ou eliminar uma sub-rede virtual Azure
titlesuffix: Azure Virtual Network
description: Saiba onde encontrar informações sobre redes virtuais e como adicionar, alterar ou eliminar uma sub-rede de rede virtual em Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: d591194f09e85c3e34ed4b904249df994a14bff5
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558589"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Adicionar, alterar ou eliminar uma sub-rede de rede virtual

Saiba como adicionar, alterar ou eliminar uma sub-rede de rede virtual. Todos os recursos do Azure implementados numa rede virtual são implementados numa sub-rede dentro dessa rede virtual. Se é novo em redes virtuais, pode saber mais sobre elas na [visão geral](virtual-networks-overview.md) da rede Virtual ou completando um [quickstart](quick-create-portal.md). Para saber mais sobre a gestão de uma rede virtual, consulte [Criar, alterar ou eliminar uma rede virtual.](manage-virtual-network.md)

## <a name="before-you-begin"></a>Antes de começar

Se não tiver uma, crie uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Em seguida, complete uma destas tarefas antes de iniciar etapas em qualquer secção deste artigo: 

- **Utilizadores do Portal**: Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta Azure.

- **Utilizadores powerShell**: Ou executam os comandos na [Azure Cloud Shell,](https://shell.azure.com/powershell)ou executam o PowerShell a partir do computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. No separador de navegador Azure Cloud Shell, encontre a lista de dropdown **de ambiente Select** e, em seguida, escolha **PowerShell** se ainda não estiver selecionado.

    Se estiver a executar o PowerShell localmente, utilize a versão 1.0.0 ou mais tarde do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az.Network` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Também corra `Connect-AzAccount` para criar uma ligação com Azure.

- **Os utilizadores da interface de linha de comando azure (CLI):** Ou executam os comandos na [Azure Cloud Shell,](https://shell.azure.com/bash)ou executam o CLI a partir do computador. Utilize a versão 2.0.31 do Azure CLI ou mais tarde se estiver a executar o Azure CLI localmente. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). Também corra `az login` para criar uma ligação com Azure.

A conta a que insinou ou se liga ao Azure deve ser atribuída à [função de colaborador da Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenha atribuído as ações [apropriadas listadas](#permissions)nas Permissões .

## <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Vá ao [portal Azure](https://portal.azure.com) para ver as suas redes virtuais. Procure e selecione **redes Virtuais.**

2. Selecione o nome da rede virtual a que pretende adicionar uma sub-rede.

3. A partir **de Definições**, selecione **sub-redes**  >  **sub-redes**.

4. Na caixa de diálogo **de sub-rede Adicionar,** introduza os valores para as seguintes definições:

    | Definições | Descrição |
    | --- | --- |
    | **Nome** | O nome tem de ser exclusivo dentro da rede virtual. Para uma compatibilidade máxima com outros serviços Azure, recomendamos a utilização de uma letra como o primeiro personagem do nome. Por exemplo, o Azure Application Gateway não implantará numa sub-rede que tenha um nome que comece com um número. |
    | **Intervalo de endereços** | <p>O alcance deve ser único dentro do espaço de endereço para a rede virtual. O alcance não pode sobrepor-se a outros intervalos de endereços da sub-rede dentro da rede virtual. O espaço do endereço deve ser especificado utilizando a notação de encaminhamento de Inter-Domain (CIDR) sem classe.</p><p>Por exemplo, numa rede virtual com o espaço de endereço *10.0.0.0/16,* poderá definir um espaço de endereço de sub-rede de *10.0.0.0/22*. A faixa mais pequena que pode especificar é */29,* que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e último endereço em cada sub-rede para conformidade com o protocolo. Três endereços adicionais estão reservados para o uso do serviço Azure. Como resultado, a definição de uma sub-rede com uma gama de endereços */29* resulta em três endereços IP utilizáveis na sub-rede.</p><p>Se planeia ligar uma rede virtual a uma porta de entrada VPN, tem de criar uma sub-rede de gateway. Saiba mais sobre [considerações específicas do intervalo de endereços para sub-redes gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Pode alterar o intervalo de endereços após a adição da sub-rede, em condições específicas. Para aprender a alterar uma gama de endereços de sub-rede, consulte [as definições da sub-rede](#change-subnet-settings)change .</p> |
    | **Grupo de segurança de rede** | Para filtrar o tráfego de rede de entrada e saída para a sub-rede, pode associar um grupo de segurança de rede existente a uma sub-rede. O grupo de segurança da rede deve existir na mesma subscrição e localização que a rede virtual. Saiba mais sobre [grupos de segurança de rede](./network-security-groups-overview.md) e como criar um grupo de segurança de [rede.](tutorial-filter-network-traffic.md) |
    | **Tabela de rotas** | Para controlar o encaminhamento de tráfego de rede para outras redes, pode associar opcionalmente uma tabela de rota existente a uma sub-rede. A tabela de rotas deve existir na mesma subscrição e localização que a rede virtual. Saiba mais sobre [o encaminhamento Azure](virtual-networks-udr-overview.md) e [como criar uma tabela de rotas.](tutorial-create-route-table-portal.md) |
    | **Pontos finais de serviço** | <p>Uma sub-rede pode opcionalmente ter um ou mais pontos finais de serviço ativados para o mesmo. Para ativar um ponto de terminação de serviço para um serviço, selecione o serviço ou serviços que pretende ativar pontos finais de serviço da lista **de Serviços.** O Azure configura a localização automaticamente para um ponto final. Por padrão, o Azure configura os pontos finais de serviço para a região da rede virtual. Para apoiar cenários regionais de failover, o Azure configura automaticamente pontos finais para [as regiões emparelhadas de Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) para o Armazenamento Azure.</p><p>Para remover um ponto final de serviço, desescolh o serviço para o quais pretende remover o ponto de terminação de serviço. Para saber mais sobre os pontos finais de serviço e os serviços para os quais podem ser ativados, consulte [os pontos finais do serviço de rede Virtual](virtual-network-service-endpoints-overview.md). Uma vez que ative um ponto final de serviço para um serviço, também deve permitir o acesso à rede para a sub-rede para um recurso criado com o serviço. Por exemplo, se ativar o ponto final do serviço para **o Microsoft.Storage,** também tem de permitir o acesso à rede a todas as contas do Azure Storage a que pretende conceder acesso à rede. Para permitir o acesso à rede às sub-redes para as quais está ativado um ponto final de serviço, consulte a documentação para o serviço individual para o qual ativou o ponto final do serviço.</p><p>Para validar que um ponto final de serviço está ativado para uma sub-rede, consulte as [rotas eficazes](diagnose-network-routing-problem.md) para qualquer interface de rede na sub-rede. Ao configurar um ponto final, vê uma rota *predefinida* com os prefixos de endereço do serviço e um próximo tipo de lúpulo de **VirtualNetworkServiceEndpoint**. Para saber mais sobre o encaminhamento, consulte [o encaminhamento de tráfego de rede virtual](virtual-networks-udr-overview.md).</p> |
    | **Delegação de sub-rede** | Uma sub-rede pode, opcionalmente, ter uma ou mais delegações habilitados para o mesmo. A delegação da Subnet dá permissões explícitas ao serviço para criar recursos específicos de serviço na sub-rede utilizando um identificador único durante a implementação do serviço. Para delegar um serviço, selecione o serviço a que pretende delegar na lista **de Serviços.** |

5. Para adicionar a sub-rede à rede virtual que selecionou, selecione **OK**.

### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Alterar definições de sub-redes

1. Vá ao [portal Azure](https://portal.azure.com) para ver as suas redes virtuais. Procure e selecione **redes Virtuais.**

2. Selecione o nome da rede virtual que contém a sub-rede que pretende alterar.

3. A partir **de Definições**, selecione **Subnetas**.

4. Na lista de sub-redes, selecione a sub-rede para a quais pretende alterar as definições.

5. Na página da sub-rede, altere qualquer uma das seguintes definições:

    | Definições | Descrição |
    | --- | --- |
    | **Intervalo de endereços** | Se não forem utilizados recursos dentro da sub-rede, pode alterar o intervalo de endereços. Se existirem recursos na sub-rede, deve mover os recursos para outra sub-rede ou eliminá-los primeiro da sub-rede. Os passos que toma para mover ou eliminar um recurso variam consoante o recurso. Para aprender a mover ou apagar recursos que estão em sub-redes, leia a documentação para cada um desses tipos de recursos. Consulte os constrangimentos para **o intervalo de endereços** no passo 4 de [Adicionar uma sub-rede](#add-a-subnet). |
    | **Utilizadores** | Pode controlar o acesso à sub-rede utilizando funções incorporadas ou as suas próprias funções personalizadas. Para saber mais sobre a atribuição de funções e utilizadores para aceder à sub-rede, consulte [as funções De Atribuição Azure](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). |
    | **Grupo de segurança de rede** e **Tabela de rota** | Ver passo 4 de [Adicionar uma sub-rede](#add-a-subnet). |
    | **Pontos finais de serviço** | <p>Consulte os pontos finais de serviço no passo 4 de [Adicionar uma sub-rede](#add-a-subnet). Ao ativar um ponto final de serviço para uma sub-rede existente, certifique-se de que não existem tarefas críticas em qualquer recurso da sub-rede. Os pontos finais de serviço trocam as rotas em todas as interfaces de rede da sub-rede. Os pontos finais de serviço passam de utilizar a rota predefinitiva com o prefixo de endereço *0.0.0.0/0* e o próximo tipo de *internet* hop, para utilizar uma nova rota com os prefixos de endereço do serviço e um próximo tipo de lúpulo de *VirtualNetworkServiceEndpoint*.</p><p>Durante o comutador, podem ser encerradas quaisquer ligações TCP abertas. O ponto final do serviço não é ativado até que os fluxos de tráfego para o serviço para todas as interfaces de rede sejam atualizados com a nova rota. Para saber mais sobre o encaminhamento, consulte [o encaminhamento de tráfego de rede virtual](virtual-networks-udr-overview.md).</p> |
    | **Delegação de sub-rede** | Consulte os pontos finais de serviço no passo 4 de [Adicionar uma sub-rede](#add-a-subnet). A delegação da sub-rede pode ser modificada para zero ou múltiplas delegações habilitadas para o mesmo. Se um recurso para um serviço já estiver implantado na sub-rede, a delegação da sub-rede não pode ser adicionada ou removida até que todos os recursos para o serviço sejam removidos. Para delegar um serviço diferente, selecione o serviço a que pretende delegar na lista **de Serviços.** |

6. Selecione **Guardar**.

### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Eliminar uma sub-rede

Só pode eliminar uma sub-rede se não houver recursos na sub-rede. Se os recursos estiverem na sub-rede, deve eliminar esses recursos antes de poder eliminar a sub-rede. Os passos que toma para eliminar um recurso variam consoante o recurso. Para aprender a eliminar recursos que estão em sub-redes, leia a documentação para cada um desses tipos de recursos.

1. Vá ao [portal Azure](https://portal.azure.com) para ver as suas redes virtuais. Procure e selecione **redes Virtuais.**

2. Selecione o nome da rede virtual que contém a sub-rede que pretende eliminar.

3. A partir **de Definições**, selecione **Subnetas**.

4. Na lista de sub-redes, selecione a sub-rede que pretende eliminar.

5. Selecione **Eliminar** e, em seguida, selecione **Sim** na caixa de diálogo de confirmação.

### <a name="commands"></a>Comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [sub-rede vnet de rede az eliminar](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Permissões

Para realizar tarefas em sub-redes, a sua conta deve ser atribuída à [função de contribuinte da Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenha atribuído as ações apropriadas no quadro seguinte:

|Ação                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Leia uma sub-rede de rede virtual              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Criar ou atualizar uma sub-rede de rede virtual  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Eliminar uma sub-rede de rede virtual            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Junte-se a uma rede virtual                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Ativar um ponto final de serviço para uma sub-rede     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Obtenha as máquinas virtuais numa sub-rede       |

## <a name="next-steps"></a>Passos seguintes

- Crie uma rede virtual e sub-redes utilizando scripts de [amostras PowerShell](powershell-samples.md) ou [Azure CLI,](cli-samples.md) ou utilizando [modelos de Gestor de Recursos](template-samples.md) Azure
- Criar e atribuir [definições de Política Azure](./policy-reference.md) para redes virtuais