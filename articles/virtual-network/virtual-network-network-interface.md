---
title: Criar, alterar ou eliminar uma interface de rede do Azure
titlesuffix: Azure Virtual Network
description: Saiba quais uma interface de rede é como criar, alterar as definições para e elimine um.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 37e1a308d63d782c6ff18c10a5874842341eeec7
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818107"
---
# <a name="create-change-or-delete-a-network-interface"></a>Criar, alterar ou eliminar uma interface de rede

Saiba como criar, alterar as definições e eliminar uma interface de rede. Uma interface de rede permite que a Máquina Virtual do Azure para comunicar com a internet, o Azure e recursos no local. Ao criar uma máquina virtual utilizando o portal do Azure, o portal cria uma interface de rede com as predefinições para. Em vez disso, pode optar por criar interfaces de rede com definições personalizadas e adicione uma ou mais interfaces de rede a uma máquina virtual quando a criar. Também poderá alterar as definições de interface de rede predefinido para uma interface de rede existente. Este artigo explica como criar uma interface de rede com definições personalizadas, alterar as definições existentes, como a atribuição de (grupo de segurança de rede) de filtro de rede, atribuição de sub-rede, definições do servidor DNS e reencaminhamento de IP e eliminar uma interface de rede.

Se precisa para adicionar, alterar ou remover endereços IP para uma interface de rede, consulte [endereços IP gerir](virtual-network-network-interface-addresses.md). Se precisar de adicionar interfaces de rede para ou remover interfaces de rede de máquinas virtuais, veja [adicionar ou remover interfaces de rede](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir os passos em qualquer secção deste artigo:

- Se ainda não tiver uma conta do Azure, inscreva-se para uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se utilizar o portal, abra https://portal.azure.come inicie sessão com a sua conta do Azure.
- Se utilizar comandos do PowerShell para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/powershell), ou ao executar o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o Azure PowerShell versão 1.0.0 do módulo ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comandos (CLI) do Azure para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/bash), ou ao executar a CLI do seu computador. Este tutorial requer a CLI do Azure versão 2.0.28 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar a CLI do Azure localmente, terá também de executar `az login` para criar uma ligação com o Azure.

A conta iniciar sessão no, ou ligar ao Azure, tem de ser atribuída para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atribuída as ações adequadas listadas na [permissões ](#permissions).

## <a name="create-a-network-interface"></a>Criar uma interface de rede

Ao criar uma máquina virtual utilizando o portal do Azure, o portal cria uma interface de rede com as predefinições para. Se em vez disso, deve especificar todas as definições de interface de rede, pode criar uma interface de rede com definições personalizadas e anexar a interface de rede a uma máquina virtual ao criar a máquina virtual (com o PowerShell ou a CLI do Azure). Também pode criar uma interface de rede e adicioná-lo a uma máquina virtual existente (com o PowerShell ou a CLI do Azure). Para saber como criar uma máquina virtual com uma interface de rede existente ou adicionar ou remover interfaces de rede de máquinas virtuais existentes, veja [adicionar ou remover interfaces de rede](virtual-network-network-interface-vm.md). Antes de criar uma interface de rede, tem de ter um existente [rede virtual](manage-virtual-network.md) na mesma localização e subscrição cria uma interface de rede no.

1. Na caixa que contém o texto *recursos de pesquisa* na parte superior do portal do Azure, escreva *interfaces de rede*. Quando **interfaces de rede** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione **+ adicionar** sob **interfaces de rede**.
3. Introduza, ou selecione os valores para as seguintes definições e selecione **criar**:

    |Definição|Necessário?|Detalhes|
    |---|---|---|
    |Name|Sim|O nome tem de ser exclusivo no grupo de recursos que selecionar. Ao longo do tempo, provavelmente, terá várias interfaces de rede na sua subscrição do Azure. Para obter sugestões durante a criação de uma convenção de nomenclatura facilitar o gerenciamento de várias interfaces de rede mais fácil, consulte [convenções de nomenclatura](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). O nome não pode ser alterado depois de criar a interface de rede.|
    |Rede virtual|Sim|Selecione a rede virtual para a interface de rede. Só pode atribuir uma interface de rede a uma rede virtual que exista na mesma subscrição e localização como a interface de rede. Depois de criar uma interface de rede, não é possível alterar a rede virtual que está atribuída a. A máquina virtual que é adicionar a interface de rede também tem de existir na mesma localização e subscrição como a interface de rede.|
    |Subrede|Sim|Selecione uma sub-rede dentro da rede virtual que selecionou. Pode alterar a sub-rede que a interface de rede é atribuída a depois de criado.|
    |Atribuição de endereços IP privados|Sim| Nessa configuração, estará a escolher o método de atribuição para o endereço IPv4. Escolha entre os seguintes métodos de atribuição: **Dinâmico:** Ao selecionar esta opção, o Azure atribui automaticamente o endereço seguinte disponível do espaço de endereço da sub-rede que selecionou. **Estático:** Ao selecionar esta opção, tem de atribuir manualmente um endereço IP disponível a partir do espaço de endereços da sub-rede que selecionou. Endereços estáticos e dinâmicos não são alterados até que alterá-los ou a interface de rede for eliminada. Pode alterar o método de atribuição depois de criar a interface de rede. O servidor DHCP do Azure atribui este endereço para a interface de rede no sistema operativo da máquina virtual.|
    |Grupo de segurança de rede|Não| Deixe definido como **None**, selecione um existente [grupo de segurança de rede](security-overview.md), ou [criar um grupo de segurança de rede](tutorial-filter-network-traffic.md). Grupos de segurança de rede permitem filtrar o tráfego de rede dentro e fora de uma interface de rede. Pode aplicar a zero ou um grupo de segurança de rede a uma interface de rede. Também pode ser aplicado a zero ou um grupo de segurança de rede para a sub-rede que a interface de rede é atribuída a. Quando um grupo de segurança de rede é aplicado a uma interface de rede e a sub-rede que a interface de rede é atribuída a, resultados inesperados, às vezes, ocorrerem. Para resolver problemas relacionados com grupos de segurança de rede aplicados às interfaces de rede e sub-redes, veja [resolver problemas dos grupos de segurança de rede](diagnose-network-traffic-filter-problem.md).|
    |Subscrição|Sim|Selecione uma das sua do Azure [subscrições](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). A máquina virtual, anexar uma interface de rede para e a rede virtual que a ligue tem de existir na mesma subscrição.|
    |Endereço IP privado (IPv6)|Não| Se selecionar esta caixa de verificação, é atribuído um endereço de IPv6 para a interface de rede, além de endereço IPv4 atribuído à interface de rede. Consulte a secção de IPv6 deste artigo para obter informações importantes sobre a utilização de IPv6 com interfaces de rede. Não é possível selecionar um método de atribuição para o endereço IPv6. Se optar por atribuir um endereço IPv6, é atribuída com o método dinâmico.
    |Nome do IPv6 (apenas será exibida quando o **endereço IP privado (IPv6)** caixa de verificação) |Sim, se o **endereço IP privado (IPv6)** caixa de verificação.| Este nome é atribuído a uma configuração de IP secundária para a interface de rede. Para saber mais sobre as configurações de IP, veja [ver definições de interface de rede](#view-network-interface-settings).|
    |Grupo de recursos|Sim|Selecione um existente [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) ou criar uma. Uma interface de rede pode existir no grupo de recursos idêntica ou diferente, que a máquina virtual, que anexar a ela, ou a rede virtual liga-o para.|
    |Localização|Sim|A máquina virtual é anexar uma interface de rede para e a rede virtual que a ligue tem de existir na mesma [localização](https://azure.microsoft.com/regions), também referido como uma região.|

O portal não fornece a opção de atribuir um endereço IP público à interface de rede quando a criar, embora o portal de criar um endereço IP público e atribuí-lo a uma interface de rede quando cria uma máquina virtual com o portal. Para saber como adicionar um endereço IP público à interface de rede depois de criá-lo, veja [endereços IP gerir](virtual-network-network-interface-addresses.md). Se quiser criar uma interface de rede com um endereço IP público, tem de utilizar a CLI ou o PowerShell para criar a interface de rede.

O portal não fornece a opção de atribuir a interface de rede para grupos de segurança de aplicações durante a criação de uma interface de rede, mas a CLI do Azure e o PowerShell fazem. Pode atribuir uma interface de rede existente para um grupo de segurança de aplicações com o portal, no entanto, enquanto a interface de rede está ligada a uma máquina virtual. Para saber como atribuir uma interface de rede a um grupo de segurança de aplicações, veja [adicionar para ou remover grupos de segurança de aplicativo](#add-to-or-remove-from-application-security-groups).

>[!Note]
> O Azure atribui um endereço MAC para a interface de rede apenas depois da interface de rede está ligada a uma máquina virtual e a máquina virtual é iniciada pela primeira vez. Não é possível especificar o endereço MAC que o Azure atribui à interface de rede. O endereço MAC permanece atribuído à interface de rede até que a interface de rede for eliminada ou o endereço IP privado atribuído à configuração de IP principal da interface de rede principal é alterado. Para saber mais sobre endereços IP e configurações de IP, consulte [endereços IP de gerir](virtual-network-network-interface-addresses.md)

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic#az_network_nic_create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Ver definições de interface de rede

Pode ver e alterar a maioria das definições para uma interface de rede depois de criado. O portal não apresenta o DNS sufixo ou aplicação associação grupo de segurança para a interface de rede. Pode utilizar o PowerShell ou a CLI do Azure [comandos](#view-settings-commands) para ver a DNS sufixo e aplicação de segurança associação de grupo.

1. Na caixa que contém o texto *recursos de pesquisa* na parte superior do portal do Azure, escreva *interfaces de rede*. Quando **interfaces de rede** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione a interface de rede que pretende ver ou alterar as definições da lista.
3. Os seguintes itens são listados para a interface de rede que selecionou:
   - **Descrição geral:** Fornece informações sobre a interface de rede, tais como os endereços IP atribuídos ele, a rede/sub-rede virtual que a interface de rede é atribuída a e a máquina virtual, que a interface de rede está ligada a (se este estiver ligado a um). A imagem seguinte mostra as definições de descrição geral para uma interface de rede com o nome **mywebserver256**: ![Descrição geral da interface de rede](./media/virtual-network-network-interface/nic-overview.png)

     Pode mover uma interface de rede para um grupo de recursos diferente ou uma subscrição ao selecionar (**alterar**) junto a **grupo de recursos** ou **nome da subscrição**. Se mover a interface de rede, tem de mover todos os recursos relacionados com a interface de rede com o mesmo. Se a interface de rede está ligada a uma máquina virtual, por exemplo, tem também de mover a máquina virtual e outros recursos relacionados com a máquina virtual. Para mover uma interface de rede, consulte [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). O artigo apresenta uma lista de pré-requisitos e como mover recursos com o portal do Azure, PowerShell e a CLI do Azure.
   - **Configurações de IP:** Públicos e privados endereços IPv4 e IPv6 atribuídos a configurações de IP estão listados aqui. Se um endereço IPv6 for atribuído a uma configuração de IP, o endereço não é apresentado. Para saber mais sobre as configurações de IP e como adicionar e remover endereços IP, veja [endereços IP de configurar para uma interface de rede do Azure](virtual-network-network-interface-addresses.md). Reencaminhamento de IP e a atribuição de sub-rede também são configuradas nesta secção. Para saber mais sobre estas definições, consulte [ativar ou desativar o reencaminhamento de IPs](#enable-or-disable-ip-forwarding) e [alterar a atribuição de sub-rede](#change-subnet-assignment).
   - **Servidores DNS:** Pode especificar que servidor DNS, uma interface de rede é atribuída pelos servidores DHCP do Azure. A interface de rede pode herdar a configuração a partir da rede virtual, que a interface de rede é atribuída a ou ter uma definição personalizada que substitui a definição de rede virtual que está atribuída a. Para modificar o que é apresentado, veja [servidores DNS de alteração](#change-dns-servers).
   - **Grupo de segurança de rede (NSG):** Apresenta que NSG é associado à interface de rede (se houver). Um NSG contém regras de entrada e saída para filtrar o tráfego de rede para a interface de rede. Se um NSG é associado à interface de rede, é apresentado o nome do NSG associado. Para modificar o que é apresentado, veja [associar ou desassociar um grupo de segurança de rede](#associate-or-dissociate-a-network-security-group).
   - **Propriedades:** Apresenta as definições de chave sobre a interface de rede, incluindo o seu endereço de MAC (em branco se a interface de rede não está anexada a uma máquina virtual) e a subscrição sai.
   - **Regras de segurança efetivas:**  Regras de segurança são listadas se a interface de rede está ligada a uma máquina virtual em execução e um NSG é associado à interface de rede, a sub-rede que está atribuída a ou ambos. Para saber mais sobre o que é apresentado, veja [ver as regras de segurança efetivas](#view-effective-security-rules). Para saber mais sobre NSGs, consulte [grupos de segurança de rede](security-overview.md).
   - **Rotas efetivas:** As rotas são apresentadas se a interface de rede está ligada a uma máquina virtual em execução. As rotas são uma combinação das rotas predefinidas do Azure e quaisquer rotas BGP que possam existir para a sub-rede que a interface de rede é atribuída a quaisquer rotas definidas pelo utilizador. Para saber mais sobre o que é apresentado, veja [ver as rotas efetivas](#view-effective-routes). Para saber mais sobre as rotas predefinidas do Azure e as rotas definidas pelo utilizador, veja [descrição geral do encaminhamento](virtual-networks-udr-overview.md).
   - **Definições comuns do Azure Resource Manager:**  Para saber mais sobre configurações comuns do Azure Resource Manager, veja [registo de atividades](../azure-monitor/platform/activity-logs-overview.md), [controlo de acesso (IAM)](../role-based-access-control/overview.md), [etiquetas](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [bloqueia](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)e o [ Script de automação](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).

<a name="view-settings-commands"></a>**Comandos**

Se um endereço IPv6 for atribuído a uma interface de rede, a saída do PowerShell devolve o fato de que o endereço é atribuído, mas isso não retorna o endereço atribuído. Da mesma forma, a CLI devolve o fato de que o endereço é atribuído, mas retorna *nulo* no respetivo resultado para o endereço.

|Ferramenta|Comando|
|---|---|
|CLI|[lista de nic de redes de AZ](/cli/azure/network/nic#az_network_nic_list) para ver interfaces de rede na subscrição; [show de nic de rede de az](/cli/azure/network/nic#az_network_nic_show) para ver as definições para uma interface de rede|
|PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) para ver interfaces de rede nas definições de vista de subscrição ou para uma interface de rede|

## <a name="change-dns-servers"></a>Servidores DNS de alteração

O servidor DNS é atribuído pelo servidor DHCP do Azure para a interface de rede dentro do sistema de operativo da máquina virtual. O servidor DNS atribuído é tudo o que é a definição do servidor DNS para uma interface de rede. Para saber mais sobre as definições de resolução de nome de uma interface de rede, veja [resolução de nomes para máquinas virtuais](virtual-networks-name-resolution-for-vms-and-role-instances.md). A interface de rede pode herdar as definições de rede virtual ou utilizar as suas próprias definições exclusivas que substituem a definição para a rede virtual.

1. Na caixa que contém o texto *recursos de pesquisa* na parte superior do portal do Azure, escreva *interfaces de rede*. Quando **interfaces de rede** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione a interface de rede que pretende alterar um servidor DNS para a partir da lista.
3. Selecione **servidores DNS** sob **definições**.
4. Selecione:
   - **Herdar da rede virtual**: Escolha esta opção para herdar a configuração de servidor DNS definida para a rede virtual, que a interface de rede é atribuída a. Ao nível da rede virtual, é definido um servidor DNS personalizado ou o servidor DNS fornecida pelo Azure. O servidor DNS fornecida pelo Azure pode resolver os nomes de anfitrião para recursos atribuídos à mesma rede virtual. FQDN deve ser utilizado para resolver para recursos atribuídos a redes virtuais diferentes.
   - **Custom**: Pode configurar o seu próprio servidor DNS para resolver nomes em várias redes virtuais. Introduza o endereço IP do servidor que pretende utilizar como um servidor DNS. O endereço do servidor DNS especificado é atribuído apenas a esta interface de rede e substitui qualquer definição de DNS para a rede virtual, que a interface de rede é atribuída a.
5. Selecione **Guardar**.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Ativar ou desativar o reencaminhamento de IP

Reencaminhamento de IP permite que a máquina virtual que está anexada uma interface de rede a:
- Receba o tráfego de rede não é destinado para um dos endereços IP atribuídos a qualquer uma das configurações de IP atribuídas à interface de rede.
- Envie tráfego de rede com um endereço IP de origem diferente daquela atribuído a uma das configurações de IP de uma interface de rede.

A definição tem de estar ativada para cada interface de rede que está ligado à máquina virtual que recebe o tráfego que a máquina virtual tem de reencaminhar. Uma máquina virtual pode reencaminhar o tráfego se ele tem várias interfaces de rede ou uma única interface de rede ligados ao mesmo. Enquanto o reencaminhamento de IP é uma definição do Azure, a máquina virtual tem também de executar um aplicativo capaz de reencaminhar o tráfego, como firewall, otimização de WAN e aplicativos de balanceamento de carga. Quando uma máquina virtual está a executar aplicações de rede, a máquina virtual é frequentemente referida como uma aplicação virtual de rede. Pode ver uma lista de pronto para implementar aplicações virtuais de rede na [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Reencaminhamento de IP é normalmente utilizado com as rotas definidas pelo utilizador. Para saber mais sobre as rotas definidas pelo utilizador, veja [rotas definidas pelo utilizador](virtual-networks-udr-overview.md).

1. Na caixa que contém o texto *recursos de pesquisa* na parte superior do portal do Azure, escreva *interfaces de rede*. Quando **interfaces de rede** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione a interface de rede que pretende ativar ou desativar o reencaminhamento para de IPs.
3. Selecione **configurações de IP** no **definições** secção.
4. Selecione **Enabled** ou **desativado** (predefinição) para alterar a definição.
5. Selecione **Guardar**.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Alterar a atribuição de sub-rede

Pode alterar a sub-rede, mas não a rede virtual, atribuída a uma interface de rede.

1. Na caixa que contém o texto *recursos de pesquisa* na parte superior do portal do Azure, escreva *interfaces de rede*. Quando **interfaces de rede** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione a interface de rede que pretende alterar a atribuição de sub-rede para.
3. Selecione **configurações de IP** sob **definições**. Se quaisquer endereços IP privados para quaisquer configurações de IP na lista têm **(estático)** junto a eles, tem de alterar o método de atribuição de endereço IP para dinâmico, concluindo os passos que se seguem. Todos os endereços IP privados tem de ser atribuídos com o método de atribuição dinâmica para alterar a atribuição de sub-rede para a interface de rede. Se os endereços são atribuídos com o método dinâmico, avance para o quinto passo. Se os endereços IPv4 são atribuídos com o método de atribuição estática, conclua os seguintes passos para alterar o método de atribuição para dinâmico:
   - Selecione a configuração de IP que pretende alterar o método de atribuição de endereço IPv4 para a partir da lista de configurações de IP.
   - Selecione **dinâmica** para o endereço IP privado **atribuição** método. Não é possível atribuir um endereço IPv6 com o método de atribuição estática.
   - Selecione **Guardar**.
4. Selecione a sub-rede que pretende mover a interface de rede a partir da **sub-rede** na lista pendente.
5. Selecione **Guardar**. Novos endereços dinâmicos são atribuídos a partir do intervalo de endereços de sub-rede para a nova sub-rede. Depois de atribuir a interface de rede para uma nova sub-rede, pode atribuir um endereço IPv4 estático do novo intervalo de endereços da sub-rede se escolher. Para saber mais sobre como adicionar, alterar e remover endereços IP para uma interface de rede, veja [endereços IP gerir](virtual-network-network-interface-addresses.md).

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Adicionar ou remover grupos de segurança de aplicação

Apenas pode adicionar uma interface de rede, ou remover uma interface de rede de um grupo de segurança de aplicação com o portal se a interface de rede está ligada a uma máquina virtual. Pode utilizar o PowerShell ou a CLI do Azure para uma interface de rede para adicionar ou remover uma interface de rede de um grupo de segurança de aplicações, se a interface de rede está ligada a uma máquina virtual ou não. Saiba mais sobre [grupos de segurança de aplicativo](security-overview.md#application-security-groups) e como [crie um grupo de segurança de aplicações](manage-network-security-group.md).

1. Na *procurar recursos, serviços e documentos* caixa na parte superior do portal, comece a escrever o nome de uma máquina virtual que tenha uma interface de rede que pretende adicionar ou remover de um grupo de segurança de aplicações. Quando o nome da sua VM for apresentada nos resultados da pesquisa, selecione-o.
2. Em **DEFINIÇÕES**, selecione **Redes**.  Selecione **configurar os grupos de segurança de aplicativo**, selecione os grupos de segurança de aplicação que pretende adicionar a interface de rede ou anule a seleção de grupos de segurança de aplicações que pretende remover a interface de rede, e, em seguida, selecione **guardar**. Apenas as interfaces de rede que existem na mesma rede virtual podem ser adicionadas ao mesmo grupo de segurança do aplicativo. O grupo de segurança de aplicações têm de existir na mesma localização que a interface de rede.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Associar ou desassociar um grupo de segurança de rede

1. Na caixa de pesquisa na parte superior do portal, introduza *interfaces de rede* na caixa de pesquisa. Quando **interfaces de rede** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione a interface de rede na lista que pretende associar um grupo de segurança de rede para ou desassociar um grupo de segurança de rede do.
3. Selecione **grupo de segurança de rede** sob **definições**.
4. Selecione **Editar**.
5. Selecione **grupo de segurança de rede** e, em seguida, selecione o grupo de segurança de rede que pretende associar à interface de rede ou selecione **nenhum**, ao desassociar a um grupo de segurança de rede.
6. Selecione **Guardar**.

**Comandos**

- CLI do Azure: [atualização de nic de rede de az](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Eliminar uma interface de rede

Pode eliminar uma interface de rede, desde que não está ligado a uma máquina virtual. Se uma interface de rede está ligada a uma máquina virtual, tem primeiro coloque a máquina virtual no estado parado (desalocada), e desanexar a interface de rede da máquina virtual. Para anular a exposição de uma interface de rede de uma máquina virtual, conclua os passos em [anular a exposição de uma interface de rede de uma máquina virtual](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). Não é possível desligar uma interface de rede de uma máquina virtual, se for a única interface de rede ligada à máquina virtual, no entanto. Uma máquina virtual sempre tem de ter, pelo menos, uma interface de rede ligada ao mesmo. A eliminar uma máquina virtual desliga todas as interfaces de rede ligadas ao mesmo, mas não elimina as interfaces de rede.

1. Na caixa que contém o texto *recursos de pesquisa* na parte superior do portal do Azure, escreva *interfaces de rede*. Quando **interfaces de rede** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione **...**  no lado direito da interface de rede que pretende eliminar a partir da lista de interfaces de rede.
3. Selecione **Eliminar**.
4. Selecione **Sim** para confirmar a eliminação da interface de rede.

Quando elimina uma interface de rede, são lançados a qualquer endereços IP ou MAC atribuídos à mesma.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic)|
|PowerShell|[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Resolver problemas de conectividade

Se não conseguir comunicar para ou a partir de uma máquina virtual, regras de segurança do grupo de segurança de rede ou rotas efetivas para uma interface de rede, poderá estar a causar o problema. Tem as seguintes opções para ajudar a resolver o problema:

### <a name="view-effective-security-rules"></a>Ver regras de segurança efetivas

As regras de segurança efetivas para cada interface de rede ligado a uma máquina virtual são uma combinação das regras que criou num grupo de segurança de rede e [regras de segurança predefinidas](security-overview.md#default-security-rules). Noções básicas sobre as regras de segurança efetivas para uma interface de rede pode ajudá-lo a determinar por que não é possível comunicar para ou a partir de uma máquina virtual. Pode ver as regras em vigor a partir de qualquer interface de rede que está ligado a uma máquina virtual em execução.

1. Na caixa de pesquisa na parte superior do portal, introduza o nome de uma máquina virtual que pretende visualizar as regras de segurança efetivas para. Se não souber o nome de uma máquina virtual, introduza *máquinas virtuais* na caixa de pesquisa. Quando **máquinas virtuais** aparecer nos resultados da pesquisa, selecione-a e, em seguida, selecione uma máquina virtual a partir da lista.
2. Selecione **Networking** sob **definições**.
3. Selecione o nome de uma interface de rede.
4. Selecione **regras de segurança efetivas** sob **suporte + resolução de problemas**.
5. Reveja a lista de regras de segurança efetivas para determinar se as regras corretas existem para a comunicação de entrada e saída necessária. Saiba mais sobre o que vê na lista na [descrição geral de grupo de segurança de rede](security-overview.md).

Verifique se o fluxo IP a funcionalidade do observador de rede do Azure pode também ajudar a determinar se as regras de segurança estão a impedir a comunicação entre uma máquina virtual e um ponto final. Para obter mais informações, consulte [fluxo de IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Comandos**

- CLI do Azure: [az network nic lista-em vigor-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Ver as rotas efetivas

As rotas efetivas para as interfaces de rede ligadas a uma máquina virtual são uma combinação de rotas predefinidas, quaisquer rotas que criou e quaisquer rotas propagadas a partir de redes no local através do BGP através de um gateway de rede virtual do Azure. Noções básicas sobre as rotas efetivas para uma interface de rede pode ajudá-lo a determinar por que não é possível comunicar para ou a partir de uma máquina virtual. Pode ver as rotas efetivas para qualquer interface de rede que está ligado a uma máquina virtual em execução.

1. Na caixa de pesquisa na parte superior do portal, introduza o nome de uma máquina virtual que pretende visualizar as regras de segurança efetivas para. Se não souber o nome de uma máquina virtual, introduza *máquinas virtuais* na caixa de pesquisa. Quando **máquinas virtuais** aparecer nos resultados da pesquisa, selecione-a e, em seguida, selecione uma máquina virtual a partir da lista.
2. Selecione **Networking** sob **definições**.
3. Selecione o nome de uma interface de rede.
4. Selecione **rotas efetivas** sob **suporte + resolução de problemas**.
5. Reveja a lista de rotas efetivas para determinar se as rotas corretas existem para a comunicação de entrada e saída necessária. Saiba mais sobre o que vê na lista na [descrição geral do encaminhamento](virtual-networks-udr-overview.md).

A funcionalidade de próximo salto do observador de rede do Azure pode também ajudar a determinar se as rotas estão a impedir a comunicação entre uma máquina virtual e um ponto final. Para obter mais informações, consulte [do próximo salto](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Comandos**

- CLI do Azure: [az network nic show-em vigor route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Permissões

Para executar tarefas em interfaces de rede, deve ser atribuída a sua conta para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) função que é atribuída as permissões adequadas listadas na tabela seguinte:

| Ação                                                                     | Name                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Obter a interface de rede                                     |
| Microsoft.Network/networkInterfaces/write                                  | Criar ou atualizar a interface de rede                        |
| Microsoft.Network/networkInterfaces/join/action                            | Anexar uma interface de rede a uma máquina virtual           |
| Microsoft.Network/networkInterfaces/delete                                 | Eliminar interface de rede                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Junte-se a um recurso para uma interface de rede através de um servi...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Obter a tabela de rotas efetivas de interface de rede               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Obter grupos de segurança efetivas de interface de rede           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Obter balanceadores de carga de interface de rede                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Obtenha a associação de serviço                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Criar ou atualizar uma associação de serviço                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Eliminar a associação de serviço                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Validar a associação de serviço                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Obter configuração de IP da interface de rede                    |

## <a name="next-steps"></a>Passos Seguintes

- Criar uma VM com vários NICs com o [CLI do Azure](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Criar um único NIC de VM com IPv4 vários endereços que utilizam o [CLI do Azure](virtual-network-multiple-ip-addresses-cli.md) ou [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Criar uma VM de NIC único com um através de endereços (por trás de um balanceador de carga do Azure) de IPv6 privado a [CLI do Azure](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), ou [modelo Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Criar uma interface de rede com [PowerShell](powershell-samples.md) ou [CLI do Azure](cli-samples.md) scripts ou utilizar o Azure de exemplo [modelo do Resource Manager](template-samples.md)
- Criar e aplicar [o Azure policy](policy-samples.md) para redes virtuais