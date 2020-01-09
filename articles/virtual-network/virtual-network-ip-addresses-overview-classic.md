---
title: Tipos de endereço IP no Azure (clássico)
titlesuffix: Azure Virtual Network
description: Saiba mais sobre endereços IP públicos e privados (clássicos) no Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: 7b197e1acf696c2ae6e919ee2eddacfb82ac3802
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646783"
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>Tipos de endereço IP e métodos de alocação (clássico) no Azure
Pode atribuir endereços IP a recursos do Azure para comunicar com outros recursos do Azure, a rede no local e a Internet. Há dois tipos de endereços IP que você pode usar no Azure: público e privado.

Os endereços IP públicos são usados para comunicação com a Internet, incluindo serviços públicos do Azure.

Os endereços IP privados são usados para comunicação em uma VNet (rede virtual) do Azure, um serviço de nuvem e sua rede local quando você usa um gateway de VPN ou um circuito de ExpressRoute para estender sua rede para o Azure.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../resource-manager-deployment-model.md).  Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que a maioria das implantações novas usem o Gerenciador de recursos. Saiba mais sobre os endereços IP no Gerenciador de recursos lendo o artigo [endereços IP](virtual-network-ip-addresses-overview-arm.md) .

## <a name="public-ip-addresses"></a>Endereços IP públicos
Os endereços IP públicos permitem que os recursos do Azure se comuniquem com a Internet e com serviços públicos do Azure, como o [cache do Azure para Redis](https://azure.microsoft.com/services/cache/), [hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/), [bancos de dados SQL](../sql-database/sql-database-technical-overview.md)e [armazenamento do Azure](../storage/common/storage-introduction.md).

Um endereço IP público é associado aos seguintes tipos de recursos:

* Serviços Cloud
* VMs (máquinas virtuais) IaaS
* Instâncias de função de PaaS
* Gateways de VPN
* Gateways de aplicação

### <a name="allocation-method"></a>Método de alocação
Quando um endereço IP público precisa ser atribuído a um recurso do Azure, ele é alocado *dinamicamente* de um pool de endereços IP públicos disponíveis dentro do local em que o recurso é criado. Esse endereço IP é liberado quando o recurso é interrompido. Com o serviço de nuvem, isso ocorre quando todas as instâncias de função são interrompidas, o que pode ser evitado usando um endereço IP *estático* (reservado) (consulte [serviços de nuvem](#cloud-services)).

> [!NOTE]
> A lista de intervalos de IP de onde os endereços IP públicos são alocados aos recursos do Azure é publicada em [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>Resolução de nomes de anfitrião DNS
Ao criar um serviço de nuvem ou uma VM IaaS, você precisa fornecer um nome DNS de serviço de nuvem que seja exclusivo em todos os recursos no Azure. Isso cria um mapeamento nos servidores DNS gerenciados pelo Azure para *DnsName*. cloudapp.net para o endereço IP público do recurso. Por exemplo, quando você cria um serviço de nuvem com um nome DNS de serviço de nuvem da **contoso**, o nome de domínio totalmente qualificado (FQDN) **contoso.cloudapp.net** será resolvido para um endereço IP público (VIP) do serviço de nuvem. Pode utilizar este FQDN para criar um registo CNAME de domínio personalizado que aponte para o endereço IP público no Azure.

### <a name="cloud-services"></a>Serviços Cloud
Um serviço de nuvem sempre tem um endereço IP público chamado de VIP (endereço IP virtual). Você pode criar pontos de extremidade em um serviço de nuvem para associar diferentes portas no VIP a portas internas em VMs e instâncias de função no serviço de nuvem. 

Um serviço de nuvem pode conter várias VMs de IaaS ou instâncias de função de PaaS, todas expostas por meio do mesmo VIP de serviço de nuvem. Você também pode atribuir [vários VIPs a um serviço de nuvem](../load-balancer/load-balancer-multivip.md), o que permite cenários de vários VIPs, como ambiente multilocatário com sites baseados em SSL.

Você pode garantir que o endereço IP público de um serviço de nuvem permaneça o mesmo, mesmo quando todas as instâncias de função são interrompidas, usando um endereço IP público *estático* , conhecido como [IP reservado](virtual-networks-reserved-public-ip.md). Você pode criar um recurso de IP estático (reservado) em um local específico e atribuí-lo a qualquer serviço de nuvem nesse local. Você não pode especificar o endereço IP real para o IP reservado, ele é alocado do pool de endereços IP disponíveis no local em que é criado. Esse endereço IP não é liberado até que você o exclua explicitamente.

Endereços IP públicos estáticos (reservados) geralmente são usados nos cenários em que um serviço de nuvem:

* requer que as regras de firewall sejam configuradas pelos usuários finais.
* depende da resolução de nomes DNS externos, e um IP dinâmico exigiria A atualização de registros A.
* consome serviços Web externos que usam o modelo de segurança baseado em IP.
* usa certificados SSL vinculados a um endereço IP.

> [!NOTE]
> Quando você cria uma VM clássica, um *serviço de nuvem* de contêiner é criado pelo Azure, que tem um endereço IP virtual (VIP). Quando a criação é feita por meio do portal, um ponto de *extremidade* RDP ou SSH padrão é configurado pelo portal para que você possa se conectar à VM por meio do VIP do serviço de nuvem. Esse VIP de serviço de nuvem pode ser reservado, o que efetivamente fornece um endereço IP reservado para se conectar à VM. Você pode abrir portas adicionais Configurando mais pontos de extremidade.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>VMs de IaaS e instâncias de função de PaaS
Você pode atribuir um endereço IP público diretamente a uma [VM](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) IaaS ou instância de função PaaS em um serviço de nuvem. Isso é chamado de endereço IP público em nível de instância ([ILPIP](virtual-networks-instance-level-public-ip.md)). Esse endereço IP público só pode ser dinâmico.

> [!NOTE]
> Isso é diferente do VIP do serviço de nuvem, que é um contêiner para VMs de IaaS ou instâncias de função de PaaS, pois um serviço de nuvem pode conter várias VMs de IaaS ou instâncias de função de PaaS, todas expostas por meio do mesmo VIP de serviço de nuvem.
> 
> 

### <a name="vpn-gateways"></a>Gateways de VPN
Um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) pode ser usado para conectar uma VNet do Azure a outras VNets do Azure ou redes locais. Um gateway de VPN recebe um endereço IP público *dinamicamente*, o que permite a comunicação com a rede remota.

### <a name="application-gateways"></a>Gateways de aplicação
Um [Gateway de aplicativo](../application-gateway/application-gateway-introduction.md) do Azure pode ser usado para o balanceamento de carga LAYER7 para rotear o tráfego de rede com base em http. O gateway de aplicativo recebe um endereço IP público *dinamicamente*, que serve como o VIP com balanceamento de carga.

### <a name="at-a-glance"></a>Síntese
A tabela a seguir mostra cada tipo de recurso com os métodos de alocação possíveis (dinâmico/estático) e a capacidade de atribuir vários endereços IP públicos.

| Recurso | Dinâmico | Estático | Vários endereços IP |
| --- | --- | --- | --- |
| Serviço cloud |Sim |Sim |Sim |
| Instância de função de PaaS ou VM IaaS |Sim |Não |Não |
| Gateway de VPN |Sim |Não |Não |
| Gateway de aplicação |Sim |Não |Não |

## <a name="private-ip-addresses"></a>Endereços IP Privados
Os endereços IP privados permitem que os recursos do Azure se comuniquem com outros recursos em um serviço de nuvem ou em uma [rede virtual](virtual-networks-overview.md)(VNet) ou em uma rede local (por meio de um gateway de VPN ou circuito de ExpressRoute), sem usar um endereço IP acessível pela Internet.

No modelo de implantação clássico do Azure, um endereço IP privado pode ser atribuído aos seguintes recursos do Azure:

* VMs de IaaS e instâncias de função de PaaS
* Balanceador de carga interno
* Gateway de aplicação

### <a name="iaas-vms-and-paas-role-instances"></a>VMs de IaaS e instâncias de função de PaaS
As máquinas virtuais (VMs) criadas com o modelo de implantação clássico sempre são colocadas em um serviço de nuvem, semelhante às instâncias de função de PaaS. O comportamento de endereços IP privados é, portanto, semelhante para esses recursos.

É importante observar que um serviço de nuvem pode ser implantado de duas maneiras:

* Como um serviço de nuvem *autônomo* , onde ele não está dentro de uma rede virtual.
* Como parte de uma rede virtual.

#### <a name="allocation-method"></a>Método de alocação
No caso de um serviço de nuvem *autônomo* , os recursos obtêm um endereço IP privado alocado *dinamicamente* do intervalo de endereços IP privados do datacenter do Azure. Ele pode ser usado somente para comunicação com outras VMs no mesmo serviço de nuvem. Esse endereço IP pode ser alterado quando o recurso é interrompido e iniciado.

No caso de um serviço de nuvem implantado em uma rede virtual, os recursos obtêm endereços IP privados alocados do intervalo de endereços das sub-redes associadas (conforme especificado em sua configuração de rede). Esses endereços IP privados podem ser usados para comunicação entre todas as VMs na VNet.

Além disso, no caso de serviços de nuvem em uma VNet, um endereço IP privado é alocado *dinamicamente* (usando DHCP) por padrão. Ele pode mudar quando o recurso é interrompido e iniciado. Para garantir que o endereço IP permaneça o mesmo, você precisa definir o método de alocação como *estático*e fornecer um endereço IP válido dentro do intervalo de endereços correspondente.

Geralmente, os endereços IP privados estáticos são utilizados para:

* VMs que funcionam como controladores de domínio ou servidores DNS.
* VMs que exigem regras de firewall usando endereços IP.
* VMs que executam serviços acessados por outros aplicativos por meio de um endereço IP.

#### <a name="internal-dns-hostname-resolution"></a>Resolução de nome de host DNS interno
Todas as instâncias de função de PaaS e VMs do Azure são configuradas com [servidores DNS gerenciados pelo Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) por padrão, a menos que você configure explicitamente servidores DNS personalizados. Esses servidores DNS fornecem resolução de nomes interna para VMs e instâncias de função que residem no mesmo serviço de nuvem ou VNet.

Quando cria uma VM, é adicionado aos servidores DNS geridos pelo Azure um mapeamento do nome de anfitrião para o respetivo endereço IP privado. Com a VM de várias NICs, o nome do host é mapeado para o endereço IP privado da NIC primária. No entanto, essas informações de mapeamento são restritas a recursos no mesmo serviço de nuvem ou VNet.

No caso de um serviço de nuvem *autônomo* , você poderá resolver nomes de host de todas as instâncias de VM/função somente no mesmo serviço de nuvem. No caso de um serviço de nuvem em uma VNet, você poderá resolver nomes de host de todas as instâncias de VMs/funções na VNet.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Balanceadores de carga internos (ILBs) e gateways de aplicação
Pode atribuir um endereço IP privado à configuração de **front-end** de um [Balanceador de Carga Interno do Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) ou de um [Gateway de Aplicação](../application-gateway/application-gateway-introduction.md). Este endereço IP privado funciona como o ponto final interno, que só é acessível pelos recursos dentro da respetiva rede virtual (VNet) e pelas redes remotas ligadas à VNet. Pode atribuir um endereço IP privado dinâmico ou estático à configuração de front-end. Você também pode atribuir vários endereços IP privados para habilitar cenários de vários VIPs.

### <a name="at-a-glance"></a>Síntese
A tabela a seguir mostra cada tipo de recurso com os métodos de alocação possíveis (dinâmico/estático) e a capacidade de atribuir vários endereços IP privados.

| Recurso | Dinâmico | Estático | Vários endereços IP |
| --- | --- | --- | --- |
| VM (em um serviço de nuvem *autônomo* ou VNet) |Sim |Sim |Sim |
| Instância de função de PaaS (em um serviço de nuvem *autônomo* ou VNet) |Sim |Não |Não |
| Front-end do balanceador de carga interno |Sim |Sim |Sim |
| Front-end do gateway de aplicativo |Sim |Sim |Sim |

## <a name="limits"></a>Limites
A tabela a seguir mostra os limites impostos sobre o endereçamento IP no Azure por assinatura. Pode [contactar o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar os limites predefinidos até aos limites máximos, com base nas necessidades da sua atividade.

|  | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Endereços IP públicos (dinâmico) |5 |contactar o suporte |
| Endereços IP públicos reservados |20 |contactar o suporte |
| VIP público por implantação (serviço de nuvem) |5 |contactar o suporte |
| VIP privado (ILB) por implantação (serviço de nuvem) |1 |1 |

Certifique-se de ler o conjunto completo de [limites para rede](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) no Azure.

## <a name="pricing"></a>Preços
Na maioria dos casos, os endereços IP públicos são gratuitos. Há um encargo nominal para usar endereços IP públicos adicionais e/ou estáticos. Certifique-se de entender a [estrutura de preços para IPS públicos](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Diferenças entre o Gerenciador de recursos e as implantações clássicas
Veja abaixo uma comparação dos recursos de endereçamento IP no Resource Manager e no modelo de implantação clássico.

|  | Recurso | Clássico | Resource Manager |
| --- | --- | --- | --- |
| **Endereço IP Público** |***VM*** |Chamado de ILPIP (somente dinâmico) |Conhecido como um IP público (dinâmico ou estático) |
|  ||Atribuído a uma VM IaaS ou a uma instância de função PaaS |Associado à NIC da VM |
|  |***Balanceador de carga voltado para a Internet*** |Conhecido como VIP (dinâmico) ou IP Reservado (estático) |Conhecido como um IP público (dinâmico ou estático) |
|  ||Atribuído a um serviço de nuvem |Associado à configuração de front-end do balanceador de carga |
|  | | | |
| **Endereço IP privado** |***VM*** |Chamado de DIP |Referido como um endereço IP privado |
|  ||Atribuído a uma VM IaaS ou a uma instância de função PaaS |Atribuído à NIC da VM |
|  |***Balanceador de carga interno (ILB)*** |Atribuído ao ILB (dinâmico ou estático) |Atribuído à configuração de front-end do ILB (dinâmico ou estático) |

## <a name="next-steps"></a>Passos seguintes
* [Implante uma VM com um endereço IP privado estático](virtual-networks-static-private-ip-classic-pportal.md) usando o portal do Azure.

