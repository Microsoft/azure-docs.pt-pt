---
title: Criar, alterar ou excluir uma rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba como criar, alterar ou excluir uma rede virtual no Azure.
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
ms.openlocfilehash: 87cdd3d18fe0e6a3655c8bbc940cdc00fa211e74
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750587"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Criar, alterar ou eliminar uma rede virtual

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Saiba como criar e excluir uma rede virtual e alterar as configurações, como servidores DNS e espaços de endereço IP, para uma rede virtual existente. Se você for novo em redes virtuais, poderá aprender mais sobre elas na [visão geral da rede virtual](virtual-networks-overview.md) ou concluindo um [tutorial](quick-create-portal.md). Uma rede virtual contém sub-redes. Para saber como criar, alterar e excluir sub-redes, consulte [gerenciar sub-redes](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Se você ainda não tiver uma conta do Azure, Inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o portal, abra https://portal.azure.com e faça logon com sua conta do Azure.
- Se estiver usando comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/powershell)ou executando o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o módulo Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se você estiver usando comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/bash)ou executando a CLI do seu computador. Este tutorial requer o CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se você estiver executando o CLI do Azure localmente, também precisará executar `az login` para criar uma conexão com o Azure.
- A conta que você faz logon ou conecta-se ao Azure com o, deve ser atribuída à função [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas em [permissões](#permissions).

## <a name="create-a-virtual-network"></a>Criar rede virtual

1. Selecione **+ criar um recurso** > **rede** > **rede virtual**.
2. Insira ou selecione valores para as seguintes configurações e, em seguida, selecione **criar**:
   - **Nome**: o nome deve ser exclusivo no [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) em que você selecionar para criar a rede virtual. Você não pode alterar o nome depois que a rede virtual é criada. Você pode criar várias redes virtuais ao longo do tempo. Para obter sugestões de nomenclatura, consulte [convenções de nomenclatura](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources). Seguir uma Convenção de nomenclatura pode ajudar a facilitar o gerenciamento de várias redes virtuais.
   - **Espaço de endereço**: o espaço de endereço para uma rede virtual é composto por um ou mais intervalos de endereços não sobrepostos que são especificados na notação CIDR. O intervalo de endereços definido pode ser público ou privado (RFC 1918). Independentemente de você definir o intervalo de endereços como público ou privado, o intervalo de endereços pode ser acessado somente de dentro da rede virtual, de redes virtuais interconectadas e de qualquer rede local que você tenha conectado à rede virtual. Você não pode adicionar os seguintes intervalos de endereços:
     - 224.0.0.0/4 (multicast)
     - 255.255.255.255/32 (difusão)
     - 127.0.0.0/8 (loopback)
     - 169.254.0.0/16 (link-local)
     - 168.63.129.16/32 (DNS interno, DHCP e Azure Load Balancer [investigação de integridade](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

     Embora você possa definir apenas um intervalo de endereços ao criar a rede virtual no portal, é possível adicionar mais intervalos de endereços ao espaço de endereço depois que a rede virtual é criada. Para saber como adicionar um intervalo de endereços a uma rede virtual existente, consulte [Adicionar ou remover um intervalo de endereços](#add-or-remove-an-address-range).

     >[!WARNING]
     >Se uma rede virtual tiver intervalos de endereços que se sobrepõem a outra rede virtual ou rede local, as duas redes não poderão ser conectadas. Antes de definir um intervalo de endereços, considere se você pode querer conectar a rede virtual a outras redes virtuais ou redes locais no futuro.
     >
     >

     - **Nome da sub-rede**: o nome da sub-rede deve ser exclusivo na rede virtual. Você não pode alterar o nome da sub-rede depois que a sub-rede é criada. O portal requer que você defina uma sub-rede ao criar uma rede virtual, mesmo que uma rede virtual não precise ter sub-redes. No portal, você pode definir apenas uma sub-rede ao criar uma rede virtual. Você pode adicionar mais sub-redes à rede virtual posteriormente, depois que a rede virtual é criada. Para adicionar uma sub-rede a uma rede virtual, consulte [gerenciar sub-redes](virtual-network-manage-subnet.md). Você pode criar uma rede virtual que tenha várias sub-redes usando o CLI do Azure ou o PowerShell.

       >[!TIP]
       >Às vezes, os administradores criam sub-redes diferentes para filtrar ou controlar o roteamento de tráfego entre as sub-redes. Antes de definir sub-redes, considere como você pode querer filtrar e rotear o tráfego entre as sub-redes. Para saber mais sobre como filtrar o tráfego entre sub-redes, confira [grupos de segurança de rede](security-overview.md). O Azure roteia automaticamente o tráfego entre sub-redes, mas você pode substituir as rotas padrão do Azure. Para saber mais sobre o roteamento de tráfego de sub-rede padrão do Azure, consulte [visão geral de roteamento](virtual-networks-udr-overview.md).
       >

     - **Intervalo de endereços de sub-rede**: o intervalo deve estar dentro do espaço de endereço inserido para a rede virtual. O menor intervalo que você pode especificar é/29, que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e o último endereço em cada sub-rede para conformidade com o protocolo. Três endereços adicionais são reservados para o uso do serviço do Azure. Como resultado, uma rede virtual com um intervalo de endereços de sub-rede de/29 tem apenas três endereços IP utilizáveis. Se você planeja conectar uma rede virtual a um gateway de VPN, você deve criar uma sub-rede de gateway. Saiba mais sobre [Considerações específicas de intervalo de endereços para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Você pode alterar o intervalo de endereços depois que a sub-rede é criada, sob condições específicas. Para saber como alterar um intervalo de endereços de sub-rede, consulte [gerenciar sub-redes](virtual-network-manage-subnet.md).
     - **Assinatura**: selecione uma [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Você não pode usar a mesma rede virtual em mais de uma assinatura do Azure. No entanto, você pode conectar uma rede virtual em uma assinatura a redes virtuais em outras assinaturas com o [emparelhamento de rede virtual](virtual-network-peering-overview.md). Qualquer recurso do Azure que você conecta à rede virtual deve estar na mesma assinatura que a rede virtual.
     - **Grupo de recursos**: selecione um [grupo de recursos](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) existente ou crie um novo. Um recurso do Azure que você conecta à rede virtual pode estar no mesmo grupo de recursos que a rede virtual ou em um grupo de recursos diferente.
     - **Local**: selecione um [local](https://azure.microsoft.com/regions/)do Azure, também conhecido como uma região. Uma rede virtual pode estar em apenas um local do Azure. No entanto, você pode conectar uma rede virtual em um local a uma rede virtual em outro local usando um gateway de VPN. Qualquer recurso do Azure que você conecta à rede virtual deve estar no mesmo local que a rede virtual.

**Comandos**

- CLI do Azure: [AZ Network vnet Create](/cli/azure/network/vnet)
- PowerShell: [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Exibir redes virtuais e configurações

1. Na caixa de pesquisa na parte superior do portal, insira *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecem nos resultados da pesquisa, selecione-as.
2. Na lista de redes virtuais, selecione a rede virtual para a qual você deseja exibir as configurações.
3. As configurações a seguir são listadas para a rede virtual selecionada:
   - **Visão geral**: fornece informações sobre a rede virtual, incluindo o espaço de endereço e os servidores DNS. A captura de tela a seguir mostra as configurações de visão geral de uma rede virtual chamada **MyVNet**:

     ![Visão geral da interface de rede](./media/manage-virtual-network/vnet-overview.png)

     Você pode mover uma rede virtual para uma assinatura ou grupo de recursos diferente selecionando **alterar** ao lado de **grupo de recursos** ou **nome da assinatura**. Para saber como mover uma rede virtual, confira [mover recursos para um grupo de recursos ou uma assinatura diferente](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). O artigo lista os pré-requisitos e como mover recursos usando o portal do Azure, o PowerShell e o CLI do Azure. Todos os recursos que estão conectados à rede virtual devem ser movidos com a rede virtual.
   - **Espaço de endereço**: os espaços de endereço atribuídos à rede virtual são listados. Para saber como adicionar e remover um intervalo de endereços para o espaço de endereço, conclua as etapas em [Adicionar ou remover um intervalo de endereços](#add-or-remove-an-address-range).
   - **Dispositivos conectados**: todos os recursos que estão conectados à rede virtual são listados. Na captura de tela anterior, três interfaces de rede e um balanceador de carga estão conectados à rede virtual. Todos os novos recursos criados e conectados à rede virtual são listados. Se você excluir um recurso que estava conectado à rede virtual, ele não aparecerá mais na lista.
   - **Sub-redes**: uma lista de sub-redes que existem na rede virtual é mostrada. Para saber como adicionar e remover uma sub-rede, consulte [gerenciar sub-redes](virtual-network-manage-subnet.md).
   - **Servidores DNS**: você pode especificar se o servidor DNS interno do Azure ou um servidor DNS personalizado fornece resolução de nomes para dispositivos que estão conectados à rede virtual. Quando você cria uma rede virtual usando a portal do Azure, os servidores DNS do Azure são usados para a resolução de nomes em uma rede virtual, por padrão. Para modificar os servidores DNS, conclua as etapas em [alterar servidores DNS](#change-dns-servers) neste artigo.
   - **Emparelhamentos**: se houver emparelhamentos existentes na assinatura, eles serão listados aqui. Você pode exibir as configurações para emparelhamentos existentes ou criar, alterar ou excluir emparelhamentos. Para saber mais sobre emparelhamentos, consulte [emparelhamento de rede virtual](virtual-network-peering-overview.md).
   - **Propriedades**: exibe as configurações sobre a rede virtual, incluindo a ID de recurso da rede virtual e a assinatura do Azure em que ela está.
   - **Diagrama**: o diagrama fornece uma representação visual de todos os dispositivos que estão conectados à rede virtual. O diagrama tem algumas informações importantes sobre os dispositivos. Para gerenciar um dispositivo neste modo de exibição, no diagrama, selecione o dispositivo.
   - **Configurações comuns do Azure**: para saber mais sobre as configurações comuns do Azure, consulte as seguintes informações:
     - [Registo de atividades](../azure-monitor/platform/platform-logs-overview.md)
     - [Controle de acesso (IAM)](../role-based-access-control/overview.md)
     - [Etiquetas](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Bloquea](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Script de automação](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Comandos**

- CLI do Azure: [AZ Network vnet show](/cli/azure/network/vnet)
- PowerShell: [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Adicionar ou remover um intervalo de endereços

Você pode adicionar e remover intervalos de endereços para uma rede virtual. Um intervalo de endereços deve ser especificado na notação CIDR e não pode se sobrepor a outros intervalos de endereços na mesma rede virtual. Os intervalos de endereços que você definir podem ser públicos ou privados (RFC 1918). Independentemente de você definir o intervalo de endereços como público ou privado, o intervalo de endereços pode ser acessado somente de dentro da rede virtual, de redes virtuais interconectadas e de qualquer rede local que você tenha conectado à rede virtual. 

Você pode diminuir o intervalo de endereços para uma rede virtual, contanto que ela ainda inclua os intervalos de quaisquer sub-redes associadas. Além disso, você pode estender o intervalo de endereços, por exemplo, alterando um/16 para/8. 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

Você não pode adicionar os seguintes intervalos de endereços:

- 224.0.0.0/4 (multicast)
- 255.255.255.255/32 (difusão)
- 127.0.0.0/8 (loopback)
- 169.254.0.0/16 (link-local)
- 168.63.129.16/32 (DNS interno, DHCP e Azure Load Balancer [investigação de integridade](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

Para adicionar ou remover um intervalo de endereços:

1. Na caixa de pesquisa na parte superior do portal, insira *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecem nos resultados da pesquisa, selecione-as.
2. Na lista de redes virtuais, selecione a rede virtual para a qual você deseja adicionar ou remover um intervalo de endereços.
3. Selecione **espaço de endereço**, em **configurações**.
4. Conclua uma das seguintes opções:
    - **Adicionar um intervalo de endereços**: Insira o novo intervalo de endereços. O intervalo de endereços não pode se sobrepor a um intervalo de endereços existente definido para a rede virtual.
    - **Remova um intervalo de endereços**: à direita do intervalo de endereços que você deseja remover, selecione **...** e, em seguida, selecione **remover**. Se existir uma sub-rede no intervalo de endereços, você não poderá remover o intervalo de endereços. Para remover um intervalo de endereços, você deve primeiro excluir todas as sub-redes (e todos os recursos nas sub-redes) que existem no intervalo de endereços.
5. Selecione **Guardar**.

**Comandos**

- CLI do Azure: [AZ Network vnet Update](/cli/azure/network/vnet)
- PowerShell: [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>Alterar servidores DNS

Todas as VMs que estão conectadas à rede virtual se registram com os servidores DNS que você especifica para a rede virtual. Eles também usam o servidor DNS especificado para resolução de nomes. Cada NIC (interface de rede) em uma VM pode ter suas próprias configurações de servidor DNS. Se uma NIC tiver suas próprias configurações de servidor DNS, elas substituirão as configurações do servidor DNS para a rede virtual. Para saber mais sobre as configurações de DNS da NIC, consulte [configurações e tarefas de interface de rede](virtual-network-network-interface.md#change-dns-servers). Para saber mais sobre a resolução de nomes para VMs e instâncias de função nos serviços de nuvem do Azure, consulte [resolução de nomes para VMs e instâncias de função](virtual-networks-name-resolution-for-vms-and-role-instances.md). Para adicionar, alterar ou remover um servidor DNS:

1. Na caixa de pesquisa na parte superior do portal, insira *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecem nos resultados da pesquisa, selecione-as.
2. Na lista de redes virtuais, selecione a rede virtual para a qual você deseja alterar os servidores DNS.
3. Selecione **servidores DNS**, em **configurações**.
4. Selecione uma das seguintes opções:
   - **Padrão (fornecido pelo Azure)** : todos os nomes de recursos e endereços IP privados são registrados automaticamente nos servidores DNS do Azure. Você pode resolver nomes entre todos os recursos que estão conectados à mesma rede virtual. Você não pode usar essa opção para resolver nomes entre redes virtuais. Para resolver nomes entre redes virtuais, você deve usar um servidor DNS personalizado.
   - **Personalizado**: você pode adicionar um ou mais servidores, até o limite do Azure para uma rede virtual. Para saber mais sobre os limites do servidor DNS, consulte [limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Existem as seguintes opções:
   - **Adicionar um endereço**: Adiciona o servidor à sua lista de servidores DNS da rede virtual. Essa opção também registra o servidor DNS com o Azure. Se você já tiver registrado um servidor DNS com o Azure, poderá selecionar esse servidor DNS na lista.
   - **Remover um endereço**: ao lado do servidor que você deseja remover, selecione **...** e, em seguida, **remover**. A exclusão do servidor remove o servidor somente desta lista de redes virtuais. O servidor DNS permanece registrado no Azure para suas outras redes virtuais usarem.
   - **Reordenar endereços do servidor DNS**: é importante verificar se os servidores DNS estão listados na ordem correta para o seu ambiente. As listas de servidores DNS são usadas na ordem em que são especificadas. Eles não funcionam como uma configuração de Round Robin. Se o primeiro servidor DNS na lista puder ser alcançado, o cliente usará esse servidor DNS, independentemente de o servidor DNS estar funcionando corretamente. Remova todos os servidores DNS listados e, em seguida, adicione-os de volta na ordem desejada.
   - **Alterar um endereço**: realce o servidor DNS na lista e, em seguida, insira o novo endereço.
5. Selecione **Guardar**.
6. Reinicie as VMs que estão conectadas à rede virtual, para que elas recebam as novas configurações do servidor DNS. As VMs continuam a usar suas configurações atuais de DNS até que sejam reiniciadas.

**Comandos**

- CLI do Azure: [AZ Network vnet Update](/cli/azure/network/vnet)
- PowerShell: [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Excluir uma rede virtual

Você só poderá excluir uma rede virtual se não houver recursos conectados a ela. Se houver recursos conectados a qualquer sub-rede na rede virtual, você deverá primeiro excluir os recursos que estão conectados a todas as sub-redes na rede virtual. As etapas executadas para excluir um recurso variam de acordo com o recurso. Para saber como excluir recursos que estão conectados a sub-redes, leia a documentação de cada tipo de recurso que você deseja excluir. Para excluir uma rede virtual:

1. Na caixa de pesquisa na parte superior do portal, insira *redes virtuais* na caixa de pesquisa. Quando **redes virtuais** aparecem nos resultados da pesquisa, selecione-as.
2. Na lista de redes virtuais, selecione a rede virtual que você deseja excluir.
3. Confirme se não há dispositivos conectados à rede virtual selecionando **dispositivos conectados**, em **configurações**. Se houver dispositivos conectados, você deverá excluí-los antes de poder excluir a rede virtual. Se não houver nenhum dispositivo conectado, selecione **visão geral**.
4. Selecione **Eliminar**.
5. Para confirmar a exclusão da rede virtual, selecione **Sim**.

**Comandos**

- CLI do Azure: [exclusão de vnet de rede do Azure](/cli/azure/network/vnet)
- PowerShell: [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Permissões

Para executar tarefas em redes virtuais, sua conta deve ser atribuída à função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas na tabela a seguir:

| Ação                                  |   Nome                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   Ler uma rede virtual              |
|Microsoft.Network/virtualNetworks/write  |   Criar ou atualizar uma rede virtual  |
|Microsoft.Network/virtualNetworks/delete |   Excluir uma rede virtual            |

## <a name="next-steps"></a>Passos seguintes

- Criar uma rede virtual usando o [PowerShell](powershell-samples.md) ou [CLI do Azure](cli-samples.md) scripts de exemplo ou usando modelos do Azure [Resource Manager](template-samples.md)
- Criar e aplicar a [política do Azure](policy-samples.md) para redes virtuais
