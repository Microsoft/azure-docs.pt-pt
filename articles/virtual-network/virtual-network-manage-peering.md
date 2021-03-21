---
title: Criar, alterar ou eliminar uma rede virtual Azure que espreita | Microsoft Docs
description: Criar, alterar ou eliminar um olhar de rede virtual. Com o olhar de rede virtual, conecta-se redes virtuais na mesma região e em regiões.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: altambaw
ms.openlocfilehash: dc8db3f1eccce2bb85f03d51fcfd1c4113823d49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222671"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Criar, alterar ou eliminar uma rede virtual de espreitar

Aprenda a criar, alterar ou apagar um olhar de rede virtual. O espreitamento de redes virtuais permite-lhe ligar redes virtuais na mesma região e em regiões (também conhecidas como Global VNet Peering) através da rede de espinha dorsal Azure. Uma vez espreitadas, as redes virtuais ainda são geridas como recursos separados. Se é novo na rede virtual a espreitar, pode saber mais sobre isso na rede virtual que espreita a [visão geral](virtual-network-peering-overview.md) ou completando um [tutorial](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Complete as seguintes tarefas antes de completar etapas em qualquer secção deste artigo:

- Se ainda não tem uma conta Azure, inscreva-se para uma [conta de teste gratuita.](https://azure.microsoft.com/free)
- Se utilizar o portal, abra https://portal.azure.com e faça login com uma conta que tenha as [permissões necessárias](#permissions) para trabalhar com os seus pares.
- Se utilizar comandos PowerShell para completar tarefas neste artigo, ou executa os comandos na [Azure Cloud Shell](https://shell.azure.com/powershell), ou executando o PowerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer a versão 1.0 ou mais tarde do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar o PowerShell localmente, também precisa de executar `Connect-AzAccount` com uma conta que tenha as permissões necessárias para trabalhar com o seu [espreitamento,](#permissions) para criar uma ligação com o Azure.
- Se utilizar os comandos da interface da linha de comando do Azure (CLI) para completar as tarefas neste artigo, ou executar os comandos na [Azure Cloud Shell](https://shell.azure.com/bash), ou executando o CLI a partir do seu computador. Este tutorial requer a versão Azure CLI 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI localmente, também precisa de executar `az login` com uma conta que tenha as permissões necessárias para trabalhar com o seu [espreitamento,](#permissions) para criar uma ligação com a Azure.

A conta em que inicia sessão, ou liga-se ao Azure, deve ser atribuída à [função de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída às ações [apropriadas listadas](#permissions)nas Permissões .

## <a name="create-a-peering"></a>Criar um olhar

Antes de criar um espreitamento, familiarize-se com os requisitos e constrangimentos e [permissões necessárias.](#permissions)

1. Na caixa de pesquisa no topo do portal Azure, introduza *redes virtuais* na caixa de pesquisa. Quando **as redes virtuais** aparecerem nos resultados da pesquisa, selecione-as. Não selecione **redes Virtuais (clássicas)** se aparecer na lista, uma vez que não é possível criar um espreitamento a partir de uma rede virtual implantada através do modelo de implementação clássico.
2. Selecione a rede virtual na lista pela quais pretende criar um espreitamento.
3. Em **DEFINIÇÕES**, **selecione Peerings**.
4. Selecione **+ Adicionar**. 
5. <a name="add-peering"></a>Introduza ou selecione valores para as seguintes definições:
    - **Nome:** O nome para o espreitamento deve ser único dentro da rede virtual.
    - **Modelo de implementação de rede virtual:** Selecione qual o modelo de implementação com que a rede virtual que pretende espreitar foi implementado através.
    - **Conheço a minha identidade de recurso:** Se leu o acesso à rede virtual com a que pretende espreitar, deixe esta caixa de verificação desmarcada. Se não tiver lido acesso à rede virtual ou subscrição com a qual pretende espreitar, verifique esta caixa. Introduza o ID completo da rede virtual com a que pretende espreitar na caixa **de identificação** de recursos que apareceu quando verificou a caixa. O ID de recursos que inseriu deve ser para uma rede virtual que exista na mesma, ou [suportado diferente](#requirements-and-constraints) [região](https://azure.microsoft.com/regions) de Azure como esta rede virtual. O ID completo do recurso parece semelhante a `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>` . Pode obter o ID de recurso para uma rede virtual visualizando as propriedades de uma rede virtual. Para aprender a visualizar as propriedades de uma rede virtual, consulte [Gerir redes virtuais.](manage-virtual-network.md#view-virtual-networks-and-settings) Se a subscrição estiver associada a um inquilino diferente do Azure Ative Directory do que a subscrição com a rede virtual a que está a criar o seu espreitamento, adicione primeiro um utilizador de cada inquilino como [um utilizador convidado](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) no inquilino oposto.
    - **Assinatura:** Selecione a [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) da rede virtual com a quais pretende espreitar. Uma ou mais subscrições estão listadas, dependendo de quantas subscrições a sua conta leu o acesso. Se verificou a caixa de verificação **de identificação** de recursos, esta definição não está disponível.
    - **Rede virtual:** Selecione a rede virtual com a quais deseja espreitar. Pode selecionar uma rede virtual criada através de um modelo de implementação Azure. Se quiser selecionar uma rede virtual numa região diferente, tem de selecionar uma rede virtual numa [região suportada.](#cross-region) Deve ter lido o acesso à rede virtual para que ficasse visível na lista. Se uma rede virtual estiver listada, mas acinzentada, pode ser porque o espaço de endereço da rede virtual se sobrepõe ao espaço de endereço desta rede virtual. Se os espaços de endereço de rede virtual se sobreporem, não podem ser espreitados. Se verificou a caixa de verificação **de identificação** de recursos, esta definição não está disponível.
    - **Permitir o acesso à rede virtual:** Selecione **Ativado** (predefinido) se pretender ativar a comunicação entre as duas redes virtuais através do fluxo predefinido. `VirtualNetwork` Permitir a comunicação entre redes virtuais permite que os recursos ligados a qualquer uma das redes virtuais se comuniquem entre si com a mesma largura de banda e latência como se estivessem ligados à mesma rede virtual. Toda a comunicação entre recursos nas duas redes virtuais é sobre a rede privada Azure. A etiqueta de serviço **VirtualNetwork** para grupos de segurança de rede engloba a rede virtual e a rede virtual esprevada quando esta definição é **Ativada**. (Para saber mais sobre tags de serviço de grupo de segurança de rede, consulte [a visão geral dos grupos de segurança da rede](./network-security-groups-overview.md#service-tags).) **Selecione Desativado** se não quiser que o tráfego flua para a rede virtual esprevada por padrão. Pode selecionar **Desativado** se tiver espreitado uma rede virtual com outra rede virtual, mas ocasionalmente pretende desativar o fluxo de tráfego padrão entre as duas redes virtuais. Pode achar que permitir/desativar é mais conveniente do que eliminar e recriar os seus pares. Quando esta definição é desativada, o tráfego não flui entre as redes virtuais espreitadas por padrão; no entanto, o tráfego pode ainda fluir se for explicitamente permitido através de uma regra [de grupo de segurança de rede](./network-security-groups-overview.md) que inclua os endereços IP apropriados ou grupos de segurança de aplicações.
      > [!WARNING]
      > Desativar a definição **de acesso de rede virtual apenas** altera a definição da etiqueta de serviço **VirtualNetwork.** *Não* impede totalmente o fluxo de tráfego através da ligação entre pares, como explicado nesta descrição de definição.    
    - **Permitir o tráfego reencaminhado:** Verifique esta caixa para permitir que o tráfego *reencaminhado* por um aparelho virtual de rede numa rede virtual (que não tenha origem na rede virtual) flua para esta rede virtual através de um espreitamento. Por exemplo, considere três redes virtuais chamadas Spoke1, Spoke2 e Hub. Existe um espreitamento entre cada rede virtual falada e a rede virtual Hub, mas os seus pares não existem entre as redes virtuais faladas. Um aparelho virtual de rede é implantado na rede virtual Hub, e as rotas definidas pelo utilizador são aplicadas a cada rede virtual falada que encaminha o tráfego entre as sub-redes através do aparelho virtual de rede. Se esta caixa de verificação não for verificada para o espreitamento entre cada rede virtual falada e a rede virtual do hub, o tráfego não flui entre as redes virtuais faladas porque o hub não está a encaminhar o tráfego entre as redes virtuais. Ao mesmo tempo que permite que esta capacidade permita o tráfego reencaminhado através do espreitamento, não cria rotas definidas pelo utilizador ou aparelhos virtuais de rede. As rotas definidas pelo utilizador e os aparelhos virtuais de rede são criados separadamente. Saiba mais [sobre as rotas definidas pelo utilizador.](virtual-networks-udr-overview.md#user-defined) Não é necessário verificar esta definição se o tráfego é reencaminhado entre redes virtuais através de um Gateway Azure VPN.
    - **Permitir o trânsito de gateway:** Verifique esta caixa se tem um gateway de rede virtual ligado a esta rede virtual e quer permitir que o tráfego da rede virtual espreitada flua através do gateway. Por exemplo, esta rede virtual pode ser anexada a uma rede no local através de um gateway de rede virtual. O portal pode ser um portal ExpressRoute ou VPN. A verificação desta caixa permite que o tráfego da rede virtual esprevada flua através do gateway ligado a esta rede virtual para a rede no local. Se verificar esta caixa, a rede virtual esprevada não pode ter um gateway configurado. A rede virtual esprevada deve ter a caixa de verificação **de gateways remotos** de utilização verificada ao configurar o espreitamento da outra rede virtual para esta rede virtual. Se deixar esta caixa desmarcada (padrão), o tráfego da rede virtual esprevada ainda flui para esta rede virtual, mas não pode fluir através de um gateway de rede virtual ligado a esta rede virtual. Se o espreitamento estiver entre uma rede virtual (Gestor de Recursos) e uma rede virtual (clássica), o gateway deve estar na rede virtual (Gestor de Recursos).

       Além de encaminhar o tráfego para uma rede no local, um gateway VPN pode avançar o tráfego de rede entre redes virtuais que são espreitadas com a rede virtual onde o gateway está, sem que as redes virtuais precisem de ser espreitadas umas com as outras. A utilização de uma porta de entrada VPN para encaminhar o tráfego é útil quando pretende utilizar uma porta de entrada VPN num hub (veja o hub e o exemplo falado descrito para **permitir o tráfego reencaminhado**) rede virtual para encaminhar o tráfego entre redes virtuais faladas que não são espreitadas umas com as outras. Para saber mais sobre a utilização de uma porta de entrada para o trânsito, consulte [configurar uma porta de entrada VPN para o trânsito numa rede virtual que espreita.](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Este cenário requer a implementação de rotas definidas pelo utilizador que especifiquem o gateway de rede virtual como o próximo tipo de lúpulo. Saiba mais [sobre as rotas definidas pelo utilizador.](virtual-networks-udr-overview.md#user-defined) Só pode especificar um gateway VPN como um próximo tipo de lúpulo numa rota definida pelo utilizador, não é possível especificar um gateway ExpressRoute como o próximo tipo de lúpulo numa rota definida pelo utilizador.

    - **Utilize gateways remotos:** Verifique esta caixa para permitir que o tráfego desta rede virtual flua através de um gateway de rede virtual ligado à rede virtual com a qual está a espreitar. Por exemplo, a rede virtual com a qual está a espreitar tem um gateway VPN ligado que permite a comunicação a uma rede no local.  A verificação desta caixa permite que o tráfego desta rede virtual flua através do gateway VPN ligado à rede virtual. Se verificar esta caixa, a rede virtual esprevada deve ter um gateway de rede virtual ligado à sua e deve ter a caixa de verificação **de trânsito de gateway de acesso** verificada. Se deixar esta caixa desmarcada (padrão), o tráfego da rede virtual perspetivo ainda pode fluir para esta rede virtual, mas não pode fluir através de um gateway de rede virtual ligado a esta rede virtual.
    
      Apenas um que espreitar para esta rede virtual pode ter esta definição ativada.

      Não pode utilizar gateways remotos se já tiver um gateway configurado na sua rede virtual. Para saber mais sobre a utilização de um portal para o trânsito, consulte [configurar uma porta de entrada VPN para o trânsito numa rede virtual que espreita](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        
    > [!NOTE]
    > Se utilizar um Gateway de Rede Virtual para enviar o tráfego no local transitivamente para um VNet espremiado, a gama IP VNet esprevada para o dispositivo VPN no local deve ser configurada para tráfego "interessante". Caso contrário, os seus recursos no local não serão capazes de comunicar com recursos no VNet espreitado.

6. Selecione **OK** para adicionar o espreitamento à rede virtual selecionada.

Para instruções passo a passo para a implementação de observação entre redes virtuais em diferentes subscrições e modelos de implantação, consulte [os próximos passos](#next-steps).

### <a name="commands"></a>Comandos

- **Azure CLI**: [az rede vnet peering create](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Ver ou alterar definições de observação

Antes de mudar um espreitamento, familiarize-se com os requisitos e constrangimentos e [permissões necessárias.](#permissions)

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **as redes virtuais** aparecerem nos resultados da pesquisa, selecione-as. Não selecione **redes Virtuais (clássicas)** se aparecer na lista, uma vez que não é possível criar um espreitamento a partir de uma rede virtual implantada através do modelo de implementação clássico.
2. Selecione a rede virtual na lista para a quais pretende alterar as definições de espreitar.
3. Em **DEFINIÇÕES**, **selecione Peerings**.
4. Selecione o que pretende visualizar ou alterar as definições para.
5. Mude a definição apropriada. Leia sobre as opções para cada definição no [passo 5](#add-peering) de Criar um espreitamento.
6. Selecione **Guardar**.

**Comandos**

- **Azure CLI**: [lista de observação de vnet de rede az](/cli/azure/network/vnet/peering) para listar os espreiteiros de uma rede virtual, [az network vnet peering show](/cli/azure/network/vnet/peering) para mostrar configurações para um esprevamento específico, e a [az rede vnet peering update](/cli/azure/network/vnet/peering) para alterar definições de peering.|
- **PowerShell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) para recuperar definições de observação e [Set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) para alterar definições.

## <a name="delete-a-peering"></a>Apagar um espreitante

Antes de apagar um espreitamento, certifique-se de que a sua conta tem as [permissões necessárias](#permissions).

Quando um espreitante é eliminado, o tráfego de uma rede virtual já não flui para a rede virtual espreitada. Quando as redes virtuais implementadas através do Resource Manager são espreitadas, cada rede virtual tem um olhar para a outra rede virtual. Embora a eliminação do espreitamento de uma rede virtual desative a comunicação entre as redes virtuais, não elimina o espreitamento da outra rede virtual. O estado de observação do espreitamento que existe na outra rede virtual é **desligado**. Não é possível recriar o espreitante até recriar o espreitamento na primeira rede virtual e o estado de espreitamento para ambas as alterações de redes virtuais para *Connected*.

Se quiser que as redes virtuais se comuniquem às vezes, mas nem sempre, em vez de eliminar um espreitamento, pode definir a definição **de acesso virtual** de rede virtual a **Desativado.** Para saber como, leia o passo 6 do Create a peering section deste artigo. Pode achar mais fácil o acesso à rede e facilitar o acesso à rede do que eliminar e recriar os seus pares.

1. Na caixa de pesquisa no topo do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **as redes virtuais** aparecerem nos resultados da pesquisa, selecione-as. Não selecione **redes Virtuais (clássicas)** se aparecer na lista, uma vez que não é possível criar um espreitamento a partir de uma rede virtual implantada através do modelo de implementação clássico.
2. Selecione a rede virtual na lista pela quais pretende eliminar um espreitê-lo.
3. Em **DEFINIÇÕES**, **selecione Peerings**.
4. No lado direito do espreitamento pretende eliminar, selecione... selecione **Delete**, em seguida, selecione **Sim** para eliminar o espreitante da primeira rede virtual. 
5. Complete os passos anteriores para eliminar o espreitamento da outra rede virtual no espreitamento.

**Comandos**

- **Azure CLI**: [az rede vnet peering delete](/cli/azure/network/vnet/peering)
- **PowerShell**: [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Requisitos e limitações

- <a name="cross-region"></a>Você pode espreitar redes virtuais na mesma região, ou em diferentes regiões. O perspeto das redes virtuais em diferentes regiões também é referido como *Global VNet Peering*. 
- Ao criar um perspeção global, as redes virtuais espreitadas podem existir em qualquer região de nuvem pública de Azure ou regiões de nuvem da China ou regiões de nuvem do Governo. Não se pode olhar através das nuvens. Por exemplo, um VNet em nuvem pública Azure não pode ser espreitado para um VNet na nuvem Azure China.
- Os recursos numa rede virtual não conseguem comunicar com o endereço IP de front-end de um balanceador de carga interno Básico numa rede virtual de peering global. O suporte para Balanceadores de Carga Básicos existe apenas na mesma região. O suporte para Balanceadores de Carga Standard existe para VNet Peering e VNet Peering Global. Os serviços que utilizam um balanceador de carga básico que não funcionará sobre o Global VNet Peering são documentados [aqui.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- Você pode usar gateways remotos ou permitir o trânsito de gateway em redes virtuais globalmente espreitadas e redes virtuais com vista local.
- As redes virtuais podem estar na mesma, ou subscrições diferentes. Ao espreitar redes virtuais em diferentes subscrições, ambas as subscrições podem ser associadas ao mesmo ou diferente inquilino do Azure Ative Directory. Se ainda não tem um inquilino da AD, pode [criar um.](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant)
- As redes virtuais que você peer deve ter espaços de endereço IP não sobrepostos.
- Não é possível adicionar intervalos de endereços ou eliminar intervalos de endereços a partir do espaço de endereços de uma rede virtual uma vez que uma rede virtual é espreitada com outra rede virtual. Para adicionar ou remover os intervalos de endereços, elimine os pontos de observação, adicione ou remova as gamas de endereços e, em seguida, re-crie o espreitante. Para adicionar intervalos de endereços ou remover intervalos de endereços de redes virtuais, consulte [Gerir redes virtuais](manage-virtual-network.md).
- Pode consultar duas redes virtuais implementadas através do Resource Manager ou uma rede virtual implantada através do Resource Manager com uma rede virtual implantada através do modelo de implementação clássico. Não é possível espreitar duas redes virtuais criadas através do modelo clássico de implementação. Se não está familiarizado com os modelos de implementação da Azure, leia o artigo dos [modelos de implementação Understand Azure.](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Pode utilizar um [Gateway de VPN](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para ligar duas redes virtuais criadas através do modelo de implementação clássica.
- Ao configurar o peering de duas redes virtuais criadas através do Resource Manager, tem de ser configurado um peering para cada rede virtual no peering. Vê-se um dos seguintes tipos para o estado de observação: 
  - *Iniciado:* Quando cria o espreitamento para a segunda rede virtual a partir da primeira rede virtual, o estado de observação é *iniciado*. 
  - *Ligado:* Quando cria o espreitamento da segunda rede virtual para a primeira rede virtual, o seu estado de observação está *ligado*. Se visualizar o estado de observação da primeira rede virtual, vê o seu estado alterado de *Iniciado* para *Conectado*. O espreitamento não é estabelecido com sucesso até que o estado de observação de ambos os espreiteiros de rede virtuais esteja *ligado*.
- Ao espreitar uma rede virtual criada através do Resource Manager com uma rede virtual criada através do modelo de implementação clássico, basta configurar um espremiamento para a rede virtual implantada através do Resource Manager. Não é possível configurar olhando para uma rede virtual (clássica), ou entre duas redes virtuais implantadas através do modelo de implementação clássico. Quando cria o espreitamento da rede virtual (Gestor de Recursos) para a rede virtual (Classic), o estado de observação é *atualizado,* em seguida, altera-se em breve para *Connected*.
- Um espreitante é estabelecido entre duas redes virtuais. Os seus olhares por si só não são transitórios. Se criar espreitamentos entre:
  - VirtualNetwork1 & VirtualNetwork2 - VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3 - VirtualNetwork2 & VirtualNetwork3


  Não existe um espreitamento entre virtualNetwork1 e VirtualNetwork3 através da VirtualNetwork2. Se pretender criar uma rede virtual entre VirtualNetwork1 e VirtualNetwork3, tem de criar um espreitamento entre virtualNetwork1 e VirtualNetwork3. Não existe um espreitamento entre virtualNetwork1 e VirtualNetwork3 através da VirtualNetwork2. Se pretender que a VirtualNetwork1 e a VirtualNetwork3 se comuniquem diretamente, tem de criar um espremia explícito entre virtualNetwork1 e VirtualNetwork3 ou passar por um NVA na rede Hub.  
- Não é possível resolver nomes em redes virtuais com vista a uma resolução de nomeS Azure padrão. Para resolver nomes noutras redes virtuais, tem de utilizar [o Azure DNS para domínios privados](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou um servidor DNS personalizado. Para aprender a configurar o seu próprio servidor DNS, consulte [a resolução Name utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Os recursos em redes virtuais espreitadas na mesma região podem comunicar entre si com a mesma largura de banda e latência como se estivessem na mesma rede virtual. No entanto, cada tamanho de máquina virtual tem a sua própria largura de banda de rede máxima. Para saber mais sobre a largura de banda máxima da rede para diferentes tamanhos de máquinas virtuais, consulte os tamanhos de máquina virtual [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux.](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Uma rede virtual pode ser espreitada para outra rede virtual, e também estar ligada a outra rede virtual com um gateway de rede virtual Azure. Quando as redes virtuais são ligadas através de um espreitante e de um gateway, o tráfego entre as redes virtuais flui através da configuração de espreitar, em vez do gateway.
- Os clientes VPN ponto-a-local devem ser novamente descarregados depois de o esparso de rede virtual ter sido configurado com sucesso para garantir que as novas rotas são descarregadas para o cliente.
- Existe uma cobrança nominal para o tráfego de entrada e de saída que utilize um peering de rede virtual. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Permissões

As contas que utiliza para trabalhar com o espreguite de rede virtual devem ser atribuídas às seguintes funções:

- [Colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): Para uma rede virtual implantada através do Gestor de Recursos.
- [Classic Network Contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): Para uma rede virtual implantada através do modelo clássico de implementação.

Se a sua conta não for atribuída a uma das funções anteriores, deve ser atribuída a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída a partir do quadro seguinte:

| Ação                                                          | Name |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Necessário para criar um espreitamento da rede virtual A para a rede virtual B. A rede virtual A deve ser uma rede virtual (Gestor de Recursos)          |
| Microsoft.Network/virtualNetworks/peer/action                   | Necessário para criar um espreitamento da rede virtual B (Gestor de Recursos) para a rede virtual A                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | Necessário para criar um espreitamento da rede virtual B (clássico) para a rede virtual A                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Leia uma rede virtual a espreitar   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Excluir um olhar de rede virtual |

## <a name="next-steps"></a>Passos seguintes

- É criado um peering de rede virtual entre redes virtuais criadas com o mesmo ou com diferentes modelos de implementação que existam na mesma ou em diferentes subscrições. Conclua um tutorial para um dos cenários seguintes:

  |Modelo de implementação do Azure             | Subscrição  |
  |---------                          |---------|
  |Ambas com Resource Manager              |[Mesma](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Diferente](create-peering-different-subscriptions.md)|
  |Uma com Resource Manager, outra com clássica  |[Mesma](create-peering-different-deployment-models.md)|
  |                                   |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

- Saiba como criar uma [topologia de rede hub and spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Crie uma rede virtual que esprete usando scripts de [amostras powerShell](powershell-samples.md) ou [Azure CLI,](cli-samples.md) ou usando [modelos de Gestor de Recursos](template-samples.md) Azure
- Criar e atribuir [definições de Política Azure](./policy-reference.md) para redes virtuais