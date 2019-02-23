---
title: Criar, alterar ou eliminar um peering de rede virtual do Azure | Documentos da Microsoft
description: Saiba como criar, alterar ou eliminar um peering de rede virtual.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: jdial;anavin
ms.openlocfilehash: a2d799a85932f8b7fdf1f6ae53dc50579c266311
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674531"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Criar, alterar ou eliminar um peering de rede virtual

Saiba como criar, alterar ou eliminar um peering de rede virtual. Peering de rede virtual permite-lhe ligar redes virtuais na mesma região e entre regiões (também conhecido como Global VNet Peering) através da rede de backbone do Azure. Uma vez executado o peering, as redes virtuais continuam a ser geridas como recursos separados. Se estiver familiarizado com o peering de rede virtual, pode saber mais sobre isso na [descrição geral de peering de rede virtual](virtual-network-peering-overview.md) ou ao preencher um [tutorial](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir os passos em qualquer secção deste artigo:

- Se ainda não tiver uma conta do Azure, inscreva-se para uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se utilizar o portal, abra https://portal.azure.come inicie sessão com uma conta que tenha o [as permissões necessárias](#permissions) para trabalhar com peerings.
- Se utilizar comandos do PowerShell para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/powershell), ou ao executar o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o Azure PowerShell versão 1.0.0 do módulo ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver executando o PowerShell localmente, terá também de ser executado `Connect-AzAccount` com uma conta que tenha o [as permissões necessárias](#permissions) para trabalhar com o peering, para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comandos (CLI) do Azure para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/bash), ou ao executar a CLI do seu computador. Este tutorial requer a versão 2.0.31 CLI do Azure ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar a CLI do Azure localmente, terá também de ser executado `az login` com uma conta que tenha o [as permissões necessárias](#permissions) para trabalhar com o peering, para criar uma ligação com o Azure.

A conta iniciar sessão no, ou ligar ao Azure, tem de ser atribuída para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atribuída as ações adequadas listadas na [permissões ](#permissions).

## <a name="create-a-peering"></a>Criar um peering

Antes de criar um peering, familiarize-se com os requisitos e restrições e [as permissões necessárias](#permissions).

1. Na caixa de pesquisa na parte superior do portal do Azure, introduza *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecer nos resultados da pesquisa, selecione-o. Não selecione **redes virtuais (clássico)** se aparece na lista, como não é possível criar um peering de uma rede virtual implementada por meio do modelo de implementação clássica.
2. Selecione a rede virtual na lista que pretende criar como peering.
3. Na lista de redes virtuais, selecione a rede virtual que pretende criar como peering.
4. Sob **configurações**, selecione **Peerings**.
5. Selecione **+ Adicionar**. 
6. <a name="add-peering"></a>Introduza ou selecione os valores para as seguintes definições:
    - **Nome:** O nome para o peering tem de ser exclusivo dentro da rede virtual.
    - **Modelo de implementação de rede virtual:** Selecione a rede virtual que pretende fazer peer foi implementada por meio de qual modelo de implementação.
    - **Eu sei o ID de recurso:** Se tiver acesso de leitura para a rede virtual que pretende fazer peer, deixe esta caixa de verificação desmarcada. Se não tiver acesso de leitura para a rede virtual ou a subscrição que pretende configurar o peering com, esta caixa de verificação. Introduza o ID de recurso completo da rede virtual que pretende configurar o peering no **ID de recurso** caixa que apareceu quando tiver selecionado a caixa. O recurso ID introduzir tem de ser para uma rede virtual que existe no mesmo, ou [suportados diferentes](#requirements-and-constraints) Azure [região](https://azure.microsoft.com/regions) como esta rede virtual. O ID do recurso completo é semelhante a /subscriptions/<Id>/resourceGroups/ < nome de grupo de recursos > /providers/Microsoft.Network/virtualNetworks/ < virtual-network-name >. Pode obter o ID de recurso para uma rede virtual ao visualizar as propriedades de uma rede virtual. Para saber como ver as propriedades de uma rede virtual, veja [gerir redes virtuais](manage-virtual-network.md#view-virtual-networks-and-settings). Se a subscrição está associada a um inquilino do Azure Active Directory diferente do que a subscrição com a rede virtual que está a criar o peering de, primeiro adicione um utilizador de cada inquilino como um [utilizador convidado](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) no inquilino oposto.
    - **Subscrição:** Selecione o [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) da rede virtual que pretende fazer peer. Uma ou mais subscrições estão listadas, dependendo do número de subscrições a sua conta tem acesso de leitura. Se tiver selecionado o **ID de recurso** caixa de verificação, esta definição não está disponível.
    - **Rede virtual:** Selecione a rede virtual que pretende fazer peer. Pode selecionar uma rede virtual criada através de qualquer modelo de implementação do Azure. Se pretender selecionar uma rede virtual numa região diferente, tem de selecionar uma rede virtual de um [suportada região](#cross-region). Tem de ter acesso de leitura para a rede virtual para que ele ficasse visível na lista. Se uma rede virtual está listada, mas a cinzento, poderá ser porque o espaço de endereços da rede virtual sobrepõe-se o espaço de endereços para esta rede virtual. Se a sobreposição de espaços de endereços de rede virtual, não podem ser agrupadas. Se tiver selecionado o **ID de recurso** caixa de verificação, esta definição não está disponível.
    - **Permitir o acesso de rede virtual:** Selecione **ativado** (predefinição), se pretender permitir a comunicação entre as duas redes virtuais. Ativar a comunicação entre redes virtuais permite que os recursos ligados a qualquer rede virtual para comunicar entre si com o mesmo da largura de banda e latência, como se estivessem conectados à mesma rede virtual. Todas as comunicações entre os recursos em duas redes virtuais é através da rede privada do Azure. O **VirtualNetwork** etiqueta de serviço para grupos de segurança de rede abrange a rede virtual e a rede virtual em modo de peering. Para obter mais informações sobre etiquetas de serviço de grupo de segurança de rede, consulte [descrição geral dos grupos de segurança de rede](security-overview.md#service-tags). Selecione **desativado** se não pretender que o tráfego flua para a rede virtual em modo de peering. Poderá selecionar **desativado** se tiver uma rede virtual com outra rede virtual em modo de peering, mas, ocasionalmente, pretende desativar o fluxo de tráfego entre as duas redes virtuais. Pode achar a ativação/desativação é mais conveniente para a eliminar e voltar a criar peerings. Quando esta definição estiver desativada, o tráfego não flui entre as redes virtuais em modo de peering.
    - **Permitir tráfego reencaminhado:** Selecione esta caixa para permitir o tráfego *reencaminhados* por uma aplicação virtual de rede numa rede virtual (que não se originam da rede virtual) para o fluxo para esta rede virtual por meio de um peering. Por exemplo, considere as três redes virtuais com o nome Spoke1 Spoke2 e Hub. Um peering existe entre cada rede virtual do spoke e rede virtual do concentrador, mas os peerings não existem entre as redes virtuais indicadas. Uma aplicação virtual de rede está implementada na rede virtual do concentrador e rotas definidas pelo utilizador são aplicadas a cada rede virtual do spoke que encaminham o tráfego entre sub-redes através do dispositivo virtual de rede. Se esta caixa de verificação não estiver marcada para o peering entre cada rede virtual do spoke e rede virtual do concentrador, tráfego não flui entre as redes virtuais "spoke", porque o hub é reencaminhar o tráfego entre as redes virtuais. Enquanto o ativar essa capacidade permite o tráfego reencaminhado através do peering, não cria quaisquer rotas definidas pelo utilizador ou dispositivos de rede virtual. Rotas definidas pelo utilizador e dispositivos de rede virtual são criados em separado. Saiba mais sobre [rotas definidas pelo utilizador](virtual-networks-udr-overview.md#user-defined). Não precisa verificar esta definição se o tráfego é encaminhado entre redes virtuais através de um Gateway de VPN do Azure.
    - **Permitir trânsito de gateway:** Selecione esta caixa se tiver um gateway de rede virtual ligado a esta rede virtual e pretende permitir o tráfego da rede virtual em modo de peering para o fluxo através do gateway. Por exemplo, esta rede virtual pode ser anexada a uma rede no local através de um gateway de rede virtual. O gateway pode ser um gateway de VPN ou ExpressRoute. Selecionar esta caixa permite o tráfego da rede virtual em modo de peering para o fluxo através do gateway anexado a esta rede virtual à rede no local. Se selecionar esta caixa, a rede virtual em modo de peering não pode ter um gateway configurado. A rede virtual em modo de peering tem de ter o **utilizar gateways remotos** caixa de seleção marcada quando configurar o peering da rede virtual para esta rede virtual. Se deixar esta caixa desmarcada (predefinição), o tráfego de rede virtual em modo de peering ainda fluxos a esta rede virtual, mas não pode fluir por um gateway de rede virtual ligado a esta rede virtual. Se o peering entre uma rede virtual (Resource Manager) e uma rede virtual (clássica), o gateway tem de ser na rede virtual (Resource Manager). Não é possível ativar esta opção se estiver peering de redes virtuais em regiões diferentes.

       Para além de encaminhar o tráfego para uma rede no local, um gateway de VPN pode reencaminhar o tráfego de rede entre redes virtuais em modo de peering com a rede virtual, que o gateway está, sem as redes virtuais que precisem de ser executado o peering entre si. Utilizar um gateway VPN para encaminhar o tráfego é útil quando pretender utilizar um gateway de VPN num hub (veja o exemplo de hub- and -spoke descrito para **permitir tráfego reencaminhado**) a rede virtual para encaminhar o tráfego entre redes virtuais "spoke" que não são configurar o peering entre si. Para saber mais sobre o que permite a utilização de um gateway para o trânsito, veja [configurar um gateway de VPN que o trânsito no peering de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este cenário requer a implementação de rotas definidas pelo utilizador que especificam o gateway de rede virtual como o tipo de salto seguinte. Saiba mais sobre [rotas definidas pelo utilizador](virtual-networks-udr-overview.md#user-defined). Apenas pode especificar um gateway de VPN como um tipo de próximo salto numa rota definida pelo utilizador, não é possível especificar um gateway ExpressRoute como o tipo de próximo salto numa rota definida pelo utilizador. Não é possível ativar esta opção se estiver peering de redes virtuais em regiões diferentes.

    - **Utilize gateways remotos:** Marque esta caixa para permitir tráfego a partir desta rede virtual flua através de um gateway de rede virtual ligado à rede virtual que estiver peering com. Por exemplo, a rede virtual que estiver peering com tem um gateway de VPN ligado que permite a comunicação a uma rede no local.  Selecionar esta caixa permite o tráfego a partir desta rede virtual flua através do gateway VPN ligado à rede virtual em modo de peering. Se selecionar esta caixa, a rede virtual em modo de peering tem de ter um gateway de rede virtual ligado ao mesmo e tem de ter o **permitir que o trânsito de gateway** caixa de verificação verificada. Se deixar esta caixa desmarcada (predefinição), o tráfego da rede virtual em modo de peering ainda pode fluir a esta rede virtual, mas não pode fluxo através de um gateway de rede virtual ligado a neste virtual rede.
    Apenas um peering para esta rede virtual pode ter esta definição ativada.

        Não é possível utilizar gateways remotos se já tiver um gateway configurado na sua rede virtual. Não é possível ativar esta opção se estiver peering de redes virtuais em regiões diferentes. Para saber mais sobre como utilizar um gateway para o trânsito, veja [configurar um gateway de VPN que o trânsito no peering de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

7. Selecione **OK** para adicionar o peering para a rede virtual que selecionou.

Para obter instruções passo a passo para implementar o peering entre redes virtuais em diferentes subscrições e modelos de implementação, consulte [passos seguintes](#next-steps).

### <a name="commands"></a>Comandos

- **CLI do Azure**: [criar peering de vnet de rede de az](/cli/azure/network/vnet/peering#create)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Ver ou alterar as definições de peering

Antes de alterar um peering, familiarize-se com os requisitos e restrições e [as permissões necessárias](#permissions).

1. Na caixa de pesquisa na parte superior do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecer nos resultados da pesquisa, selecione-o. Não selecione **redes virtuais (clássico)** se aparece na lista, como não é possível criar um peering de uma rede virtual implementada por meio do modelo de implementação clássica.
2. Na lista que pretende alterar as definições de peering para, selecione a rede virtual.
3. Na lista de redes virtuais, selecione a rede virtual que pretende alterar as definições de peering para.
4. Sob **configurações**, selecione **Peerings**.
5. Selecione o que pretende ver ou alterar as definições de peering.
6. Altere a definição adequada. Leia sobre as opções para cada definição em [passo 6](#add-peering) de criar um peering.
7. Selecione **Guardar**.

**Comandos**

- **CLI do Azure**: [lista de peering de vnet de rede de az](/cli/azure/network/vnet/peering) para peerings de lista para uma rede virtual, [az network vnet show de peering](/cli/azure/network/vnet/peering#az_network_vnet_peering_show) para mostrar as definições para um peering específico, e [rede az atualização de peering de vnet](/cli/azure/network/vnet/peering#az_network_vnet_peering_update) para alterar as definições de peering. |
- **PowerShell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) para obter as definições de peering do modo de exibição e [conjunto AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) para alterar as definições.

## <a name="delete-a-peering"></a>Eliminar um peering

Antes de eliminar um peering, certifique-se a sua conta tem o [as permissões necessárias](#permissions).

Quando um peering for eliminado, o tráfego de uma rede virtual já não fluxos para a rede virtual em modo de peering. Quando criado o peering entre redes virtuais implementadas através do Resource Manager, a cada rede virtual tem um peering para outra rede virtual. Embora a eliminar o peering de uma rede virtual desativa a comunicação entre as redes virtuais, não elimina o peering da rede virtual. É o estado do peering para o peering que existe na rede virtual **desligado**. Não é possível recriar o peering até voltar a criar o peering da primeira rede virtual e o estado do peering para ambas as alterações de redes virtuais *ligado*.

Se pretender que as redes virtuais para comunicar-se por vezes, mas nem sempre, em vez de eliminar um peering, pode definir o **permitir o acesso de rede virtual** definição **desativado** em vez disso. Para saber como, leia o passo 6 do [criar um peering](#create-a-peering) seção deste artigo. Pode achar desabilitando e habilitando o acesso à rede mais fácil do que a eliminação e recriação de peerings.

1. Na caixa de pesquisa na parte superior do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecer nos resultados da pesquisa, selecione-o. Não selecione **redes virtuais (clássico)** se aparece na lista, como não é possível criar um peering de uma rede virtual implementada por meio do modelo de implementação clássica.
2. Selecione a rede virtual na lista que pretende eliminar o peering.
3. Na lista de redes virtuais, selecione a rede virtual que pretende eliminar o peering.
4. Sob **configurações**, selecione **Peerings**.
5. No lado direito do peering que pretende eliminar, selecione **...** , selecione **eliminar**, em seguida, selecione **Sim** para eliminar o peering a partir da primeira rede virtual.
6. Conclua os passos anteriores para eliminar o peering a partir de outra rede virtual no peering.

**Comandos**

- **CLI do Azure**: [delete de peering de vnet do az rede](/cli/azure/network/vnet/peering)
- **PowerShell**: [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Requisitos e limitações

- <a name="cross-region"></a>Pode configurar o peering entre redes virtuais na mesma região ou regiões diferentes. Peering de redes virtuais em diferentes regiões também é referido como *global peering*.
- Ao criar um peering global, as redes virtuais em modo de peering podem existir em qualquer região de cloud pública do Azure, regiões da cloud na China ou regiões de cloud do Governo. Não pode configurar o peering entre nuvens.
- Recursos numa rede virtual não consegue comunicar com o endereço IP Front-end do Balanceador de carga interno do Azure numa rede virtual em modo de peering global. O Balanceador de carga e os recursos que se comunicam com o mesmo tem de ser numa rede virtual na mesma região. Se as redes virtuais em modo de peering na mesma região no entanto, os recursos em qualquer rede virtual podem comunicar com o endereço IP Front-end do Balanceador de carga interno do Azure em qualquer rede virtual no peering.
- Não é possível utilizar gateways remotos ou permitir que o trânsito de gateway em redes virtuais global em modo de peering. Para utilizar gateways remotos ou permitir que o trânsito de gateway, as redes virtuais em modo de peering tem de ser na mesma região.
- As redes virtuais podem estar nas subscrições idêntica ou diferentes. Quando configurar o peering de redes virtuais em diferentes subscrições, ambas as subscrições podem ser associadas ao inquilino do Azure Active Directory idêntica ou diferente. Se ainda não tiver um inquilino do AD, pode [criá-lo](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant). Suporte para o peering entre redes virtuais das subscrições associadas aos diferentes inquilinos do Azure Active Directory não está disponível no Portal. Pode utilizar modelos, PowerShell ou CLI.
- As redes virtuais que configurar o peering tem de ter espaços de endereços IP não sobrepostos.
- Não é possível adicionar intervalos de endereços para ou eliminar intervalos de endereços do espaço de endereços da rede virtual depois de uma rede virtual é executado o peering com outra rede virtual. Para adicionar ou remover intervalos de endereços, eliminar o peering, adicionar ou remover os intervalos de endereços, em seguida, recrie o peering. Para intervalos de endereços para adicionar ou remover intervalos de endereços das redes virtuais, veja [gerir redes virtuais](manage-virtual-network.md).
- Pode configurar o peering entre duas redes virtuais implementadas através do Gestor de recursos ou uma rede virtual implementado através do Resource Manager com uma rede virtual implementada por meio do modelo de implementação clássica. Não é possível configurar o peering entre duas redes virtuais criadas através do modelo de implementação clássica. Se não estiver familiarizado com os modelos de implementação do Azure, leia os [modelos de implementação do Azure compreender](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo. Pode utilizar um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para ligar duas redes virtuais criadas através do modelo de implementação clássica.
- Ao configurar o peering de duas redes virtuais criadas através do Resource Manager, tem de ser configurado um peering para cada rede virtual no peering. Veja um dos seguintes tipos de estado do peering: 
  - *Iniciado:* Quando cria o peering para a segunda rede virtual a partir da primeira rede virtual, o estado do peering é *iniciado*. 
  - *Ligado:* Quando cria o peering a partir da segunda rede virtual para a primeira rede virtual, o estado do peering é *ligado*. Se visualizar o estado do peering para a primeira rede virtual, verá que o estado mudou de *iniciado* ao *ligado*. O peering não é estabelecido com êxito até que o estado do peering para ambos os peerings de rede virtual seja *ligado*.
- Quando o peering de rede virtual criada através do Resource Manager com uma rede virtual criada através do modelo de implementação clássica, apenas configurar um peering da rede virtual implementado através do Resource Manager. Não é possível configurar o peering para uma rede virtual (clássica) ou entre duas redes virtuais implementadas através do modelo de implementação clássica. Quando cria o peering da rede virtual (Resource Manager) para a rede virtual (clássico), o estado do peering é *atualização*, em seguida, em breve, altera a *ligado*.
- Um peering ser estabelecido entre duas redes virtuais. Peerings não é transitivas. Se criar peerings entre:
  - VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3

  Não existe nenhum peering entre VirtualNetwork1 e VirtualNetwork3 por meio de VirtualNetwork2. Se quiser criar uma rede virtual peering entre VirtualNetwork1 e VirtualNetwork3, terá de criar um peering entre VirtualNetwork1 e VirtualNetwork3.
- Não é possível resolver os nomes de redes virtuais em modo de peering com a resolução de nomes do Azure de predefinição. Para resolver os nomes de outras redes virtuais, tem de utilizar [DNS do Azure para domínios privados](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou um servidor DNS personalizado. Para saber como configurar o seu próprio servidor DNS, consulte [resolução de nomes através de seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Recursos em redes virtuais em modo de peering na mesma região podem comunicar entre si com o mesmo da largura de banda e latência, como se estivessem na mesma rede virtual. Cada tamanho da máquina virtual no entanto tem sua própria largura de banda máxima da rede. Para saber mais sobre a largura de banda de rede máxima para tamanhos de máquinas virtuais, veja [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tamanhos de máquinas virtuais.
- Uma rede virtual pode ser executado o peering de redes virtuais e também de estar ligada a outra rede virtual com um gateway de rede virtual do Azure. Quando as redes virtuais estão ligadas através de peering e um gateway, o tráfego entre as redes virtuais flui através da configuração de peering, em vez do gateway.
- Os clientes VPN ponto a Site tem de ser transferidos novamente depois de peering de rede virtual tem foi configurado com êxito para garantir que as rotas novos são transferidas para o cliente.
- Existe uma cobrança nominal para o tráfego de entrada e de saída que utilize um peering de rede virtual. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Permissões

As contas de que usar para trabalhar com o peering de rede virtual devem ser atribuídas para as seguintes funções:

- [Contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): Para uma rede virtual implementada através do Resource Manager.
- [Contribuinte de rede clássica](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): Para uma rede virtual implementada por meio do modelo de implementação clássica.

Se a sua conta não está atribuída a uma das funções do anteriores, tem de ser atribuído a um [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atribuída as ações necessárias a partir da seguinte tabela:

| Ação                                                          | Name |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Necessário para criar um peering de rede virtual A rede virtual B. virtuais uma de rede tem de ser uma rede virtual (Resource Manager)          |
| Microsoft.Network/virtualNetworks/peer/action                   | Necessário para criar um peering da rede virtual B (Resource Manager) para a rede virtual A                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer                   | Necessário para criar um peering da rede virtual B (clássico) para a rede virtual A                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Ler um peering de rede virtual   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Eliminar um peering de rede virtual |

## <a name="next-steps"></a>Passos Seguintes

- É criado um peering de rede virtual entre redes virtuais criadas com o mesmo ou com diferentes modelos de implementação que existam na mesma ou em diferentes subscrições. Conclua um tutorial para um dos cenários seguintes:

  |Modelo de implementação do Azure             | Subscrição  |
  |---------                          |---------|
  |Ambas com Resource Manager              |[Mesma](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Diferente](create-peering-different-subscriptions.md)|
  |Uma com Resource Manager, outra com clássica  |[Mesma](create-peering-different-deployment-models.md)|
  |                                   |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

- Saiba como criar uma [topologia de rede hub and spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Criar um peering de rede virtual com [PowerShell](powershell-samples.md) ou [CLI do Azure](cli-samples.md) scripts ou utilizar o Azure de exemplo [modelos do Resource Manager](template-samples.md)
- Criar e aplicar [o Azure policy](policy-samples.md) para redes virtuais
