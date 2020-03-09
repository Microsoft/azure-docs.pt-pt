---
title: Criar, alterar ou eliminar uma rede virtual Azure
titlesuffix: Azure Virtual Network
description: Aprenda a criar, alterar ou eliminar uma rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 70523dc12f3f20362fcf4a2c3cb456a182038e8a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356464"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Criar, alterar ou eliminar uma rede virtual

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aprenda a criar e eliminar uma rede virtual e alterar definições, como servidores DNS e espaços de endereçoIP, para uma rede virtual existente. Se você é novo em redes virtuais, você pode aprender mais sobre eles na visão geral da [rede Virtual](virtual-networks-overview.md) ou completando um [tutorial](quick-create-portal.md). Uma rede virtual contém subredes. Para aprender a criar, alterar e eliminar subredes, ver [Gerir subredes](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Antes de começar

Complete as seguintes tarefas antes de completar os passos em qualquer secção deste artigo:

- Se ainda não tem uma conta Azure, inscreva-se numa [conta de teste gratuita.](https://azure.microsoft.com/free)
- Se utilizar o portal, abra https://portal.azure.come faça login na sua conta Azure.
- Se utilizar os comandos PowerShell para completar tarefas neste artigo, execute os comandos na Casca de [Nuvem Azure,](https://shell.azure.com/powershell)ou executando powerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer a versão 1.0.0 ou posterior do módulo PowerShell Azure. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comando Azure (CLI) para completar tarefas neste artigo, execute os comandos na [Casca de Nuvem Azure,](https://shell.azure.com/bash)ou executando o CLI a partir do seu computador. Este tutorial requer a versão Azure CLI 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI localmente, também precisa de correr `az login` para criar uma ligação com o Azure.
- A conta em que inicia sessão, ou liga-se ao Azure, deve ser atribuída à função de contribuinte da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que lhe seja atribuída as ações adequadas enumeradas nas [Permissões](#permissions).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Selecione **+ Criar um recurso** > **rede** > **rede virtual.**
2. Introduza ou selecione valores para as seguintes definições e, em seguida, selecione **Criar:**
   - **Nome**: O nome deve ser único no grupo de [recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) que seleciona para criar a rede virtual. Não é possível alterar o nome depois da rede virtual ser criada. Pode criar várias redes virtuais ao longo do tempo. Para nomear sugestões, consulte [convenções de nomeação.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources) Seguir uma convenção de nomeação pode ajudar a facilitar a gestão de várias redes virtuais.
   - **Espaço de endereço**: O espaço de endereço para uma rede virtual é composto por uma ou mais gamas de endereços não sobrepostas que são especificadas na notação CIDR. A gama de endereços que define pode ser pública ou privada (RFC 1918). Quer defina a gama de endereços como pública ou privada, a gama de endereços só é acessível a partir da rede virtual, a partir de redes virtuais interligadas e de quaisquer redes no local que tenha ligado à rede virtual. Não é possível adicionar as seguintes gamas de endereços:
     - 224.0.0.0.0/4 (Multicast)
     - 255.255.255.255/32 (Transmissão)
     - 127.0.0.0.0/8 (Loopback)
     - 169.254.0.0.0/16 (Link-local)
     - 168.63.129.16/32 (DNS interno, DHCP e sonda de [saúde](../load-balancer/load-balancer-custom-probe-overview.md#probesource)Azure Load Balancer)

     Embora possa definir apenas uma gama de endereços quando criar a rede virtual no portal, pode adicionar mais gamas de endereços ao espaço de endereçoapós a criação da rede virtual. Para aprender a adicionar uma gama de endereços a uma rede virtual existente, consulte Adicionar ou remover uma gama de [endereços](#add-or-remove-an-address-range).

     >[!WARNING]
     >Se uma rede virtual tiver gamas de endereços que se sobreponham a outra rede virtual ou rede no local, as duas redes não podem ser ligadas. Antes de definir uma gama de endereços, considere se poderá querer ligar a rede virtual a outras redes virtuais ou redes no local no futuro.
     >
     >

     - **Nome da sub-rede**: O nome da sub-rede deve ser único dentro da rede virtual. Não é possível alterar o nome da sub-rede depois da criação da sub-rede. O portal requer que se defina uma subneta quando cria uma rede virtual, mesmo que uma rede virtual não seja necessária para ter quaisquer subredes. No portal, pode definir apenas uma subnet quando cria uma rede virtual. Pode adicionar mais subredes à rede virtual mais tarde, após a criação da rede virtual. Para adicionar uma subliga a uma rede virtual, consulte [Gerir subredes](virtual-network-manage-subnet.md). Pode criar uma rede virtual que tem várias subredes utilizando o Azure CLI ou o PowerShell.

       >[!TIP]
       >Por vezes, os administradores criam diferentes subredes para filtrar ou controlar o encaminhamento de tráfego entre as subredes. Antes de definir as subredes, considere como pode querer filtrar e encaminhar o tráfego entre as suas subredes. Para saber mais sobre filtrar o tráfego entre subredes, consulte [os grupos de segurança da Rede](security-overview.md). O Azure encaminha automaticamente o tráfego entre as subredes, mas pode substituir as rotas padrão do Azure. Para saber mais sobre o encaminhamento de tráfego de sub-rede de Azures, consulte a visão geral do [Routing](virtual-networks-udr-overview.md).
       >

     - Intervalo de **endereços subnet**: O intervalo deve estar dentro do espaço de endereço introduzido para a rede virtual. A gama mais pequena que pode especificar é /29, que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e último endereço em cada subnet para a conformidade do protocolo. Três endereços adicionais estão reservados para a utilização do serviço Azure. Como resultado, uma rede virtual com uma gama de endereços de sub-rede de /29 tem apenas três endereços IP utilizáveis. Se planeia ligar uma rede virtual a um gateway VPN, tem de criar uma sub-rede de gateway. Saiba mais sobre [considerações específicas de gama de endereços para subredes gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Pode alterar o intervalo de endereços após a criação da sub-rede, em condições específicas. Para aprender a alterar uma gama de endereços de sub-rede, consulte [Gerir subredes](virtual-network-manage-subnet.md).
     - **Subscrição**: Selecione uma [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Não é possível utilizar a mesma rede virtual em mais de uma subscrição Azure. No entanto, pode ligar uma rede virtual numa subscrição a redes virtuais noutras subscrições com [o peering de rede virtual.](virtual-network-peering-overview.md) Qualquer recurso Azure que ligue à rede virtual deve estar na mesma subscrição que a rede virtual.
     - **Grupo de recursos**: Selecione um grupo de [recursos](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) existente ou crie um novo. Um recurso Azure que se conecta à rede virtual pode estar no mesmo grupo de recursos que a rede virtual ou num grupo de recursos diferente.
     - **Localização**: Selecione uma [localização](https://azure.microsoft.com/regions/)Azure, também conhecida como região. Uma rede virtual pode estar em apenas um local Azure. No entanto, pode ligar uma rede virtual num local a uma rede virtual noutro local, utilizando um gateway VPN. Qualquer recurso Azure que ligue à rede virtual deve estar no mesmo local que a rede virtual.

**Comandos**

- Azure CLI: [az rede vnet criar](/cli/azure/network/vnet)
- PowerShell: [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Ver redes e configurações virtuais

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando as **redes Virtuais** aparecerem nos resultados da pesquisa, selecione-as.
2. A partir da lista de redes virtuais, selecione a rede virtual para a sua escolha.
3. As seguintes definições estão listadas para a rede virtual selecionada:
   - **Visão geral**: Fornece informações sobre a rede virtual, incluindo espaço de endereço e servidores DNS. A imagem que se segue mostra as definições de visão geral para uma rede virtual chamada **MyVNet:**

     ![Visão geral da interface da rede](./media/manage-virtual-network/vnet-overview.png)

     Pode mover uma rede virtual para um grupo de subscrição ou recursos diferente, selecionando **Alterar** ao lado do **grupo de Recursos** ou nome de **subscrição**. Para aprender a mover uma rede virtual, consulte [move recursos para um grupo de recursos diferenteou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json). O artigo lista pré-requisitos e como movimentar recursos utilizando o portal Azure, PowerShell e Azure CLI. Todos os recursos ligados à rede virtual devem mover-se com a rede virtual.
   - **Espaço de endereços**: Os espaços de endereço saem à rede virtual. Para aprender a adicionar e remover um intervalo de endereço sintetizador para o espaço de endereço, complete os passos em Adicionar ou remova uma gama de [endereços](#add-or-remove-an-address-range).
   - **Dispositivos conectados**: Todos os recursos ligados à rede virtual estão listados. Na imagem anterior, três interfaces de rede e um equilibrador de carga estão ligados à rede virtual. Estão listados novos recursos que crie e ligue à rede virtual. Se eliminar um recurso ligado à rede virtual, já não aparece na lista.
   - **Sub-redes**: É apresentada uma lista de subredes existentes dentro da rede virtual. Para aprender a adicionar e remover uma sub-rede, consulte [Gerir subredes](virtual-network-manage-subnet.md).
   - **Servidores DNS**: Pode especificar se o servidor DNS interno do Azure ou um servidor DNS personalizado fornece resolução de nome para dispositivos ligados à rede virtual. Quando cria uma rede virtual utilizando o portal Azure, os servidores DNS do Azure são utilizados para resolução de nomes dentro de uma rede virtual, por padrão. Para modificar os servidores DNS, complete os passos nos [servidores DNS da Alteração](#change-dns-servers) neste artigo.
   - **Peerings**: Se houver pares existentes na subscrição, eles estão listados aqui. Pode visualizar as definições para os pares existentes, ou criar, alterar ou eliminar os pares. Para saber mais sobre os pares, consulte o [peering da rede Virtual.](virtual-network-peering-overview.md)
   - **Propriedades**: Exibe definições sobre a rede virtual, incluindo o ID de recursos da rede virtual e a subscrição Azure em que se encontra.
   - **Diagrama**: O diagrama proporciona uma representação visual de todos os dispositivos que estão ligados à rede virtual. O diagrama tem algumas informações chave sobre os dispositivos. Para gerir um dispositivo nesta vista, no diagrama, selecione o dispositivo.
   - **Definições comuns**do Azure : Para saber mais sobre as definições comuns do Azure, consulte as seguintes informações:
     - [Registo de atividades](../azure-monitor/platform/platform-logs-overview.md)
     - [Controlo de acesso (IAM)](../role-based-access-control/overview.md)
     - [Etiquetas](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Fechaduras](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Roteiro de automação](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Comandos**

- Azure CLI: [az network vnet show](/cli/azure/network/vnet)
- PowerShell: [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Adicionar ou remover uma gama de endereços

Pode adicionar e remover os intervalos de endereços para uma rede virtual. Uma gama de endereços deve ser especificada na notação CIDR e não pode sobrepor-se a outras gamas de endereços dentro da mesma rede virtual. As gamas de endereços que define podem ser públicas ou privadas (RFC 1918). Quer defina a gama de endereços como pública ou privada, a gama de endereços só é acessível a partir da rede virtual, a partir de redes virtuais interligadas e de quaisquer redes no local que tenha ligado à rede virtual. 

Pode diminuir a gama de endereços para uma rede virtual, desde que inclua ainda as gamas de quaisquer subredes associadas. Além disso, pode alargar o intervalo de endereços, por exemplo, alterando uma /16 para /8. 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

Não é possível adicionar as seguintes gamas de endereços:

- 224.0.0.0.0/4 (Multicast)
- 255.255.255.255/32 (Transmissão)
- 127.0.0.0.0/8 (Loopback)
- 169.254.0.0.0/16 (Link-local)
- 168.63.129.16/32 (DNS interno, DHCP e sonda de [saúde](../load-balancer/load-balancer-custom-probe-overview.md#probesource)Azure Load Balancer)

Para adicionar ou remover uma gama de endereços:

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando as **redes Virtuais** aparecerem nos resultados da pesquisa, selecione-as.
2. A partir da lista de redes virtuais, selecione a rede virtual para a qual pretende adicionar ou remover um intervalo de endereços.
3. Selecione **espaço de endereço,** em **DEFINIÇÕES**.
4. Complete uma das seguintes opções:
    - **Adicione uma gama de endereços**: Introduza a nova gama de endereços. A gama de endereços não pode sobrepor-se a uma gama de endereços existente que é definida para a rede virtual.
    - **Remova uma gama**de endereços : À direita do intervalo de endereços que pretende remover, selecione **...** e, em seguida, selecione **Remover**. Se existir uma sub-rede no intervalo de endereços, não é possível remover o intervalo de endereços. Para remover uma gama de endereços, deve primeiro eliminar quaisquer subredes (e quaisquer recursos nas subredes) que existam na gama de endereços.
5. Selecione **Guardar**.

**Comandos**

- Azure CLI: [az network vnet update](/cli/azure/network/vnet)
- PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>Alterar servidores DNS

Todos os VMs que estão ligados ao registo de rede virtual com os servidores DNS que especifica para a rede virtual. Também utilizam o servidor DNS especificado para resolução de nomes. Cada interface de rede (NIC) num VM pode ter as suas próprias definições de servidor DNS. Se um NIC tiver as suas próprias definições de servidor DNS, sobrepõe-se às definições do servidor DNS para a rede virtual. Para saber mais sobre as definições de NIC DNS, consulte [as tarefas e configurações](virtual-network-network-interface.md#change-dns-servers)da interface da rede . Para saber mais sobre a resolução de nomes para VMs e exemplos de papéis nos Serviços De Nuvem Azure, consulte a [resolução de nomes para VMs e exemplos](virtual-networks-name-resolution-for-vms-and-role-instances.md)de papéis . Para adicionar, alterar ou remover um servidor DNS:

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando as **redes Virtuais** aparecerem nos resultados da pesquisa, selecione-as.
2. A partir da lista de redes virtuais, selecione a rede virtual para a qual pretende alterar os servidores DNS.
3. Selecione **servidores DNS**, em **DEFINIÇÕES**.
4. Selecione uma das seguintes opções:
   - **Predefinição (fornecido pelo Azure)** : Todos os nomes de recursos e endereços IP privados estão automaticamente registados nos servidores DNS do Azure. Pode resolver nomes entre quaisquer recursos ligados à mesma rede virtual. Não é possível utilizar esta opção para resolver nomes através de redes virtuais. Para resolver nomes em redes virtuais, deve utilizar um servidor DNS personalizado.
   - **Personalizado:** Pode adicionar um ou mais servidores, até ao limite Azure para uma rede virtual. Para saber mais sobre os limites do servidor DNS, consulte [os limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Existem as seguintes opções:
   - **Adicione um endereço**: Adiciona o servidor à lista de servidores DNS da rede virtual. Esta opção também regista o servidor DNS com o Azure. Se já registou um servidor DNS com o Azure, pode selecionar o servidor DNS na lista.
   - **Remova um endereço**: Junto ao servidor que pretende remover, selecione **...** , em **seguida, remova**. A eliminação do servidor remove o servidor apenas desta lista de rede virtual. O servidor DNS permanece registado em Azure para as suas outras redes virtuais a utilizar.
   - **Reencomende os endereços do servidor DNS**: É importante verificar se lista os seus servidores DNS na ordem correta para o seu ambiente. As listas de servidores DNS são utilizadas na ordem que são especificadas. Não funcionam como uma configuração de rodapé. Se o primeiro servidor DNS da lista puder ser alcançado, o cliente utiliza esse servidor DNS, independentemente de o servidor DNS estar a funcionar corretamente. Remova todos os servidores DNS listados e, em seguida, adicione-os de volta na ordem que deseja.
   - **Alterar um endereço**: Realce o servidor DNS na lista e, em seguida, introduza o novo endereço.
5. Selecione **Guardar**.
6. Reinicie os VMs que estão ligados à rede virtual, para que lhes sejam atribuídas as novas definições do servidor DNS. Os VMs continuam a utilizar as definições dNS atuais até serem reiniciados.

**Comandos**

- Azure CLI: [az network vnet update](/cli/azure/network/vnet)
- PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Eliminar uma rede virtual

Só é possível eliminar uma rede virtual se não houver recursos ligados à rede. Se houver recursos ligados a qualquer subrede dentro da rede virtual, tem primeiro de eliminar os recursos que estão ligados a todas as redes dentro da rede virtual. Os passos que toma para eliminar um recurso variam consoante o recurso. Para saber como eliminar recursos ligados a subredes, leia a documentação para cada tipo de recurso que pretende eliminar. Para eliminar uma rede virtual:

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando as **redes Virtuais** aparecerem nos resultados da pesquisa, selecione-as.
2. A partir da lista de redes virtuais, selecione a rede virtual que pretende eliminar.
3. Confirme que não existem dispositivos ligados à rede virtual selecionando **dispositivos Conectados**, em **DEFINIÇÕES**. Se existirem dispositivos ligados, deve eliminá-los antes de poder eliminar a rede virtual. Se não existirem dispositivos ligados, selecione **Visão Geral**.
4. Selecione **Eliminar**.
5. Para confirmar a eliminação da rede virtual, selecione **Sim**.

**Comandos**

- Azure CLI: [rede azure vnet apagar](/cli/azure/network/vnet)
- PowerShell: [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Permissões

Para executar tarefas em redes virtuais, a sua conta deve ser atribuída à função de contribuinte da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que lhe seja atribuída as ações adequadas enumeradas na tabela seguinte:

| Ação                                  |   Nome                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   Leia uma Rede Virtual              |
|Microsoft.Network/virtualNetworks/write  |   Criar ou atualizar uma rede virtual  |
|Microsoft.Network/virtualNetworks/delete |   Eliminar uma rede virtual            |

## <a name="next-steps"></a>Passos seguintes

- Criar uma rede virtual utilizando scripts de amostra [PowerShell](powershell-samples.md) ou [Azure CLI,](cli-samples.md) ou utilizando [modelos](template-samples.md) de Gestor de Recursos Azure
- Criar e aplicar [a política azure](policy-samples.md) para redes virtuais
