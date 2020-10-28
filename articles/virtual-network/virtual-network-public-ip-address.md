---
title: Gerir endereços IP públicos ! Microsoft Docs
titleSuffix: Azure Virtual Network
description: Gerir endereços IP públicos.  Saiba também como um endereço IP público é um recurso com as suas próprias configurações configuráveis.
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
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: a8847768cf4cf1ea85df8646a8848aac02cbd621
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629260"
---
# <a name="manage-public-ip-addresses"></a>Gerir endereços IP públicos

Saiba mais sobre um endereço IP público e como criar, alterar e apagar um. Um endereço IP público é um recurso com as suas próprias configurações configuráveis. A atribuição de um endereço IP público a um recurso Azure que suporte endereços IP públicos permite:
- Comunicação de entrada da Internet para o recurso, tais como Azure Virtual Machines (VM), Azure Application Gateways, Azure Load Balancers, Azure VPN Gateways, entre outros. Você ainda pode comunicar com alguns recursos, tais como VMs, da Internet, se um VM não tiver um endereço IP público atribuído a ele, desde que o VM faça parte de um pool de back-end do balanceador de carga, e o equilibrador de carga é atribuído um endereço IP público. Para determinar se um recurso para um serviço Azure específico pode ser atribuído um endereço IP público, ou se pode ser comunicado através do endereço IP público de um recurso Azure diferente, consulte a documentação do serviço.
- Conectividade de saída para a Internet usando um endereço IP previsível. Por exemplo, uma máquina virtual pode comunicar saída para a Internet sem um endereço IP público que lhe seja atribuído, mas o seu endereço é o endereço de rede traduzido pelo Azure para um endereço público imprevisível, por padrão. A atribuição de um endereço IP público a um recurso permite-lhe saber qual o endereço IP utilizado para a ligação de saída. Embora previsível, o endereço pode mudar, dependendo do método de atribuição escolhido. Para mais informações, consulte [Criar um endereço IP público.](#create-a-public-ip-address) Para saber mais sobre as ligações de saída a partir dos recursos Azure, consulte [as ligações de saída.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Complete as seguintes tarefas antes de completar etapas em qualquer secção deste artigo:

- Se ainda não tem uma conta Azure, inscreva-se para uma [conta de teste gratuita.](https://azure.microsoft.com/free)
- Se utilizar o portal, abra https://portal.azure.com e faça login com a sua conta Azure.
- Se utilizar comandos PowerShell para completar tarefas neste artigo, ou executa os comandos na [Azure Cloud Shell](https://shell.azure.com/powershell), ou executando o PowerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer a versão 1.0 ou mais tarde do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.
- Se utilizar os comandos da interface da linha de comando do Azure (CLI) para completar as tarefas neste artigo, ou executar os comandos na [Azure Cloud Shell](https://shell.azure.com/bash), ou executando o CLI a partir do seu computador. Este tutorial requer a versão Azure CLI 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). Se estiver a executar o Azure CLI localmente, também precisa de correr `az login` para criar uma ligação com o Azure.

A conta em que inicia sessão, ou liga-se ao Azure, deve ser atribuída à [função de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída às ações [apropriadas listadas](#permissions)nas Permissões .

Os endereços IP públicos têm uma taxa nominal. Para ver os preços, leia a página [de preços do endereço IP.](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Para obter instruções sobre como criar endereços IP públicos utilizando o Portal, PowerShell ou CLI -- consulte as seguintes páginas:

 * [Criar endereços IP públicos - portal](https://docs.microsoft.com/azure/virtual-network/create-public-ip-portal?tabs=option-create-public-ip-standard-zones)
 * [Criar endereços IP públicos - PowerShell](https://docs.microsoft.com/azure/virtual-network/create-public-ip-powershell?tabs=option-create-public-ip-standard-zones)
 * [Criar endereços IP públicos - CLI do Azure](https://docs.microsoft.com/azure/virtual-network/create-public-ip-cli?tabs=option-create-public-ip-standard-zones)

>[!NOTE]
>Embora o portal forneça a opção de criar dois recursos de endereço IP públicos (um IPv4 e um IPv6), os comandos PowerShell e CLI criam um recurso com um endereço para uma versão IP ou outra. Se pretender dois recursos de endereço IP públicos, um para cada versão IP, tem de executar o comando duas vezes, especificando nomes diferentes e versões IP para os recursos de endereço IP públicos.

Para obter mais detalhes sobre os atributos específicos de um endereço IP público durante a criação, consulte a tabela abaixo.

   |Definição|Necessário?|Detalhes|
   |---|---|---|
   |Versão IP|Sim| Selecione IPv4 ou IPv6 ou Ambos. A seleção de Ambos resultará na criação de 2 endereços IP públicos: endereço 1 IPv4 e endereço 1 IPv6. Saiba mais sobre [o IPv6 em Azure VNETs](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
   |SKU|Sim|Todos os endereços IP públicos criados antes da introdução de SKUs são endereços IP públicos **básicos** do SKU. Não é possível alterar o SKU após a criação do endereço IP público. Uma máquina virtual autónoma, máquinas virtuais dentro de um conjunto de disponibilidade, ou conjuntos de escala de máquina virtual podem usar SKUs básicos ou padrão. Não é permitida a mistura de SKUs entre máquinas virtuais dentro de conjuntos de disponibilidade ou conjuntos de escala ou VMs autónomos. **Básico** SKU: Se estiver a criar um endereço IP público numa região que suporte zonas de disponibilidade, a definição **de zona de Disponibilidade** está definida para *Nenhum* por padrão. Os IPs públicos básicos não suportam zonas de disponibilidade. **Padrão** SKU: Um IP público Standard SKU pode ser associado a uma máquina virtual ou a uma extremidade frontal do balanceador de carga. Se estiver a criar um endereço IP público numa região que suporte zonas de disponibilidade, a definição **de zona de disponibilidade** está definida como zona *redundante* por padrão. Para obter mais informações sobre as zonas de disponibilidade, consulte a definição **da zona de Disponibilidade.** O SKU padrão é necessário se associar o endereço a um equilibrador de carga Standard. Para saber mais sobre os balanceadores de carga padrão, consulte [o EKU do balanceador de carga Azure](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Quando atribui um endereço IP público de SKU standard a uma interface de rede de máquina virtual, tem de permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até criar e associar um grupo de segurança de rede e permitir explicitamente o tráfego pretendido.|
   |Nome|Sim|O nome deve ser único dentro do grupo de recursos que seleciona.|
   |Atribuição de endereços IP|Sim|**Dinâmico:** Os endereços dinâmicos são atribuídos apenas após um endereço IP público estar associado a um recurso Azure, e o recurso é iniciado pela primeira vez. Os endereços dinâmicos podem ser alterados se forem atribuídos a um recurso, como uma máquina virtual, e a máquina virtual é interrompida (locada por negócios) e depois reiniciada. O endereço permanece o mesmo se uma máquina virtual for reiniciada ou parada (mas não negociada). Os endereços dinâmicos são libertados quando um recurso de endereço IP público é dissociado de um recurso a que está associado. **Estática:** Os endereços estáticos são atribuídos quando um endereço IP público é criado. Os endereços estáticos não são liberados até que um recurso de endereço IP público seja eliminado. Se o endereço não estiver associado a um recurso, pode alterar o método de atribuição após a criação do endereço. Se o endereço estiver associado a um recurso, poderá não conseguir alterar o método de atribuição. Se selecionar *o IPv6* para a **versão IP,** o método de atribuição deve ser *Dinâmico* para SKU Básico.  Os endereços SKU standard são *estáticos* tanto para IPv4 como IPv6. |
   |Tempo de 20 minutos (minutos)|Não|Quantos minutos para manter aberta uma ligação TCP ou HTTP sem depender dos clientes para enviar mensagens de vida. Se selecionar O IPv6 para **versão IP,** este valor não pode ser alterado. |
   |Etiqueta de nome DNS|Não|Deve ser único dentro da localização Azure em que cria o nome (em todas as subscrições e todos os clientes). O Azure regista automaticamente o nome e o endereço IP no seu DNS para que possa ligar-se a um recurso com o nome. O Azure anexa uma sub-rede predefinida, como *location.cloudapp.azure.com* (onde a localização é a localização selecionada) para o nome que fornece, para criar o nome DNS totalmente qualificado. Se optar por criar ambas as versões de endereço, o mesmo nome DNS é atribuído tanto aos endereços IPv4 como IPv6. O DNS padrão do Azure contém registos de nomes IPv4 A e IPv6 AAAA e responde com ambos os registos quando o nome DNS é procurado. O cliente escolhe qual endereço (IPv4 ou IPv6) para comunicar. Em vez de, ou além de, utilizar a etiqueta de nome DNS com o sufixo predefinido, pode utilizar o serviço DNS do Azure para configurar um nome DNS com um sufixo personalizado que é resolvido para o endereço IP público. Para obter mais informações, veja [Utilizar o DNS do Azure com um endereço IP público do Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Nome (Só visível se selecionar versão IP de **Ambos)**|Sim, se selecionar a versão IP de **Ambos**|O nome deve ser diferente do nome que introduz para o primeiro **nome** desta lista. Se optar por criar um endereço IPv4 e IPv6, o portal cria dois recursos de endereço IP públicos separados, um com cada versão de endereço IP atribuído.|
   |Atribuição de endereço IP (Apenas visível se selecionar versão IP de **Ambos** )|Sim, se selecionar a versão IP de **Ambos**|Mesmas restrições que a atribuição de endereços IP acima|
   |Subscrição|Sim|Deve existir na mesma [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que o recurso ao qual irá associar o IP público.|
   |Grupo de recursos|Sim|Pode existir no mesmo [grupo de recursos,](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) ou diferente, que o recurso ao qual irá associar o IP público.|
   |Localização|Sim|Deve existir no mesmo [local](https://azure.microsoft.com/regions), também referido como região, como o recurso ao qual irá associar os IP's públicos.|
   |Zona de disponibilidade| Não | Esta definição só aparece se selecionar um local suportado. Para obter uma lista de locais suportados, consulte [a visão geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se selecionou o **SKU Básico,** *nenhum* é selecionado automaticamente para si. Se preferir garantir uma zona específica, pode selecionar uma zona específica. Qualquer uma das escolhas não é redundante. Se selecionou o **Standard** SKU: O redundante de zona é automaticamente selecionado para si e torna o seu caminho de dados resiliente à falha da zona. Se preferir garantir uma zona específica, que não é resistente à falha da zona, pode selecionar uma zona específica.

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>Ver, modificar as definições para ou eliminar um endereço IP público

   - **Visualização/Lista** : Para rever as definições de um IP público, incluindo o SKU, endereço, qualquer associação aplicável (por exemplo, Virtual Machine NIC, Load Balancer Frontend).
   - **Modificar:** Modificar as definições utilizando as informações no passo 4 de [criar um endereço IP público](#create-a-public-ip-address), como o tempo limite de marcha lenta, a etiqueta do nome DNS ou o método de atribuição.  (Para o processo completo de atualização de um SKU IP público de Base para Standard, consulte [endereços IP públicos de upgrade](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address-upgrade).)
   >[!WARNING]
   >Para alterar a atribuição de um endereço IP público de estática para dinâmica, tem primeiro de dissociar o endereço de quaisquer configurações IP aplicáveis (ver secção **Eliminar).**  Note também que, quando altera o método de atribuição de estática para dinâmica, perde-se o endereço IP que foi atribuído ao endereço IP público. Enquanto os servidores DNS públicos Azure mantêm um mapeamento entre endereços estáticos ou dinâmicos e qualquer etiqueta de nome DNS (se tiver definido um), um endereço IP dinâmico pode mudar quando a máquina virtual é iniciada depois de estar no estado parado (deallocated). Para evitar que o endereço se mude, atribua um endereço IP estático.
   
|Operação|Portal do Azure|Azure PowerShell|CLI do Azure|
|---|---|---|---|
|Vista | Na secção **Geral** de um IP Público |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para recuperar um objeto de endereço IP público e ver as suas definições| [az rede público-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) para mostrar configurações|
|Lista | Na categoria **de endereços IP públicos** |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para recuperar um ou mais objetos de endereço IP públicos e ver as suas definições|[lista de ip públicos da rede az](/cli/azure/network/public-ip#az-network-public-ip-list) para listar endereços IP públicos|
|Modificar | Para um IP dissociado, selecione **Configuração** para modificar o tempo limite de marcha lenta, etiqueta de nome DNS ou alterar a atribuição de IP básico de Estática para Dinâmica  |[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) para atualizar definições |[atualização de ip público da rede az](/cli/azure/network/public-ip#az-network-public-ip-update) para atualizar |

   - **Excluir** : A eliminação de IPs públicos exige que o objeto IP público não seja associado a qualquer configuração IP ou Nic de Máquina Virtual. Veja a tabela abaixo para mais detalhes.

|Recurso|Portal do Azure|Azure PowerShell|CLI do Azure|
|---|---|---|---|
|[Máquina Virtual](https://docs.microsoft.com/azure/virtual-network/remove-public-ip-address-vm)|Selecione **Dissociar** para dissociar o endereço IP a partir da configuração NIC e, em seguida, selecione **Delete** .|[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) para dissociar o endereço IP da configuração NIC; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) para eliminar|[a atualização az rede pública-ip --remover](/cli/azure/network/public-ip#az-network-public-ip-update) para dissociar o endereço IP da configuração NIC; [az rede público-ip apagar](/cli/azure/network/public-ip#az-network-public-ip-delete) para eliminar |
|Frontend de balançador de carga | Navegue para um endereço IP público não utilizado e selecione **Associate** e escolha o Balanceador de Carga com a configuração IP frontal relevante para substituí-lo (em seguida, o ip antigo pode ser eliminado usando o mesmo método que para VM)  | [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) para associar o novo Frontend IP config com o Balancer de Carga Pública; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) para eliminar; também pode usar [Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) para remover Frontend IP Config se houver mais de um |[az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az_network_lb_frontend_ip_update) para associar novo Frontend IP config com Public Load Balancer; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) para eliminar; também pode usar [a az rede lb frontend-ip apagar](/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az_network_lb_frontend_ip_delete) para remover Frontend IP Config se houver mais de um|
|Firewall|N/D| [Deallocate()](https://docs.microsoft.com/azure/firewall/firewall-faq#how-can-i-stop-and-start-azure-firewall) para negociar firewall e remover todas as configurações IP | [az firewall ip-config eliminar](/cli/azure/ext/azure-firewall/network/firewall/ip-config#ext_azure_firewall_az_network_firewall_ip_config_delete) para remover IP (mas deve usar PowerShell para negociar primeiro)|

## <a name="virtual-machine-scale-sets"></a>Conjuntos de Dimensionamento de Máquinas Virtuais

Ao utilizar uma balança de máquina virtual definida com IPs públicos, não existem objetos IP públicos separados associados às instâncias individuais da máquina virtual. No entanto, um objeto prefixo IP público [pode ser usado para gerar o caso IPs](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).

Para listar os IPs públicos num conjunto de escala de máquina virtual, pode utilizar PowerShell[(Get-AzPublicIpAddress -VirtualMachineScaleSetName)](/powershell/module/az.network/get-azpublicipaddress)ou CLI[(az vmss list-instance-public-ips).](/cli/azure/vmss?view=azure-cli-latest#az_vmss_list_instance_public_ips)

Para obter mais informações, consulte [conjuntos de balanças de máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking#public-ipv4-per-virtual-machine).

## <a name="assign-a-public-ip-address"></a>Atribuir um endereço IP público

Saiba como atribuir um endereço IP público aos seguintes recursos:

- Uma máquina virtual [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ao criar), ou para uma [máquina virtual existente](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Balanceador de Carga Pública](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway de Aplicação](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Ligação local-local utilizando um Gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conjunto de escala de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Permissões

Para executar tarefas em endereços IP públicos, a sua conta deve ser atribuída à função [de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída às ações apropriadas listadas no quadro seguinte:

| Ação                                                             | Nome                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Leia um endereço IP público                                          |
| Microsoft.Network/publicIPAddresses/write                          | Criar ou atualizar um endereço IP público                           |
| Microsoft.Network/publicIPAddresses/delete                         | Eliminar um endereço IP público                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Associar um endereço IP público a um recurso                    |

## <a name="next-steps"></a>Passos seguintes

- Crie um endereço IP público utilizando scripts de [amostras powerShell](powershell-samples.md) ou [Azure CLI,](cli-samples.md) ou usando [modelos de Gestor de Recursos](template-samples.md) Azure
- Criar e atribuir [definições de Política Azure](policy-samples.md) para endereços IP públicos
