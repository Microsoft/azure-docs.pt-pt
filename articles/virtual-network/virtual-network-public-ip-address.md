---
title: Criar, alterar ou eliminar um endereço IP público do Azure | Documentos da Microsoft
description: Saiba como criar, alterar ou eliminar um endereço IP público.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 2e6f3ce0c01674913dcb1f1980264d205eb4fcd3
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652786"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Criar, alterar ou eliminar um endereço IP público

Saiba mais sobre um endereço IP público e como criar, alterar e eliminar um. Um endereço IP público é um recurso com suas próprias definições configuráveis. Permite que a atribuição de um endereço IP público a um recurso do Azure que suporta endereços IP públicos:
- Comunicação de entrada da Internet para o recurso, tal como máquinas virtuais (VM) do Azure, os Gateways de aplicação do Azure, balanceadores de carga do Azure, Gateways de VPN do Azure e outros. Continua a pode comunicar com alguns recursos, tais como VMs, a partir da Internet, se uma VM não tem um endereço IP público atribuído ao mesmo, desde que a VM é a parte de um conjunto de back-end de Balanceador de carga e o Balanceador de carga é atribuído um endereço IP público. Para determinar se um recurso para um serviço do Azure específico pode ser atribuído um endereço IP público ou, se ele pode ser comunicado através do endereço IP público de um recurso do Azure diferente, consulte a documentação para o serviço.
- Conectividade de saída à Internet através de um endereço IP previsível. Por exemplo, uma máquina virtual podem comunicar saída à Internet sem um endereço IP público atribuído ao mesmo, mas o seu endereço é o endereço de rede traduzido pelo Azure para um endereço público imprevisível, por predefinição. Atribuir um endereço IP público a um recurso permite-lhe saber qual o endereço IP é utilizado para a ligação de saída. Embora previsível, pode alterar o endereço, dependendo do método de atribuição escolhido. Para obter mais informações, consulte [criar um endereço IP público](#create-a-public-ip-address). Para saber mais sobre as ligações de saída dos recursos do Azure, veja [compreender as ligações de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir os passos em qualquer secção deste artigo:

- Se ainda não tiver uma conta do Azure, inscreva-se para uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se utilizar o portal, abra https://portal.azure.come inicie sessão com a sua conta do Azure.
- Se utilizar comandos do PowerShell para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/powershell), ou ao executar o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o Azure PowerShell versão 1.0.0 do módulo ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comandos (CLI) do Azure para concluir tarefas neste artigo, a executar os comandos do [Azure Cloud Shell](https://shell.azure.com/bash), ou ao executar a CLI do seu computador. Este tutorial requer a versão 2.0.31 CLI do Azure ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar a CLI do Azure localmente, terá também de executar `az login` para criar uma ligação com o Azure.

A conta iniciar sessão no, ou ligar ao Azure, tem de ser atribuída para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atribuída as ações adequadas listadas na [permissões ](#permissions).

Endereços IP públicos têm um custo nominal. Para ver os preços, leia os [preços de endereços IP](https://azure.microsoft.com/pricing/details/ip-addresses) página.

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

1. No canto superior, à esquerda do portal, selecione **+ criar um recurso**.
2. Introduza *endereço ip público* no *pesquisar no Marketplace* caixa. Quando **endereço IP público** aparecer nos resultados da pesquisa, selecione.
3. Sob **endereço IP público**, selecione **criar**.
4. Introduza ou selecione os valores para as seguintes definições em **Criar endereço IP público**, em seguida, selecione **criar**:

   |Definição|Necessário?|Detalhes|
   |---|---|---|
   |Name|Sim|O nome tem de ser exclusivo no grupo de recursos que selecionar.|
   |SKU|Sim|Endereços IP de todos os públicos criados antes da introdução de SKUs são **básica** endereços IP públicos de SKU. Não é possível alterar o SKU depois do endereço IP público é criado. Uma máquina virtual autónoma, máquinas virtuais dentro de um conjunto de disponibilidade ou conjuntos de dimensionamento de máquina virtual pode utilizar o básico ou Standard SKUs. Não é permitida a mistura de SKUs entre máquinas virtuais dentro de conjuntos de disponibilidade ou conjuntos de dimensionamento. **Básico** SKU: Se estiver a criar um endereço IP público numa região que suporta zonas de disponibilidade, o **zona de disponibilidade** definição está definida como *nenhum* por predefinição. Pode optar por selecionar uma zona de disponibilidade para garantir uma zona específica para seu endereço IP público. **Padrão** SKU: Um IP público de SKU padrão pode ser associado a uma máquina virtual ou um load balancer front-end. Se estiver a criar um endereço IP público numa região que suporta zonas de disponibilidade, o **zona de disponibilidade** definição está definida como *redundância de zona* por predefinição. Para obter mais informações sobre as zonas de disponibilidade, consulte a **zona de disponibilidade** definição. O SKU standard é necessário se associar o endereço para um balanceador de carga Standard. Para saber mais sobre balanceadores de carga standard, veja [Balanceador de carga standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Quando atribui um endereço IP público de SKU standard a uma interface de rede de máquina virtual, tem de permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até criar e associar um grupo de segurança de rede e permitir explicitamente o tráfego pretendido.|
   |Versão do IP|Sim| Selecione IPv4 ou IPv6. Enquanto os endereços IPv4 públicos podem ser atribuídos a vários recursos do Azure, um endereço IP público IPv6 só pode ser atribuído a um balanceador de carga com acesso à Internet. O Balanceador de carga pode balancear o tráfego de IPv6 para máquinas virtuais do Azure. Saiba mais sobre [tráfego de IPv6 para máquinas virtuais de balanceamento de carga](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se tiver selecionado o **Standard SKU**, não tem a opção de selecionar *IPv6*. Só pode criar um endereço IPv4 ao utilizar o **Standard SKU**.|
   |Atribuição de endereços IP|Sim|**Dinâmico:** Endereços dinâmicos são atribuídos apenas depois de um endereço IP público é associado a um recurso do Azure e o recurso é iniciado pela primeira vez. Endereços dinâmicos podem mudar se foram atribuídos a um recurso, como uma máquina virtual, e a máquina virtual está parada (desalocada) e, em seguida, reiniciada. O endereço permanece o mesmo se uma máquina virtual é reiniciada ou parada (mas não desalocada). Endereços dinâmicos são lançados quando um recurso de endereço IP público é desassociado de um recurso que está associada. **Estático:** Endereços estáticos são atribuídos quando é criado um endereço IP público. Endereços estáticos não são lançados até que um recurso de endereço IP público seja eliminado. Se o endereço não está associado a um recurso, pode alterar o método de atribuição depois do endereço é criado. Se o endereço está associado a um recurso, não poderá alterar o método de atribuição. Se selecionou *IPv6* para o **versão IP**, o método de atribuição é *dinâmica*. Se selecionou *padrão* para **SKU**, o método de atribuição é *estático*.|
   |Tempo limite de inatividade (minutos)|Não|O número de minutos para manter uma ligação TCP ou HTTP aberta sem depender de clientes para enviar mensagens keep-alive. Se selecionar o IPv6 para **versão do IP**, este valor não pode ser alterado. |
   |Etiqueta de nome DNS|Não|Tem de ser exclusivo no local do Azure que cria o nome de no (em todas as subscrições e todos os clientes). Azure regista automaticamente o nome e endereço IP no seu DNS para que possa ligar a um recurso com o nome. Azure acrescenta uma sub-rede de predefinição como *location.cloudapp.azure.com* (onde a localização é a localização que selecionou) para o nome que indicar, para criar o nome DNS completamente qualificado. Se optar por criar ambas as versões de endereço, o mesmo nome DNS é atribuído para endereços IPv4 e IPv6. Padrão do Azure DNS contém tanto IPv4 e IPv6 AAAA registos de nomes e responde com ambos os registros, quando o nome DNS é pesquisado. O cliente escolhe qual o endereço (IPv4 ou IPv6) para comunicar com. Em vez de, ou além de, utilizar a etiqueta de nome DNS com o sufixo predefinido, pode utilizar o serviço DNS do Azure para configurar um nome DNS com um sufixo personalizado que é resolvido para o endereço IP público. Para obter mais informações, veja [Utilizar o DNS do Azure com um endereço IP público do Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Criar um endereço IPv6 (ou IPv4)|Não| Se é apresentado o IPv6 ou IPv4 está dependente de a selecionar para **versão do IP**. Por exemplo, se selecionar **IPv4** para **versão de IP**, **IPv6** é apresentado aqui. Se selecionou *padrão* para **SKU**, não tem a opção para criar um endereço IPv6.
   |Nome (apenas visíveis, se tiver selecionado o **criar um endereço IPv6 (ou IPv4)** caixa de verificação)|Sim, se selecionar a **criar um IPv6** (ou IPv4) caixa de verificação.|O nome tem de ser diferente do nome que introduzir para o primeiro **nome** nesta lista. Se optar por criar um IPv4 e um endereço IPv6, o portal cria dois separados endereço recursos de IP público, um com cada versão do endereço IP atribuído ao mesmo.|
   |Atribuição de endereços IP (apenas visíveis, se tiver selecionado o **criar um endereço IPv6 (ou IPv4)** caixa de verificação)|Sim, se selecionar a **criar um IPv6** (ou IPv4) caixa de verificação.|Se a caixa de verificação indicar **criar um endereço IPv4**, pode selecionar um método de atribuição. Se a caixa de verificação indicar **criar um endereço IPv6**, não é possível selecionar um método de atribuição, como deve ser **dinâmico**.|
   |Subscrição|Sim|Tem de existir na mesma [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) como o recurso que pretende associar o endereço IP público a.|
   |Grupo de recursos|Sim|Pode existir na mesma ou diferentes, [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) como o recurso que pretende associar o endereço IP público a.|
   |Localização|Sim|Tem de existir na mesma [localização](https://azure.microsoft.com/regions), também referido como região, como o recurso que pretende associar o IP público de endereços para.|
   |Zona de disponibilidade| Não | Esta definição só é apresentada se selecionar uma localização suportada. Para obter uma lista das localizações suportadas, consulte [descrição geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se tiver selecionado o **básica** SKU, *nenhum* é selecionado automaticamente para. Se preferir garantir uma zona específica, pode selecionar uma zona específica. De qualquer opção não está com redundância de zona. Se tiver selecionado o **padrão** SKU: Com redundância de zona é selecionado automaticamente para e torna o seu caminho de dados resiliente a falhas de zona. Se preferir garantir uma zona específica, que não é resiliente a falhas de zona, pode selecionar uma zona específica.

**Comandos**

Embora o portal fornece a opção para criar dois públicos recursos de endereço IP (IPv4 e um IPv6), os seguintes comandos da CLI e o PowerShell criam um recurso com um endereço para uma versão IP ou o outro. Se pretender que os dois endereços recursos de IP público, um para cada versão IP, tem de executar duas vezes, o comando especificando nomes diferentes e versões para os recursos de endereço IP públicos.

|Ferramenta|Comando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Ver, alterar as definições para ou eliminar um endereço IP público

1. Na caixa que contém o texto *recursos de pesquisa* na parte superior do portal do Azure, escreva *endereço ip público*. Quando **endereços IP públicos** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione o nome do endereço IP público que pretende ver, alterar as definições para ou eliminar da lista.
3. Realize uma das seguintes opções, dependendo se pretende ver, eliminar ou alterar o endereço IP público.
   - **Vista**: O **descrição geral** secção mostra as definições de chave para o endereço IP público, como a interface de rede está associado à (se o endereço está associado a uma interface de rede). O portal não apresenta a versão do endereço (IPv4 ou IPv6). Para ver as informações de versão, utilize o comando do PowerShell ou a CLI para ver o endereço IP público. Se a versão do endereço IP IPv6, o endereço atribuído não é apresentado no portal, PowerShell ou a CLI.
   - **Delete**: Para eliminar o endereço IP público, selecione **elimine** no **descrição geral** secção. Se o endereço está atualmente associado a uma configuração de IP, não pode ser eliminada. Se o endereço está atualmente associado uma configuração, selecione **desassocie** ao desassociar o endereço da configuração de IP.
   - **Alteração**: selecione **configuração**. Alterar as definições com as informações no passo 4 da [criar um endereço IP público](#create-a-public-ip-address). Para alterar a atribuição de um endereço IPv4 de estático para dinâmico, tem de dissociá primeiro o endereço IPv4 público da configuração de IP que está associado à. Em seguida, pode alterar o método de atribuição dinâmica e selecione **associar** para associar o IP endereço para a mesma configuração de IP, uma configuração diferente ou pode deixá-lo a foi desassociada. Para desassociar um endereço IP público, na **descrição geral** secção, selecione **desassocie**.

   >[!WARNING]
   >Quando altera o método de atribuição de estático para dinâmico, perde o endereço IP que foi atribuído para o endereço IP público. Embora os servidores DNS públicos do Azure manter um mapeamento entre endereços estáticos ou dinâmicos e qualquer etiqueta de nome DNS (se tiver definido um), um endereço IP dinâmico pode alterar quando a máquina virtual é iniciada depois de estar em estado parado (desalocada). Para impedir que o endereço de alteração, atribua um endereço IP estático.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[lista de public-ip de rede de AZ](/cli/azure/network/public-ip#az-network-public-ip-list) a lista os endereços IP públicos, [show de public-ip de rede de az](/cli/azure/network/public-ip#az-network-public-ip-show) para mostrar as definições; [atualização de public-ip de rede de az](/cli/azure/network/public-ip#az-network-public-ip-update) atualizar; [eliminar a rede de az public-ip](/cli/azure/network/public-ip#az-network-public-ip-delete) para eliminar|
|PowerShell|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para obter um objeto de endereço IP público e ver as respetivas definições, [conjunto AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) ao atualizar as definições; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) para eliminar|

## <a name="assign-a-public-ip-address"></a>Atribuir um endereço IP público

Saiba como atribuir um endereço IP público para os seguintes recursos:

- R [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM (ao criar), ou para um [VM existente](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Balanceador de carga com acesso à Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway de aplicação do Azure](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Ligação de site a site com um Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conjunto de dimensionamento de Máquina Virtual do Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Permissões

Para executar tarefas em endereços IP públicos, deve ser atribuída a sua conta para o [contribuinte de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) função ou a um [personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) função que é atribuída as ações adequadas listadas na tabela seguinte:

| Ação                                                             | Name                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Ler um endereço IP público                                          |
| Microsoft.Network/publicIPAddresses/write                          | Criar ou atualizar um endereço IP público                           |
| Microsoft.Network/publicIPAddresses/delete                         | Eliminar um endereço IP público                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Associar um endereço IP público a um recurso                    |

## <a name="next-steps"></a>Passos Seguintes

- Criar um através de endereços IP público [PowerShell](powershell-samples.md) ou [CLI do Azure](cli-samples.md) scripts ou utilizar o Azure de exemplo [modelos do Resource Manager](template-samples.md)
- Criar e aplicar [o Azure policy](policy-samples.md) para endereços IP público