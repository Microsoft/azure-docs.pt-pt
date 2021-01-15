---
title: Criar, alterar ou eliminar uma interface de rede Azure
titlesuffix: Azure Virtual Network
description: Saiba o que é uma interface de rede e como criar, alterar definições e apagar uma.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/22/2020
ms.author: kumud
ms.openlocfilehash: 6cf082aa33d2063982d85cf0c2fdd68d61072217
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216945"
---
# <a name="create-change-or-delete-a-network-interface"></a>Criar, alterar ou eliminar uma interface de rede

Aprenda a criar, alterar definições e elimine uma interface de rede. Uma interface de rede permite que uma Máquina Virtual Azure comunique com internet, Azure e recursos no local. Ao criar uma máquina virtual utilizando o portal Azure, o portal cria uma interface de rede com definições padrão para si. Em vez disso, pode optar por criar interfaces de rede com definições personalizadas e adicionar uma ou mais interfaces de rede a uma máquina virtual quando a criar. Também pode querer alterar as definições de interface de rede padrão para uma interface de rede existente. Este artigo explica como criar uma interface de rede com definições personalizadas, alterar as definições existentes, tais como a atribuição de filtro de rede (grupo de segurança de rede), atribuição de sub-redes, definições de servidor DENS e encaminhamento IP, e eliminar uma interface de rede.

Se precisar de adicionar, alterar ou remover endereços IP para uma interface de rede, consulte [os endereços IP da Gestão](virtual-network-network-interface-addresses.md). Se precisar de adicionar interfaces de rede ou remover interfaces de rede de máquinas virtuais, consulte [Adicionar ou remover interfaces](virtual-network-network-interface-vm.md)de rede .

## <a name="before-you-begin"></a>Before you begin

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Complete as seguintes tarefas antes de completar etapas em qualquer secção deste artigo:

- Se ainda não tem uma conta Azure, inscreva-se para uma [conta de teste gratuita.](https://azure.microsoft.com/free)
- Se utilizar o portal, abra https://portal.azure.com e faça login com a sua conta Azure.
- Se utilizar comandos PowerShell para completar tarefas neste artigo, ou executa os comandos na [Azure Cloud Shell](https://shell.azure.com/powershell), ou executando o PowerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer a versão 1.0 ou mais tarde do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar os comandos da interface da linha de comando do Azure (CLI) para completar as tarefas neste artigo, ou executar os comandos na [Azure Cloud Shell](https://shell.azure.com/bash), ou executando o CLI a partir do seu computador. Este tutorial requer a versão Azure CLI 2.0.28 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI localmente, também precisa de correr `az login` para criar uma ligação com o Azure.

A conta em que inicia sessão, ou liga-se ao Azure, deve ser atribuída à [função de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída às ações [apropriadas listadas](#permissions)nas Permissões .

## <a name="create-a-network-interface"></a>Criar uma interface de rede

Ao criar uma máquina virtual utilizando o portal Azure, o portal cria uma interface de rede com definições padrão para si. Se preferir especificar todas as definições da interface de rede, pode criar uma interface de rede com definições personalizadas e anexar a interface de rede a uma máquina virtual ao criar a máquina virtual (utilizando o PowerShell ou o Azure CLI). Também pode criar uma interface de rede e adicioná-la a uma máquina virtual existente (utilizando o PowerShell ou o Azure CLI). Para aprender a criar uma máquina virtual com uma interface de rede existente ou para adicionar ou remover interfaces de rede de máquinas virtuais existentes, consulte [Adicionar ou remover interfaces](virtual-network-network-interface-vm.md)de rede . Antes de criar uma interface de rede, deve ter uma [rede virtual](manage-virtual-network.md) existente na mesma localização e subscrição em que cria uma interface de rede.

1. Na caixa que contém o texto *Procurar recursos* no topo do portal Azure, *digitar interfaces de rede*. Quando **as interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. **Selecione + Adicionar** em **interfaces de rede.**
3. Introduza ou selecione valores para as seguintes definições e, em seguida, **selecione Criar**:

    |Definição|Necessário?|Detalhes|
    |---|---|---|
    |Name|Yes|O nome deve ser único dentro do grupo de recursos que seleciona. Com o tempo, provavelmente terá várias interfaces de rede na sua subscrição Azure. Para sugestões ao criar uma convenção de nomeação para facilitar a gestão de várias interfaces de rede, consulte [as convenções de nomeação.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) O nome não pode ser alterado após a criação da interface de rede.|
    |Rede virtual|Yes|Selecione a rede virtual para a interface de rede. Só é possível atribuir uma interface de rede a uma rede virtual que exista na mesma subscrição e localização que a interface de rede. Uma vez criada uma interface de rede, não é possível alterar a rede virtual a que é atribuída. A máquina virtual que adiciona a interface de rede também deve existir na mesma localização e subscrição que a interface de rede.|
    |Sub-rede|Yes|Selecione uma sub-rede dentro da rede virtual selecionada. Pode alterar a sub-rede a que a interface de rede é atribuída após a sua criação.|
    |Atribuição de endereços IP privados|Yes| Neste cenário, está a escolher o método de atribuição para o endereço IPv4. Escolha entre os seguintes métodos de atribuição: **Dinâmico:** Ao selecionar esta opção, o Azure atribui automaticamente o próximo endereço disponível a partir do espaço de endereço da sub-rede selecionada. **Estática:** Ao selecionar esta opção, deve atribuir manualmente um endereço IP disponível a partir do espaço de endereço da sub-rede selecionada. Os endereços estáticos e dinâmicos não mudam até que os altere ou a interface de rede seja eliminada. Pode alterar o método de atribuição após a criação da interface de rede. O servidor Azure DHCP atribui este endereço à interface de rede dentro do sistema operativo da máquina virtual.|
    |Grupo de segurança de rede|No| Deixe o conjunto de **Nenhum**, selecione um grupo de segurança de [rede](./network-security-groups-overview.md)existente, ou crie um grupo de segurança [de rede](tutorial-filter-network-traffic.md). Os grupos de segurança da rede permitem filtrar o tráfego de rede dentro e fora de uma interface de rede. Pode aplicar zero ou um grupo de segurança de rede numa interface de rede. Zero ou um grupo de segurança de rede também podem ser aplicados na sub-rede a que a interface de rede é atribuída. Quando um grupo de segurança de rede é aplicado a uma interface de rede e a sub-rede a que a interface de rede é atribuída, por vezes ocorrem resultados inesperados. Para resolver os problemas de grupos de segurança da rede aplicados a interfaces de rede e sub-redes, consulte [grupos de segurança da rede Troubleshoot](diagnose-network-traffic-filter-problem.md).|
    |Subscrição|Yes|Selecione uma das suas [subscrições](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)Azure . A máquina virtual a que se liga uma interface de rede e a rede virtual a que a liga deve existir na mesma subscrição.|
    |Endereço IP privado (IPv6)|No| Se selecionar esta caixa de verificação, é atribuído um endereço IPv6 à interface de rede, para além do endereço IPv4 atribuído à interface de rede. Consulte a secção IPv6 deste artigo para obter informações importantes sobre a utilização do IPv6 com interfaces de rede. Não é possível selecionar um método de atribuição para o endereço IPv6. Se optar por atribuir um endereço IPv6, é atribuído com o método dinâmico.
    |Nome IPv6 (só aparece quando a caixa de verificação **do endereço IP privado (IPv6)** é verificada) |Sim, se a caixa de verificação **do endereço IP privado (IPv6)** for verificada.| Este nome é atribuído a uma configuração IP secundária para a interface de rede. Para saber mais sobre as configurações IP, consulte [as definições da interface de rede](#view-network-interface-settings).|
    |Grupo de recursos|Yes|Selecione um grupo de [recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) existente ou crie um. Uma interface de rede pode existir no mesmo grupo de recursos, ou diferente, do que a máquina virtual a que a liga, ou a rede virtual a que a liga.|
    |Localização|Sim|A máquina virtual a que se liga uma interface de rede e a rede virtual a que a liga deve existir no mesmo [local](https://azure.microsoft.com/regions), também referida como região.|

O portal não oferece a opção de atribuir um endereço IP público à interface de rede quando o cria, embora o portal crie um endereço IP público e o atribua a uma interface de rede quando cria uma máquina virtual utilizando o portal. Para aprender a adicionar um endereço IP público à interface de rede depois de o criar, consulte [os endereços IP da Manage](virtual-network-network-interface-addresses.md). Se pretender criar uma interface de rede com um endereço IP público, tem de utilizar o CLI ou o PowerShell para criar a interface de rede.

O portal não oferece a opção de atribuir a interface de rede aos grupos de segurança da aplicação ao criar uma interface de rede, mas o Azure CLI e o PowerShell o fazem. No entanto, pode atribuir uma interface de rede existente a um grupo de segurança de aplicações utilizando o portal, desde que a interface de rede esteja ligada a uma máquina virtual. Para aprender a atribuir uma interface de rede a um grupo de segurança de aplicações, consulte [Adicionar ou remover dos grupos de segurança da aplicação.](#add-to-or-remove-from-application-security-groups)

>[!Note]
> O Azure atribui um endereço MAC à interface de rede apenas depois de a interface de rede ser ligada a uma máquina virtual e a máquina virtual ser iniciada pela primeira vez. Não é possível especificar o endereço MAC que o Azure atribui à interface de rede. O endereço MAC permanece atribuído à interface de rede até que a interface de rede seja eliminada ou o endereço IP privado atribuído à configuração IP primária da interface de rede primária é alterado. Para saber mais sobre endereços IP e configurações IP, consulte [Gerir endereços IP](virtual-network-network-interface-addresses.md)

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[Novo AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Ver definições de interface de rede

Pode visualizar e alterar a maioria das definições para uma interface de rede depois de ser criada. O portal não apresenta o sufixo DNS ou a adesão do grupo de segurança à aplicação para a interface de rede. Pode utilizar os [comandos](#view-settings-commands) PowerShell ou Azure CLI para visualizar o sufixo DNS e a adesão ao grupo de segurança de aplicações.

1. Na caixa que contém o texto *Procurar recursos* no topo do portal Azure, *digitar interfaces de rede*. Quando **as interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione a interface de rede que pretende visualizar ou alterar as definições para a partir da lista.
3. Os seguintes itens estão listados para a interface de rede que selecionou:
   - **Visão geral:** Fornece informações sobre a interface de rede, como os endereços IP que lhe são atribuídos, a rede virtual/sub-rede a que a interface de rede é atribuída, e a máquina virtual a que a interface de rede está anexada (se estiver ligada a uma). A imagem a seguir mostra as definições de visão geral para uma interface de rede chamada **mywebserver256**: ![ Visão geral da interface da rede](./media/virtual-network-network-interface/nic-overview.png)

     Pode mover uma interface de rede para um grupo de recursos diferente ou subscrição selecionando **(alteração)** ao lado do **grupo de Recursos** ou nome de **Subscrição.** Se mover a interface de rede para uma nova subscrição, deve mover todos os recursos relacionados com a interface de rede com ela. Se a interface de rede estiver ligada a uma máquina virtual, por exemplo, também deve mover a máquina virtual e outros recursos virtuais relacionados com máquinas. Para mover uma interface de rede, consulte [o recurso Move para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal). O artigo lista pré-requisitos e como mover recursos usando o portal Azure, PowerShell e o Azure CLI.
   - **Configurações IP:** Os endereços IPv4 e IPv6 públicos e privados atribuídos às configurações IP estão listados aqui. Se um endereço IPv6 for atribuído a uma configuração IP, o endereço não é apresentado. Para saber mais sobre as configurações IP e como adicionar e remover endereços IP, consulte [endereços IP configurar para uma interface de rede Azure](virtual-network-network-interface-addresses.md). O reencaminhamento IP e a atribuição de sub-redes também estão configurados nesta secção. Para saber mais sobre estas definições, consulte [Ativar ou desativar a atribuição de sub-redes IP](#enable-or-disable-ip-forwarding) e alterar a sua designação de [sub-rede](#change-subnet-assignment).
   - **Servidores DNS:** Pode especificar qual o servidor DNS que uma interface de rede é atribuída pelos servidores Azure DHCP. A interface de rede pode herdar a definição da rede virtual a que a interface de rede é atribuída, ou ter uma definição personalizada que substitui a definição para a rede virtual a que está atribuída. Para modificar o que é apresentado, consulte [os servidores DE ALTERAÇÃO DNS](#change-dns-servers).
   - **Grupo de segurança de rede (NSG):** Apresenta quais NSG está associado à interface de rede (se houver). Um NSG contém regras de entrada e saída para filtrar o tráfego da rede para a interface de rede. Se um NSG estiver associado à interface de rede, o nome do NSG associado é apresentado. Para modificar o que está exposto, consulte [Associate ou dissocite um grupo de segurança de rede](#associate-or-dissociate-a-network-security-group).
   - **Propriedades:** Apresenta definições chave sobre a interface de rede, incluindo o seu endereço MAC (em branco se a interface de rede não estiver ligada a uma máquina virtual) e a subscrição em que existe.
   - **Regras de segurança eficazes:**  As regras de segurança estão listadas se a interface de rede estiver ligada a uma máquina virtual em execução, e um NSG estiver associado à interface de rede, à sub-rede a que é atribuída, ou ambos. Para saber mais sobre o que é exibido, consulte [as regras de segurança eficazes.](#view-effective-security-rules) Para saber mais sobre os NSGs, consulte [os grupos de segurança da Rede.](./network-security-groups-overview.md)
   - **Rotas eficazes:** As rotas são listadas se a interface de rede estiver ligada a uma máquina virtual em funcionamento. As rotas são uma combinação das rotas predefinidas Azure, quaisquer rotas definidas pelo utilizador, e quaisquer rotas BGP que possam existir para a sub-rede a que a interface de rede é atribuída. Para saber mais sobre o que é exibido, consulte [as rotas eficazes.](#view-effective-routes) Para saber mais sobre as rotas padrão do Azure e as rotas definidas pelo utilizador, consulte [a visão geral do encaminhamento](virtual-networks-udr-overview.md).
Definições comuns do Gestor de Recursos Azure: Para saber mais sobre as definições comuns do Gestor de Recursos Azure, consulte [o registo de atividades,](../azure-monitor/platform/platform-logs-overview.md)o controlo de [acesso (IAM)](../role-based-access-control/overview.md), [as tags,](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [as fechaduras](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)e [o script de automação](../azure-resource-manager/templates/export-template-portal.md).

<a name="view-settings-commands"></a>**Comandos**

Se um endereço IPv6 for atribuído a uma interface de rede, a saída PowerShell devolve o facto de que o endereço está atribuído, mas não devolve o endereço atribuído. Da mesma forma, o CLI devolve o facto de que o endereço está atribuído, mas devolve *nulos* na sua saída para o endereço.

|Ferramenta|Comando|
|---|---|
|CLI|[lista de nic de rede az](/cli/azure/network/nic) para visualizar interfaces de rede na subscrição; [az network nic show](/cli/azure/network/nic) para visualizar definições para uma interface de rede|
|PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) para visualizar interfaces de rede na subscrição ou visualizar definições para uma interface de rede|

## <a name="change-dns-servers"></a>Alterar servidores DNS

O servidor DNS é atribuído pelo servidor Azure DHCP à interface de rede dentro do sistema operativo da máquina virtual. O servidor DNS atribuído é o que quer que a definição do servidor DNS seja para uma interface de rede. Para saber mais sobre as definições de resolução de nomes para uma interface de rede, consulte [a resolução Nome para máquinas virtuais](virtual-networks-name-resolution-for-vms-and-role-instances.md). A interface de rede pode herdar as definições da rede virtual ou usar as suas próprias definições únicas que substituem a definição para a rede virtual.

1. Na caixa que contém o texto *Procurar recursos* no topo do portal Azure, *digitar interfaces de rede*. Quando **as interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione a interface de rede para a quais pretende alterar um servidor DNS da lista.
3. Selecione **servidores DNS** em **DEFINIÇÕES**.
4. Selecione:
   - **Herda da rede virtual**: Escolha esta opção para herdar a definição do servidor DNS definida para a rede virtual a que a interface de rede está atribuída. Ao nível da rede virtual, é definido um servidor DNS personalizado ou o servidor DNS fornecido pelo Azure. O servidor DNS fornecido pelo Azure pode resolver nomes de anfitriões para recursos atribuídos à mesma rede virtual. A FQDN deve ser utilizada para resolver os recursos atribuídos a diferentes redes virtuais.
   - **Personalizado**: Pode configurar o seu próprio servidor DNS para resolver nomes em várias redes virtuais. Introduza o endereço IP do servidor que pretende utilizar como servidor DNS. O endereço do servidor DNS que especifica é atribuído apenas a esta interface de rede e substitui qualquer definição de DNS para a rede virtual a que a interface de rede está atribuída.
     >[!Note]
     >Se o VM utilizar um NIC que faz parte de um conjunto de disponibilidade, todos os servidores DNS especificados para cada um dos VMs de todos os NICs que fazem parte do conjunto de disponibilidade serão herdados.
5. Selecione **Guardar**.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic atualização](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Ativar ou desativar o encaminhamento ip

O reencaminhamento IP permite à máquina virtual que uma interface de rede está ligada a:
- Receber tráfego de rede não destinado a um dos endereços IP atribuídos a qualquer uma das configurações IP atribuídas à interface de rede.
- Envie tráfego de rede com um endereço IP de origem diferente daquele atribuído a uma das configurações IP de uma interface de rede.

A definição deve ser ativada para todas as interfaces de rede que estejam ligadas à máquina virtual que recebe o tráfego que a máquina virtual precisa de encaminhar. Uma máquina virtual pode encaminhar o tráfego quer tenha múltiplas interfaces de rede ou uma única interface de rede anexada à mesma. Embora o encaminhamento IP seja uma definição de Azure, a máquina virtual também deve executar uma aplicação capaz de encaminhar o tráfego, como firewall, otimização WAN e aplicações de equilíbrio de carga. Quando uma máquina virtual está a executar aplicações de rede, a máquina virtual é frequentemente referida como um aparelho virtual de rede. Pode ver uma lista de aparelhos virtuais prontos para implantar a rede no [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) O reencaminhamento IP é normalmente utilizado com rotas definidas pelo utilizador. Para saber mais sobre as rotas definidas pelo utilizador, consulte [as rotas definidas pelo Utilizador.](virtual-networks-udr-overview.md)

1. Na caixa que contém o texto *Procurar recursos* no topo do portal Azure, *digitar interfaces de rede*. Quando **as interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione a interface de rede para a seguinte forma ativar ou desativar o encaminhamento IP.
3. Selecione **configurações IP** na secção **DEFINIÇÕES.**
4. Selecione **Ativado** ou **Desativado** (definição predefinitiva) para alterar a definição.
5. Selecione **Guardar**.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic atualização](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Alterar a atribuição de sub-redes

Pode alterar a sub-rede, mas não a rede virtual, a que uma interface de rede é atribuída.

1. Na caixa que contém o texto *Procurar recursos* no topo do portal Azure, *digitar interfaces de rede*. Quando **as interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione a interface de rede para a quais pretende alterar a atribuição da sub-rede.
3. Selecione **configurações IP** em **DEFINIÇÕES**. Se quaisquer endereços IP privados para quaisquer configurações IP listadas tiverem **(Estática)** ao lado, deve alterar o método de atribuição de endereço IP para dinâmico, completando os passos que se seguem. Todos os endereços IP privados devem ser atribuídos com o método de atribuição dinâmico para alterar a atribuição de sub-rede para a interface de rede. Se os endereços forem atribuídos com o método dinâmico, continue a passo cinco. Se quaisquer endereços IPv4 forem atribuídos com o método de atribuição estática, complete os seguintes passos para alterar o método de atribuição para dinâmico:
   - Selecione a configuração IP que pretende alterar o método de atribuição de endereços IPv4 para a lista de configurações IP.
   - Selecione **Dynamic** para o método de **atribuição** de endereço IP privado. Não é possível atribuir um endereço IPv6 com o método de atribuição estática.
   - Selecione **Guardar**.
4. Selecione a sub-rede que pretende mover a interface de rede para a lista de drop-down **sub-rede.**
5. Selecione **Guardar**. Novos endereços dinâmicos são atribuídos a partir da gama de endereços da sub-rede para a nova sub-rede. Depois de atribuir a interface de rede a uma nova sub-rede, pode atribuir um endereço IPv4 estático a partir da nova gama de endereços da sub-rede, se escolher. Para saber mais sobre a adição, alteração e remoção de endereços IP para uma interface de rede, consulte [gerir endereços IP](virtual-network-network-interface-addresses.md).

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic ip-config atualização](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Adicionar ou remover de grupos de segurança de aplicações

Só é possível adicionar uma interface de rede ou remover uma interface de rede de um grupo de segurança de aplicações que utilize o portal se a interface de rede estiver ligada a uma máquina virtual. Pode utilizar o PowerShell ou o CLI Azure para adicionar uma interface de rede ou remover uma interface de rede de um grupo de segurança de aplicações, quer a interface da rede esteja ou não ligada a uma máquina virtual. Saiba mais sobre [os grupos de segurança de aplicações](./network-security-groups-overview.md#application-security-groups) e como [criar um grupo de segurança de aplicações.](manage-network-security-group.md)

1. Na caixa de *recursos, serviços e docs de busca* no topo do portal, comece a digitar o nome de uma máquina virtual que tem uma interface de rede a que pretende adicionar ou remover de um grupo de segurança de aplicações. Quando o nome do seu VM aparecer nos resultados da pesquisa, selecione-o.
2. Em **DEFINIÇÕES**, selecione **Redes**.  Selecione **Grupos de Segurança de Aplicações** e, em seguida, **Configure os grupos** de segurança da aplicação elege os grupos de segurança de aplicação a que pretende adicionar a interface de rede ou desescolh os grupos de segurança da aplicação a que pretende remover a interface de rede e, em seguida, selecione **Save**. Apenas as interfaces de rede existentes na mesma rede virtual podem ser adicionadas ao mesmo grupo de segurança de aplicações. O grupo de segurança da aplicação deve existir no mesmo local que a interface de rede.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az network nic atualização](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Associar ou dissociar um grupo de segurança de rede

1. Na caixa de pesquisa no topo do portal, introduza *interfaces* de rede na caixa de pesquisa. Quando **as interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione a interface de rede na lista a que pretende associar um grupo de segurança de rede ou dissociar um grupo de segurança de rede.
3. Selecione **grupo de segurança de rede** em **DEFINIÇÕES**.
4. Selecione **Editar**.
5. Selecione **o grupo de segurança da Rede** e, em seguida, selecione o grupo de segurança de rede que pretende associar à interface de rede, ou selecione **Nenhum**, para dissociar um grupo de segurança de rede.
6. Selecione **Guardar**.

**Comandos**

- Azure CLI: [atualização do nic da rede az](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Eliminar uma interface de rede

Pode eliminar uma interface de rede desde que não esteja ligada a uma máquina virtual. Se uma interface de rede estiver ligada a uma máquina virtual, deve primeiro colocar a máquina virtual no estado parado (deallocated) e, em seguida, separar a interface de rede da máquina virtual. Para separar uma interface de rede de uma máquina virtual, complete os passos em [separar uma interface de rede de uma máquina virtual](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). No entanto, não é possível descodémar uma interface de rede de uma máquina virtual se for a única interface de rede ligada à máquina virtual. Uma máquina virtual deve ter sempre pelo menos uma interface de rede ligada à mesma. A eliminação de uma máquina virtual destaca todas as interfaces de rede que lhe estão ligadas, mas não elimina as interfaces de rede.

1. Na caixa que contém o texto *Procurar recursos* no topo do portal Azure, *digitar interfaces de rede*. Quando **as interfaces de rede** aparecerem nos resultados da pesquisa, selecione-as.
2. Selecione a interface de rede na lista que pretende eliminar.
3. Para **visão geral** selecione **eliminar**.
4. Selecione **Sim** para confirmar a eliminação da interface de rede.

Quando elimina uma interface de rede, quaisquer endereços MAC ou IP que lhe sejam atribuídos são lançados.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[az rede nic delete](/cli/azure/network/nic)|
|PowerShell|[Remover-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Resolver problemas de conectividade

Se não conseguir comunicar de ou para uma máquina virtual, as regras de segurança do grupo de segurança de rede ou as rotas eficazes para uma interface de rede podem estar a causar o problema. Tem as seguintes opções para ajudar a resolver o problema:

### <a name="view-effective-security-rules"></a>Ver regras de segurança eficazes

As regras de segurança eficazes para cada interface de rede anexada a uma máquina virtual são uma combinação das regras que criou num grupo de segurança de rede e [regras de segurança predefinidos](./network-security-groups-overview.md#default-security-rules). Compreender as regras de segurança eficazes de uma interface de rede pode ajudá-lo a determinar por que não consegue comunicar de ou para uma máquina virtual. Pode ver as regras eficazes para qualquer interface de rede que esteja ligada a uma máquina virtual em funcionamento.

1. Na caixa de pesquisa no topo do portal, insira o nome de uma máquina virtual para a seguinte a visão de regras de segurança eficazes. Se não souber o nome de uma máquina virtual, introduza *máquinas virtuais* na caixa de pesquisa. Quando **as máquinas virtuais** aparecerem nos resultados da pesquisa, selecione-as e, em seguida, selecione uma máquina virtual da lista.
2. Selecione **rede em** **DEFINIÇÕES**.
3. Selecione o nome de uma interface de rede.
4. Selecione **regras de segurança eficazes** ao abrigo **do SUPPORT + TROUBLESHOOTING**.
5. Reveja a lista de regras de segurança eficazes para determinar se existem as regras corretas para a sua comunicação de entrada e saída necessária. Saiba mais sobre o que vê na lista na [visão geral do grupo de segurança network](./network-security-groups-overview.md).

A funcionalidade de verificação do fluxo IP do Azure Network Watcher também pode ajudá-lo a determinar se as regras de segurança estão a impedir a comunicação entre uma máquina virtual e um ponto final. Para saber mais, consulte o [fluxo IP verificar](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Comandos**

- Azure CLI: [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Ver rotas eficazes

As rotas eficazes para as interfaces de rede ligadas a uma máquina virtual são uma combinação de rotas padrão, quaisquer rotas que tenha criado, e quaisquer rotas propagadas a partir de redes de acesso através de BGP através de um gateway de rede virtual Azure. Compreender as rotas eficazes de uma interface de rede pode ajudá-lo a determinar por que não consegue comunicar de ou para uma máquina virtual. Pode ver as rotas eficazes para qualquer interface de rede que esteja ligada a uma máquina virtual em execução.

1. Na caixa de pesquisa no topo do portal, insira o nome de uma máquina virtual para a seguinte a visão de regras de segurança eficazes. Se não souber o nome de uma máquina virtual, introduza *máquinas virtuais* na caixa de pesquisa. Quando **as máquinas virtuais** aparecerem nos resultados da pesquisa, selecione-as e, em seguida, selecione uma máquina virtual da lista.
2. Selecione **rede em** **DEFINIÇÕES**.
3. Selecione o nome de uma interface de rede.
4. Selecione **rotas eficazes** em **SUPORTE + RESOLUÇÃO DE PROBLEMAS.**
5. Reveja a lista de rotas eficazes para determinar se existem as rotas corretas para a sua comunicação de entrada e saída necessária. Saiba mais sobre o que vê na lista na [visão geral do Encaminhamento](virtual-networks-udr-overview.md).

A próxima funcionalidade de lúpulo do Azure Network Watcher também pode ajudá-lo a determinar se as rotas estão a impedir a comunicação entre uma máquina virtual e um ponto final. Para saber mais, consulte [o Next Hop.](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

**Comandos**

- Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Permissões

Para executar tarefas em interfaces de rede, a sua conta deve ser atribuída à função [de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída às permissões apropriadas listadas no quadro seguinte:

| Ação                                                                     | Name                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Obtenha interface de rede                                     |
| Microsoft.Network/networkInterfaces/write                                  | Criar ou atualizar interface de rede                        |
| Microsoft.Network/networkInterfaces/join/action                            | Anexar uma interface de rede a uma máquina virtual           |
| Microsoft.Network/networkInterfaces/delete                                 | Eliminar interface de rede                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Junte-se a um recurso para uma interface de rede através de uma servi...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Obtenha tabela de rota eficaz interface de rede               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/ação  | Obtenha grupos de segurança eficazes da interface de rede           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Obter equilibradores de carga de interface de rede                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Obter associação de serviços                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Criar ou atualizar uma associação de serviços                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Eliminar associação de serviços                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/valide/action    | Validar associação de serviços                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Obtenha configuração IP de interface de rede                    |

## <a name="next-steps"></a>Passos seguintes

- Criar um VM com vários NICs utilizando o [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Criar um único NIC VM com vários endereços IPv4 utilizando o [Azure CLI](virtual-network-multiple-ip-addresses-cli.md) ou [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Crie um único NIC VM com um endereço IPv6 privado (atrás de um Equilibrista de Carga Azure) utilizando o modelo [Azure CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ou [Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Crie uma interface de rede utilizando scripts de amostra [powerShell](powershell-samples.md) ou [Azure CLI,](cli-samples.md) ou usando o modelo do Gestor [de Recursos](template-samples.md) Azure
- Criar e atribuir [definições de Política Azure](./policy-reference.md) para redes virtuais