---
title: Criar, alterar ou excluir um emparelhamento de rede virtual do Azure | Microsoft Docs
description: Saiba como criar, alterar ou excluir um emparelhamento de rede virtual.
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978360"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Criar, alterar ou excluir um emparelhamento de rede virtual

Saiba como criar, alterar ou excluir um emparelhamento de rede virtual. O emparelhamento de rede virtual permite que você conecte as redes virtuais na mesma região e entre regiões (também conhecidas como emparelhamento de VNet global) por meio da rede de backbone do Azure. Uma vez emparelhadas, as redes virtuais ainda são gerenciadas como recursos separados. Se você for novo no emparelhamento de rede virtual, poderá aprender mais sobre ele na [visão geral de emparelhamento de rede virtual](virtual-network-peering-overview.md) ou concluindo um [tutorial](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Se você ainda não tiver uma conta do Azure, Inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o portal, abra https://portal.azure.com e faça logon com uma conta que tenha as [permissões necessárias](#permissions) para trabalhar com emparelhamentos.
- Se estiver usando comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/powershell)ou executando o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o módulo Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar `Connect-AzAccount` com uma conta que tenha as [permissões necessárias](#permissions) para trabalhar com o emparelhamento, para criar uma conexão com o Azure.
- Se você estiver usando comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/bash)ou executando a CLI do seu computador. Este tutorial requer o CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se você estiver executando o CLI do Azure localmente, também precisará executar `az login` com uma conta que tenha as [permissões necessárias](#permissions) para trabalhar com o emparelhamento, para criar uma conexão com o Azure.

A conta que você faz logon ou conecta-se ao Azure com o, deve ser atribuída à função [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas em [permissões](#permissions).

## <a name="create-a-peering"></a>Criar um emparelhamento

Antes de criar um emparelhamento, familiarize-se com os requisitos e restrições e [as permissões necessárias](#permissions).

1. Na caixa de pesquisa na parte superior da portal do Azure, insira *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecem nos resultados da pesquisa, selecione-as. Não selecione **redes virtuais (clássicas)** se ela aparecer na lista, pois você não pode criar um emparelhamento de uma rede virtual implantada por meio do modelo de implantação clássico.
2. Selecione a rede virtual na lista para a qual você deseja criar um emparelhamento.
3. Em **configurações**, selecione **emparelhamentos**.
4. Selecione **+ Adicionar**. 
5. <a name="add-peering"></a>Insira ou selecione valores para as seguintes configurações:
    - **Nome:** O nome do emparelhamento deve ser exclusivo na rede virtual.
    - **Modelo de implantação de rede virtual:** Selecione a qual modelo de implantação a rede virtual com a qual você deseja emparelhar foi implantada.
    - **Sei minha ID de recurso:** Se você tiver acesso de leitura à rede virtual com a qual deseja emparelhar, deixe essa caixa de seleção desmarcada. Se você não tiver acesso de leitura à rede virtual ou à assinatura com a qual deseja emparelhar, marque esta caixa. Insira a ID de recurso completa da rede virtual com a qual você deseja emparelhar na caixa **ID de recurso** exibida quando você marcou a caixa. A ID do recurso que você inserir deve ser para uma rede virtual que exista na mesma [região](https://azure.microsoft.com/regions) do Azure ou [com suporte diferente](#requirements-and-constraints) para essa rede virtual. A ID de recurso completo é semelhante a `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>`. Você pode obter a ID de recurso para uma rede virtual exibindo as propriedades de uma rede virtual. Para saber como exibir as propriedades de uma rede virtual, consulte [gerenciar redes virtuais](manage-virtual-network.md#view-virtual-networks-and-settings). Se a assinatura estiver associada a um locatário de Azure Active Directory diferente do que a assinatura com a rede virtual da qual você está criando o emparelhamento, primeiro adicione um usuário de cada locatário como um [usuário convidado](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) no locatário oposto.
    - **Assinatura:** Selecione a [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) da rede virtual com a qual você deseja emparelhar. Uma ou mais assinaturas estão listadas, dependendo de quantas assinaturas sua conta tem acesso de leitura. Se você marcou a caixa de seleção **ID do recurso** , essa configuração não estará disponível.
    - **Rede virtual:** Selecione a rede virtual com a qual você deseja emparelhar. Você pode selecionar uma rede virtual criada por meio de um modelo de implantação do Azure. Se você quiser selecionar uma rede virtual em uma região diferente, deverá selecionar uma rede virtual em uma [região com suporte](#cross-region). Você deve ter acesso de leitura à rede virtual para que ela fique visível na lista. Se uma rede virtual estiver listada, mas esmaecida, pode ser porque o espaço de endereço para a rede virtual se sobrepõe ao espaço de endereço para essa rede virtual. Se os espaços de endereço de rede virtual se sobrepõem, eles não podem ser emparelhados. Se você marcou a caixa de seleção **ID do recurso** , essa configuração não estará disponível.
    - **Permitir acesso à rede virtual:** Selecione **habilitado** (padrão) se desejar habilitar a comunicação entre as duas redes virtuais. Habilitar a comunicação entre redes virtuais permite que os recursos conectados a uma rede virtual se comuniquem entre si com a mesma largura de banda e latência como se estivessem conectados à mesma rede virtual. Toda a comunicação entre os recursos nas duas redes virtuais é por meio da rede privada do Azure. A marca de serviço do **VirtualNetwork** para grupos de segurança de rede abrange a rede virtual e a rede virtual emparelhada. Para saber mais sobre as marcas de serviço do grupo de segurança de rede, consulte [visão geral dos grupos de segurança de rede](security-overview.md#service-tags). Selecione **desabilitado** se você não quiser que o tráfego flua para a rede virtual emparelhada. Você poderá selecionar **desabilitado** se tiver emparelhado uma rede virtual com outra rede virtual, mas, ocasionalmente, quiser desabilitar o fluxo de tráfego entre as duas redes virtuais. Você pode achar que habilitar/desabilitar é mais conveniente do que excluir e recriar emparelhamentos. Quando essa configuração é desabilitada, o tráfego não flui entre as redes virtuais emparelhadas.
    - **Permitir tráfego encaminhado:** Marque esta caixa para permitir que o tráfego *encaminhado* por uma solução de virtualização de rede em uma rede virtual (que não originou da rede virtual) flua para essa rede virtual por meio de um emparelhamento. Por exemplo, considere três redes virtuais chamadas Spoke1, Spoke2 e Hub. Existe um emparelhamento entre cada rede virtual do spoke e a rede virtual do Hub, mas os emparelhamentos não existem entre as redes virtuais do spoke. Uma solução de virtualização de rede é implantada na rede virtual do Hub, e as rotas definidas pelo usuário são aplicadas a cada rede virtual spoke que roteia o tráfego entre as sub-redes por meio da solução de virtualização de rede. Se essa caixa de seleção não for verificada para o emparelhamento entre cada rede virtual do spoke e a rede virtual do Hub, o tráfego não fluirá entre as redes virtuais do spoke porque o Hub não está encaminhando o tráfego entre as redes virtuais. Embora a habilitação desse recurso permita o tráfego encaminhado por meio do emparelhamento, ele não cria nenhuma rota definida pelo usuário nem soluções de virtualização de rede. As rotas definidas pelo usuário e as soluções de virtualização de rede são criadas separadamente. Saiba mais sobre [rotas definidas pelo usuário](virtual-networks-udr-overview.md#user-defined). Você não precisará verificar essa configuração se o tráfego for encaminhado entre redes virtuais por meio de um gateway de VPN do Azure.
    - **Permitir trânsito de gateway:** Marque esta caixa se você tiver um gateway de rede virtual conectado a essa rede virtual e quiser permitir que o tráfego da rede virtual emparelhada flua pelo Gateway. Por exemplo, essa rede virtual pode ser anexada a uma rede local por meio de um gateway de rede virtual. O gateway pode ser um gateway de ExpressRoute ou de VPN. Marcar essa caixa permite que o tráfego da rede virtual emparelhada flua pelo gateway anexado a essa rede virtual para a rede local. Se você marcar essa caixa, a rede virtual emparelhada não poderá ter um gateway configurado. A rede virtual emparelhada deve ter a caixa de seleção **usar gateways remotos** marcada ao configurar o emparelhamento de outra rede virtual para esta rede virtual. Se você deixar essa caixa desmarcada (padrão), o tráfego da rede virtual emparelhada ainda fluirá para essa rede virtual, mas não poderá fluir por um gateway de rede virtual anexado a essa rede virtual. Se o emparelhamento estiver entre uma rede virtual (Resource Manager) e uma rede virtual (clássica), o gateway deverá estar na rede virtual (Resource Manager).

       Além de encaminhar o tráfego para uma rede local, um gateway de VPN pode encaminhar o tráfego de rede entre redes virtuais emparelhadas com a rede virtual em que o gateway está, sem que as redes virtuais precisem ser emparelhadas entre si. Usar um gateway de VPN para encaminhar o tráfego é útil quando você deseja usar um gateway de VPN em um Hub (consulte o exemplo de Hub e spoke descrito para **permitir o tráfego encaminhado**) para rotear o tráfego entre redes virtuais spoke que não são emparelhadas entre si. Para saber mais sobre como permitir o uso de um gateway para trânsito, consulte [configurar um gateway de VPN para trânsito em um emparelhamento de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Esse cenário requer a implementação de rotas definidas pelo usuário que especificam o gateway de rede virtual como o tipo do próximo salto. Saiba mais sobre [rotas definidas pelo usuário](virtual-networks-udr-overview.md#user-defined). Você só pode especificar um gateway de VPN como um tipo do próximo salto em uma rota definida pelo usuário, não pode especificar um gateway do ExpressRoute como o tipo do próximo salto em uma rota definida pelo usuário.

    - **Usar gateways remotos:** Marque esta caixa para permitir que o tráfego dessa rede virtual flua por meio de um gateway de rede virtual conectado à rede virtual com a qual você está emparelhando. Por exemplo, a rede virtual com a qual você está emparelhando tem um gateway de VPN conectado que permite a comunicação com uma rede local.  Marcar essa caixa permite que o tráfego dessa rede virtual flua por meio do gateway de VPN conectado à rede virtual emparelhada. Se você marcar essa caixa, a rede virtual emparelhada deverá ter um gateway de rede virtual anexado e deverá ter a caixa de seleção **Permitir trânsito de gateway** marcada. Se você deixar essa caixa desmarcada (padrão), o tráfego da rede virtual emparelhada ainda poderá fluir para essa rede virtual, mas não poderá fluir por um gateway de rede virtual anexado a essa rede virtual.
    Somente um emparelhamento para essa rede virtual pode ter essa configuração habilitada.

        Você não poderá usar gateways remotos se já tiver um gateway configurado em sua rede virtual. Para saber mais sobre como usar um gateway para trânsito, consulte [configurar um gateway de VPN para trânsito em um emparelhamento de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

6. Selecione **OK** para adicionar o emparelhamento à rede virtual selecionada.

Para obter as instruções passo a passo para implementar o emparelhamento entre redes virtuais em diferentes assinaturas e modelos de implantação, consulte [as próximas etapas](#next-steps).

### <a name="commands"></a>Comandos

- **CLI do Azure**: [AZ Network vnet emparelhamento criar](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Exibir ou alterar as configurações de emparelhamento

Antes de alterar um emparelhamento, familiarize-se com os requisitos e restrições e [as permissões necessárias](#permissions).

1. Na caixa de pesquisa na parte superior do portal, insira *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecem nos resultados da pesquisa, selecione-as. Não selecione **redes virtuais (clássicas)** se ela aparecer na lista, pois você não pode criar um emparelhamento de uma rede virtual implantada por meio do modelo de implantação clássico.
2. Selecione a rede virtual na lista para a qual você deseja alterar as configurações de emparelhamento.
3. Em **configurações**, selecione **emparelhamentos**.
4. Selecione o emparelhamento que você deseja exibir ou alterar as configurações.
5. Altere a configuração apropriada. Leia sobre as opções para cada configuração na [etapa 5](#add-peering) de criar um emparelhamento.
6. Selecione **Guardar**.

**Comandos**

- **CLI do Azure**: [AZ Network vnet emparelhamento List](/cli/azure/network/vnet/peering) para listar emparelhamentos para uma rede virtual, [AZ Network vnet emparelhamento](/cli/azure/network/vnet/peering) para mostrar as configurações de um emparelhamento específico e [AZ Network vnet peering Update](/cli/azure/network/vnet/peering) para alterar as configurações de emparelhamento. |
- **PowerShell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) para recuperar as configurações de emparelhamento de exibição e [set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) para alterar as configurações.

## <a name="delete-a-peering"></a>Excluir um emparelhamento

Antes de excluir um emparelhamento, verifique se sua conta tem as [permissões necessárias](#permissions).

Quando um emparelhamento é excluído, o tráfego de uma rede virtual não flui mais para a rede virtual emparelhada. Quando as redes virtuais implantadas por meio do Resource Manager são emparelhadas, cada rede virtual tem um emparelhamento para a outra rede virtual. Embora a exclusão do emparelhamento de uma rede virtual desabilite a comunicação entre as redes virtuais, ela não exclui o emparelhamento de outra rede virtual. O status de emparelhamento para o emparelhamento que existe na outra rede virtual é **desconectado**. Você não pode recriar o emparelhamento até recriar o emparelhamento na primeira rede virtual e o status de emparelhamento para ambas as redes virtuais mudarem para *conectado*.

Se você quiser que as redes virtuais se comuniquem às vezes, mas nem sempre, em vez de excluir um emparelhamento, você pode definir a configuração **permitir acesso à rede virtual** como **desabilitada** . Para saber como, leia a etapa 6 da seção criar um emparelhamento deste artigo. Você pode achar mais fácil desabilitar e habilitar o acesso à rede do que excluir e recriar emparelhamentos.

1. Na caixa de pesquisa na parte superior do portal, insira *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecem nos resultados da pesquisa, selecione-as. Não selecione **redes virtuais (clássicas)** se ela aparecer na lista, pois você não pode criar um emparelhamento de uma rede virtual implantada por meio do modelo de implantação clássico.
2. Selecione a rede virtual na lista para a qual você deseja excluir um emparelhamento.
3. Em **configurações**, selecione **emparelhamentos**.
4. No lado direito do emparelhamento que você deseja excluir, selecione **...** , selecione **excluir**e, em seguida, selecione **Sim** para excluir o emparelhamento da primeira rede virtual.
5. Conclua as etapas anteriores para excluir o emparelhamento de outra rede virtual no emparelhamento.

**Comandos**

- **CLI do Azure**: [AZ Network vnet emparelhamento Delete](/cli/azure/network/vnet/peering)
- **PowerShell**: [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Requisitos e limitações

- <a name="cross-region"></a>Você pode emparelhar redes virtuais na mesma região ou em regiões diferentes. O emparelhamento de redes virtuais em regiões diferentes também é conhecido como *emparelhamento VNet global*. 
- Ao criar um emparelhamento global, as redes virtuais emparelhadas podem existir em qualquer região da nuvem pública do Azure ou regiões de nuvem da China ou regiões de nuvem do governo. Você não pode emparelhar entre nuvens. Por exemplo, uma VNet na nuvem pública do Azure não pode ser emparelhada com uma VNet na nuvem do Azure na China.
- Os recursos numa rede virtual não conseguem comunicar com o endereço IP de front-end de um balanceador de carga interno Básico numa rede virtual de peering global. O suporte para Balanceadores de Carga Básicos existe apenas na mesma região. O suporte para Balanceadores de Carga Standard existe para VNet Peering e VNet Peering Global. Os serviços que usam um balanceador de carga básico que não funcionará em emparelhamento VNet global são documentados [aqui.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- Você pode usar gateways remotos ou permitir o trânsito de gateway em redes virtuais emparelhadas globalmente e em redes virtuais emparelhadas localmente.
- As redes virtuais podem estar na mesma assinatura ou em assinaturas diferentes. Quando configurar o peering de redes virtuais em diferentes subscrições, ambas as subscrições podem ser associadas ao inquilino do Azure Active Directory idêntica ou diferente. Se você ainda não tiver um locatário do AD, poderá [criar um](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant). O suporte para emparelhamento entre redes virtuais de assinaturas associadas a diferentes locatários de Azure Active Directory não está disponível no Portal. Você pode usar a CLI, o PowerShell ou os modelos.
- As redes virtuais que você emparelhar devem ter espaços de endereço IP não sobrepostos.
- Você não pode adicionar intervalos de endereços ou excluir intervalos de endereços do espaço de endereço de uma rede virtual depois que uma rede virtual estiver emparelhada com outra rede virtual. Para adicionar ou remover intervalos de endereços, exclua o emparelhamento, adicione ou remova os intervalos de endereços e recrie o emparelhamento. Para adicionar intervalos de endereços ou remover intervalos de endereços de redes virtuais, consulte [gerenciar redes virtuais](manage-virtual-network.md).
- Você pode emparelhar duas redes virtuais implantadas por meio do Resource Manager ou de uma rede virtual implantada por meio do Resource Manager com uma rede virtual implantada por meio do modelo de implantação clássico. Você não pode emparelhar duas redes virtuais criadas por meio do modelo de implantação clássico. Se você não estiver familiarizado com os modelos de implantação do Azure, leia o artigo [entender os modelos de implantação do Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Pode utilizar um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para ligar duas redes virtuais criadas através do modelo de implementação clássica.
- Ao configurar o peering de duas redes virtuais criadas através do Resource Manager, tem de ser configurado um peering para cada rede virtual no peering. Você verá um dos seguintes tipos de status de emparelhamento: 
  - *Iniciado em:* Quando você cria o emparelhamento para a segunda rede virtual da primeira rede virtual, o status de emparelhamento é *iniciado*. 
  - *Conectado:* Quando você cria o emparelhamento da segunda rede virtual para a primeira rede virtual, seu status de emparelhamento é *conectado*. Se você exibir o status de emparelhamento para a primeira rede virtual, verá seu status alterado de *iniciado* para *conectado*. O emparelhamento não é estabelecido com êxito até que o status de emparelhamento de ambos os emparelhamentos de rede virtual esteja *conectado*.
- Ao emparelhar uma rede virtual criada por meio do Resource Manager com uma rede virtual criada por meio do modelo de implantação clássico, você só configura um emparelhamento para a rede virtual implantada por meio do Resource Manager. Você não pode configurar o emparelhamento para uma rede virtual (clássica) ou entre duas redes virtuais implantadas por meio do modelo de implantação clássico. Quando você cria o emparelhamento da rede virtual (Resource Manager) para a rede virtual (clássica), o status de emparelhamento é *atualizando*e, em seguida, muda para *conectado*.
- Um emparelhamento é estabelecido entre duas redes virtuais. Os emparelhamentos não são transitivos. Se você criar emparelhamentos entre:
  - VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3

  Não há nenhum emparelhamento entre VirtualNetwork1 e VirtualNetwork3 por meio de VirtualNetwork2. Se você quiser criar um emparelhamento de rede virtual entre VirtualNetwork1 e VirtualNetwork3, você precisará criar um emparelhamento entre VirtualNetwork1 e VirtualNetwork3.
- Você não pode resolver nomes em redes virtuais emparelhadas usando a resolução de nomes do Azure padrão. Para resolver nomes em outras redes virtuais, você deve usar o [DNS do Azure para domínios privados](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou um servidor DNS personalizado. Para saber como configurar seu próprio servidor DNS, consulte resolução de [nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Os recursos nas redes virtuais emparelhadas na mesma região podem se comunicar entre si com a mesma largura de banda e latência como se estivessem na mesma rede virtual. No entanto, cada tamanho de máquina virtual tem sua própria largura de banda de rede máxima. Para saber mais sobre a largura de banda de rede máxima para tamanhos de máquina virtual diferentes, confira tamanhos de máquina virtual do [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .
- Uma rede virtual pode ser emparelhada a outra rede virtual e também estar conectada a outra rede virtual com um gateway de rede virtual do Azure. Quando as redes virtuais são conectadas por meio de emparelhamento e de um gateway, o tráfego entre as redes virtuais flui por meio da configuração de emparelhamento, em vez do gateway.
- Os clientes VPN ponto a site devem ser baixados novamente depois que o emparelhamento de rede virtual tiver sido configurado com êxito para garantir que as novas rotas sejam baixadas no cliente.
- Existe uma cobrança nominal para o tráfego de entrada e de saída que utilize um peering de rede virtual. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Permissões

As contas usadas para trabalhar com o emparelhamento de rede virtual devem ser atribuídas às seguintes funções:

- [Colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): para uma rede virtual implantada por meio do Gerenciador de recursos.
- [Colaborador de rede clássico](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): para uma rede virtual implantada por meio do modelo de implantação clássico.

Se sua conta não estiver atribuída a uma das funções anteriores, ela deverá ser atribuída a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações necessárias da tabela a seguir:

| Ação                                                          | Nome |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Necessário para criar um emparelhamento da rede virtual a para a rede virtual B. A rede virtual A deve ser uma rede virtual (Resource Manager)          |
| Microsoft.Network/virtualNetworks/peer/action                   | Necessário para criar um emparelhamento da rede virtual B (Resource Manager) para a rede virtual A                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | Necessário para criar um emparelhamento da rede virtual B (clássico) para a rede virtual A                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Ler um emparelhamento de rede virtual   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Excluir um emparelhamento de rede virtual |

## <a name="next-steps"></a>Passos seguintes

- É criado um peering de rede virtual entre redes virtuais criadas com o mesmo ou com diferentes modelos de implementação que existam na mesma ou em diferentes subscrições. Conclua um tutorial para um dos cenários seguintes:

  |Modelo de implementação do Azure             | Subscrição  |
  |---------                          |---------|
  |Ambas com Resource Manager              |[Mesma](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Diferente](create-peering-different-subscriptions.md)|
  |Uma com Resource Manager, outra com clássica  |[Mesma](create-peering-different-deployment-models.md)|
  |                                   |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

- Saiba como criar uma [topologia de rede hub and spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Criar um emparelhamento de rede virtual usando o [PowerShell](powershell-samples.md) ou [CLI do Azure](cli-samples.md) scripts de exemplo ou usando modelos do Azure [Resource Manager](template-samples.md)
- Criar e aplicar a [política do Azure](policy-samples.md) para redes virtuais
