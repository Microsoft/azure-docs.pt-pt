---
title: Solução Azure VMware by CloudSimple - Lista de verificação de rede
description: Lista de verificação para alocar rede CIDR na Solução Azure VMware by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77025015"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Pré-requisitos de networking para a solução Azure VMware by CloudSimple

A Azure VMware Solution by CloudSimple oferece um ambiente de nuvem privada VMware acessível para utilizadores e aplicações a partir de ambientes no local, dispositivos geridos pela empresa e recursos Azure. A conectividade é transmitida através de serviços de networking, tais como VPNs e ligações Azure ExpressRoute. Alguns destes serviços de networking exigem que especifique as gamas de endereços da rede para permitir os serviços. 

Os quadros deste artigo descrevem o conjunto de gamas de endereços e os serviços correspondentes que utilizam os endereços especificados. Alguns dos endereços são obrigatórios e alguns dependem dos serviços que pretende implementar. Estes espaços de endereço não devem sobrepor-se a nenhuma das suas subredes no local, subnets da Rede Virtual Azure ou subredes de carga de trabalho CloudSimple planeadas.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Gamas de endereços de rede necessárias para criar uma nuvem privada

Durante a criação de um serviço CloudSimple e de uma nuvem privada, deve cumprir as gamas especificadas de encaminhamento inter-domínio sem classe (CIDR) da rede, da seguinte forma.

| Nome/utilizado para     | Descrição                                                                                                                            | Intervalo de endereços            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Gateway CIDR      | Necessário para serviços de borda (gateways VPN).  Este CIDR é necessário durante a criação do Serviço CloudSimple e deve ser do espaço RFC 1918. | /28                      |
| vSphere/vSAN CIDR | Necessário para redes de gestão VMware. Este CIDR deve ser especificado durante a criação privada de nuvens.                                    | /24 ou /23 ou /22 ou /21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Gama de endereços de rede necessária para a ligação da rede Azure a uma rede no local

A ligação de uma rede no local à rede privada de nuvem através do [ExpressRoute](on-premises-connection.md) estabelece uma ligação Global Reach.  A ligação utiliza o Border Gateway Protocol (BGP) para trocar rotas entre a sua rede no local, a sua rede privada de nuvem e as suas redes Azure.

| Nome/utilizado para             | Descrição                                                                                                                                                                             | Intervalo de endereços |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute Peering CIDR | Necessário quando utiliza o ExpressRoute Global Reach para a conectividade no local. Este CIDR deve ser fornecido quando um pedido de ligação Global Reach é feito através de um bilhete de apoio. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Gama de endereços de rede necessária para a utilização de uma ligação VPN site-a-site a uma rede no local

A ligação de uma rede no local à rede privada de [nuvem utilizando VPN site-to-site](vpn-gateway.md) requer os seguintes endereços IP, rede no local e identificadores. 

| Intervalo de endereços/endereços | Descrição                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Peer IP               | No local, o endereço IP público de gateway VPN. Necessário para estabelecer uma ligação VPN site-to-site entre um datacenter no local e a região de Serviço CloudSimple. Este endereço IP é necessário durante a criação de gateway VPN do site-para-site.                                         |
| Identificador de pares       | Identificador de pares do gateway VPN no local. Isto é geralmente o mesmo que **peer IP**.  Se for especificado um identificador único no seu gateway VPN no local, o identificador deve ser especificado.  O ID peer é necessário durante a criação de gateway VPN site-to-site.   |
| Redes no local   | Prefixos no local que precisam de acesso às redes CloudSimple na região.  Inclua todos os prefixos de uma rede no local que acederá à rede CloudSimple, incluindo a rede de clientes a partir de onde os utilizadores irão aceder à rede.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Gama de endereços de rede necessária para a utilização de ligações VPN ponto-a-local

Uma ligação VPN ponto-a-site permite o acesso à rede CloudSimple a partir de uma máquina de cliente.  [Para configurar VPN ponto-a-local,](vpn-gateway.md)deve especificar o seguinte intervalo de endereços de rede.

| Intervalo de endereços/endereços | Descrição                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sub-rede de clientes         | Os endereços DHCP são fornecidos pela sub-rede do cliente quando se conecta utilizando uma VPN ponto-a-local. Esta sub-rede é necessária enquanto está a criar um gateway VPN ponto-a-site num portal CloudSimple.  A rede é dividida em duas subredes; uma para a ligação UDP e outra para ligações TCP. |

## <a name="next-steps"></a>Passos seguintes

* [Configuração de firewall no local para aceder à sua nuvem privada](on-premises-firewall-configuration.md)
* [Quickstart - Criar um serviço CloudSimple](quickstart-create-cloudsimple-service.md)
* [Quickstart- Configure uma nuvem privada](quickstart-create-private-cloud.md)
* Saiba mais sobre [as ligações de rede Azure](cloudsimple-azure-network-connection.md)
* Saiba mais sobre [gateways VPN](cloudsimple-vpn-gateways.md)
