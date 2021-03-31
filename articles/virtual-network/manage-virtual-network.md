---
title: Criar, alterar ou eliminar uma rede virtual Azure
titlesuffix: Azure Virtual Network
description: Crie e elimine uma rede virtual e altere as definições, como servidores DNS e espaços de endereço IP, para uma rede virtual existente.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 1ec4e26e266a1777857fe0e890b093f60a3597f0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612529"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Criar, alterar ou eliminar uma rede virtual

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Saiba como criar e eliminar uma rede virtual e alterar definições, como servidores DNS e espaços de endereço IP, para uma rede virtual existente. Se é novo em redes virtuais, pode saber mais sobre elas na [visão geral](virtual-networks-overview.md) da rede Virtual ou completando um [tutorial](quick-create-portal.md). Uma rede virtual contém sub-redes. Para aprender a criar, alterar e eliminar sub-redes, consulte [Gerir sub-redes](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Antes de começar

Complete as seguintes tarefas antes de completar etapas em qualquer secção deste artigo:

- Se ainda não tem uma conta Azure, inscreva-se para uma [conta de teste gratuita.](https://azure.microsoft.com/free)
- Se utilizar o portal, abra https://portal.azure.com e faça login com a sua conta Azure.
- Se utilizar comandos PowerShell para completar tarefas neste artigo, ou executa os comandos na [Azure Cloud Shell](https://shell.azure.com/powershell), ou executando o PowerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer a versão 1.0 ou mais tarde do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar os comandos da interface da linha de comando do Azure (CLI) para completar as tarefas neste artigo, ou executar os comandos na [Azure Cloud Shell](https://shell.azure.com/bash), ou executando o CLI a partir do seu computador. Este tutorial requer a versão Azure CLI 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI localmente, também precisa de correr `az login` para criar uma ligação com o Azure.
- A conta em que inicia sessão, ou liga-se ao Azure, deve ser atribuída à [função de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída às ações [apropriadas listadas](#permissions)nas Permissões .

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Selecione **+ Criar uma** rede virtual  >  **de rede de**  >  recursos.
2. Introduza ou selecione valores para as seguintes definições e, em seguida, **selecione Criar**:
   - **Nome**: O nome deve ser único no [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) que seleciona para criar a rede virtual em. Não é possível alterar o nome após a criação da rede virtual. Pode criar várias redes virtuais ao longo do tempo. Para nomear sugestões, consulte [as convenções de nomeação.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources) Seguir uma convenção de nomeação pode ajudar a facilitar a gestão de várias redes virtuais.
   - **Espaço de endereço**: O espaço de endereço de uma rede virtual é composto por uma ou mais gamas de endereços não sobrepostas que são especificadas na notação CIDR. O intervalo de endereços que define pode ser público ou privado (RFC 1918). Quer se defina o intervalo de endereços como público ou privado, o intervalo de endereços só é acessível a partir da rede virtual, a partir de redes virtuais interligadas e de quaisquer redes no local que tenha ligado à rede virtual. Não é possível adicionar os seguintes intervalos de endereços:
     - 224.0.0.0/4 (Multicast)
     - 255.255.255.255/32 (Transmissão)
     - 127.0.0.0/8 (Loopback)
     - 169.254.0.0/16 (Link-local)
     - 168.63.129.16/32 (Sonda de [Saúde](../load-balancer/load-balancer-custom-probe-overview.md#probesource)DSIs, DHCP e Azure Load Balancer)

     Embora possa definir apenas uma gama de endereços quando criar a rede virtual no portal, pode adicionar mais intervalos de endereços ao espaço de endereço após a criação da rede virtual. Para aprender a adicionar um intervalo de endereços a uma rede virtual existente, consulte [Adicionar ou remover uma gama de endereços](#add-or-remove-an-address-range).

     > [!WARNING]
     > Se uma rede virtual tiver alcances de endereço que se sobreponham a outra rede virtual ou rede no local, as duas redes não podem ser ligadas. Antes de definir um intervalo de endereços, considere se pretende ligar a rede virtual a outras redes virtuais ou redes no local no futuro. A Microsoft recomenda configurar gamas de endereços de rede virtuais com espaço de endereço privado ou espaço de endereço público propriedade da sua organização.
     >

     - **Nome da sub-rede**: O nome da sub-rede deve ser único dentro da rede virtual. Não é possível alterar o nome da sub-rede após a criação da sub-rede. O portal requer que defina uma sub-rede quando cria uma rede virtual, mesmo que uma rede virtual não seja necessária para ter sub-redes. No portal, pode definir uma ou mais sub-redes quando criar uma rede virtual. Pode adicionar mais sub-redes à rede virtual mais tarde, após a criação da rede virtual. Para adicionar uma sub-rede a uma rede virtual, consulte [Gerir sub-redes](virtual-network-manage-subnet.md). Pode criar uma rede virtual que tenha várias sub-redes utilizando O Azure CLI ou PowerShell.

       >[!TIP]
       >Por vezes, os administradores criam sub-redes diferentes para filtrar ou controlar o encaminhamento de tráfego entre as sub-redes. Antes de definir sub-redes, considere como pretende filtrar e encaminhar o tráfego entre as suas sub-redes. Para saber mais sobre a filtragem do tráfego entre sub-redes, consulte [os grupos de segurança da Rede](./network-security-groups-overview.md). O Azure liga automaticamente o tráfego entre sub-redes, mas pode sobrepor-se às rotas predefinidas do Azure. Para saber mais sobre o encaminhamento de tráfego de sub-rede padrão da Azures, consulte [a visão geral do encaminhamento](virtual-networks-udr-overview.md).
       >

     - **Intervalo de endereços da sub-rede**: O intervalo deve estar dentro do espaço de endereço que introduziu para a rede virtual. A faixa mais pequena que pode especificar é /29, que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e último endereço em cada sub-rede para conformidade com o protocolo. Três endereços adicionais estão reservados para o uso do serviço Azure. Como resultado, uma rede virtual com uma gama de endereços de sub-rede de /29 tem apenas três endereços IP utilizáveis. Se planeia ligar uma rede virtual a uma porta de entrada VPN, tem de criar uma sub-rede de gateway. Saiba mais sobre [considerações específicas do intervalo de endereços para sub-redes gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Pode alterar o intervalo de endereços após a criação da sub-rede, em condições específicas. Para aprender a alterar uma gama de endereços de sub-rede, consulte [Gerir sub-redes](virtual-network-manage-subnet.md).
     - **Assinatura**: Selecione uma [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Não é possível utilizar a mesma rede virtual em mais de uma subscrição do Azure. No entanto, pode ligar uma rede virtual numa subscrição a redes virtuais noutras subscrições com [o espreitamento de rede virtual.](virtual-network-peering-overview.md) Qualquer recurso Azure que se conecte à rede virtual deve estar na mesma subscrição que a rede virtual.
     - **Grupo de recursos**: Selecione um grupo de [recursos](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) existente ou crie um novo. Um recurso Azure que se conecta à rede virtual pode estar no mesmo grupo de recursos que a rede virtual ou num grupo de recursos diferente.
     - **Localização**: Selecione uma [localização Azure,](https://azure.microsoft.com/regions/)também conhecida como região. Uma rede virtual pode estar em apenas uma localização Azure. No entanto, pode ligar uma rede virtual num local a uma rede virtual noutro local, utilizando um gateway VPN. Qualquer recurso Azure que se liga à rede virtual deve estar no mesmo local que a rede virtual.

**Comandos**

- Azure CLI: [az rede vnet criar](/cli/azure/network/vnet)
- PowerShell: [Rede New-AzVirtual](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Ver redes e configurações virtuais

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **as redes virtuais** aparecerem nos resultados da pesquisa, selecione-as.
2. Na lista de redes virtuais, selecione a rede virtual para a quais pretende visualizar as definições.
3. As seguintes definições estão listadas para a rede virtual selecionada:
   - **Visão geral**: Fornece informações sobre a rede virtual, incluindo o espaço de endereço e servidores DNS. A imagem que se segue mostra as definições de visão geral de uma rede virtual chamada **MyVNet**:

     ![Visão geral da interface da rede](./media/manage-virtual-network/vnet-overview.png)

     Pode mover uma rede virtual para um grupo de subscrição ou recursos diferente, selecionando **Change** ao lado **do grupo de Recursos** ou nome de **Subscrição.** Para aprender a mover uma rede virtual, consulte [os recursos movem-se para um grupo de recursos ou subscrição diferente.](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json) O artigo lista pré-requisitos e como mover recursos usando o portal Azure, PowerShell e Azure CLI. Todos os recursos ligados à rede virtual devem mover-se com a rede virtual.
   - **Espaço de endereço**: Os espaços de endereço atribuídos à rede virtual estão listados. Para aprender a adicionar e remover um intervalo de endereços para o espaço do endereço, complete os passos no [Add ou remova um intervalo de endereços](#add-or-remove-an-address-range).
   - **Dispositivos ligados**: Todos os recursos ligados à rede virtual estão listados. Na imagem anterior, três interfaces de rede e um balanceador de carga estão ligados à rede virtual. Todos os novos recursos que crie e conecte à rede virtual estão listados. Se eliminar um recurso que estava ligado à rede virtual, deixará de aparecer na lista.
   - **Sub-redes**: É mostrada uma lista de sub-redes existentes na rede virtual. Para aprender a adicionar e remover uma sub-rede, consulte [Gerir sub-redes](virtual-network-manage-subnet.md).
   - **Servidores DNS**: Pode especificar se o servidor DNS interno do Azure ou um servidor DNS personalizado fornece resolução de nome para dispositivos que estão ligados à rede virtual. Quando cria uma rede virtual utilizando o portal Azure, os servidores DNS do Azure são utilizados para a resolução de nomes dentro de uma rede virtual, por padrão. Para modificar os servidores DNS, preencha os passos nos [servidores DoRs](#change-dns-servers) change neste artigo.
   - **Os seus pares**: Se existirem espreitadores existentes na subscrição, estão listados aqui. Pode visualizar as definições para os pares existentes, ou criar, alterar ou eliminar os seus pares. Para saber mais sobre os seus olhos, consulte [a rede Virtual a espreitar.](virtual-network-peering-overview.md)
   - **Propriedades**: Exibe definições sobre a rede virtual, incluindo o ID de recursos da rede virtual e a subscrição Azure em que se encontra.
   - **Diagrama**: O diagrama proporciona uma representação visual de todos os dispositivos ligados à rede virtual. O diagrama tem algumas informações chave sobre os dispositivos. Para gerir um dispositivo nesta vista, no diagrama, selecione o dispositivo.
   - **Configurações do Azul Comum**: Para saber mais sobre as definições comuns do Azure, consulte as seguintes informações:
     - [Registo de atividades](../azure-monitor/essentials/platform-logs-overview.md)
     - [Controlo de acesso (IAM)](../role-based-access-control/overview.md)
     - [Etiquetas](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Bloqueios](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Script de automatização](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Comandos**

- Azure CLI: [show de vnet de rede az](/cli/azure/network/vnet)
- PowerShell: [Rede Get-AzVirtual](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Adicionar ou remover uma gama de endereços

Pode adicionar e remover intervalos de endereços para uma rede virtual. Um intervalo de endereços deve ser especificado na notação CIDR e não pode sobrepor-se a outros intervalos de endereços dentro da mesma rede virtual. Os intervalos de endereços que definir podem ser públicos ou privados (RFC 1918). Quer se defina o intervalo de endereços como público ou privado, o intervalo de endereços só é acessível a partir da rede virtual, a partir de redes virtuais interligadas e de quaisquer redes no local que tenha ligado à rede virtual. 

Pode diminuir o intervalo de endereços para uma rede virtual, desde que ainda inclua as gamas de quaisquer sub-redes associadas. Além disso, pode alargar o intervalo de endereços, por exemplo, alterando a /16 para /8. 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

Não é possível adicionar os seguintes intervalos de endereços:

- 224.0.0.0/4 (Multicast)
- 255.255.255.255/32 (Transmissão)
- 127.0.0.0/8 (Loopback)
- 169.254.0.0/16 (Link-local)
- 168.63.129.16/32 (Sonda de [Saúde](../load-balancer/load-balancer-custom-probe-overview.md#probesource)DSIs, DHCP e Azure Load Balancer)

Para adicionar ou remover uma gama de endereços:

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **as redes virtuais** aparecerem nos resultados da pesquisa, selecione-as.
2. A partir da lista de redes virtuais, selecione a rede virtual para a qual pretende adicionar ou remover um intervalo de endereços.
3. Selecione **o espaço de endereço**, em **DEFINIÇÕES**.
4. Complete uma das seguintes opções:
    - **Adicione uma gama de endereços**: Introduza o novo intervalo de endereços. O intervalo de endereços não pode sobrepor-se a um intervalo de endereços existente definido para a rede virtual.
    - **Remova um intervalo** de endereços : À direita do intervalo de endereços que pretende remover, selecione... **e,** em seguida, selecione **Remover**. Se existir uma sub-rede no intervalo de endereços, não é possível remover o intervalo de endereços. Para remover um intervalo de endereços, deve primeiro eliminar quaisquer sub-redes (e quaisquer recursos nas sub-redes) que existam no intervalo de endereços.
5. Selecione **Guardar**.

**Comandos**

- Azure CLI: [atualização vnet da rede az](/cli/azure/network/vnet)
- PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>Alterar servidores DNS

Todos os VMs que estão ligados ao registo de rede virtual com os servidores DNS que especifica para a rede virtual. Também utilizam o servidor DNS especificado para resolução de nomes. Cada interface de rede (NIC) num VM pode ter as suas próprias definições de servidor DNS. Se um NIC tiver as suas próprias definições de servidor DNS, eles substituem as definições do servidor DNS para a rede virtual. Para saber mais sobre as definições de DNS do NIC, consulte [as tarefas e definições da interface da Rede](virtual-network-network-interface.md#change-dns-servers). Para saber mais sobre a resolução de nomes para VMs e instâncias de papel em Azure Cloud Services, consulte [a resolução de nomes para VMs e instâncias de função](virtual-networks-name-resolution-for-vms-and-role-instances.md). Para adicionar, alterar ou remover um servidor DNS:

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **as redes virtuais** aparecerem nos resultados da pesquisa, selecione-as.
2. A partir da lista de redes virtuais, selecione a rede virtual para a qual pretende alterar servidores DNS.
3. Selecione **servidores DNS**, em **DEFINIÇÕES**.
4. Selecione uma das seguintes opções:
   - **Predefinido (fornecido pelo Azure)**: Todos os nomes de recursos e endereços IP privados são automaticamente registados nos servidores Azure DNS. Pode resolver nomes entre quaisquer recursos que estejam ligados à mesma rede virtual. Não é possível utilizar esta opção para resolver nomes em redes virtuais. Para resolver nomes através de redes virtuais, tem de utilizar um servidor DNS personalizado.
   - **Personalizado**: Pode adicionar um ou mais servidores, até ao limite de Azure para uma rede virtual. Para saber mais sobre os limites do servidor DNS, consulte [os limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Tem as seguintes opções:
   - **Adicione um endereço**: Adiciona o servidor à sua lista de servidores DNS de rede virtual. Esta opção também regista o servidor DNS com Azure. Se já registou um servidor DNS com o Azure, pode selecionar o servidor DNS da lista.
   - **Remover um endereço**: Ao lado do servidor que pretende remover, selecione... **e,** em seguida, **remova**. A eliminação do servidor remove o servidor apenas a partir desta lista de rede virtual. O servidor DNS permanece registado no Azure para que as suas outras redes virtuais utilizem.
   - **Reencomenda os endereços do servidor DNS**: É importante verificar se lista os servidores DNS na ordem correta para o seu ambiente. As listas de servidores DNS são utilizadas na ordem em que são especificadas. Não funcionam como uma armadilha. Se o primeiro servidor DNS da lista puder ser alcançado, o cliente utiliza esse servidor DNS, independentemente de o servidor DNS estar a funcionar corretamente. Remova todos os servidores DNS que estão listados e, em seguida, adicione-os de volta na ordem que deseja.
   - **Alterar um endereço**: Realce o servidor DNS na lista e, em seguida, introduza o novo endereço.
5. Selecione **Guardar**.
6. Reinicie os VMs que estão ligados à rede virtual, para que lhes sejam atribuídas as novas definições do servidor DNS. Os VMs continuam a utilizar as definições de DNS atuais até serem reiniciadas.

**Comandos**

- Azure CLI: [atualização vnet da rede az](/cli/azure/network/vnet)
- PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Eliminar uma rede virtual

Só pode excluir uma rede virtual se não houver recursos ligados à rede. Se existirem recursos ligados a qualquer sub-rede dentro da rede virtual, deve primeiro eliminar os recursos que estão ligados a todas as sub-redes dentro da rede virtual. Os passos que toma para eliminar um recurso variam consoante o recurso. Para aprender a eliminar recursos ligados a sub-redes, leia a documentação para cada tipo de recurso que pretende eliminar. Para eliminar uma rede virtual:

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **as redes virtuais** aparecerem nos resultados da pesquisa, selecione-as.
2. Na lista de redes virtuais, selecione a rede virtual que pretende eliminar.
3. Confirme que não existem dispositivos ligados à rede virtual selecionando **dispositivos Conectados**, em **DEFINIÇÕES**. Se existirem dispositivos ligados, deve eliminá-los antes de poder eliminar a rede virtual. Se não houver dispositivos ligados, selecione **Overview**.
4. Selecione **Eliminar**.
5. Para confirmar a eliminação da rede virtual, selecione **Sim**.

**Comandos**

- Azure CLI: [vnet de rede azul delete](/cli/azure/network/vnet)
- PowerShell: [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Permissões

Para executar tarefas em redes virtuais, a sua conta deve ser atribuída à [função de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída às ações apropriadas listadas no quadro seguinte:

| Ação                                  |   Name                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   Ler uma rede virtual              |
|Microsoft.Network/virtualNetworks/write  |   Criar ou atualizar uma rede virtual  |
|Microsoft.Network/virtualNetworks/delete |   Eliminar uma rede virtual            |

## <a name="next-steps"></a>Passos seguintes

- Crie uma rede virtual utilizando scripts de [amostras powerShell](powershell-samples.md) ou [Azure CLI,](cli-samples.md) ou usando [modelos de Gestor de Recursos](template-samples.md) Azure
- Criar e atribuir [definições de Política Azure](./policy-reference.md) para redes virtuais
