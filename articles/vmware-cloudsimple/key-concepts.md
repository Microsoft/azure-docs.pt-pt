---
title: Conceitos-chave para administrar a Solução Azure VMware pela CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve conceitos-chave para administrar soluções Azure VMware pela CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efe6cc132b062e833dc85c13cf2f6c5f6289484c
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77564624"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Conceitos-chave para administração de Soluções Azure VMware pela CloudSimple

Administrar soluções Azure VMware pela CloudSimple requer uma compreensão dos seguintes conceitos:

* Serviço CloudSimple, que é apresentado como Soluções Azure VMware by CloudSimple - Serviço
* CloudSimple nó, que é exibido como Soluções Azure VMware by CloudSimple - Nó
* NuvemSimple nuvem privada
* Rede de serviços
* CloudSimple máquina virtual, que é exibida como Soluções Azure VMware por CloudSimple - Máquina virtual

## <a name="cloudsimple-service"></a>Serviço CloudSimple

Com o serviço CloudSimple, pode criar e gerir todos os recursos associados às Soluções VMware pela CloudSimple a partir do portal Azure. Crie um recurso de serviço em todas as regiões onde pretende utilizar o serviço.

Saiba mais sobre o [serviço CloudSimple.](cloudsimple-service.md)

## <a name="cloudsimple-node"></a>CloudSimple nó

Um nó CloudSimple é um conjunto dedicado, de metal nu, hiperconvergente e anfitrião de armazenamento no qual o hipervisor VMware ESXi é implantado. Este nó é então incorporado nas plataformas VMware vSphere, vCenter, vSAN e NSX. Os serviços de rede CloudSimple e os serviços de rede de borda também estão ativados. Cada nó serve como uma unidade de computação e capacidade de armazenamento que você pode fornecer para criar [nuvens privadas CloudSimple](cloudsimple-private-cloud.md). Você disponibiliza ou reserva nódosos numa região onde o serviço CloudSimple está disponível.

Saiba mais sobre [os nós CloudSimple](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>NuvemSimple nuvem privada

Uma nuvem privada CloudSimple é um ambiente de pilha de VMware isolado gerido por um servidor vCenter no seu próprio domínio de gestão. A pilha VMware inclui anfitriões ESXi, vSphere, vCenter, vSAN e NSX. A pilha funciona em nós dedicados (hardware de metal nu dedicado e isolado) e é consumida pelos utilizadores através de ferramentas vMware nativas que incluem vCenter e NSX Manager. Os nóteos dedicados são implantados em localizações do Azure e são geridos pelo Azure. Cada nuvem privada pode ser segmentada e protegida utilizando serviços de networking tais como VLANs e subredes e tabelas de firewall. As ligações ao seu ambiente no local e à rede Azure são criadas utilizando ligações VPN seguras e privadas e Azure ExpressRoute.

Saiba mais sobre [a cloudSimple nuvem privada](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Rede de serviços

O serviço CloudSimple fornece uma rede por região onde o seu serviço CloudSimple está implantado. A rede é um único espaço de endereço TCP Layer 3 com encaminhamento ativado por padrão. Todas as nuvens privadas e subredes criadas nesta região comunicam entre si sem qualquer configuração adicional. Cria grupos portuários distribuídos no vCenter utilizando os VLANs. Pode utilizar as seguintes funcionalidades de rede para configurar e proteger os seus recursos de carga de trabalho na sua nuvem privada:

* [VLANs e subredes](cloudsimple-vlans-subnets.md)
* [Tabelas de firewall](cloudsimple-firewall-tables.md)
* [Gateways VPN](cloudsimple-vpn-gateways.md)
* [IP Público](cloudsimple-public-ip-address.md)
* [Ligação à rede Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Máquina virtual CloudSimple

Com o serviço CloudSimple, pode gerir máquinas virtuais VMware a partir do portal Azure. Um ou mais clusters ou conjuntos de recursos do seu ambiente vSphere podem ser mapeados para a subscrição em que o serviço é criado.

Saiba mais sobre:

* [Máquinas virtuais CloudSimple](cloudsimple-virtual-machines.md)
* [Mapeamento de assinatura Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
