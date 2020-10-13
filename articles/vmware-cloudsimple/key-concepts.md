---
title: Conceitos-chave para administrar Azure VMware Solution by CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve conceitos-chave para administrar soluções Azure VMware por CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8677bc7e4b9f4c2b24b5439aeefb307b9c544ed3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86525029"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Conceitos-chave para a administração de Azure VMware Solutions by CloudSimple

A administração de Soluções VMware Azure pela CloudSimple requer uma compreensão dos seguintes conceitos:

* Serviço CloudSimple, que é apresentado como Azure VMware Solutions by CloudSimple - Serviço
* Nó CloudSimple, que é apresentado como Azure VMware Solutions by CloudSimple - Nó
* Nuvem Simple nuvem privada
* Rede de serviços
* Máquina virtual CloudSimple, que é exibida como Azure VMware Solutions by CloudSimple - Máquina virtual

## <a name="cloudsimple-service"></a>Serviço CloudSimple

Com o serviço CloudSimple, pode criar e gerir todos os recursos associados à VMware Solutions by CloudSimple a partir do portal Azure. Crie um recurso de serviço em todas as regiões onde pretende utilizar o serviço.

Saiba mais sobre o [serviço CloudSimple.](cloudsimple-service.md)

## <a name="cloudsimple-node"></a>Nó CloudSimple

Um nó CloudSimple é um hospedeiro dedicado, de metal nu, hiperconvergado e de armazenamento no qual é implantado o hipervisor VMware ESXi. Este nó é então incorporado nas plataformas VMware vSphere, vCenter, vSAN e NSX. Os serviços de networking cloudSimple e os serviços de rede de borda também estão habilitados. Cada nó serve como uma unidade de capacidade de computação e armazenamento que você pode providenciar para criar [nuvens privadas CloudSimple](cloudsimple-private-cloud.md). Você fornece ou reserva nós em uma região onde o serviço CloudSimple está disponível.

Saiba mais sobre [os nós CloudSimple](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>Nuvem Simple nuvem privada

Uma nuvem privada CloudSimple é um ambiente isolado de pilha de VMware gerido por um servidor vCenter no seu próprio domínio de gestão. A pilha VMware inclui anfitriões ESXi, vSphere, vCenter, vSAN e NSX. A stack funciona em nós dedicados (hardware de metal nu dedicado e isolado) e é consumida pelos utilizadores através de ferramentas nativas de VMware que incluem vCenter e NSX Manager. Os nós dedicados são implantados em locais Azure e são geridos pela Azure. Cada nuvem privada pode ser segmentada e protegida utilizando serviços de networking como VLANs e sub-redes e tabelas de firewall. As ligações ao ambiente no local e à rede Azure são criadas através de ligações VPN e Azure ExpressRoute seguras e privadas.

Saiba mais sobre [cloudSimple nuvem privada.](cloudsimple-private-cloud.md)

## <a name="service-networking"></a>Rede de serviços

O serviço CloudSimple fornece uma rede por região onde o seu serviço CloudSimple é implantado. A rede é um único espaço de endereço TCP Layer 3 com encaminhamento ativado por padrão. Todas as nuvens e sub-redes privadas criadas nesta região comunicam entre si sem qualquer configuração adicional. Cria grupos porta distribuídos no vCenter utilizando os VLANs. Pode utilizar as seguintes funcionalidades de rede para configurar e proteger os seus recursos de carga de trabalho na sua nuvem privada:

* [VLANs e sub-redes](cloudsimple-vlans-subnets.md)
* [Tabelas de firewall](cloudsimple-firewall-tables.md)
* [Gateways de VPN](cloudsimple-vpn-gateways.md)
* [IP público](cloudsimple-public-ip-address.md)
* [Ligação de rede do Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Máquina virtual CloudSimple

Com o serviço CloudSimple, pode gerir máquinas virtuais VMware a partir do portal Azure. Um ou mais aglomerados ou conjuntos de recursos do seu ambiente vSphere podem ser mapeados para a subscrição em que o serviço é criado.

Saiba mais sobre:

* [Máquinas virtuais CloudSimple](cloudsimple-virtual-machines.md)
* [Mapeamento de subscrição Azure](./azure-subscription-mapping.md)
