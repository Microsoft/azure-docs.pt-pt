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
ms.openlocfilehash: 0d890553ee145ca6aafed5a34d158c6a34d9af36
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358172"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Conceitos chave para a administração da solução de VMware do Azure por CloudSimple

Administrar o Azure VMware solução por CloudSimple necessita de compreender os seguintes conceitos.

* Serviço de CloudSimple (apresentados como solução de VMware do Azure por CloudSimple - Service)
* Nó de CloudSimple (apresentados como solução de VMware do Azure por CloudSimple - nó)
* Nuvem privada de CloudSimple
* Serviço de rede
* Máquina Virtual de CloudSimple (apresentados como solução de VMware do Azure por CloudSimple - Máquina Virtual)

## <a name="cloudsimple-service"></a>Serviço de CloudSimple

O serviço de CloudSimple permite-lhe criar e gerir todos os recursos associados às soluções de VMware por CloudSimple do portal do Azure. Crie um recurso de serviço em cada região em que pretende usar o serviço. 

Saiba mais sobre o [CloudSimple serviço](cloudsimple-service.md)

## <a name="cloudsimple-node"></a>Nó de CloudSimple

Um nó de CloudSimple é um dedicado de bare-metal, o anfitrião de computação e armazenamento de hiperconvergido no qual o hipervisor do VMware ESXi está implementado. Este nó, em seguida, é incorporado no VMware vSphere, vCenter, vSAN e plataformas NSX. Serviços de rede CloudSimple e serviços de rede de borda também estão ativados. Cada nó serve como uma unidade de capacidade de computação e armazenamento que pode ser adquirida criem [nuvens privadas CloudSimple](cloudsimple-private-cloud.md). Comprar ou reserva de nós numa região em que o serviço de CloudSimple está disponível.


Saiba mais sobre [CloudSimple nós](cloudsimple-node.md)

## <a name="cloudsimple-private-cloud"></a>Nuvem privada de CloudSimple

Uma nuvem privada CloudSimple é um ambiente isolado de pilha de VMware gerido por um vCenter server no seu próprio domínio de gestão. Pilha do VMware inclui ESXi anfitriões vSphere, vCenter, vSAN e NSX.  O pilha é executado em nós (dedicado e isolado bare-metal hardware) dedicados e é consumido por utilizadores através de ferramentas nativas do VMware que incluem o vCenter e NSX Manager. Nós dedicados são implementados em localizações do Azure e são geridos pelo Azure. Cada nuvem privada podem ser segmentado e protegida através de serviços, como VLANs/sub-redes de rede e tabelas de firewall.  Ligações ao seu ambiente no local e a rede do Azure são criadas através da VPN segura e privada e as ligações do ExpressRoute do Azure.

Saiba mais sobre [nuvem privada CloudSimple](cloudsimple-private-cloud.md)

## <a name="service-networking"></a>Rede de serviço

O serviço de CloudSimple fornece uma rede por região em que o seu serviço CloudSimple é implementado. A rede é um único espaço de endereço TCP camada 3 com o encaminhamento ativado por predefinição. Todas as nuvens privadas e as sub-redes criadas nesta região comunicam entre si sem qualquer configuração adicional. Criar grupos de portas distribuída no vCenter com as VLANs.  Pode utilizar as seguintes funcionalidades de rede para configurar e proteger os seus recursos de carga de trabalho na sua nuvem privada.

* [VLANs/sub-redes](cloudsimple-vlans-subnets.md)
* [Tabelas de firewall](cloudsimple-firewall-tables.md)
* [Gateways de VPN](cloudsimple-vpn-gateways.md)
* [IP público](cloudsimple-public-ip-address.md)
* [Ligação de rede do Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Máquina virtual de CloudSimple

O serviço de CloudSimple permite-lhe gerir máquinas virtuais VMware no portal do Azure. Um ou mais clusters ou agrupamentos de recursos do seu ambiente de vSphere podem ser mapeados para a subscrição em que o serviço foi criado.

Saiba mais sobre:

* [Máquinas de virtuais de CloudSimple](cloudsimple-virtual-machines.md)
* [Mapeamento de subscrição do Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
