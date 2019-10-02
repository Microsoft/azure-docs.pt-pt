---
title: Lista de verificação da solução Azure VMware por CloudSimple-Network
description: Lista de verificação para alocar o CIDR de rede na solução VMware do Azure por CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c31942b198697ddc913f3732fd41409334ff0ef6
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817478"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Pré-requisitos de rede para a solução do Azure VMware por CloudSimple

A solução Azure VMware da CloudSimple oferece um ambiente de nuvem privada VMware que é acessível para usuários e aplicativos de ambientes locais, de dispositivos corporativos gerenciados, bem como de recursos do Azure. A conectividade é fornecida usando serviços de rede, como VPNs e conexões do ExpressRoute.  Alguns dos serviços de rede exigem que você especifique intervalos de endereços de rede para habilitar os serviços.  As tabelas neste artigo descrevem o conjunto de intervalos de endereços e os serviços correspondentes que usam os endereços especificados.  Alguns dos endereçados são obrigatórios e alguns dependem dos serviços que você deseja implantar.  Esses espaços de endereço não devem se sobrepor a nenhuma das suas sub-redes locais, sub-redes de rede virtual do Azure ou sub-redes de carga de trabalho CloudSimple planejadas.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Intervalos de endereços de rede necessários para a criação de uma nuvem privada

Durante a criação do serviço CloudSimple e de uma nuvem privada, é necessário o seguinte intervalo de CIDR de rede.

| Nome/usado para     | Descrição                                                                                                                            | Intervalos de Endereços            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| CIDR do Gateway      | Necessário para serviços de borda (gateways de VPN).  Esse CIDR é necessário durante a criação do serviço CloudSimple e deve ser do espaço RFC 1918. | /28                      |
| CIDR vSphere/vSAN | Necessário para redes de gerenciamento do VMware. Esse CIDR deve ser especificado durante a criação da nuvem privada.                                    | /24 ou/23 ou/22 ou/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-on-premises-network"></a>Intervalo de endereços de rede necessário para a conexão de rede do Azure para a rede local

Conectar-se de uma [rede local à rede de nuvem privada usando o ExpressRoute](on-premises-connection.md) estabelece uma conexão alcance global.  A conexão irá trocar rotas via BGP entre sua rede local, rede de nuvem privada e suas redes do Azure.

| Nome/usado para             | Descrição                                                                                                                                                                             | Intervalos de Endereços |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| CIDR de emparelhamento do ExpressRoute | Necessário ao usar o ExpressRoute Alcance Global é usado para conectividade local. Esse CIDR precisa ser fornecido quando uma solicitação de conexão Alcance Global é feita por meio de um tíquete de suporte. | /29           |

## <a name="network-address-range-required-for-using-site-to-site-vpn-connection-to-on-premises-network"></a>Intervalo de endereços de rede necessário para usar a conexão VPN site a site para a rede local

Conectar-se de [uma rede local à rede de nuvem privada usando VPN site a site](vpn-gateway.md) requer os seguintes endereços IP, rede local e identificadores. 

| Intervalo de endereços/endereços | Descrição                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| IP de Elemento de Rede               | Endereço IP público do gateway de VPN local. Necessário para estabelecer a conexão VPN site a site entre o datacenter local e a região do serviço CloudSimple. Esse IP é necessário durante a criação do gateway de VPN site a site.                                         |
| Identificador do Elemento de Rede       | Identificador de par do gateway de VPN local. Isso geralmente é o mesmo que o **IP par**.  Se um identificador exclusivo for especificado no gateway de VPN local, o identificador precisará ser especificado.  A ID de par é necessária durante a criação do gateway de VPN site a site.   |
| Redes locais   | Prefixos locais que precisam acessar redes CloudSimple na região.  Inclua todos os prefixos da rede local que acessarão a rede CloudSimple, incluindo a rede do cliente de onde os usuários acessarão.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Intervalo de endereços de rede necessário para usar conexões VPN ponto a site

A conexão VPN ponto a site permite o acesso à rede CloudSimple de um computador cliente.  A [configuração de VPN ponto a site](vpn-gateway.md) requer que o intervalo de endereços de rede a seguir seja especificado.

| Intervalo de endereços/endereços | Descrição                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sub-rede do cliente         | Os endereços DHCP serão fornecidos na sub-rede do cliente quando você se conectar usando VPN ponto a site. Essa sub-rede é necessária ao criar um gateway de VPN ponto a site no portal do CloudSimple.  A rede será dividida em duas sub-redes e uma será usada para conexão UDP e outras para conexões TCP. |

## <a name="next-steps"></a>Passos seguintes

* [Início rápido-criar serviço CloudSimple](quickstart-create-cloudsimple-service.md)
* [início rápido-criar-privado-nuvem](quickstart-create-private-cloud.md)
* Saiba mais sobre [as conexões de rede do Azure](cloudsimple-azure-network-connection.md)
* Saiba mais sobre [gateways de VPN](cloudsimple-vpn-gateways.md)
