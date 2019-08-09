---
title: Criar, alterar ou excluir um endereço IP público do Azure | Microsoft Docs
description: Saiba como criar, alterar ou excluir um endereço IP público.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: 6103a88aa06dac5c0a7e6f1fba39b0d1d0706d51
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854328"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Criar, alterar ou excluir um endereço IP público

Saiba mais sobre um endereço IP público e como criar, alterar e excluir um. Um endereço IP público é um recurso com suas próprias configurações configuráveis. A atribuição de um endereço IP público a um recurso do Azure que dá suporte a endereços IP públicos permite:
- Comunicação de entrada da Internet para o recurso, como VMS (máquinas virtuais) do Azure, gateways de Aplicativo Azure, balanceadores de carga do Azure, gateways de VPN do Azure e outros. Você ainda poderá se comunicar com alguns recursos, como VMs, da Internet, se uma VM não tiver um endereço IP público atribuído a ela, desde que a VM faça parte de um pool de back-end do balanceador de carga e o balanceador de carga receba um endereço IP público. Para determinar se um recurso para um serviço específico do Azure pode ser atribuído a um endereço IP público ou se ele pode ser comunicado por meio do endereço IP público de um recurso diferente do Azure, consulte a documentação do serviço.
- Conectividade de saída para a Internet usando um endereço IP previsível. Por exemplo, uma máquina virtual pode comunicar a saída à Internet sem um endereço IP público atribuído a ela, mas seu endereço é o endereço de rede traduzido pelo Azure para um endereço público imprevisível, por padrão. A atribuição de um endereço IP público a um recurso permite que você saiba qual endereço IP é usado para a conexão de saída. Embora previsível, o endereço pode ser alterado, dependendo do método de atribuição escolhido. Para obter mais informações, consulte [criar um endereço IP público](#create-a-public-ip-address). Para saber mais sobre as conexões de saída dos recursos do Azure, confira [entender as conexões de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Se você ainda não tiver uma conta do Azure, Inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o portal, https://portal.azure.com abra e faça logon com sua conta do Azure.
- Se estiver usando comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/powershell)ou executando o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o módulo Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se você estiver usando comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure cloud Shell](https://shell.azure.com/bash)ou executando a CLI do seu computador. Este tutorial requer o CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se você estiver executando o CLI do Azure localmente, também precisará executar `az login` para criar uma conexão com o Azure.

A conta que você faz logon ou conecta-se ao Azure com o, deve ser atribuída à função [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas em [permissões](#permissions).

Os endereços IP públicos têm um custo nominal. Para exibir os preços, leia a página de [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

1. Na parte superior, canto esquerdo do portal, selecione **+ criar um recurso**.
2. Insira o *endereço IP público* na caixa *Pesquisar no Marketplace* . Quando o **endereço IP público** aparecer nos resultados da pesquisa, selecione-o.
3. Em **endereço IP público**, selecione **criar**.
4. Insira ou selecione valores para as configurações a seguir, em **criar endereço IP público**e selecione **criar**:

   |Definição|Obrigatório?|Detalhes|
   |---|---|---|
   |Nome|Sim|O nome deve ser exclusivo dentro do grupo de recursos que você selecionar.|
   |SKU|Sim|Todos os endereços IP públicos criados antes da introdução de SKUs são endereços IP públicos de SKU **básico** . Você não pode alterar o SKU depois que o endereço IP público é criado. Uma máquina virtual autônoma, máquinas virtuais em um conjunto de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais podem usar SKUs Basic ou Standard. Não é permitido misturar SKUs entre máquinas virtuais em conjuntos de disponibilidade ou conjuntos de dimensionamento. **Básico** SKU Se você estiver criando um endereço IP público em uma região que dá suporte a zonas de disponibilidade, a configuração **zona de disponibilidade** será definida como *nenhum* por padrão. Os IPs públicos básicos não dão suporte a zonas de disponibilidade. **Padrão** SKU Um IP público de SKU padrão pode ser associado a uma máquina virtual ou a um front-end de balanceador de carga. Se você estiver criando um endereço IP público em uma região que dá suporte a zonas de disponibilidade, a configuração **zona de disponibilidade** será definida como com *redundância de zona* por padrão. Para obter mais informações sobre zonas de disponibilidade, consulte a configuração de **zona de disponibilidade** . O SKU padrão será necessário se você associar o endereço a um balanceador de carga padrão. Para saber mais sobre os balanceadores de carga padrão, consulte [SKU do Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Quando atribui um endereço IP público de SKU standard a uma interface de rede de máquina virtual, tem de permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até criar e associar um grupo de segurança de rede e permitir explicitamente o tráfego pretendido.|
   |Versão do IP|Sim| Selecione IPv4 ou IPv6. Embora os endereços IPv4 públicos possam ser atribuídos a vários recursos do Azure, um endereço IP público IPv6 só pode ser atribuído a um balanceador de carga voltado para a Internet. O balanceador de carga pode balancear a carga do tráfego IPv6 para as máquinas virtuais do Azure. Saiba mais sobre o [tráfego IPv6 de balanceamento de carga para máquinas virtuais](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se você selecionou o **SKU Standard**, não terá a opção de selecionar *IPv6*. Você só pode criar um endereço IPv4 ao usar o **SKU Standard**.|
   |Atribuição de endereços IP|Sim|**Dinâmico** Os endereços dinâmicos são atribuídos somente depois que um endereço IP público é associado a um recurso do Azure e o recurso é iniciado pela primeira vez. Os endereços dinâmicos podem ser alterados se forem atribuídos a um recurso, como uma máquina virtual, e a máquina virtual for interrompida (desalocada) e, em seguida, reiniciada. O endereço permanece o mesmo se uma máquina virtual for reinicializada ou interrompida (mas não desalocada). Os endereços dinâmicos são liberados quando um recurso de endereço IP público é dissociado de um recurso ao qual ele está associado. **Auto-estática** Os endereços estáticos são atribuídos quando um endereço IP público é criado. Os endereços estáticos não são liberados até que um recurso de endereço IP público seja excluído. Se o endereço não estiver associado a um recurso, você poderá alterar o método de atribuição depois que o endereço for criado. Se o endereço estiver associado a um recurso, talvez você não consiga alterar o método de atribuição. Se você selecionar *IPv6* para a **versão de IP**, o método de atribuição será *dinâmico*. Se você selecionar *Standard* para **SKU**, o método de atribuição será *estático*.|
   |Tempo limite de inatividade (minutos)|Não|A quantidade de minutos para manter uma conexão TCP ou HTTP aberta sem depender de clientes para enviar mensagens Keep-Alive. Se você selecionar IPv6 para a **versão IP**, esse valor não poderá ser alterado. |
   |Etiqueta de nome DNS|Não|Deve ser exclusivo no local do Azure em que você cria o nome (em todas as assinaturas e todos os clientes). O Azure registra automaticamente o nome e o endereço IP em seu DNS para que você possa se conectar a um recurso com o nome. O Azure acrescenta uma sub-rede padrão, como *Location.cloudapp.Azure.com* (em que local é o local selecionado) para o nome que você fornece, para criar o nome DNS totalmente qualificado. Se você optar por criar ambas as versões de endereço, o mesmo nome DNS será atribuído aos endereços IPv4 e IPv6. O DNS padrão do Azure contém registros de nome IPv4 A e IPv6 AAAA e responde com ambos os registros quando o nome DNS é pesquisado. O cliente escolhe a qual endereço (IPv4 ou IPv6) se comunicará. Em vez de, ou além de, utilizar a etiqueta de nome DNS com o sufixo predefinido, pode utilizar o serviço DNS do Azure para configurar um nome DNS com um sufixo personalizado que é resolvido para o endereço IP público. Para obter mais informações, veja [Utilizar o DNS do Azure com um endereço IP público do Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Criar um endereço IPv6 (ou IPv4)|Não| Se o IPv6 ou IPv4 é exibido depende do que você selecionou para a **versão IP**. Por exemplo, se você selecionar **IPv4** para a **versão IP**, o **IPv6** será exibido aqui. Se você selecionar *Standard* para **SKU**, não terá a opção de criar um endereço IPv6.
   |Nome (visível somente se você marcou a caixa de seleção **criar um endereço IPv6 (ou IPv4)** )|Sim, se você marcar a caixa de seleção **criar um IPv6** (ou IPv4).|O nome deve ser diferente do nome que você inserir para o primeiro **nome** da lista. Se você optar por criar um endereço IPv4 e IPv6, o portal criará dois recursos de endereço IP público separados, um com cada versão de endereço IP atribuída a ele.|
   |Atribuição de endereço IP (visível somente se você marcou a caixa de seleção **criar um endereço IPv6 (ou IPv4)** )|Sim, se você marcar a caixa de seleção **criar um IPv6** (ou IPv4).|Se a caixa de seleção disser **criar um endereço IPv4**, você poderá selecionar um método de atribuição. Se a caixa de seleção disser **criar um endereço IPv6**, você não poderá selecionar um método de atribuição, pois ele deve ser **dinâmico**.|
   |Subscription|Sim|Deve existir na mesma [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que o recurso ao qual você deseja associar o endereço IP público.|
   |Resource group|Sim|Pode existir no mesmo [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) , ou diferente, que o recurso ao qual você deseja associar o endereço IP público.|
   |Location|Sim|Deve existir no mesmo [local](https://azure.microsoft.com/regions), também conhecido como região, como o recurso ao qual você deseja associar o endereço IP público.|
   |Zona de disponibilidade| Não | Essa configuração só aparecerá se você selecionar um local com suporte. Para obter uma lista de locais com suporte, consulte [visão geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se você selecionou o SKU **básico** , *nenhum* será selecionado automaticamente para você. Se preferir garantir uma zona específica, você poderá selecionar uma zona específica. Qualquer uma das opções não tem redundância de zona. Se você selecionou o SKU **Standard** : Com redundância de zona é selecionado automaticamente para você e torna o caminho de dados resiliente a falhas de zona. Se preferir garantir uma zona específica, que não seja resiliente à falha de zona, você poderá selecionar uma zona específica.

**Comandos**

Embora o portal forneça a opção de criar dois recursos de endereço IP público (um IPv4 e um IPv6), os comandos da CLI e do PowerShell a seguir criam um recurso com um endereço para uma versão IP ou outra. Se você quiser dois recursos de endereço IP público, um para cada versão de IP, deverá executar o comando duas vezes, especificando diferentes nomes e versões para os recursos de endereço IP público.

|Ferramenta|Comando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Exibir, alterar as configurações ou excluir um endereço IP público

1. Na caixa que contém os recursos de *pesquisa* de texto na parte superior da portal do Azure, digite *endereço IP público*. Quando os **endereços IP públicos** aparecerem nos resultados da pesquisa, selecione-os.
2. Selecione o nome do endereço IP público que você deseja exibir, altere as configurações ou exclua da lista.
3. Conclua uma das opções a seguir, dependendo se você deseja exibir, excluir ou alterar o endereço IP público.
   - **Exibição**: A seção **visão geral** mostra as configurações de chave para o endereço IP público, como o adaptador de rede ao qual ela está associada (se o endereço estiver associado a um adaptador de rede). O portal não exibe a versão do endereço (IPv4 ou IPv6). Para exibir as informações de versão, use o comando do PowerShell ou da CLI para exibir o endereço IP público. Se a versão do endereço IP for IPv6, o endereço atribuído não será exibido pelo portal, pelo PowerShell ou pela CLI.
   - **Excluir**: Para excluir o endereço IP público, selecione **excluir** na seção **visão geral** . Se o endereço estiver atualmente associado a uma configuração de IP, ele não poderá ser excluído. Se o endereço estiver atualmente associado a uma configuração, selecione **dissociar** para dissociar o endereço da configuração de IP.
   - **Alterar**: selecione **configuração**. Altere as configurações usando as informações na etapa 4 de [criar um endereço IP público](#create-a-public-ip-address). Para alterar a atribuição de um endereço IPv4 de estático para dinâmico, primeiro você deve dissociar o endereço IPv4 público da configuração de IP à qual ele está associado. Você pode alterar o método de atribuição para dinâmico e selecionar **associar** para associar o endereço IP à mesma configuração de IP, uma configuração diferente ou pode deixá-lo dissociado. Para dissociar um endereço IP público, na seção **visão geral** , selecione **dissociar**.

   >[!WARNING]
   >Quando você altera o método de atribuição de estático para dinâmico, você perde o endereço IP que foi atribuído ao endereço IP público. Embora os servidores DNS públicos do Azure mantenham um mapeamento entre endereços estáticos ou dinâmicos e qualquer rótulo de nome DNS (se você tiver definido um), um endereço IP dinâmico pode ser alterado quando a máquina virtual é iniciada depois de estar no estado parado (desalocado). Para impedir que o endereço seja alterado, atribua um endereço IP estático.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[AZ Network Public-IP List](/cli/azure/network/public-ip#az-network-public-ip-list) para listar endereços IP públicos, [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show) para mostrar as configurações; [AZ Network Public-IP Update](/cli/azure/network/public-ip#az-network-public-ip-update) para atualizar; [AZ Network Public-IP Delete](/cli/azure/network/public-ip#az-network-public-ip-delete) para excluir|
|PowerShell|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para recuperar um objeto de endereço IP público e exibir suas configurações, [set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) para atualizar as configurações; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) para excluir|

## <a name="assign-a-public-ip-address"></a>Atribuir um endereço IP público

Saiba como atribuir um endereço IP público aos seguintes recursos:

- Uma VM [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ao criar) ou para uma [VM existente](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Load Balancer voltadas para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Aplicativo Azure gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conexão site a site usando um gateway de VPN do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conjunto de dimensionamento de máquinas virtuais do Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Permissões

Para executar tarefas em endereços IP públicos, sua conta deve ser atribuída à função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas na tabela a seguir:

| Action                                                             | Nome                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Ler um endereço IP público                                          |
| Microsoft.Network/publicIPAddresses/write                          | Criar ou atualizar um endereço IP público                           |
| Microsoft.Network/publicIPAddresses/delete                         | Excluir um endereço IP público                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Associar um endereço IP público a um recurso                    |

## <a name="next-steps"></a>Passos Seguintes

- Criar um endereço IP público usando o [PowerShell](powershell-samples.md) ou [CLI do Azure](cli-samples.md) scripts de exemplo ou usando modelos do Azure [Resource Manager](template-samples.md)
- Criar e aplicar a [política do Azure](policy-samples.md) para endereços IP públicos
