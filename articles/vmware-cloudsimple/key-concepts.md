---
title: Conceitos-chave para administrar soluções Azure VMware (AVS)
description: Descreve conceitos-chave para administrar soluções Azure VMware (AVS)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c581ffbba7fbaefc8fbd0ffc7e92350bd69802b8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025236"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-avs"></a>Conceitos-chave para a administração da Azure VMware Solutions (AVS)

A administração de Soluções Azure VMware (AVS) requer uma compreensão dos seguintes conceitos:

* Serviço AVS, que é apresentado como Azure VMware Solutions (AVS) - Serviço
* Nó AVS, que é apresentado como Azure VMware Solutions (AVS) - Nó
* Nuvem privada AVS
* Rede de serviço
* Máquina virtual AVS, que é exibida como Azure VMware Solutions (AVS) - Máquina virtual

## <a name="avs-service"></a>Serviço AVS

Com o serviço AVS, pode criar e gerir todos os recursos associados à VMware Solutions (AVS) a partir do portal Azure. Crie um recurso de serviço em todas as regiões em que você pretende usar o serviço.

Saiba mais sobre o [serviço AVS.](cloudsimple-service.md)

## <a name="avs-node"></a>Nó AVS

Um nó AVS é um conjunto dedicado, de metal nu, hiperconvergente e anfitrião de armazenamento no qual o hipervisor VMware ESXi é implantado. Esse nó é então incorporado às plataformas VMware vSphere, vCenter, vSAN e NSX. Os serviços de rede AVS e os serviços de rede de borda também estão ativados. Cada nó serve como uma unidade de computação e capacidade de armazenamento que você pode fornecer para criar [nuvens privadas AVS](cloudsimple-private-cloud.md). Você disponibiliza ou reserva nódosos numa região onde o serviço AVS está disponível.

Saiba mais sobre [os nós da AVS.](cloudsimple-node.md)

## <a name="avs-private-cloud"></a>Nuvem privada AVS

Uma nuvem privada AVS é um ambiente de pilha de VMware isolado gerido por um servidor vCenter no seu próprio domínio de gestão. O VMware Stack inclui hosts ESXi, vSphere, vCenter, vSAN e NSX. A pilha é executada em nós dedicados (hardware bare-metal dedicado e isolado) e é consumida pelos usuários por meio de ferramentas nativas do VMware que incluem o vCenter e o NSX Manager. Os nós dedicados são implantados em locais do Azure e são gerenciados pelo Azure. Cada Nuvem Privada AVS pode ser segmentada e protegida utilizando serviços de networking tais como VLANs e subredes e tabelas de firewall. As conexões com seu ambiente local e a rede do Azure são criadas usando conexões seguras, VPN privadas e Azure ExpressRoute.

Saiba mais sobre [a nuvem privada AVS.](cloudsimple-private-cloud.md)

## <a name="service-networking"></a>Rede de serviço

O serviço AVS fornece uma rede por região onde o seu serviço AVS é implantado. A rede é um único espaço de endereço de camada TCP 3 com roteamento habilitado por padrão. Todas as Nuvens Privadas aVS e subredes criadas nesta região comunicam entre si sem qualquer configuração adicional. Você cria grupos de portas distribuídas no vCenter usando as VLANs. Pode utilizar as seguintes funcionalidades de rede para configurar e proteger os seus recursos de carga de trabalho na sua Nuvem Privada AVS:

* [VLANs e sub-redes](cloudsimple-vlans-subnets.md)
* [Tabelas de firewall](cloudsimple-firewall-tables.md)
* [Gateways de VPN](cloudsimple-vpn-gateways.md)
* [IP público](cloudsimple-public-ip-address.md)
* [Conexão de rede do Azure](cloudsimple-azure-network-connection.md)

## <a name="avs-virtual-machine"></a>Máquina virtual AVS

Com o serviço AVS, pode gerir máquinas virtuais VMware a partir do portal Azure. Um ou mais clusters ou pools de recursos do seu ambiente vSphere podem ser mapeados para a assinatura na qual o serviço é criado.

Saiba mais sobre:

* [Máquinas virtuais AVS](cloudsimple-virtual-machines.md)
* [Mapeamento de assinatura do Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
