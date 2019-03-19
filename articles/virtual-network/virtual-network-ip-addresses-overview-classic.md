---
title: Tipos de endereços IP no Azure (clássico)
titlesuffix: Azure Virtual Network
description: Saiba mais sobre endereços públicos e privados IP (clássico) no Azure.
services: virtual-network
documentationcenter: na
author: genlin
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: 3a295a5c8a202b2f3186e696bb281002090fcad4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112607"
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>Tipos de endereços IP e métodos de alocação no Azure (clássicos)
Pode atribuir endereços IP a recursos do Azure para comunicar com outros recursos do Azure, a rede no local e a Internet. Existem dois tipos de endereços IP, pode utilizar no Azure: públicas e privadas.

Endereços IP públicos são utilizados para comunicação com a Internet, incluindo os serviços do Azure destinados ao público.

Endereços IP privados são utilizados para comunicação dentro de uma rede virtual do Azure (VNet), um serviço em nuvem e a sua rede no local ao utilizar um gateway de VPN ou circuito ExpressRoute para expandir a sua rede para o Azure.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos:  [Resource Manager e clássica](../resource-manager-deployment-model.md).  Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o Resource Manager. Saiba mais sobre endereços IP no Resource Manager lendo os [endereços IP](virtual-network-ip-addresses-overview-arm.md) artigo.

## <a name="public-ip-addresses"></a>Endereços IP públicos
Endereços IP públicos permitem que os recursos do Azure comunicar com os serviços destinados ao público de Internet e o Azure, tal como [a Cache de Redis do Azure](https://azure.microsoft.com/services/cache/), [Event Hubs do Azure](https://azure.microsoft.com/services/event-hubs/), [bases de dados do SQL](../sql-database/sql-database-technical-overview.md), e [armazenamento do Azure](../storage/common/storage-introduction.md).

Um endereço IP público é associado com os seguintes tipos de recurso:

* Serviços em nuvem
* Máquinas virtuais (VMs) IaaS
* Instâncias de função de PaaS
* Gateways de VPN
* Gateways de aplicação

### <a name="allocation-method"></a>Método de alocação
Quando um endereço IP público tem de ser atribuídos a um recurso do Azure, é *dinamicamente* alocados a partir de um conjunto de endereço IP público disponível na localização do recurso é criado. Este endereço IP é libertado quando o recurso está parado. Com o serviço em nuvem, isto acontece quando todas as instâncias de função são paradas, o que pode ser evitada, utilizando um *estático* endereço IP (reservado) (consulte [serviços Cloud](#Cloud-services)).

> [!NOTE]
> A lista de intervalos de IP a partir do qual os endereços IP públicos são alocados a recursos do Azure está publicada em [intervalos de IP de Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>Resolução de nomes de anfitrião DNS
Quando cria um serviço cloud ou uma VM de IaaS, terá de fornecer um nome DNS do serviço de cloud que seja exclusivo em todos os recursos no Azure. Esta ação cria um mapeamento nos servidores DNS geridos pelo Azure para *dnsname*. cloudapp.net para o endereço IP público do recurso. Por exemplo, quando cria um serviço em nuvem com um nome DNS do serviço de nuvem do **contoso**, o nome de domínio completamente qualificado (FQDN) **contoso.cloudapp.net** será resolvido para um endereço IP público (VIP) da cloud serviço. Pode utilizar este FQDN para criar um registo CNAME de domínio personalizado que aponte para o endereço IP público no Azure.

### <a name="cloud-services"></a>Serviços em nuvem
Um serviço em nuvem tem sempre um endereço IP público, conhecido como um endereço IP virtual (VIP). Pode criar pontos finais num serviço cloud para associar portas diferentes no VIP se nas portas internas em VMs e instâncias de função no serviço cloud. 

Um serviço em nuvem pode conter várias VMs de IaaS ou instâncias de função de PaaS, tudo expostas por meio do mesmo VIP do serviço cloud. Também pode atribuir [vários VIPs para um serviço em nuvem](../load-balancer/load-balancer-multivip.md), que permite cenários de vários VIPS, como o ambiente multi-inquilino com Web sites baseados em SSL.

Pode certificar-se o endereço IP público de um serviço cloud permanece a mesma, mesmo quando todas as instâncias de função são paradas, ao utilizar um *estático* endereço IP público, conhecido como [IP reservado](virtual-networks-reserved-public-ip.md). Pode criar um recurso IP estático (reservado) num local específico e atribuí-lo a qualquer serviço cloud nessa localização. Não é possível especificar o endereço IP real para o IP reservado, é alocado partir de conjunto de endereços IP disponíveis na localização que é criado. Este endereço IP não é libertado até os eliminar explicitamente.

Estáticos (reservados) os endereços IP públicos são comumente usados nos cenários em que um serviço em nuvem:

* precisa de regras de firewall para ser configurado por utilizadores finais.
* depende de resolução de nomes DNS externa, e um IP dinâmico exigiria registos a fossem atualizados.
* usa serviços da web externos que utilizam o modelo de segurança com base no IP.
* utiliza certificados SSL associados a um endereço IP.

> [!NOTE]
> Quando cria uma VM clássica, um contêiner *serviço em nuvem* é criado pelo Azure, que tem um endereço IP virtual (VIP). Quando a criação é feita através do portal, um padrão RDP ou SSH *ponto final* é configurado pelo portal para que possam ligar à VM através do VIP do serviço cloud. Este VIP do serviço cloud pode ser reservada, que efetivamente fornece um endereço IP reservado para ligar à VM. É possível abrir portas adicionais ao configurar mais pontos finais.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>Instâncias de função de VMs de IaaS e PaaS
Pode atribuir um endereço IP público diretamente a uma IaaS [VM](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou instância de função de PaaS num serviço cloud. Isto é referido como um endereço IP público ao nível da instância ([ILPIP](virtual-networks-instance-level-public-ip.md)). Este endereço IP público só pode ser dinâmico.

> [!NOTE]
> Isso é diferente de VIP do serviço cloud, que é um contentor para VMs de IaaS ou PaaS instâncias da função, uma vez que um serviço em nuvem pode conter várias VMs de IaaS ou instâncias de função de PaaS, todas expostas por meio do mesmo VIP do serviço cloud.
> 
> 

### <a name="vpn-gateways"></a>Gateways de VPN
R [gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) pode ser utilizado para ligar uma VNet do Azure a outras redes de VNets do Azure ou no local. Um gateway de VPN é atribuído um endereço IP público *dinamicamente*, que permite a comunicação com a rede remota.

### <a name="application-gateways"></a>Gateways de aplicação
Do Azure [gateway de aplicação](../application-gateway/application-gateway-introduction.md) pode ser utilizado para Layer7 balanceamento de carga para encaminhar tráfego de rede baseado em HTTP. Gateway de aplicação é atribuído um endereço IP público *dinamicamente*, que serve como o VIP com balanceamento de carga.

### <a name="at-a-glance"></a>Síntese
A tabela abaixo mostra cada tipo de recurso com os métodos de alocação possíveis (dinâmicos/estáticos) e a capacidade de atribuir vários endereços IP públicos.

| Recurso | Dinâmica | Estático | Vários endereços IP |
| --- | --- | --- | --- |
| Serviço cloud |Sim |Sim |Sim |
| Instância de função de VM de IaaS ou PaaS |Sim |Não |Não |
| Gateway de VPN |Sim |Não |Não |
| Gateway de aplicação |Sim |Não |Não |

## <a name="private-ip-addresses"></a>Endereços IP Privados
Endereços IP privados permitem que os recursos do Azure comunicar com outros recursos num serviço cloud ou uma [rede virtual](virtual-networks-overview.md)(VNet), ou a rede no local (por meio de um gateway de VPN ou circuito ExpressRoute), sem utilizar um Endereço IP acessível pela interne.

No modelo de implementação clássica do Azure, pode ser atribuído um endereço IP privado para os seguintes recursos do Azure:

* Instâncias de função de VMs de IaaS e PaaS
* Balanceador de carga interno
* Gateway de aplicação

### <a name="iaas-vms-and-paas-role-instances"></a>Instâncias de função de VMs de IaaS e PaaS
Máquinas virtuais (VMs) criadas com o modelo de implementação clássica sempre são colocadas num serviço cloud, semelhante às instâncias de função de PaaS. O comportamento de endereços IP privados, portanto, é semelhante para estes recursos.

É importante observar que um serviço em nuvem pode ser implementado de duas formas:

* Como um *autónomo* cloud service, onde não é uma rede virtual.
* Como parte de uma rede virtual.

#### <a name="allocation-method"></a>Método de alocação
No caso de um *autónomo* serviço em nuvem; get recursos alocado de um endereço IP privado *dinamicamente* intervalo de endereços de IP privado de datacenter do Azure. Ele pode ser usado apenas para comunicação com outras VMs no mesmo serviço cloud. Este endereço IP pode alterar quando o recurso é parado e iniciado.

Em caso de um serviço em nuvem implementado numa rede virtual, recursos obter privada IP endereço (s) alocados a partir do intervalo de endereços das sub-redes de associada (conforme especificado na respetiva configuração de rede). Este endereço IP privado (s) pode ser utilizado para comunicação entre todas as VMs dentro da VNet.

Além disso, em caso de serviços de cloud dentro de uma VNet, um endereço IP privado é alocado *dinamicamente* (utilizando DHCP) por predefinição. Ele pode mudar quando o recurso é parado e iniciado. Para garantir que o endereço IP permanece o mesmo, tem de definir o método de alocação como *estático*e forneça um endereço IP válido no intervalo de endereços correspondente.

Geralmente, os endereços IP privados estáticos são utilizados para:

* VMs que funcionam como controladores de domínio ou servidores DNS.
* VMs que necessitam de regras de firewall com endereços IP.
* VMs a executar os serviços acedidos por outras aplicações através de um endereço IP.

#### <a name="internal-dns-hostname-resolution"></a>Resolução de nome de anfitrião DNS internos
Todas as instâncias de função de PaaS e VMs do Azure estão configuradas com [servidores DNS geridos pelo Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) por predefinição, a menos que configure explicitamente servidores DNS personalizados. Estes servidores DNS fornecem resolução de nomes internos para VMs e instâncias de função que residem no mesmo serviço de VNet ou na cloud.

Quando cria uma VM, é adicionado aos servidores DNS geridos pelo Azure um mapeamento do nome de anfitrião para o respetivo endereço IP privado. Com uma VM multi-NIC, o nome do anfitrião é mapeado para o endereço IP privado da NIC principal. No entanto, estas informações de mapeamento são restritas para recursos dentro do mesmo serviço cloud ou a VNet.

No caso de um *autónomo* serviço em nuvem; será capaz de resolver os nomes de anfitrião de todas as instâncias de VMs/função dentro do mesmo serviço cloud apenas. Em caso de um serviço em nuvem numa VNet, será capaz de resolver os nomes de anfitrião de todas as instâncias de VMs/função dentro da VNet.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Balanceadores de carga internos (ILBs) e gateways de aplicação
Pode atribuir um endereço IP privado à configuração de **front-end** de um [Balanceador de Carga Interno do Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) ou de um [Gateway de Aplicação](../application-gateway/application-gateway-introduction.md). Este endereço IP privado funciona como o ponto final interno, que só é acessível pelos recursos dentro da respetiva rede virtual (VNet) e pelas redes remotas ligadas à VNet. Pode atribuir um endereço IP privado dinâmico ou estático à configuração de front-end. Também pode atribuir vários endereços IP privados para ativar cenários de vários vips.

### <a name="at-a-glance"></a>Síntese
A tabela abaixo mostra cada tipo de recurso com os métodos de alocação possíveis (dinâmicos/estáticos) e a capacidade de atribuir vários endereços IP privados.

| Recurso | Dinâmica | Estático | Vários endereços IP |
| --- | --- | --- | --- |
| VM (num *autónomo* do serviço cloud ou VNet) |Sim |Sim |Sim |
| Instância de função de PaaS (num *autónomo* do serviço cloud ou VNet) |Sim |Não |Não |
| Fim de front-de Balanceador de carga interno |Sim |Sim |Sim |
| Front-end da aplicação gateway |Sim |Sim |Sim |

## <a name="limits"></a>Limites
A tabela abaixo mostra os limites impostos no IP de endereçamento no Azure por subscrição. Pode [contactar o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar os limites predefinidos até aos limites máximos, com base nas necessidades da sua atividade.

|  | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Endereços IP públicos (dinâmico) |5 |contactar o suporte |
| Endereços IP públicos reservados |20 |contactar o suporte |
| VIP público por implementação (serviço em nuvem) |5 |contactar o suporte |
| VIP privado (ILB) por implementação (serviço em nuvem) |1 |1 |

Não deixe de ler o conjunto completo de [limites para redes](../azure-subscription-service-limits.md#networking-limits) no Azure.

## <a name="pricing"></a>Preços
Na maioria dos casos, os endereços IP públicos são gratuitos. Existe uma cobrança nominal para utilizar endereços IP públicos adicionais e/ou estáticos. Certifique-se de que compreende os [estrutura de preços para IPs públicos](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Diferenças entre o Resource Manager e implementações clássicas
Segue-se uma comparação de recursos de endereçamento de IP no modelo de implementação clássica e Resource Manager.

|  | Recurso | Clássica | Resource Manager |
| --- | --- | --- | --- |
| **Endereço IP público** |***VM*** |Conhecido como um ILPIP (apenas dinâmico) |Conhecido como um IP público (dinâmico ou estático) |
|  ||Atribuído a uma VM de IaaS ou uma instância de função de PaaS |Associado à NIC da VM |
|  |***Balanceador de carga com acesso à Internet*** |Chamado de VIP (dinâmico) ou o IP reservado (estático) |Conhecido como um IP público (dinâmico ou estático) |
|  ||Atribuído a um serviço em nuvem |Associados à configuração de front-end do Balanceador de carga |
|  | | | |
| **Endereço IP privado** |***VM*** |Conhecido como um DIP |Conhecido como um endereço IP privado |
|  ||Atribuído a uma VM de IaaS ou uma instância de função de PaaS |Atribuído a NIC da VM |
|  |***Balanceador de carga interno (ILB)*** |Atribuído para o ILB (dinâmico ou estático) |Atribuído a configuração de front-end do ILB (dinâmica ou estática) |

## <a name="next-steps"></a>Passos Seguintes
* [Implementar uma VM com um endereço IP privado estático](virtual-networks-static-private-ip-classic-pportal.md) no portal do Azure.

