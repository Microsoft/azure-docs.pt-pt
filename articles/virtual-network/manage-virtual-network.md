---
title: Criar, alterar ou eliminar uma rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba como criar, alterar ou eliminar uma rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: jdial
ms.openlocfilehash: c1bbfbbdc2fd98ef3637ec021038a9f68cc8fb40
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820625"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Criar, alterar ou eliminar uma rede virtual

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Saiba como criar e eliminar uma rede virtual e alterar as definições, como servidores DNS e espaços de endereços IP para uma rede virtual existente. Se estiver familiarizado com redes virtuais, pode saber mais sobre os mesmos no [descrição geral de rede Virtual](virtual-networks-overview.md) ou ao preencher um [tutorial](quick-create-portal.md). Redes virtuais contenham sub-redes. Para saber como criar, alterar e eliminar sub-redes, veja [gerir sub-redes](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir os passos em qualquer secção deste artigo:

- Se ainda não tiver uma conta do Azure, inscreva-se para uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se utilizar o portal, abra https://portal.azure.come inicie sessão com a sua conta do Azure.
- Se utilizar comandos do PowerShell para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/powershell), ou ao executar o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o Azure PowerShell versão 1.0.0 do módulo ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comandos (CLI) do Azure para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/bash), ou ao executar a CLI do seu computador. Este tutorial requer a versão 2.0.31 CLI do Azure ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar a CLI do Azure localmente, terá também de executar `az login` para criar uma ligação com o Azure.
- A conta iniciar sessão no, ou ligar ao Azure, tem de ser atribuída para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atribuída as ações adequadas listadas na [permissões ](#permissions).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Selecione **+ criar um recurso** > **rede** > **rede Virtual**.
2. Introduza ou selecione os valores para as seguintes definições e selecione **criar**:
   - **Nome**: O nome tem de ser exclusivo no [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) que selecionar para criar a rede virtual no. Não é possível alterar o nome depois de criar a rede virtual. Pode criar várias redes virtuais ao longo do tempo. Para sugestões de nomenclatura, veja [convenções de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). Ao seguir uma convenção de nomenclatura pode ajudar a tornar mais fácil de gerir várias redes virtuais.
   - **Espaço de endereços**: O espaço de endereços para uma rede virtual é composto por um ou mais sem sobreposição intervalos de endereços que estão especificados na notação CIDR. Define o intervalo de endereços pode ser públicas ou privadas (RFC 1918). Se definir o intervalo de endereços como público ou privado, o intervalo de endereços é acessível apenas a partir de dentro da rede virtual, de redes virtuais interligadas e de quaisquer redes no local que tiver ligado à rede virtual. Não é possível adicionar os seguintes intervalos de endereços:
     - 224.0.0.0/4 (Multicast)
     - 255.255.255.255/32 (difusão)
     - 127.0.0.0/8 (Loopback)
     - 169.254.0.0/16 (Link-local)
     - 168.63.129.16/32 (Balanceador de carga do Azure, DHCP e DNS interno [sonda de estado de funcionamento](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

     Embora pode definir o intervalo de endereços apenas um ao criar a rede virtual, pode adicionar mais intervalos de endereços ao espaço de endereços, depois de criar a rede virtual. Para saber como adicionar um intervalo de endereços para uma rede virtual existente, veja [adicionar ou remover um intervalo de endereços](#add-or-remove-an-address-range).

     >[!WARNING]
     >Se uma rede virtual tem intervalos de endereços se sobrepõe a outra rede virtual ou rede no local, não não possível ligar duas redes. Antes de definir um intervalo de endereços, considere se poderá ligar a rede virtual a outras redes virtuais ou redes no local no futuro.
     >
     >

    - **Nome da sub-rede**: O nome da sub-rede tem de ser exclusivo dentro da rede virtual. Não é possível alterar o nome da sub-rede depois de criar a sub-rede. O portal requer que defina uma sub-rede quando criar uma rede virtual, mesmo que uma rede virtual não é necessária ter quaisquer sub-redes. No portal, pode definir apenas uma sub-rede quando criar uma rede virtual. Pode adicionar mais sub-redes para a rede virtual mais tarde, depois de criar a rede virtual. Para adicionar uma sub-rede a uma rede virtual, consulte [gerir sub-redes](virtual-network-manage-subnet.md). Pode criar uma rede virtual que tem várias sub-redes com a CLI do Azure ou o PowerShell.

      >[!TIP]
      >Às vezes, os administradores criar sub-redes diferentes para filtrar ou controlar o encaminhamento de tráfego entre as sub-redes. Antes de definir sub-redes, considere como pode deseja filtrar e encaminhar o tráfego entre as sub-redes. Para saber mais sobre a filtragem de tráfego entre sub-redes, veja [grupos de segurança de rede](security-overview.md). Azure automaticamente encaminha o tráfego entre sub-redes, mas pode substituir as rotas predefinidas do Azure. Para saber mais sobre o encaminhamento de tráfego do Azures padrão sub-rede, veja [descrição geral do encaminhamento](virtual-networks-udr-overview.md).
      >

    - **Intervalo de endereços da sub-rede**: O intervalo tem de ser dentro do espaço de endereço que introduziu para a rede virtual. O intervalo mais pequeno que pode especificar é /29, que fornece oito endereços IP para a sub-rede. O Azure reserva o endereço do primeiro e último em cada sub-rede para conformidade com o protocolo. Três endereços adicionais estão reservados para utilização do serviço do Azure. Como resultado, uma rede virtual com um intervalo de endereços da sub-rede de/29 tem apenas três endereços IP utilizáveis. Se planear ligar uma rede virtual a um gateway VPN, tem de criar uma sub-rede de gateway. Saiba mais sobre [considerações de intervalo de endereço específico para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Pode alterar o intervalo de endereços depois de criar a sub-rede, em condições específicas. Para saber como alterar um intervalo de endereços da sub-rede, veja [gerir sub-redes](virtual-network-manage-subnet.md).
    - **Subscrição**: Selecione um [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Não é possível utilizar a mesma rede virtual em mais do que uma subscrição do Azure. No entanto, pode ligar uma rede virtual de uma subscrição para redes virtuais em outras subscrições com [peering de rede virtual](virtual-network-peering-overview.md). Qualquer recurso do Azure que se liga à rede virtual tem de ser na mesma subscrição que a rede virtual.
    - **Grupo de recursos**: Selecione um existente [grupo de recursos](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) ou criar um novo. Um recurso do Azure que se liga à rede virtual pode estar no mesmo grupo de recursos que a rede virtual ou num grupo de recursos diferentes.
    - **Localização**: Selecione do Azure [localização](https://azure.microsoft.com/regions/), também conhecido como uma região. Uma rede virtual pode estar em apenas uma localização do Azure. No entanto, pode ligar uma rede virtual num único local a uma rede virtual em outro local com um gateway VPN. Qualquer recurso do Azure que se liga à rede virtual tem de ser na mesma localização que a rede virtual.

**Comandos**

- CLI do Azure: [criar az rede vnet](/cli/azure/network/vnet)
- PowerShell: [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Vista de redes virtuais e as definições

1. Na caixa de pesquisa na parte superior do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecer nos resultados da pesquisa, selecione-o.
2. Na lista de redes virtuais, selecione a rede virtual que pretende ver as definições para.
3. As seguintes definições estão listadas para a rede virtual que selecionou:
   - **Descrição geral**: Fornece informações sobre a rede virtual, incluindo o espaço de endereços e servidores DNS. Captura de ecrã seguinte mostra as definições de descrição geral para uma rede virtual denominada **MyVNet**:

     ![Descrição geral da interface de rede](./media/manage-virtual-network/vnet-overview.png)

     Pode mover uma rede virtual para um grupo diferente de subscrição ou ao recurso, selecionando **alteração** junto a **grupo de recursos** ou **nome da subscrição**. Para saber como mover uma rede virtual, veja [mover recursos para um grupo de recursos diferente ou uma subscrição](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). O artigo apresenta uma lista de pré-requisitos e como mover recursos com o portal do Azure, PowerShell e CLI do Azure. Tem de mover todos os recursos que estão ligados à rede virtual com a rede virtual.
   - **Espaço de endereços**: Os espaços de endereços que estão atribuídos à rede virtual estão listados. Para saber como adicionar e remover um intervalo de endereços ao espaço de endereços, conclua os passos em [adicionar ou remover um intervalo de endereços](#add-or-remove-an-address-range).
   - **Dispositivos ligados**: São listados todos os recursos que estão ligados à rede virtual. Captura de ecrã anterior, três interfaces de rede e um balanceador de carga estão ligados à rede virtual. São listados todos os novos recursos que criar e ligar à rede virtual. Se eliminar um recurso que foi ligado à rede virtual, ele já não está a aparecer na lista.
   - **Sub-redes**: É apresentada uma lista de sub-redes que existe dentro da rede virtual. Para saber como adicionar e remover uma sub-rede, veja [gerir sub-redes](virtual-network-manage-subnet.md).
   - **Servidores DNS**: Pode especificar se o servidor DNS interno do Azure ou um servidor DNS personalizado fornece resolução de nomes para os dispositivos que estão ligados à rede virtual. Quando cria uma rede virtual com o portal do Azure, os servidores DNS do Azure são utilizados para resolução de nomes numa rede virtual, por predefinição. Para modificar os servidores DNS, conclua os passos na [servidores DNS de alteração](#change-dns-servers) neste artigo.
   - **Peerings**: Se existirem peerings existentes na subscrição, estes são apresentados aqui. Pode ver as definições para peerings existente, ou criar, alterar ou eliminar peerings. Para saber mais sobre os peerings, veja [peering de rede Virtual](virtual-network-peering-overview.md).
   - **Propriedades**: Apresenta as definições sobre a rede virtual, incluindo o ID de recurso da rede virtual e a subscrição do Azure está a ser.
   - **Diagrama**: O diagrama fornece uma representação visual de todos os dispositivos que estão ligadas à rede virtual. O diagrama tem algumas informações chave sobre os dispositivos. Para gerir um dispositivo nesta vista, no diagrama, selecione o dispositivo.
   - **Definições comuns de Azure**: Para saber mais sobre as definições do Azure comuns, consulte as seguintes informações:
     - [Registo de atividades](../azure-monitor/platform/activity-logs-overview.md)
     - [Controlo de acesso (IAM)](../role-based-access-control/overview.md)
     - [Etiquetas](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Bloqueios](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Script de automação](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Comandos**

- CLI do Azure: [show de vnet de rede de az](/cli/azure/network/vnet#az_network_vnet_show)
- PowerShell: [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Adicionar ou remover um intervalo de endereços

Pode adicionar e remover os intervalos de endereços para uma rede virtual. Um intervalo de endereços tem de ser especificado na notação CIDR e não pode sobrepor-se com outros intervalos de endereços dentro da mesma rede virtual. Os intervalos de endereços que é definir podem ser públicas ou privadas (RFC 1918). Se definir o intervalo de endereços como público ou privado, o intervalo de endereços é acessível apenas a partir de dentro da rede virtual, de redes virtuais interligadas e de quaisquer redes no local que tiver ligado à rede virtual. 

Se não tiver quaisquer sub-redes associados a ele, pode diminuir o intervalo de endereços para uma rede virtual. Caso contrário, só pode prolongar o intervalo de endereços, por exemplo, alterar um /16 para /8. Pode começar com um intervalo de endereços pequenos e, em seguida, expandi-lo mais tarde ou adicionar intervalos de adicionais.

<!-- the last two sentences above are added per GitHub issue https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

Não é possível adicionar os seguintes intervalos de endereços:

- 224.0.0.0/4 (Multicast)
- 255.255.255.255/32 (difusão)
- 127.0.0.0/8 (Loopback)
- 169.254.0.0/16 (Link-local)
- 168.63.129.16/32 (Balanceador de carga do Azure, DHCP e DNS interno [sonda de estado de funcionamento](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

Para adicionar ou remover um intervalo de endereços:

1. Na caixa de pesquisa na parte superior do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecer nos resultados da pesquisa, selecione-o.
2. Na lista de redes virtuais, selecione a rede virtual para o qual pretende adicionar ou remover um intervalo de endereços.
3. Selecione **espaço de endereços**, em **definições**.
4. Realize uma das seguintes opções:
    - **Adicionar um intervalo de endereços**: Introduza o novo intervalo de endereços. O intervalo de endereços não pode sobrepor-se com um intervalo de endereços existente que está definido para a rede virtual.
    - **Remover um intervalo de endereços**: À direita do intervalo de endereços que pretende remover, selecione **...** , em seguida, selecione **remover**. Se existe uma sub-rede no intervalo de endereços, não é possível remover o intervalo de endereços. Para remover um intervalo de endereços, tem primeiro de eliminar quaisquer sub-redes (e todos os recursos nas sub-redes) que existe no intervalo de endereços.
5. Selecione **Guardar**.

**Comandos**

- CLI do Azure: [atualização de vnet de rede de az](/cli/azure/network/vnet)
- PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>Servidores DNS de alteração

Todas as VMs que estão ligadas para o registro de rede virtual com os servidores DNS que especificar para a rede virtual. Também utilizam o servidor DNS especificado para resolução de nomes. Cada interface de rede (NIC) numa VM pode ter suas próprias definições de servidor DNS. Se um NIC tem suas próprias definições de servidor DNS, elas substituirão as definições do servidor DNS para a rede virtual. Para saber mais sobre as definições de DNS de NIC, consulte [tarefas de interface e as definições de rede](virtual-network-network-interface.md#change-dns-servers). Para saber mais sobre a resolução de nomes para VMs e instâncias da função nos serviços Cloud do Azure, veja [resolução de nomes para VMs e instâncias de função](virtual-networks-name-resolution-for-vms-and-role-instances.md). Para adicionar, alterar ou remover um servidor DNS:

1. Na caixa de pesquisa na parte superior do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecer nos resultados da pesquisa, selecione-o.
2. Na lista de redes virtuais, selecione a rede virtual para o qual pretende alterar servidores DNS para.
3. Selecione **servidores DNS**, em **definições**.
4. Selecione uma das seguintes opções:
   - **Predefinição (fornecida pelo Azure)**: Todos os nomes de recursos e endereços IP privados são automaticamente registrados para os servidores de DNS do Azure. Pode resolver nomes entre todos os recursos que estão ligados à mesma rede virtual. Não é possível utilizar esta opção para resolver nomes através de redes virtuais. Para resolver nomes através de redes virtuais, tem de utilizar um servidor DNS personalizado.
   - **Custom**: Pode adicionar um ou mais servidores, até ao limite do Azure para uma rede virtual. Para saber mais sobre os limites do servidor DNS, consulte [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Existem as seguintes opções:
   - **Adicionar um endereço**: Adiciona o servidor à sua lista de servidores DNS de rede virtual. Esta opção também registra o servidor DNS com o Azure. Se já tiver registrado um servidor DNS com o Azure, pode selecionar esse servidor DNS na lista.
   - **Remover um endereço**: Ao lado do servidor que pretende remover, selecione **...** , em seguida, **remover**. A eliminação do servidor remove o servidor apenas a partir desta lista de rede virtual. O servidor DNS permanece registado no Azure para as suas outras redes virtuais para utilizar.
   - **Reordenar os endereços do servidor DNS**: É importante verificar que lista os servidores DNS na ordem correta para o seu ambiente. Listas de servidor DNS são utilizadas pela ordem que estão especificadas. Eles não funcionam como uma configuração de round robin. Se o primeiro servidor DNS na lista pode ser contatado, o cliente utiliza esse servidor DNS, independentemente se o servidor DNS está a funcionar corretamente. Remova todos os servidores DNS que estão listados e, em seguida, adicioná-las na ordem em que desejar.
   - **Alterar um endereço**: Realce o servidor DNS na lista e, em seguida, introduza o novo endereço.
5. Selecione **Guardar**.
6. Reinicie as VMs que estão ligadas à rede virtual, pelo que são atribuídas as definições do servidor DNS novo. As VMs continuam a utilizar as definições de DNS atuais até que eles são reiniciados.

**Comandos**

- CLI do Azure: [atualização de vnet de rede de az](/cli/azure/network/vnet)
- PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Eliminar uma rede virtual

Pode eliminar uma rede virtual apenas se não houver nenhum recurso conectado a ele. Se existirem recursos ligados a qualquer sub-rede na rede virtual, tem primeiro de eliminar os recursos que estão ligados a todas as sub-redes na rede virtual. Os passos que efetuar para eliminar um recurso variam dependendo do recurso. Para saber como eliminar recursos que estão ligados a sub-redes, leia a documentação para cada tipo de recurso que pretende eliminar. Para eliminar uma rede virtual:

1. Na caixa de pesquisa na parte superior do portal, introduza *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecer nos resultados da pesquisa, selecione-o.
2. Na lista de redes virtuais, selecione a rede virtual que pretende eliminar.
3. Confirme que não há nenhum dispositivo ligado à rede virtual, selecionando **dispositivos ligados**, em **definições**. Se existirem dispositivos ligados, tem de eliminá-los antes de poder eliminar a rede virtual. Se não existirem não existem dispositivos ligados, selecione **descrição geral**.
4. Selecione **Eliminar**.
5. Para confirmar a eliminação da rede virtual, selecione **Sim**.

**Comandos**

- CLI do Azure: [eliminar vnet de rede do azure](/cli/azure/network/vnet#az_network_vnet_delete)
- PowerShell: [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Permissões

Para executar tarefas em redes virtuais, deve ser atribuída a sua conta para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) função que é atribuída as ações adequadas listadas na tabela a seguir:

| Ação                                  |   Name                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   Ler uma rede virtual              |
|Microsoft.Network/virtualNetworks/write  |   Criar ou atualizar uma rede virtual  |
|Microsoft.Network/virtualNetworks/delete |   Eliminar uma rede virtual            |

## <a name="next-steps"></a>Passos Seguintes

- Criar uma rede virtual com [PowerShell](powershell-samples.md) ou [CLI do Azure](cli-samples.md) scripts ou utilizar o Azure de exemplo [modelos do Resource Manager](template-samples.md)
- Criar e aplicar [o Azure policy](policy-samples.md) para redes virtuais
