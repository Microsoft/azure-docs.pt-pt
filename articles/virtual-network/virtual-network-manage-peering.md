---
title: Criar, alterar ou apagar uma rede virtual Azure a espreitar  ) Microsoft Docs
description: Aprenda a criar, alterar ou eliminar um olhar de rede virtual.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: anavin
ms.openlocfilehash: 4103930e0d089f5f7c17586f22616431c8aa11d9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279653"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Criar, alterar ou eliminar um peering de rede virtual

Aprenda a criar, alterar ou eliminar um olhar de rede virtual. O peering de rede virtual permite-lhe ligar redes virtuais na mesma região e em todas as regiões (também conhecidas como Global VNet Peering) através da rede de espinha dorsal Azure. Uma vez espreitadas, as redes virtuais ainda são geridas como recursos separados. Se você é novo no virtual network peering, você pode aprender mais sobre isso na visão geral da [rede virtual](virtual-network-peering-overview.md) ou completando um [tutorial](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Complete as seguintes tarefas antes de completar os passos em qualquer secção deste artigo:

- Se ainda não tem uma conta Azure, inscreva-se numa [conta de teste gratuita.](https://azure.microsoft.com/free)
- Se utilizar o portal, abra https://portal.azure.come faça login com uma conta que tenha as [permissões necessárias](#permissions) para trabalhar com os pares.
- Se utilizar os comandos PowerShell para completar tarefas neste artigo, execute os comandos na Casca de [Nuvem Azure,](https://shell.azure.com/powershell)ou executando powerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer a versão 1.0.0 ou posterior do módulo PowerShell Azure. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar a PowerShell localmente, também precisa de executar `Connect-AzAccount` com uma conta que tenha as [permissões necessárias](#permissions) para trabalhar com o peering, para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comando Azure (CLI) para completar tarefas neste artigo, execute os comandos na [Casca de Nuvem Azure,](https://shell.azure.com/bash)ou executando o CLI a partir do seu computador. Este tutorial requer a versão Azure CLI 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI localmente, também precisa de executar `az login` com uma conta que tenha as [permissões necessárias](#permissions) para trabalhar com o peering, para criar uma ligação com o Azure.

A conta em que inicia sessão, ou liga-se ao Azure, deve ser atribuída à função de contribuinte da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que lhe seja atribuída as ações adequadas enumeradas nas [Permissões](#permissions).

## <a name="create-a-peering"></a>Criar um olhar

Antes de criar um olhar, familiarize-se com os requisitos e constrangimentos e [as permissões necessárias.](#permissions)

1. Na caixa de pesquisa no topo do portal Azure, introduza *redes virtuais* na caixa de pesquisa. Quando as **redes Virtuais** aparecerem nos resultados da pesquisa, selecione-as. Não selecione **redes Virtuais (clássicas)** se aparecer na lista, uma vez que não pode criar um peering a partir de uma rede virtual implementada através do modelo de implementação clássico.
2. Selecione a rede virtual na lista que pretende criar um speering.
3. Em **DEFINIÇÕES,** selecione **Peerings**.
4. Selecione **+ Adicionar**. 
5. <a name="add-peering"></a>Introduza ou selecione valores para as seguintes definições:
    - **Nome:** O nome para o epeering deve ser único dentro da rede virtual.
    - **Modelo de implementação de rede virtual:** Selecione qual o modelo de implementação com que a rede virtual que pretende utilizar foi implementada.
    - **Conheço a minha identificação** de recursos: Se leu o acesso à rede virtual com a que pretende espreitar, deixe esta caixa de verificação desmarcada. Se não tiver lido o acesso à rede virtual ou subscrição que pretende consultar, verifique esta caixa. Introduza a identificação completa do recurso da rede virtual com a que pretende espreitar na caixa de ID de **Recursos** que apareceu quando verificou a caixa. O ID de recurso em que entra deve ser para uma rede virtual que exista na mesma, ou [suportado diferente](#requirements-and-constraints) [região](https://azure.microsoft.com/regions) azure como esta rede virtual. O ID de recursos completos é semelhante ao `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>`. Você pode obter o ID de recursos para uma rede virtual, visualizando as propriedades para uma rede virtual. Para aprender a visualizar as propriedades para uma rede virtual, consulte [Gerir redes virtuais.](manage-virtual-network.md#view-virtual-networks-and-settings) Se a subscrição estiver associada a um inquilino do Azure Ative Directory diferente da subscrição com a rede virtual de onde está a criar o peering, primeiro adicione um utilizador de cada inquilino como [utilizador convidado](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) no inquilino oposto.
    - **Subscrição:** Selecione a [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) da rede virtual com a que pretende si. Uma ou mais subscrições estão listadas, dependendo de quantas subscrições a sua conta leu acesso. Se verificou a caixa de verificação de ID de **recursos,** esta definição não está disponível.
    - **Rede virtual:** Selecione a rede virtual com a que pretende espreitar. Pode selecionar uma rede virtual criada através de qualquer modelo de implementação do Azure. Se pretender selecionar uma rede virtual numa região diferente, deve selecionar uma rede virtual numa [região apoiada](#cross-region). Deve ter lido o acesso à rede virtual para que seja visível na lista. Se uma rede virtual estiver listada, mas cinzenta, pode ser porque o espaço de endereço para a rede virtual se sobrepõe ao espaço de endereço para esta rede virtual. Se os espaços de endereço suporem a rede virtual, não podem ser espreitados. Se verificou a caixa de verificação de ID de **recursos,** esta definição não está disponível.
    - **Permitir o acesso virtual à rede:** Selecione **Ativado** (predefinido) se pretender ativar a comunicação entre as duas redes virtuais. Permitir a comunicação entre redes virtuais permite que os recursos ligados a uma rede virtual se comuniquem entre si com a mesma largura de banda e latência como se estivessem ligados à mesma rede virtual. Toda a comunicação entre recursos nas duas redes virtuais é sobre a rede privada Azure. A etiqueta de serviço **VirtualNetwork** para grupos de segurança de rede engloba a rede virtual e a rede virtual peered. Para saber mais sobre as etiquetas de serviço do grupo de segurança da rede, consulte a visão geral dos [grupos de segurança da rede](security-overview.md#service-tags). Selecione **Desativado** se não quiser que o tráfego flua para a rede virtual esparsada. Pode selecionar **Desativado** se tiver espreitado uma rede virtual com outra rede virtual, mas ocasionalmente quer desativar o fluxo de tráfego entre as duas redes virtuais. Pode achar que permitir/desativar é mais conveniente do que apagar e recriar os pares. Quando esta configuração é desativada, o tráfego não flui entre as redes virtuais esparsadas.
    - **Permitir o tráfego encaminhado:** Verifique esta caixa para permitir que o tráfego *encaminhado* por um aparelho virtual de rede numa rede virtual (que não tenha origem na rede virtual) flua para esta rede virtual através de um espreitamento. Por exemplo, considere três redes virtuais chamadas Spoke1, Spoke2 e Hub. Existe um olhar entre cada rede virtual falada e a rede virtual Hub, mas os pares não existem entre as redes virtuais faladas. Um aparelho virtual de rede é implantado na rede virtual Hub, e as rotas definidas pelo utilizador são aplicadas a cada rede virtual falada que encaminha o tráfego entre as subredes através do aparelho virtual da rede. Se esta caixa de verificação não for verificada para o peering entre cada rede virtual falada e a rede virtual do hub, o tráfego não flui entre as redes virtuais faladas porque o hub não está a encaminhar o tráfego entre as redes virtuais. Ao mesmo tempo que permite que esta capacidade permita o tráfego encaminhado através do peering, não cria nenhuma rota definida pelo utilizador ou aparelhos virtuais de rede. As rotas definidas pelo utilizador e os aparelhos virtuais da rede são criados separadamente. Saiba mais sobre [as rotas definidas pelo utilizador](virtual-networks-udr-overview.md#user-defined). Não é necessário verificar se o tráfego é reencaminhado entre redes virtuais através de um Azure VPN Gateway.
    - **Permitir o trânsito de gateway:** Verifique esta caixa se tem um portal de rede virtual ligado a esta rede virtual e pretende permitir que o tráfego da rede virtual peered flua através do portal. Por exemplo, esta rede virtual pode ser anexada a uma rede no local através de um portal de rede virtual. O portal pode ser um gateway ExpressRoute ou VPN. A verificação desta caixa permite que o tráfego da rede virtual peered flua através do portal ligado a esta rede virtual para a rede no local. Se verificar esta caixa, a rede virtual em pares não pode ter um gateway configurado. A rede virtual peered deve ter a caixa de verificação **de gateways remotos Use** verificada ao configurar o peering da outra rede virtual para esta rede virtual. Se deixar esta caixa desmarcada (predefinido), o tráfego da rede virtual peered ainda flui para esta rede virtual, mas não pode fluir através de um portal de rede virtual ligado a esta rede virtual. Se o epeering estiver entre uma rede virtual (Gestor de Recursos) e uma rede virtual (clássica), o gateway deve estar na rede virtual (Gestor de Recursos).

       Além de encaminhar o tráfego para uma rede no local, um gateway VPN pode transmitir o tráfego de rede entre redes virtuais que são espreitadas com a rede virtual em que o portal se encontra, sem que as redes virtuais precisem de ser espumosas umas com as outras. Usar uma porta de entrada VPN para encaminhar o tráfego é útil quando se quer usar um gateway VPN num hub (ver o hub e o exemplo falado descrito para **permitir o tráfego encaminhado**) rede virtual para encaminhar o tráfego entre redes virtuais faladas que não são espreitadas umas com as outras. Para saber mais sobre a utilização de uma porta de entrada para o trânsito, consulte Configure uma porta de [entrada VPN para trânsito numa rede virtual de observação](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este cenário requer a implementação de rotas definidas pelo utilizador que especificam o gateway da rede virtual como o próximo tipo de lúpulo. Saiba mais sobre [as rotas definidas pelo utilizador](virtual-networks-udr-overview.md#user-defined). Só pode especificar um gateway VPN como um próximo tipo de lúpulo numa rota definida pelo utilizador, não pode especificar um gateway ExpressRoute como o próximo tipo de lúpulo numa rota definida pelo utilizador.

    - **Utilize gateways remotos:** Verifique esta caixa para permitir que o tráfego desta rede virtual flua através de um portal de rede virtual ligado à rede virtual com a qual está a espreitar. Por exemplo, a rede virtual com a qual está a espreitar tem um gateway VPN ligado que permite a comunicação a uma rede no local.  A verificação desta caixa permite que o tráfego desta rede virtual flua através do gateway VPN ligado à rede virtual peered. Se verificar esta caixa, a rede virtual em pares deve ter um portal de rede virtual ligado a ela e deve ter a caixa de verificação de **trânsito de gateway Permitir.** Se deixar esta caixa desmarcada (predefinido), o tráfego da rede virtual peered ainda pode fluir para esta rede virtual, mas não pode fluir através de um portal de rede virtual ligado a esta rede virtual.
    Apenas um olhar para esta rede virtual pode ter esta definição ativada.

        Não é possível utilizar gateways remotos se já tiver um portal configurado na sua rede virtual. Para saber mais sobre a utilização de uma porta de entrada para o trânsito, consulte Configure uma porta de [entrada VPN para o trânsito numa rede virtual de espreitar](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

6. Selecione **OK** para adicionar o epeering à rede virtual selecionada.

Para obter instruções passo a passo para implementar o peering entre redes virtuais em diferentes subscrições e modelos de implementação, consulte [os próximos passos](#next-steps).

### <a name="commands"></a>Comandos

- **Azure CLI**: [az rede vnet peering criar](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Ver ou alterar definições de peering

Antes de alterar um olhar, familiarize-se com os requisitos e constrangimentos e [as permissões necessárias](#permissions).

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando as **redes Virtuais** aparecerem nos resultados da pesquisa, selecione-as. Não selecione **redes Virtuais (clássicas)** se aparecer na lista, uma vez que não pode criar um peering a partir de uma rede virtual implementada através do modelo de implementação clássico.
2. Selecione a rede virtual na lista para a quais pretende alterar as definições de verificação.
3. Em **DEFINIÇÕES,** selecione **Peerings**.
4. Selecione o epeering que pretende visualizar ou alterar as definições.
5. Altere a definição adequada. Leia sobre as opções para cada definição no [passo 5](#add-peering) de Criar um olhar.
6. Selecione **Guardar**.

**Comandos**

- **Azure CLI**: [az network vnet peering list](/cli/azure/network/vnet/peering) list peerings for a virtual network, [az network vnet peering show](/cli/azure/network/vnet/peering) show to show to show for a specific peering, and [az network vnet peering update](/cli/azure/network/vnet/peering) to change peering settings.
- **PowerShell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) para recuperar as definições de visualização e [set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) para alterar as definições.

## <a name="delete-a-peering"></a>Eliminar um olhar

Antes de apagar um epeering, certifique-se de que a sua conta tem as [permissões necessárias](#permissions).

Quando um epeering é apagado, o tráfego de uma rede virtual já não flui para a rede virtual peered. Quando as redes virtuais implementadas através do Gestor de Recursos são espartadas, cada rede virtual tem um olhar para a outra rede virtual. Embora apagar o epeering de uma rede virtual desativa a comunicação entre as redes virtuais, não elimina o epeering da outra rede virtual. O estatuto de peering para o peering que existe na outra rede virtual é **desligado**. Não é possível recriar o epeering até recriar o peering na primeira rede virtual e o estatuto de peering para ambas as redes virtuais alterações ao *Connected*.

Se quiser que as redes virtuais se comuniquem às vezes, mas nem sempre, em vez de apagar um espectado, pode definir a definição de **acesso à rede virtual permitir** aos **deficientes.** Para saber como, leia o passo 6 da Criação de uma secção de observação deste artigo. Pode achar o acesso incapacitante e a permitir o acesso à rede mais fácil do que apagar e recriar os pares.

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando as **redes Virtuais** aparecerem nos resultados da pesquisa, selecione-as. Não selecione **redes Virtuais (clássicas)** se aparecer na lista, uma vez que não pode criar um peering a partir de uma rede virtual implementada através do modelo de implementação clássico.
2. Selecione a rede virtual na lista que pretende eliminar um epeering.
3. Em **DEFINIÇÕES,** selecione **Peerings**.
4. No lado direito do peering pretende eliminar, selecione... **selecione Eliminar,** e depois selecione **Sim** para eliminar o peering da primeira rede virtual.
5. Complete os passos anteriores para eliminar o epeering da outra rede virtual no epeering.

**Comandos**

- **Azure CLI**: [az rede vnet peering delete](/cli/azure/network/vnet/peering)
- **PowerShell**: [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Requisitos e limitações

- <a name="cross-region"></a>Você pode ver redes virtuais na mesma região, ou regiões diferentes. O levoing redes virtuais em diferentes regiões também é referido como *Global VNet Peering*. 
- Ao criar um epeering global, as redes virtuais espreitadas podem existir em qualquer região de nuvem pública de Azure ou regiões de nuvem da China ou regiões de nuvem do Governo. Não pode espreitar através das nuvens. Por exemplo, um VNet em azure nuvem pública não pode ser espreitado para um VNet na nuvem Azure China.
- Os recursos numa rede virtual não conseguem comunicar com o endereço IP de front-end de um balanceador de carga interno Básico numa rede virtual de peering global. O suporte para Balanceadores de Carga Básicos existe apenas na mesma região. O suporte para Balanceadores de Carga Standard existe para VNet Peering e VNet Peering Global. Os serviços que utilizam um equilibrador de carga básico que não funcionará sobre o Global VNet Peering estão documentados [aqui.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- Você pode usar gateways remotos ou permitir o trânsito de gateway em redes virtuais globalmente espreitadas e redes virtuais com um público local.
- As redes virtuais podem estar nas mesmas subscrições, ou diferentes. Quando configurar o peering de redes virtuais em diferentes subscrições, ambas as subscrições podem ser associadas ao inquilino do Azure Active Directory idêntica ou diferente. Se ainda não tem um inquilino da AD, pode [criar um.](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant) O apoio ao peering através de redes virtuais a partir de subscrições associadas a diferentes inquilinos do Azure Ative Directory não está disponível no Portal. Pode utilizar CLI, PowerShell ou Templates.
- As redes virtuais que você peer devem ter espaços de endereço IP não sobrepostos.
- Não é possível adicionar intervalos de endereços ou eliminar intervalos de endereços a partir do espaço de endereçode uma rede virtual uma vez que uma rede virtual é escutada com outra rede virtual. Para adicionar ou remover os intervalos de endereços, eliminar o peering, adicionar ou remover as gamas de endereços e, em seguida, recriar o epeering. Para adicionar intervalos de endereços ou remover intervalos de endereços de redes virtuais, ver [Gerir redes virtuais](manage-virtual-network.md).
- Pode utilizar duas redes virtuais implementadas através do Gestor de Recursos ou de uma rede virtual implementada através do Gestor de Recursos com uma rede virtual implementada através do modelo de implementação clássico. Não é possível espreitar duas redes virtuais criadas através do modelo clássico de implantação. Se não estiver familiarizado com os modelos de implementação do Azure, leia o artigo dos [modelos de implementação Do Understand Azure.](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Pode utilizar um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para ligar duas redes virtuais criadas através do modelo de implementação clássica.
- Ao configurar o peering de duas redes virtuais criadas através do Resource Manager, tem de ser configurado um peering para cada rede virtual no peering. Você vê um dos seguintes tipos para o estatuto de peering: 
  - *Iniciado:* Quando cria o peering para a segunda rede virtual a partir da primeira rede virtual, o estado de observação é *Iniciado*. 
  - *Ligado:* Quando cria o peering da segunda rede virtual para a primeira rede virtual, o seu estatuto de peering está *Ligado*. Se vir o estado de observação da primeira rede virtual, verá o seu estado alterado de *Iniciado* para *Connected*. O peering não é estabelecido com sucesso até que o estatuto de peering para ambos os pares de rede virtual esteja *conectado*.
- Ao espreitar uma rede virtual criada através do Gestor de Recursos com uma rede virtual criada através do modelo de implementação clássico, apenas configura um olhar para a rede virtual implementada através do Gestor de Recursos. Não é possível configurar o epeering de uma rede virtual (clássica), ou entre duas redes virtuais implementadas através do modelo de implementação clássico. Quando cria o peering da rede virtual (Gestor de Recursos) para a rede virtual (Classic), o estado de peering é *Update*, em seguida, em breve muda para *Connected*.
- É estabelecido um epeering entre duas redes virtuais. Os olhares não são transitórios. Se criar espeerings entre:
  - VirtualNetwork1 e VirtualNetwork2
  - VirtualNetwork2 e VirtualNetwork3

  Não existe um epeering entre virtualNetwork1 e VirtualNetwork3 através do VirtualNetwork2. Se quiser criar uma rede virtual entre virtualNetwork1 e VirtualNetwork3, tem de criar um peering entre virtualNetwork1 e VirtualNetwork3.
- Não é possível resolver nomes em redes virtuais com recurso à resolução de nomes do Azure padrão. Para resolver nomes noutras redes virtuais, deve utilizar [o Azure DNS para domínios privados](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou um servidor DNS personalizado. Para aprender a configurar o seu próprio servidor DNS, consulte a [resolução de nome utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Os recursos em redes virtuais espreitadas na mesma região podem comunicar entre si com a mesma largura de banda e latência como se estivessem na mesma rede virtual. No entanto, cada tamanho de máquina virtual tem a sua largura de banda máxima de rede. Para saber mais sobre a largura de banda máxima da rede para diferentes tamanhos de máquinavirtual, consulte o tamanho da máquina virtual [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux.](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Uma rede virtual pode ser espreitada para outra rede virtual, e também ser ligada a outra rede virtual com um portal de rede virtual Azure. Quando as redes virtuais são ligadas através de um olhar e de um portal, o tráfego entre as redes virtuais flui através da configuração de observação, em vez da porta de entrada.
- Os clientes VPN ponto-a-site devem ser descarregados novamente depois de o peering de rede virtual ter sido configurado com sucesso para garantir que as novas rotas são descarregadas para o cliente.
- Existe uma cobrança nominal para o tráfego de entrada e de saída que utilize um peering de rede virtual. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Permissões

As contas que utiliza para trabalhar com o epeering de rede virtual devem ser atribuídas às seguintes funções:

- [Colaborador de Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): Para uma rede virtual implementada através do Gestor de Recursos.
- [Classic Network Contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): Para uma rede virtual implementada através do modelo de implantação clássico.

Se a sua conta não for atribuída a uma das funções anteriores, deve ser atribuída a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que lhe seja atribuída as ações necessárias a partir da tabela seguinte:

| Ação                                                          | Nome |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Necessário para criar um peering da rede virtual A para a rede virtual B. A rede virtual A deve ser uma rede virtual (Gestor de Recursos)          |
| Microsoft.Network/virtualNetworks/peer/action                   | Necessário para criar um peering da rede virtual B (Gestor de Recursos) para a rede virtual A                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | Necessário para criar um peering da rede virtual B (clássica) para a rede virtual A                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Leia uma rede virtual de espreitar   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Eliminar um olhar de rede virtual |

## <a name="next-steps"></a>Passos seguintes

- É criado um peering de rede virtual entre redes virtuais criadas com o mesmo ou com diferentes modelos de implementação que existam na mesma ou em diferentes subscrições. Conclua um tutorial para um dos cenários seguintes:

  |Modelo de implementação do Azure             | Subscrição  |
  |---------                          |---------|
  |Ambas com Resource Manager              |[Mesma](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Diferente](create-peering-different-subscriptions.md)|
  |Uma com Resource Manager, outra com clássica  |[Mesma](create-peering-different-deployment-models.md)|
  |                                   |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

- Saiba como criar uma [topologia de rede hub and spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Criar uma rede virtual de peering usando scripts de amostra [PowerShell](powershell-samples.md) ou [Azure CLI,](cli-samples.md) ou usando [modelos](template-samples.md) de Gestor de Recursos Azure
- Criar e aplicar [a política azure](policy-samples.md) para redes virtuais
