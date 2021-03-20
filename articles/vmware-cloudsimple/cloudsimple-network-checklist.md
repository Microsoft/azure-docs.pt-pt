---
title: Solução Azure VMware by CloudSimple - Lista de verificação de rede
description: Lista de verificação para alocar ciDR de rede na Solução VMware Azure por CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "77025015"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Pré-requisitos de networking para Azure VMware Solution by CloudSimple

A Azure VMware Solution by CloudSimple oferece um ambiente de nuvem privada VMware acessível para utilizadores e aplicações a partir de ambientes no local, dispositivos geridos pela empresa e recursos Azure. A conectividade é prestada através de serviços de networking, tais como VPNs e ligações Azure ExpressRoute. Alguns destes serviços de networking exigem que especifique os intervalos de endereços de rede para permitir os serviços. 

As tabelas deste artigo descrevem o conjunto de intervalos de endereços e serviços correspondentes que utilizam os endereços especificados. Alguns dos endereços são obrigatórios e alguns dependem dos serviços que pretende implementar. Estes espaços de endereço não devem sobrepor-se a nenhuma das suas sub-redes no local, sub-redes de Rede Virtual Azure ou sub-redes de carga de trabalho planeadas cloudSimple.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Intervalos de endereços de rede necessários para criar uma nuvem privada

Durante a criação de um serviço CloudSimple e de uma nuvem privada, deve cumprir as gamas de encaminhamento inter-domínio (CIDR) sem classe especificadas, da seguinte forma.

| Nome/usado para     | Description                                                                                                                            | Intervalo de endereços            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Gateway CIDR      | Necessário para serviços de borda (gateways VPN).  Este CIDR é necessário durante a criação do Serviço CloudSimple e deve ser do espaço RFC 1918. | /28                      |
| vSphere/vSAN CIDR | Obrigatório para redes de gestão VMware. Este CIDR deve ser especificado durante a criação de nuvens privadas.                                    | /24 ou /23 ou /22 ou /21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Intervalo de endereços de rede necessário para a ligação da rede Azure a uma rede no local

A ligação de uma [rede no local à rede de nuvem privada através do ExpressRoute](on-premises-connection.md) estabelece uma ligação Global Reach.  A ligação utiliza o Border Gateway Protocol (BGP) para trocar rotas entre a sua rede no local, a sua rede de nuvem privada e as suas redes Azure.

| Nome/usado para             | Description                                                                                                                                                                             | Intervalo de endereços |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute Peering CIDR | Necessário quando utilizar o ExpressRoute Global Reach para conectividade no local. Este CIDR deve ser fornecido quando um pedido de ligação Global Reach é feito através de um bilhete de apoio. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Intervalo de endereços de rede necessário para a utilização de uma ligação VPN site-to-site a uma rede no local

A ligação de uma [rede no local à rede de nuvem privada através da utilização de VPN site-to-site](vpn-gateway.md) requer os seguintes endereços IP, rede no local e identificadores. 

| Intervalo de endereço/endereço | Description                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Peer IP               | Endereço IP público de gateway VPN no local. Necessário estabelecer uma ligação VPN local-a-local entre um datacenter no local e a região de Serviço CloudSimple. Este endereço IP é necessário durante a criação de gateway VPN site-to-site.                                         |
| Identificador de pares       | Identificador de pares do portal VPN no local. Isto é geralmente o mesmo **que peer IP**.  Se for especificado um identificador único no seu gateway VPN no local, o identificador deve ser especificado.  O ID do peer é necessário durante a criação de gateway VPN local.   |
| Redes no local   | Prefixos no local que precisam de acesso às redes CloudSimple na região.  Inclua todos os prefixos de uma rede no local que aceda à rede CloudSimple, incluindo a rede de clientes de onde os utilizadores irão aceder à rede.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Intervalo de endereços de rede necessário para a utilização de ligações VPN ponto-a-local

Uma ligação VPN ponto-a-local permite o acesso à rede CloudSimple a partir de uma máquina cliente.  [Para configurar a VPN ponto a local,](vpn-gateway.md)tem de especificar o seguinte intervalo de endereços de rede.

| Intervalo de endereço/endereço | Description                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sub-rede de clientes         | Os endereços DHCP são fornecidos pela sub-rede do cliente quando se conecta utilizando uma VPN ponto-a-local. Esta sub-rede é necessária enquanto está a criar uma porta VPN ponto a local num portal CloudSimple.  A rede é dividida em duas sub-redes; um para a ligação UDP e o outro para ligações TCP. |

## <a name="next-steps"></a>Passos seguintes

* [Configuração de firewall no local para aceder à sua nuvem privada](on-premises-firewall-configuration.md)
* [Quickstart - Criar um serviço CloudSimple](quickstart-create-cloudsimple-service.md)
* [Quickstart- Configurar uma nuvem privada](quickstart-create-private-cloud.md)
* Saiba mais sobre [as ligações à rede Azure](cloudsimple-azure-network-connection.md)
* Saiba mais sobre [gateways VPN](cloudsimple-vpn-gateways.md)
