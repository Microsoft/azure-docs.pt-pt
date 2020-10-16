---
title: Endereços IP públicos em Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre os endereços IP públicos no Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: fbd4c4ecfa2be9815e5d301a02460dc28171716a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329266"
---
# <a name="public-ip-addresses"></a>Endereços IP públicos

Os endereços IP públicos permitem que os recursos da Internet comuniquem com os recursos do Azure à entrada. Os endereços IP públicos permitem aos recursos Azure comunicar à Internet e aos serviços Azure virados para o público. O endereço é dedicado ao recurso, até que não seja atribuído por si. Um recurso sem um IP público atribuído pode comunicar saída. O Azure atribui dinamicamente um endereço IP disponível que não é dedicado ao recurso. Para obter mais informações sobre ligações de saída no Azure, veja [Noções sobre ligações de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

No Azure Resource Manager, os endereços [IP públicos](virtual-network-public-ip-address.md) são recursos que tem as suas próprias propriedades. Alguns dos recursos que pode associar um recurso de endereço IP público com:

* Interfaces de rede de máquina virtual
* Balanceadores de carga com acesso à Internet
* Gateways de VPN
* Gateways de aplicação
* Azure Firewall

## <a name="ip-address-version"></a>Versão do endereço IP

Os endereços IP públicos são criados com um endereço IPv4 ou IPv6. 

## <a name="sku"></a>SKU

Os endereços IP públicos são criados com um dos SKUs seguintes:

>[!IMPORTANT]
> Os SKUs correspondentes são necessários para o balanceador de carga e os recursos IP públicos. Não é possível ter uma mistura de recursos de SKU básicos e recursos de SKU standard. Não é possível anexar máquinas virtuais autónomas, máquinas virtuais num recurso de conjunto de disponibilidade ou uma máquina virtual dos recursos do conjunto de dimensionamento para ambos os SKUs em simultâneo.  Os novos designs devem ponderar a utilização de recursos SKU Standard.  Veja [Balanceador de Carga Standard](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter mais detalhes.

### <a name="standard"></a>Standard

Endereços IP públicos standard SKU:

- Utilize sempre o método de atribuição estática.
- Tenha um tempo de saída de entrada recímido ajustável de 4-30 minutos, com um padrão de 4 minutos, e uma saída fixa originou o tempo de saída inativo de 4 minutos.
- Seguro por defeito e fechado ao tráfego de entrada. Permitir a lista de tráfego de entrada com um [grupo de segurança de rede](security-overview.md#network-security-groups).
- Atribuído a interfaces de rede, balanceadores de carga público padrão ou Gateways de aplicação. Para obter mais informações sobre o balanceador de carga Standard, consulte [o Balanceador de Carga Standard Azure](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Pode ser zona redundante ou zonal (pode ser criado zonal e garantido numa zona de disponibilidade específica). Para obter mais informações sobre zonas de disponibilidade, veja [Descrição geral de zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Balanceador de Carga Standard e Zonas de Disponibilidade](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
 
> [!NOTE]
> A comunicação de entrada com um recurso Standard SKU falha até criar e associar um [grupo de segurança de rede](security-overview.md#network-security-groups) e permitir explicitamente o tráfego de entrada pretendido.

> [!NOTE]
> Apenas endereços IP públicos com SKU básico estão disponíveis quando se utilizam [o IMDS do serviço de metadados de exemplo.](../virtual-machines/windows/instance-metadata-service.md) O SKU padrão não é suportado.

### <a name="basic"></a>Básico

Todos os endereços IP públicos criados antes da introdução de SKUs são endereços IP públicos de SKU Básico. 

Com a introdução de SKUs, especifique qual o endereço IP público que gostaria que fosse o endereço IP público. 

Endereços SKU básicos:

- São atribuídos com o método de alocação estático ou dinâmico.
- Tenha um tempo de saída de entrada recímido ajustável de 4-30 minutos, com um padrão de 4 minutos, e uma saída fixa originou o tempo de saída inativo de 4 minutos.
- Estão abertos por predefinição.  Os grupos de segurança de rede são recomendados, mas opcionais para restringir o tráfego de entrada ou de saída.
- Atribuído a qualquer recurso Azure que possa ser atribuído um endereço IP público, tais como:
    * Interfaces de rede
    * Gateways de VPN
    * Gateways de Aplicação
    * Balançadores de carga pública
- Não apoie cenários de Zona de Disponibilidade. Utilize o IP público Standard SKU para cenários de Zona de Disponibilidade. Para obter mais informações sobre zonas de disponibilidade, veja [Descrição geral de zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Balanceador de Carga Standard e Zonas de Disponibilidade](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="allocation-method"></a>Método de alocação

Os IPs públicos básicos e padrão suportam a atribuição **estática.**  O recurso é atribuído a um endereço IP no momento em que é criado. O endereço IP é liberado quando o recurso é eliminado.

Os endereços IP públicos básicos do SKU suportam uma atribuição **dinâmica.** Dinâmica é o método de atribuição padrão. O endereço IP **não é** dado ao recurso no momento da criação na hora de selecionar dinâmica.

O IP é atribuído quando associa o recurso de endereço IP público a um:

* Máquina virtual 
* A primeira máquina virtual está associada ao pool de backend de um equilibrador de carga.

O endereço IP é libertado quando para (ou elimina) o recurso.  

Por exemplo, um recurso IP público é libertado a partir de um recurso chamado **Recurso A**. **O Recurso A** recebe um IP diferente no arranque se o recurso IP público for reatribuído.

O endereço IP é liberado quando o método de atribuição é alterado de **estática** para **dinâmica**. Para garantir que o endereço IP para o recurso associado permanece o mesmo, dedipa o método de atribuição explicitamente para **estático**. É atribuído imediatamente um endereço IP estático.

> [!NOTE]
> Mesmo se definir o método de alocação como **estático**, não pode especificar o endereço IP real atribuído ao recurso de endereço IP público. O Azure atribui o endereço IP a partir de um conjunto de endereços IP disponíveis na localização do Azure na qual o recurso é criado.
>

Normalmente, os endereços IP públicos são utilizados nos cenários seguintes:

* Quando o utilizador tem de atualizar as regras de firewall para comunicar com os seus recursos do Azure.
* Resolução de nomes DNS, em que a alteração de um endereço IP exigiria que os registos A fossem atualizados.
* Os seus recursos do Azure comunicam com outros serviços ou aplicações que utilizam um modelo de segurança baseado no endereço IP.
* Utiliza certificados TLS/SSL ligados a um endereço IP.

> [!NOTE]
> O Azure aloca endereços IP públicos a partir de um intervalo exclusivo de cada região em cada cloud do Azure. Pode transferir a lista de intervalos (prefixos) das clouds [Pública](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) e [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.
>

## <a name="dns-hostname-resolution"></a>Resolução de nomes de anfitrião DNS

Selecione a opção de especificar uma etiqueta de nome de domínio DNS para um recurso IP público. 

Esta seleção cria um mapeamento para **domainnamelabel.** **localização**.cloudapp.azure.com ao IP público no DNS gerido pelo Azure. 

Por exemplo, a criação de um IP público com:

* **contoso** como um **domainnamelabel**
* **Eua Ocidentais** Local **azul**

O nome de domínio totalmente qualificado (FQDN) **contoso.westus.cloudapp.azure.com** resolve para o endereço IP público do recurso.

> [!IMPORTANT]
> Cada etiqueta de nome de domínio criada tem de ser exclusiva no âmbito da respetiva localização do Azure.  
>

## <a name="dns-recommendations"></a>Recomendações do DNS

Se for necessário um movimento regional, não pode migrar o FQDN do seu IP público. Utilize o FQDN para criar um registo CNAME personalizado que indique o endereço IP público. 

Se for necessária uma mudança para um IP público diferente, atualize o registo CNAME em vez de atualizar o FQDN.

Pode utilizar [o Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) ou um fornecedor externo de DNS para o seu DNS Record. 

## <a name="virtual-machines"></a>Máquinas virtuais

Pode associar um endereço IP público a uma máquina virtual do [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou do [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ao atribuí-lo à respetiva **interface de rede**. 

Escolha **dinâmico** ou **estático** para o endereço IP público. Saiba mais sobre como [atribuir endereços IP a interfaces de rede](virtual-network-network-interface-addresses.md).

## <a name="internet-facing-load-balancers"></a>Balanceadores de carga com acesso à Internet

Pode associar um endereço IP público de qualquer um dos [SKU](#sku) a um [Balançador de Carga Azure,](../load-balancer/load-balancer-overview.md)atribuindo-o à configuração frontal do balançador **de** carga. O IP público serve como um IP equilibrado em carga. 

Pode atribuir um endereço IP público dinâmico ou estático a um front-end de balanceador de carga. Pode atribuir vários endereços IP públicos a uma extremidade frontal do balançador de carga. Esta configuração permite cenários [multi-VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) como um ambiente multi-inquilino com websites baseados em TLS. 

Para obter mais informações sobre os SKUs de balanceador de carga do Azure, veja [SKU standard de balanceador de carga do Azure](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="vpn-gateways"></a>Gateways de VPN

[A Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) conecta uma rede virtual Azure a:

* Redes virtuais do Azure
* Rede no local. 

Um endereço IP público é atribuído ao Gateway VPN para permitir a comunicação com a rede remota. Só pode atribuir um endereço IP público básico *dinâmico* a um gateway de VPN.

## <a name="application-gateways"></a>Gateways de aplicação

Pode associar um endereço IP público a um [Gateway de Aplicação](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure, ao atribuí-lo à configuração do **front-end** do gateway. 

* Atribua um IP público básico **dinâmico** a uma configuração frontal do gateway de aplicação V1. 
* Atribua um endereço SKU padrão **estático** a uma configuração frontal V2.

## <a name="azure-firewall"></a>Azure Firewall

[O Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) permite-lhe criar, impor e registar políticas de aplicação e conectividade de rede em subscrições e redes virtuais.

Só pode associar endereços IP públicos **estáticos** padrão com uma firewall. Isto permite que firewalls externos identifiquem tráfego originário da sua rede virtual. 


## <a name="at-a-glance"></a>De relance

A tabela a seguir mostra a propriedade através da qual um IP público pode ser associado a um recurso de alto nível e aos possíveis métodos de atribuição.

| Recurso de nível superior | Associação de endereço IP | Dinâmica | Estático |
| --- | --- | --- | --- |
| Máquina virtual |Interface de rede |Sim |Sim |
| Balanceador de carga com acesso à Internet |Configuração de front-end |Sim |Sim |
| Gateway de VPN |Configuração de IP do gateway |Sim |Não |
| Gateway de aplicação |Configuração de front-end |Sim (apenas V1) |Sim (apenas V2) |
| Azure Firewall | Configuração de front-end | Não | Sim|

## <a name="limits"></a>Limites

Os limites para a endereçamento de IP estão listados no conjunto completo de [limites para a ligação](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) em rede em Azure. 

Os limites são por região e por subscrição. [Suporte de contato](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar os limites de incumprimento até aos limites máximos baseados nas necessidades do seu negócio.

## <a name="pricing"></a>Preços

Os endereços IP públicos podem ter custos nominais. Para saber mais sobre os preços dos endereços IP no Azure, reveja a página [Preços de Endereços IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais [sobre endereços IP privados em Azure](private-ip-addresses.md)
* [Deploy a VM with a static public IP using the Azure portal (Implementar uma VM com IP público estático através do portal do Azure)](virtual-network-deploy-static-pip-arm-portal.md)

