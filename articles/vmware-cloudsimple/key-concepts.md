---
title: Conceitos chave para administrar o Azure VMware solução por CloudSimple
description: Descreve conceitos chave para administrar o Azure VMware solução por CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3eff61408cb190396987ace6dee21182cff4f25c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165194"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Conceitos chave para a administração da solução de VMware do Azure por CloudSimple

Administrar o Azure VMware solução por CloudSimple requer uma compreensão dos conceitos seguintes:

* Serviço CloudSimple, que é apresentado como solução de VMware do Azure por CloudSimple - serviço
* Nó de CloudSimple, que é apresentado como solução de VMware do Azure por CloudSimple - nó
* CloudSimple de nuvem privada
* Rede de serviço
* Máquina virtual de CloudSimple, que é apresentada como solução de VMware do Azure por CloudSimple - Máquina Virtual

## <a name="cloudsimple-service"></a>Serviço de CloudSimple

Com o serviço de CloudSimple, pode criar e gerir todos os recursos associados às soluções de VMware por CloudSimple do portal do Azure. Crie um recurso de serviço em cada região em que pretende usar o serviço.

Saiba mais sobre o [CloudSimple serviço](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>Nó de CloudSimple

Um nó de CloudSimple é um anfitrião de computação e armazenamento de hiperconvergido dedicado de bare-metal, no qual o hipervisor do VMware ESXi está implementado. Este nó, em seguida, é incorporado no VMware vSphere, vCenter, vSAN e plataformas NSX. Serviços de rede CloudSimple e serviços de rede de borda também estão ativados. Cada nó serve como uma unidade de capacidade de computação e armazenamento que pode aprovisionar criem [nuvens privadas de CloudSimple](cloudsimple-private-cloud.md). Aprovisionar ou reserva de nós numa região em que o serviço de CloudSimple está disponível.


Saiba mais sobre [CloudSimple nós](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>CloudSimple de nuvem privada

Uma nuvem privada CloudSimple é um ambiente isolado de pilha de VMware gerido por um vCenter server no seu próprio domínio de gestão. A pilha de VMware inclui ESXi anfitriões vSphere, vCenter, vSAN e NSX. O pilha é executado em nós (dedicado e isolado hardware bare-metal) dedicados e é consumido por utilizadores através de ferramentas nativas do VMware que incluem o vCenter e NSX Manager. Nós dedicados são implementados em localizações do Azure e são geridos pelo Azure. Cada nuvem privada pode ser segmentada e protegido com serviços de rede, como VLANs e sub-redes e tabelas de firewall. Ligações ao seu ambiente no local e a rede do Azure são criadas ao utilizar seguras, privadas VPN e Azure ExpressRoute ligações.

Saiba mais sobre [CloudSimple de nuvem privada](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Rede de serviço

O serviço de CloudSimple fornece uma rede por região em que o seu serviço CloudSimple é implementado. A rede é um único espaço de endereço TCP camada 3 com o encaminhamento ativado por predefinição. Todas as nuvens privadas e as sub-redes criadas nesta região comunicam entre si sem qualquer configuração adicional. Criar grupos de portas distribuída no vCenter com as VLANs. Pode utilizar as seguintes funcionalidades de rede para configurar e proteger os seus recursos de carga de trabalho na sua cloud privada:

* [VLANs e sub-redes](cloudsimple-vlans-subnets.md)
* [Tabelas de firewall](cloudsimple-firewall-tables.md)
* [Gateways de VPN](cloudsimple-vpn-gateways.md)
* [IP público](cloudsimple-public-ip-address.md)
* [Ligação de rede do Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Máquina virtual de CloudSimple

Com o serviço de CloudSimple, pode gerir máquinas virtuais VMware no portal do Azure. Um ou mais clusters ou agrupamentos de recursos do seu ambiente de vSphere podem ser mapeados para a subscrição em que o serviço foi criado.

Saiba mais sobre:

* [Máquinas de virtuais de CloudSimple](cloudsimple-virtual-machines.md)
* [Mapeamento de subscrição do Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
