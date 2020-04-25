---
title: Criar, alterar ou apagar um endereço IP público do Azure [ Microsoft Docs
description: Aprenda a criar, alterar ou apagar um endereço IP público.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: a03ace1553bd845d8a221e458fd47f3d8aa3d611
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146529"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Criar, alterar ou eliminar endereços IP públicos

Saiba mais sobre um endereço IP público e como criar, alterar e apagar um. Um endereço IP público é um recurso com as suas próprias configurações configuráveis. A atribuição de um endereço IP público a um recurso Azure que suporta endereços IP públicos permite:
- Comunicação de entrada da Internet para o recurso, tais como Máquinas Virtuais Azure (VM), Gateways de Aplicação Azure, Equilíbrios de Carga Azure, Gateways VPN Azure, entre outros. Ainda pode comunicar com alguns recursos, como VMs, a partir da Internet, se um VM não tiver um endereço IP público atribuído a ele, desde que o VM faça parte de um pool de back-end do equilibrador de carga, e o equilibrador de carga seja atribuído a um endereço IP público. Para determinar se um recurso para um serviço Azure específico pode ser atribuído a um endereço IP público, ou se pode ser comunicado através do endereço IP público de um recurso Azure diferente, consulte a documentação para o serviço.
- Conectividade de saída para a Internet usando um endereço IP previsível. Por exemplo, uma máquina virtual pode comunicar a saída para a Internet sem um endereço IP público atribuído à sua, mas o seu endereço é endereço de rede traduzido pelo Azure para um endereço público imprevisível, por padrão. Atribuir um endereço IP público a um recurso permite-lhe saber qual o endereço IP utilizado para a ligação de saída. Embora previsível, o endereço pode mudar, dependendo do método de atribuição escolhido. Para mais informações, consulte [Para obter informações](#create-a-public-ip-address)sobre o qual é o endereço IP. Para saber mais sobre as ligações de saída dos recursos azure, consulte [compreender as ligações de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Complete as seguintes tarefas antes de completar os passos em qualquer secção deste artigo:

- Se ainda não tem uma conta Azure, inscreva-se numa [conta de teste gratuita.](https://azure.microsoft.com/free)
- Se utilizar o https://portal.azure.comportal, abra e faça login na sua conta Azure.
- Se utilizar os comandos PowerShell para completar tarefas neste artigo, execute os comandos na Casca de [Nuvem Azure,](https://shell.azure.com/powershell)ou executando powerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer a versão 1.0.0 ou posterior do módulo PowerShell Azure. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comando Azure (CLI) para completar tarefas neste artigo, execute os comandos na [Casca de Nuvem Azure,](https://shell.azure.com/bash)ou executando o CLI a partir do seu computador. Este tutorial requer a versão Azure CLI 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI `az login` localmente, também precisa de correr para criar uma ligação com o Azure.

A conta em que inicia sessão, ou liga-se ao Azure, deve ser atribuída à função de contribuinte da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que lhe seja atribuída as ações adequadas enumeradas nas [Permissões](#permissions).

Os endereços IP públicos têm uma taxa nominal. Para ver os preços, leia a página de preços do [endereço IP.](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**.
2. Insira o *endereço IP público* na caixa Do *Mercado.* Quando o **endereço IP público** aparecer nos resultados da pesquisa, selecione-o.
3. Sob **o endereço IP público,** selecione **Criar**.
4. Introduza ou selecione valores para as seguintes definições, sob o **endereço IP criar público,** e depois selecione **Criar:**

   |Definição|Necessário?|Detalhes|
   |---|---|---|
   |Versão IP|Sim| Selecione IPv4 ou IPv6 ou Both. A seleção de Ambos resultará na criação de 2 endereços IP públicos- 1 endereço IPv4 e 1 endereço IPv6. Saiba mais sobre [o IPv6 em VNETs Azure.](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
   |SKU|Sim|Todos os endereços IP públicos criados antes da introdução de SKUs são endereços IP públicos **básicos** sKU. Não é possível alterar o SKU após a criação do endereço IP público. Uma máquina virtual autónoma, máquinas virtuais dentro de um conjunto de disponibilidade, ou conjuntos de escala de máquina virtual podem usar SKUs Básicoou Standard. Não é permitida a mistura de SKUs entre máquinas virtuais dentro de conjuntos de disponibilidade ou conjuntos de escala ou VMs autónomos. **Básico** SKU: Se estiver a criar um endereço IP público numa região que suporta zonas de disponibilidade, a definição **da zona de disponibilidade** está definida para *Nenhuma* por padrão. Os IPs públicos básicos não suportam as zonas de disponibilidade. **Padrão** SKU: Um IP público Padrão SKU pode ser associado a uma máquina virtual ou a uma extremidade frontal do equilíbrio de carga. Se estiver a criar um endereço IP público numa região que suporta zonas de disponibilidade, a definição **da zona de disponibilidade** está definida para zona *redundante* por padrão. Para mais informações sobre as zonas de disponibilidade, consulte a definição da **zona de disponibilidade.** O SKU padrão é necessário se associar o endereço a um equilibrista de carga Standard. Para saber mais sobre os equilibradores de carga padrão, consulte [o balanceer de carga Azure padrão SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Quando atribui um endereço IP público de SKU standard a uma interface de rede de máquina virtual, tem de permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até criar e associar um grupo de segurança de rede e permitir explicitamente o tráfego pretendido.|
   |Nome|Sim|O nome deve ser único dentro do grupo de recursos que selecionar.|
   |Atribuição de endereçoIP|Sim|**Dinâmico:** Os endereços dinâmicos só são atribuídos depois de um endereço IP público estar associado a um recurso Azure, e o recurso é iniciado pela primeira vez. Os endereços dinâmicos podem mudar se forem atribuídos a um recurso, como uma máquina virtual, e a máquina virtual for interrompida (negociada), e depois reiniciada. O endereço permanece o mesmo se uma máquina virtual for reiniciada ou parada (mas não ser negociada). Os endereços dinâmicos são lançados quando um recurso de endereço IP público é dissociado de um recurso a que está associado. **Estática:** Os endereços estáticos são atribuídos quando é criado um endereço IP público. Os endereços estáticos não são divulgados até que um recurso de endereço IP público seja eliminado. Se o endereço não estiver associado a um recurso, pode alterar o método de atribuição após a criação do endereço. Se o endereço estiver associado a um recurso, poderá não ser capaz de alterar o método de atribuição. Se selecionar *o IPv6* para a **versão IP,** o método de atribuição deve ser *Dinâmico* para SKU Básico.  Os endereços SKU padrão são *estáticos* tanto para iPv4 como IPv6. |
   |Tempo limite (minutos)|Não|Quantos minutos para manter aberta uma ligação TCP ou HTTP sem depender dos clientes para enviar mensagens de "keep-alive". Se selecionar iPv6 para **versão IP,** este valor não pode ser alterado. |
   |Etiqueta de nome DNS|Não|Deve ser único dentro da localização Azure em que cria o nome (em todas as subscrições e em todos os clientes). O Azure regista automaticamente o nome e o endereço IP no seu DNS para que possa ligar-se a um recurso com o nome. O Azure atribua uma sub-rede predefinida como *location.cloudapp.azure.com* (onde a localização é a localização que seleciona) ao nome que fornece, para criar o nome DNS totalmente qualificado. Se optar por criar ambas as versões de endereço, o mesmo nome DNS é atribuído tanto aos endereços IPv4 como IPv6. O DNS padrão da Azure contém os registos de nomeS IPv4 A e IPv6 AAAA e responde com ambos os registos quando o nome DNS é procurado. O cliente escolhe com que endereço (IPv4 ou IPv6) para comunicar. Em vez de, ou além de, utilizar a etiqueta de nome DNS com o sufixo predefinido, pode utilizar o serviço DNS do Azure para configurar um nome DNS com um sufixo personalizado que é resolvido para o endereço IP público. Para obter mais informações, veja [Utilizar o DNS do Azure com um endereço IP público do Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Nome (Apenas visível se selecionar versão IP de **ambos)**|Sim, se selecionar versão IP de **ambos**|O nome deve ser diferente do nome que insere para o primeiro **nome** nesta lista. Se optar por criar um endereço IPv4 e iPv6, o portal cria dois recursos de endereço IP públicos separados, um com cada versão ip endereço atribuído ao mesmo.|
   |Atribuição de endereçoIP (Apenas visível se selecionar versão IP de **ambos)**|Sim, se selecionar versão IP de **ambos**|As mesmas restrições que a atribuição de endereços IP acima|
   |Subscrição|Sim|Deve existir na mesma [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que o recurso a que irá associar os IP públicos.|
   |Grupo de recursos|Sim|Pode existir no mesmo grupo de [recursos,](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) ou diferente, como o recurso ao qual irá associar os IP públicos.|
   |Localização|Sim|Deve existir no mesmo [local](https://azure.microsoft.com/regions), também referido como região, como o recurso a que irá associar os IP públicos.|
   |Zona de disponibilidade| Não | Esta definição só aparece se selecionar um local suportado. Para obter uma lista de locais suportados, consulte a visão geral das [zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se selecionou o SKU **Básico,** *nenhum* é automaticamente selecionado para si. Se preferir garantir uma zona específica, pode selecionar uma zona específica. Qualquer uma das escolhas não é redundante. Se selecionou o **SKU Padrão:** O redundante de zona é automaticamente selecionado para si e torna o seu caminho de dados resiliente à falha da zona. Se preferir garantir uma zona específica, que não é resistente à falha da zona, pode selecionar uma zona específica.

**Comandos**

Embora o portal ofereça a opção de criar dois recursos públicos de endereço IP (um IPv4 e um IPv6), os seguintes comandos CLI e PowerShell criam um recurso com um endereço para uma versão IP ou outra. Se quiser dois recursos públicos de endereço IP, um para cada versão IP, deve executar o comando duas vezes, especificando diferentes nomes e versões IP para os recursos de endereço IP públicos.

|Ferramenta|Comando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Ver, alterar definições ou apagar um endereço IP público

1. Na caixa que contém os recursos de *pesquisa* de texto no topo do portal Azure, digite *endereço ip público*. Quando os **endereços IP públicos** aparecerem nos resultados da pesquisa, selecione-os.
2. Selecione o nome do endereço IP público que pretende visualizar, alterar as definições ou eliminar da lista.
3. Complete uma das seguintes opções, dependendo se pretende ver, excluir ou alterar o endereço IP público.
   - **Ver**: A secção **'Visão Geral'** mostra as definições de chave para o endereço IP público, como a interface de rede a que está associada (se o endereço estiver associado a uma interface de rede). O portal não exibe a versão do endereço (IPv4 ou IPv6). Para visualizar as informações da versão, utilize o comando PowerShell ou CLI para visualizar o endereço IP público. Se a versão ip endereço for IPv6, o endereço atribuído não é apresentado pelo portal, PowerShell ou clI.
   - **Eliminar**: Para eliminar o endereço IP público, **selecione Eliminar** na secção **Visão Geral.** Se o endereço estiver atualmente associado a uma configuração IP, não pode ser eliminado. Se o endereço estiver atualmente associado a uma configuração, selecione **Dissociar** para dissociar o endereço da configuração IP.
   - **Alteração**: **selecione Configuração**. Alterar as definições utilizando a informação no passo 4 de [Criar um endereço IP público](#create-a-public-ip-address). Para alterar a atribuição de um endereço IPv4 de estática para dinâmica, tem primeiro de dissociar o endereço iPv4 público da configuração IP a que está associado. Em seguida, pode alterar o método de atribuição para dinâmico e selecionar **Associado** para associar o endereço IP à mesma configuração IP, uma configuração diferente, ou pode deixá-lo dissociado. Para dissociar um endereço IP público, na secção **Visão Geral,** selecione **Dissociar**.

   >[!WARNING]
   >Quando muda o método de atribuição de estática para dinâmica, perde-se o endereço IP que foi atribuído ao endereço IP público. Enquanto os servidores DNS públicos do Azure mantêm um mapeamento entre endereços estáticos ou dinâmicos e qualquer etiqueta de nome DNS (se definiu um), um endereço IP dinâmico pode mudar quando a máquina virtual é iniciada depois de estar no estado de parado (deallocated). Para evitar que o endereço se mude, atribua um endereço IP estático.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[Az rede lista pública ip](/cli/azure/network/public-ip#az-network-public-ip-list) para listar endereços IP públicos, [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) para mostrar configurações; [Az rede atualização pública-ip](/cli/azure/network/public-ip#az-network-public-ip-update) para atualizar; [az rede público-ip excluir](/cli/azure/network/public-ip#az-network-public-ip-delete) para apagar|
|PowerShell|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para recuperar um objeto de endereço IP público e ver as suas definições, [Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) para atualizar definições; [Remover-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) para eliminar|

## <a name="assign-a-public-ip-address"></a>Atribuir um endereço IP público

Saiba como atribuir um endereço IP público aos seguintes recursos:

- Um [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM (ao criar), ou a um [VM existente](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Equilibrador de carga virado para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway de Aplicação do Azure](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Ligação site-a-site usando um Gateway VpN Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conjunto de escala de máquina virtual azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Permissões

Para executar tarefas em endereços IP públicos, a sua conta deve ser atribuída à função de contribuinte da [rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que lhe seja atribuída as ações adequadas enumeradas na tabela seguinte:

| Ação                                                             | Nome                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresss/read                           | Leia um endereço IP público                                          |
| Microsoft.Network/publicIPAddresss/write                          | Criar ou atualizar um endereço IP público                           |
| Microsoft.Network/publicIPAddresss/delete                         | Apagar um endereço IP público                                     |
| Microsoft.Network/publicIPAddresss/join/action                    | Associar um endereço IP público a um recurso                    |

## <a name="next-steps"></a>Passos seguintes

- Crie um endereço IP público utilizando scripts de amostra [PowerShell](powershell-samples.md) ou [Azure CLI,](cli-samples.md) ou utilizando [modelos](template-samples.md) de Gestor de Recursos Azure
- Criar e aplicar [a política azure](policy-samples.md) para endereços IP públicos
