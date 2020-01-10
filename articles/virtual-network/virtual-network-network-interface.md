---
title: Criar, alterar ou excluir uma interface de rede do Azure
titlesuffix: Azure Virtual Network
description: Saiba o que é uma interface de rede e como criar, alterar as configurações e excluir uma.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: kumud
ms.openlocfilehash: 95ed6df3192043f33cf8d911387fc2a318153ac7
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751166"
---
# <a name="create-change-or-delete-a-network-interface"></a>Criar, alterar ou excluir uma interface de rede

Saiba como criar, alterar as configurações e excluir uma interface de rede. Uma interface de rede permite que uma máquina virtual do Azure se comunique com a Internet, com o Azure e com recursos locais. Ao criar uma máquina virtual usando a portal do Azure, o portal cria uma interface de rede com as configurações padrão para você. Em vez disso, você pode optar por criar interfaces de rede com configurações personalizadas e adicionar uma ou mais interfaces de rede a uma máquina virtual ao criá-la. Talvez você também queira alterar as configurações padrão da interface de rede para uma interface de rede existente. Este artigo explica como criar um adaptador de rede com configurações personalizadas, alterar configurações existentes, como atribuição de filtro de rede (grupo de segurança de rede), atribuição de sub-rede, configurações do servidor DNS e encaminhamento de IP, e excluir uma interface de rede.

Se você precisar adicionar, alterar ou remover endereços IP para uma interface de rede, consulte [gerenciar endereços IP](virtual-network-network-interface-addresses.md). Se você precisar adicionar adaptadores de rede ou remover adaptadores de rede de máquinas virtuais, consulte [Adicionar ou remover interfaces de rede](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Se você ainda não tiver uma conta do Azure, Inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o portal, abra https://portal.azure.com e faça logon com sua conta do Azure.
- Se estiver usando comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/powershell)ou executando o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o módulo Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se você estiver usando comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/bash)ou executando a CLI do seu computador. Este tutorial requer o CLI do Azure versão 2.0.28 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se você estiver executando o CLI do Azure localmente, também precisará executar `az login` para criar uma conexão com o Azure.

A conta que você faz logon ou conecta-se ao Azure com o, deve ser atribuída à função [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas em [permissões](#permissions).

## <a name="create-a-network-interface"></a>Criar uma interface de rede

Ao criar uma máquina virtual usando o portal do Azure, o portal cria um adaptador de rede com as configurações padrão para você. Se preferir especificar todas as suas configurações de interface de rede, você poderá criar um adaptador de rede com configurações personalizadas e anexar a interface de rede a uma máquina virtual ao criar a máquina virtual (usando o PowerShell ou o CLI do Azure). Você também pode criar uma interface de rede e adicioná-la a uma máquina virtual existente (usando o PowerShell ou o CLI do Azure). Para saber como criar uma máquina virtual com uma interface de rede existente ou para adicionar ou remover adaptadores de rede de máquinas virtuais existentes, consulte [Adicionar ou remover adaptadores de rede](virtual-network-network-interface-vm.md). Antes de criar uma interface de rede, você deve ter uma [rede virtual](manage-virtual-network.md) existente no mesmo local e assinatura em que cria um adaptador de rede.

1. Na caixa que contém os recursos de *pesquisa* de texto na parte superior da portal do Azure, digite *interfaces de rede*. Quando as **interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione **+ Adicionar** em **interfaces de rede**.
3. Insira ou selecione valores para as seguintes configurações e, em seguida, selecione **criar**:

    |Definição|Necessário?|Detalhes|
    |---|---|---|
    |Nome|Sim|O nome deve ser exclusivo dentro do grupo de recursos que você selecionar. Ao longo do tempo, você provavelmente terá várias interfaces de rede em sua assinatura do Azure. Para obter sugestões ao criar uma Convenção de nomenclatura para facilitar o gerenciamento de várias interfaces de rede, consulte [convenções de nomenclatura](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). O nome não pode ser alterado após a criação da interface de rede.|
    |Rede virtual|Sim|Selecione a rede virtual para a interface de rede. Você só pode atribuir um adaptador de rede a uma rede virtual que exista na mesma assinatura e local que a interface de rede. Depois que um adaptador de rede é criado, você não pode alterar a rede virtual à qual ele está atribuído. A máquina virtual à qual você adiciona o adaptador de rede também deve existir no mesmo local e assinatura que a interface de rede.|
    |Subrede|Sim|Selecione uma sub-rede na rede virtual que você selecionou. Você pode alterar a sub-rede à qual a interface de rede está atribuída após sua criação.|
    |Atribuição de endereço IP privado|Sim| Nessa configuração, você está escolhendo o método de atribuição para o endereço IPv4. Escolha entre os seguintes métodos de atribuição: **dinâmico:** ao selecionar essa opção, o Azure atribui automaticamente o próximo endereço disponível do espaço de endereço da sub-rede que você selecionou. **Estático:** Ao selecionar essa opção, você deve atribuir manualmente um endereço IP disponível de dentro do espaço de endereço da sub-rede que você selecionou. Os endereços estáticos e dinâmicos não são alterados até que você os altere ou a interface de rede seja excluída. Você pode alterar o método de atribuição após a criação da interface de rede. O servidor DHCP do Azure atribui esse endereço à interface de rede dentro do sistema operacional da máquina virtual.|
    |Grupo de segurança de rede|Não| Deixe definido como **nenhum**, selecione um [grupo de segurança de rede](security-overview.md)existente ou [crie um grupo de segurança de rede](tutorial-filter-network-traffic.md). Os grupos de segurança de rede permitem filtrar o tráfego de rede dentro e fora de uma interface de rede. Você pode aplicar zero ou um grupo de segurança de rede a uma interface de rede. Nenhum ou um grupo de segurança de rede também pode ser aplicado à sub-rede à qual a interface de rede está atribuída. Quando um grupo de segurança de rede é aplicado a um adaptador de rede e à sub-rede à qual a interface de rede é atribuída, às vezes, ocorrem resultados inesperados. Para solucionar problemas de grupos de segurança de rede aplicados a interfaces de rede e sub-redes, consulte [solucionar problemas de grupos de segurança de rede](diagnose-network-traffic-filter-problem.md).|
    |Subscrição|Sim|Selecione uma das suas [assinaturas](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)do Azure. A máquina virtual à qual você anexa um adaptador de rede e a rede virtual à qual você se conecta deve existir na mesma assinatura.|
    |Endereço IP privado (IPv6)|Não| Se você marcar essa caixa de seleção, um endereço IPv6 será atribuído à interface de rede, além do endereço IPv4 atribuído à interface de rede. Consulte a seção IPv6 deste artigo para obter informações importantes sobre o uso do IPv6 com interfaces de rede. Não é possível selecionar um método de atribuição para o endereço IPv6. Se você optar por atribuir um endereço IPv6, ele será atribuído com o método dinâmico.
    |Nome IPv6 (aparece apenas quando a caixa de seleção **endereço IP privado (IPv6)** está marcada) |Sim, se a caixa de seleção **endereço IP privado (IPv6)** estiver marcada.| Esse nome é atribuído a uma configuração de IP secundário para a interface de rede. Para saber mais sobre as configurações de IP, consulte [exibir configurações de interface de rede](#view-network-interface-settings).|
    |Grupo de recursos|Sim|Selecione um [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) existente ou crie um. Um adaptador de rede pode existir no mesmo grupo de recursos ou em um diferente da máquina virtual à qual você o anexa ou à rede virtual à qual você se conecta.|
    |Localização|Sim|A máquina virtual à qual você anexa um adaptador de rede e a rede virtual à qual você se conecta deve existir no mesmo [local](https://azure.microsoft.com/regions), também conhecido como região.|

O portal não fornece a opção de atribuir um endereço IP público ao adaptador de rede ao criá-lo, embora o portal crie um endereço IP público e o atribua a um adaptador de rede ao criar uma máquina virtual usando o Portal. Para saber como adicionar um endereço IP público ao adaptador de rede depois de criá-lo, consulte [gerenciar endereços IP](virtual-network-network-interface-addresses.md). Se você quiser criar um adaptador de rede com um endereço IP público, deverá usar a CLI ou o PowerShell para criar a interface de rede.

O portal não fornece a opção de atribuir a interface de rede a grupos de segurança de aplicativo ao criar um adaptador de rede, mas o CLI do Azure e o PowerShell fazem. No entanto, você pode atribuir um adaptador de rede existente a um grupo de segurança de aplicativo usando o portal, desde que o adaptador de rede esteja conectado a uma máquina virtual. Para saber como atribuir uma interface de rede a um grupo de segurança de aplicativo, consulte [Adicionar ou remover de grupos de segurança de aplicativo](#add-to-or-remove-from-application-security-groups).

>[!Note]
> O Azure atribui um endereço MAC ao adaptador de rede somente depois que o adaptador de rede é anexado a uma máquina virtual e a máquina virtual é iniciada pela primeira vez. Você não pode especificar o endereço MAC que o Azure atribui à interface de rede. O endereço MAC permanece atribuído à interface de rede até que a interface de rede seja excluída ou o endereço IP privado atribuído à configuração de IP primário da interface de rede primária seja alterado. Para saber mais sobre endereços IP e configurações de IP, consulte [gerenciar endereços IP](virtual-network-network-interface-addresses.md)

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Exibir configurações de interface de rede

Você pode exibir e alterar a maioria das configurações de uma interface de rede após sua criação. O portal não exibe o sufixo DNS ou a associação de grupo de segurança do aplicativo para a interface de rede. Você pode usar os [comandos](#view-settings-commands) do PowerShell ou do CLI do Azure para exibir o sufixo DNS e a associação ao grupo de segurança do aplicativo.

1. Na caixa que contém os recursos de *pesquisa* de texto na parte superior da portal do Azure, digite *interfaces de rede*. Quando as **interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione o adaptador de rede que você deseja exibir ou altere as configurações da lista.
3. Os seguintes itens são listados para a interface de rede que você selecionou:
   - **Visão geral:** Fornece informações sobre a interface de rede, como os endereços IP atribuídos a ela, a rede virtual/sub-rede à qual o adaptador de rede está atribuído e a máquina virtual à qual o adaptador de rede está anexado (se estiver conectado a um). A imagem a seguir mostra as configurações de visão geral de um adaptador de rede chamado **mywebserver256**: visão geral do ![interface de rede](./media/virtual-network-network-interface/nic-overview.png)

     Você pode mover uma interface de rede para um grupo de recursos ou assinatura diferente selecionando (**alterar**) ao lado do **grupo de recursos** ou **nome da assinatura**. Se você mover a interface de rede, deverá mover todos os recursos relacionados à interface de rede com ela. Se a interface de rede estiver conectada a uma máquina virtual, por exemplo, você também deverá mover a máquina virtual e outros recursos relacionados à máquina virtual. Para mover um adaptador de rede, consulte [mover o recurso para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal). O artigo lista os pré-requisitos e como mover recursos usando o portal do Azure, o PowerShell e o CLI do Azure.
   - **Configurações de IP:** Os endereços IPv4 e IPv6 públicos e privados atribuídos às configurações de IP estão listados aqui. Se um endereço IPv6 for atribuído a uma configuração de IP, o endereço não será exibido. Para saber mais sobre as configurações de IP e como adicionar e remover endereços IP, consulte [configurar endereços IP para uma interface de rede do Azure](virtual-network-network-interface-addresses.md). O encaminhamento de IP e a atribuição de sub-rede também são configurados nesta seção. Para saber mais sobre essas configurações, consulte [habilitar ou desabilitar encaminhamento de IP](#enable-or-disable-ip-forwarding) e [alterar atribuição de sub-rede](#change-subnet-assignment).
   - **Servidores DNS:** Você pode especificar qual servidor DNS uma interface de rede é atribuída pelos servidores DHCP do Azure. A interface de rede pode herdar a configuração da rede virtual à qual o adaptador de rede está atribuído ou ter uma configuração personalizada que substitui a configuração da rede virtual à qual ela está atribuída. Para modificar o que é exibido, consulte [alterar servidores DNS](#change-dns-servers).
   - **NSG (grupo de segurança de rede):** Exibe qual NSG está associado à interface de rede (se houver). Um NSG contém regras de entrada e saída para filtrar o tráfego de rede para a interface de rede. Se um NSG estiver associado à interface de rede, o nome do NSG associado será exibido. Para modificar o que é exibido, consulte [associar ou dissociar um grupo de segurança de rede](#associate-or-dissociate-a-network-security-group).
   - **Propriedades:** Exibe as principais configurações sobre o adaptador de rede, incluindo seu endereço MAC (em branco se a interface de rede não estiver anexada a uma máquina virtual) e a assinatura na qual ela existe.
   - **Regras de segurança em vigor:**  As regras de segurança são listadas se o adaptador de rede estiver conectado a uma máquina virtual em execução e um NSG estiver associado à interface de rede, à sub-rede à qual ele está atribuído ou a ambos. Para saber mais sobre o que é exibido, consulte [Exibir regras de segurança em vigor](#view-effective-security-rules). Para saber mais sobre o NSGs, confira [grupos de segurança de rede](security-overview.md).
   - **Rotas efetivas:** As rotas são listadas se a interface de rede estiver conectada a uma máquina virtual em execução. As rotas são uma combinação das rotas padrão do Azure, quaisquer rotas definidas pelo usuário e quaisquer rotas BGP que possam existir para a sub-rede à qual a interface de rede está atribuída. Para saber mais sobre o que é exibido, consulte [Exibir rotas efetivas](#view-effective-routes). Para saber mais sobre as rotas padrão do Azure e as rotas definidas pelo usuário, consulte [visão geral de roteamento](virtual-networks-udr-overview.md).
   - **Configurações comuns de Azure Resource Manager:**  Para saber mais sobre configurações comuns de Azure Resource Manager, consulte [log de atividades](../azure-monitor/platform/platform-logs-overview.md), [controle de acesso (iam)](../role-based-access-control/overview.md), [marcas](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [bloqueios](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)e [script de automação](../azure-resource-manager/templates/export-template-portal.md).

<a name="view-settings-commands"></a>**Comandos**

Se um endereço IPv6 for atribuído a um adaptador de rede, a saída do PowerShell retornará o fato de que o endereço é atribuído, mas ele não retorna o endereço atribuído. Da mesma forma, a CLI retorna o fato de que o endereço é atribuído, mas retorna *NULL* em sua saída para o endereço.

|Ferramenta|Comando|
|---|---|
|CLI|[AZ Network NIC List](/cli/azure/network/nic) para exibir interfaces de rede na assinatura; [AZ Network NIC show](/cli/azure/network/nic) para exibir as configurações de uma interface de rede|
|PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) para exibir os adaptadores de rede na assinatura ou exibir as configurações de uma interface de rede|

## <a name="change-dns-servers"></a>Alterar servidores DNS

O servidor DNS é atribuído pelo servidor DHCP do Azure para a interface de rede no sistema operacional da máquina virtual. O servidor DNS atribuído é qualquer que seja a configuração do servidor DNS para uma interface de rede. Para saber mais sobre as configurações de resolução de nome de uma interface de rede, consulte [resolução de nomes para máquinas virtuais](virtual-networks-name-resolution-for-vms-and-role-instances.md). A interface de rede pode herdar as configurações da rede virtual ou usar suas próprias configurações exclusivas que substituem a configuração da rede virtual.

1. Na caixa que contém os recursos de *pesquisa* de texto na parte superior da portal do Azure, digite *interfaces de rede*. Quando as **interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione a interface de rede para a qual você deseja alterar um servidor DNS na lista.
3. Selecione **servidores DNS** em **configurações**.
4. Selecione:
   - **Herdar da rede virtual**: escolha esta opção para herdar a configuração do servidor DNS definida para a rede virtual à qual a interface de rede está atribuída. No nível da rede virtual, é definido um servidor DNS personalizado ou o servidor DNS fornecido pelo Azure. O servidor DNS fornecido pelo Azure pode resolver nomes de host para recursos atribuídos à mesma rede virtual. O FQDN deve ser usado para resolver recursos atribuídos a diferentes redes virtuais.
   - **Personalizado**: você pode configurar seu próprio servidor DNS para resolver nomes entre várias redes virtuais. Insira o endereço IP do servidor que você deseja usar como um servidor DNS. O endereço do servidor DNS especificado é atribuído somente a esse adaptador de rede e substitui qualquer configuração de DNS para a rede virtual à qual o adaptador de rede está atribuído.
     >[!Note]
     >Se a VM usar uma NIC que faz parte de um conjunto de disponibilidade, todos os servidores DNS especificados para cada uma das VMs de todas as NICs que fazem parte do conjunto de disponibilidade serão herdados.
5. Selecione **Guardar**.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[atualização de NIC de rede AZ](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Habilitar ou desabilitar o encaminhamento de IP

O encaminhamento de IP permite que a máquina virtual à qual o adaptador de rede está conectado:
- Receba o tráfego de rede não destinado a um dos endereços IP atribuídos a qualquer uma das configurações de IP atribuídas ao adaptador de rede.
- Envie o tráfego de rede com um endereço IP de origem diferente daquele atribuído a uma das configurações de IP da interface de rede.

A configuração deve ser habilitada para cada interface de rede que é anexada à máquina virtual que recebe o tráfego que a máquina virtual precisa para encaminhar. Uma máquina virtual pode encaminhar o tráfego se ele tiver várias interfaces de rede ou uma única interface de rede anexada a ela. Enquanto o encaminhamento de IP é uma configuração do Azure, a máquina virtual também deve executar um aplicativo capaz de encaminhar o tráfego, como firewall, otimização de WAN e aplicativos de balanceamento de carga. Quando uma máquina virtual está executando aplicativos de rede, a máquina virtual é geralmente chamada de solução de virtualização de rede. Você pode exibir uma lista de dispositivos de virtualização de rede pronta para implantar no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). O encaminhamento de IP normalmente é usado com rotas definidas pelo usuário. Para saber mais sobre as rotas definidas pelo usuário, consulte [rotas definidas pelo usuário](virtual-networks-udr-overview.md).

1. Na caixa que contém os recursos de *pesquisa* de texto na parte superior da portal do Azure, digite *interfaces de rede*. Quando as **interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione a interface de rede para a qual você deseja habilitar ou desabilitar o encaminhamento de IP.
3. Selecione **configurações de IP** na seção **configurações** .
4. Selecione **habilitado** ou **desabilitado** (configuração padrão) para alterar a configuração.
5. Selecione **Guardar**.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[atualização de NIC de rede AZ](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Alterar atribuição de sub-rede

Você pode alterar a sub-rede, mas não a rede virtual, à qual uma interface de rede é atribuída.

1. Na caixa que contém os recursos de *pesquisa* de texto na parte superior da portal do Azure, digite *interfaces de rede*. Quando as **interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione a interface de rede para a qual você deseja alterar a atribuição de sub-rede.
3. Selecione **configurações de IP** em **configurações**. Se qualquer endereço IP privado para qualquer configuração de IP listado tiver **(estático)** ao lado deles, você deverá alterar o método de atribuição de endereço IP para dinâmico, concluindo as etapas a seguir. Todos os endereços IP privados devem ser atribuídos com o método de atribuição dinâmica para alterar a atribuição de sub-rede para a interface de rede. Se os endereços forem atribuídos com o método dinâmico, continue na etapa cinco. Se algum endereço IPv4 for atribuído com o método de atribuição estática, conclua as seguintes etapas para alterar o método de atribuição para dinâmico:
   - Selecione a configuração de IP para a qual você deseja alterar o método de atribuição de endereço IPv4 da lista de configurações de IP.
   - Selecione **dinâmico** para o método de **atribuição** de endereço IP privado. Você não pode atribuir um endereço IPv6 com o método de atribuição estático.
   - Selecione **Guardar**.
4. Selecione a sub-rede para a qual você deseja mover o adaptador de rede na lista suspensa **sub-rede** .
5. Selecione **Guardar**. Novos endereços dinâmicos são atribuídos do intervalo de endereços de sub-rede para a nova sub-rede. Depois de atribuir o adaptador de rede a uma nova sub-rede, você poderá atribuir um endereço IPv4 estático do novo intervalo de endereços de sub-rede se escolher. Para saber mais sobre como adicionar, alterar e remover endereços IP para uma interface de rede, consulte [gerenciar endereços IP](virtual-network-network-interface-addresses.md).

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Adicionar ou remover de grupos de segurança de aplicativo

Você só poderá adicionar um adaptador de rede ou remover um adaptador de rede de um grupo de segurança de aplicativo usando o portal se o adaptador de rede estiver conectado a uma máquina virtual. Você pode usar o PowerShell ou o CLI do Azure para adicionar um adaptador de rede ou remover um adaptador de rede de um grupo de segurança de aplicativo, independentemente de o adaptador de rede estar conectado a uma máquina virtual ou não. Saiba mais sobre [grupos de segurança de aplicativo](security-overview.md#application-security-groups) e como [criar um grupo de segurança de aplicativo](manage-network-security-group.md).

1. Na caixa *Pesquisar recursos, serviços e documentos* na parte superior do portal, comece a digitar o nome de uma máquina virtual que tem um adaptador de rede que você deseja adicionar ou remover de um grupo de segurança de aplicativo. Quando o nome da VM aparecer nos resultados da pesquisa, selecione-o.
2. Em **DEFINIÇÕES**, selecione **Redes**.  Selecione **configurar os grupos de segurança de aplicativo**, selecione os grupos de segurança de aplicativo aos quais você deseja adicionar o adaptador de rede ou desmarque os grupos de segurança de aplicativo dos quais deseja remover o adaptador de rede e, em seguida, selecione **salvar**. Somente as interfaces de rede que existem na mesma rede virtual podem ser adicionadas ao mesmo grupo de segurança de aplicativo. O grupo de segurança do aplicativo deve existir no mesmo local que a interface de rede.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[atualização de NIC de rede AZ](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Associar ou dissociar um grupo de segurança de rede

1. Na caixa de pesquisa na parte superior do portal, insira *interfaces de rede* na caixa de pesquisa. Quando as **interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione o adaptador de rede na lista ao qual você deseja associar um grupo de segurança de rede ou dissociar um grupo de segurança de rede.
3. Selecione **grupo de segurança de rede** em **configurações**.
4. Selecione **Editar**.
5. Selecione **grupo de segurança de rede** e, em seguida, selecione o grupo de segurança de rede que você deseja associar ao adaptador de rede ou selecione **nenhum**para dissociar um grupo de segurança de rede.
6. Selecione **Guardar**.

**Comandos**

- CLI do Azure: [AZ Network NIC Update](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Excluir uma interface de rede

Você pode excluir um adaptador de rede, desde que ele não esteja anexado a uma máquina virtual. Se um adaptador de rede estiver conectado a uma máquina virtual, você deverá primeiro posicionar a máquina virtual no estado parado (desalocado) e desanexar a interface de rede da máquina virtual. Para desanexar uma interface de rede de uma máquina virtual, conclua as etapas em [desanexar uma interface de rede de uma máquina virtual](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). No entanto, você não poderá desanexar uma interface de rede de uma máquina virtual se ela for a única interface de rede anexada à máquina virtual. Uma máquina virtual sempre deve ter pelo menos uma interface de rede anexada a ela. A exclusão de uma máquina virtual desanexa todas as interfaces de rede anexadas a ela, mas não exclui as interfaces de rede.

1. Na caixa que contém os recursos de *pesquisa* de texto na parte superior da portal do Azure, digite *interfaces de rede*. Quando as **interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione **...** no lado direito da interface de rede que você deseja excluir da lista de interfaces de rede.
3. Selecione **Eliminar**.
4. Selecione **Sim** para confirmar a exclusão da interface de rede.

Quando você exclui um adaptador de rede, todos os endereços MAC ou IP atribuídos a ele são liberados.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[AZ Network NIC Delete](/cli/azure/network/nic)|
|PowerShell|[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Resolver problemas de conectividade

Se você não conseguir se comunicar com uma máquina virtual, as regras de segurança do grupo de segurança de rede ou as rotas efetivas para uma interface de rede poderão estar causando o problema. Você tem as seguintes opções para ajudar a resolver o problema:

### <a name="view-effective-security-rules"></a>Exibir regras de segurança em vigor

As regras de segurança em vigor para cada interface de rede anexada a uma máquina virtual são uma combinação das regras que você criou em um grupo de segurança de rede e [regras de segurança padrão](security-overview.md#default-security-rules). Entender as regras de segurança em vigor para uma interface de rede pode ajudá-lo a determinar por que você não consegue se comunicar com uma máquina virtual ou a partir dela. Você pode exibir as regras efetivas para qualquer interface de rede conectada a uma máquina virtual em execução.

1. Na caixa de pesquisa na parte superior do portal, insira o nome de uma máquina virtual para a qual você deseja exibir regras de segurança em vigor. Se você não souber o nome de uma máquina virtual, insira *máquinas virtuais* na caixa de pesquisa. Quando as **máquinas virtuais** aparecem nos resultados da pesquisa, selecione-a e, em seguida, selecione uma máquina virtual na lista.
2. Selecione **rede** em **configurações**.
3. Selecione o nome de uma interface de rede.
4. Selecione **regras de segurança efetivas** em **suporte + solução de problemas**.
5. Examine a lista de regras de segurança em vigor para determinar se as regras corretas existem para a comunicação de entrada e saída necessária. Saiba mais sobre o que você vê na lista de [visão geral do grupo de segurança de rede](security-overview.md).

O recurso de verificação de fluxo de IP do observador de rede do Azure também pode ajudá-lo a determinar se as regras de segurança estão impedindo a comunicação entre uma máquina virtual e um ponto de extremidade. Para saber mais, confira [verificação de fluxo de IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Comandos**

- CLI do Azure: [AZ Network NIC List-efetivo-NSG](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Exibir rotas efetivas

As rotas efetivas para as interfaces de rede anexadas a uma máquina virtual são uma combinação de rotas padrão, todas as rotas que você criou e todas as rotas propagadas de redes locais via BGP por meio de um gateway de rede virtual do Azure. Entender as rotas efetivas para uma interface de rede pode ajudá-lo a determinar por que você não consegue se comunicar com uma máquina virtual ou a partir dela. Você pode exibir as rotas em vigor para qualquer interface de rede conectada a uma máquina virtual em execução.

1. Na caixa de pesquisa na parte superior do portal, insira o nome de uma máquina virtual para a qual você deseja exibir regras de segurança em vigor. Se você não souber o nome de uma máquina virtual, insira *máquinas virtuais* na caixa de pesquisa. Quando as **máquinas virtuais** aparecem nos resultados da pesquisa, selecione-a e, em seguida, selecione uma máquina virtual na lista.
2. Selecione **rede** em **configurações**.
3. Selecione o nome de uma interface de rede.
4. Selecione **rotas efetivas** em **suporte + solução de problemas**.
5. Examine a lista de rotas efetivas para determinar se as rotas corretas existem para sua comunicação de entrada e saída necessária. Saiba mais sobre o que você vê na lista em [visão geral de roteamento](virtual-networks-udr-overview.md).

O recurso de próximo salto do observador de rede do Azure também pode ajudá-lo a determinar se as rotas estão impedindo a comunicação entre uma máquina virtual e um ponto de extremidade. Para saber mais, veja o [próximo salto](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Comandos**

- CLI do Azure: [AZ Network NIC show-efetivo-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Permissões

Para executar tarefas em interfaces de rede, sua conta deve ser atribuída à função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as permissões apropriadas listadas na tabela a seguir:

| Ação                                                                     | Nome                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Obter interface de rede                                     |
| Microsoft.Network/networkInterfaces/write                                  | Criar ou atualizar a interface de rede                        |
| Microsoft.Network/networkInterfaces/join/action                            | Anexar uma interface de rede a uma máquina virtual           |
| Microsoft.Network/networkInterfaces/delete                                 | Excluir interface de rede                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Unir um recurso a uma interface de rede por meio de um servi...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Obter tabela de rotas efetivas da interface de rede               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Obter grupos de segurança efetivos da interface de rede           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Obter balanceadores de carga de interface de rede                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Obter Associação de serviço                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Criar ou atualizar uma associação de serviço                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Excluir Associação de serviço                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Validar Associação de serviço                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Obter configuração de IP da interface de rede                    |

## <a name="next-steps"></a>Passos seguintes

- Criar uma VM com várias NICs usando o [CLI do Azure](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou o [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Criar uma VM de NIC única com vários endereços IPv4 usando o [CLI do Azure](virtual-network-multiple-ip-addresses-cli.md) ou o [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Criar uma VM de NIC única com um endereço IPv6 privado (por trás de um Azure Load Balancer) usando o [CLI do Azure](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), o [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ou o [modelo de Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Criar uma interface de rede usando o [PowerShell](powershell-samples.md) ou [CLI do Azure](cli-samples.md) scripts de exemplo ou usando o modelo do Azure [Resource Manager](template-samples.md)
- Criar e aplicar a [política do Azure](policy-samples.md) para redes virtuais
