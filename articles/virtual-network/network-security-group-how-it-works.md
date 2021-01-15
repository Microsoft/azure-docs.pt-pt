---
title: Grupo de segurança de rede - como funciona
titlesuffix: Azure Virtual Network
description: Saiba como os grupos de segurança de rede o ajudam a filtrar o tráfego de rede entre os recursos do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 5cf0345ccffe95286b95607c6c7322752df6342b
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223283"
---
# <a name="how-network-security-groups-filter-network-traffic"></a>Como os grupos de segurança da rede filtram o tráfego da rede
<a name="network-security-groups"></a>

Pode utilizar um grupo de segurança da rede Azure para filtrar o tráfego da rede de e para os recursos Azure numa rede virtual Azure. Os grupos de segurança de rede contêm [regras de segurança](./network-security-groups-overview.md#security-rules) que permitem ou negam o tráfego de entrada ou de saída de e para vários tipos de recursos do Azure. Para cada regra, pode especificar a origem e o destino, a porta e o protocolo.

Pode implementar recursos de vários serviços do Azure numa rede virtual do Azure. Para obter uma lista completa, veja [Services that can be deployed into a virtual network](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (Serviços que podem ser implementados numa rede virtual). Pode associar nenhum ou um grupo de segurança de rede à [sub-rede](virtual-network-manage-subnet.md#change-subnet-settings) e à [interface de rede](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) de cada rede virtual numa máquina virtual. O mesmo grupo de segurança de rede pode ser associado a tantas sub-redes e interfaces de rede que escolher.

A imagem seguinte ilustra diferentes cenários de implementação dos grupos de segurança de rede para permitir o tráfego de rede de e para a Internet a partir da porta TCP 80:

![NSG-processing](./media/network-security-group-how-it-works/network-security-group-interaction.png)

Veja a imagem anterior, juntamente com o texto seguinte, para compreender de que forma é que o Azure processa as regras de entrada e de saída para os grupos de segurança de rede:

## <a name="inbound-traffic"></a>Tráfego de entrada

Relativamente ao tráfego de entrada, o Azure processa primeiro as regras num grupo de segurança de rede associado a uma sub-rede, se existir alguma, e, depois, as regras num grupo de segurança de rede associado à interface de rede, se existir alguma.

- **VM1**: as regras de segurança em *NSG1* são processadas, uma vez que está associado a *Subnet1* e *VM1* está em *Subnet1*. A menos que tenha criado uma regra que permita a porta de entrada 80, o tráfego é negado pela regra de segurança predefinida [DenyAllInbound](./network-security-groups-overview.md#denyallinbound) e nunca é avaliado por *NSG2*, pois *NSG2* está associado à interface de rede. Se *NSG1* tiver uma regra de segurança que permite a porta 80, o tráfego é processado por *NSG2*. Para permitir a porta 80 na máquina virtual, tanto *NSG1*, como *NSG2*, têm de ter uma regra que permite a porta 80 a partir da Internet.
- **VM2**: as regras em *NSG1* são processadas, porque *VM2* também está em *Subnet1*. Uma vez que *VM2* não tem um grupo de segurança de rede associado à respetiva interface de rede, recebe todo o tráfego permitido através *NSG1* ou é-lhe negado todo o tráfego negado por *NSG1*. O tráfego é permitido ou negado para todos os recursos na mesma sub-rede se um grupo de segurança de rede estiver associado a uma sub-rede.
- **VM3**: uma vez que não existe nenhum grupo de segurança de rede associado a *Subnet2*, o tráfego é permitido na sub-rede e processado por *NSG2*, pois *NSG2* está associado à interface de rede anexada a *VM3*.
- **VM4**: o tráfego é permitido para *VM4,* porque não existe nenhum grupo de segurança de rede associado a *Subnet3* ou à interface de rede na máquina virtual. Todo o tráfego de rede é permitido através de uma sub-rede e de uma interface de rede se não houver nenhum grupo de segurança de rede associado às mesmas.

## <a name="outbound-traffic"></a>Tráfego de saída

Relativamente ao tráfego de saída, o Azure processa primeiro as regras num grupo de segurança de rede associado a uma interface de rede, se existir alguma, e, depois, as regras num grupo de segurança de rede associado à sub-rede, se existir alguma.

- **VM1**: as regras de segurança em *NSG2* são processadas. A menos que crie uma regra de segurança que negue a porta 80 de saída para a Internet, o tráfego é permitido pela regra de segurança predefinida [AllowInternetOutbound](./network-security-groups-overview.md#allowinternetoutbound) em *NSG1* e *NSG2*. Se *NSG2* tiver uma regra de segurança que negue a porta 80, o tráfego é negado e nunca avaliado por *NSG1*. Para negar a porta 80 a partir da máquina virtual, um ou ambos os grupos de segurança de rede têm de ter uma regra que negue a porta 80 para a Internet.
- **VM2**: todo o tráfego é enviado da interface de rede para a sub-rede, uma vez que a interface de rede anexada a *VM2* não tem um grupo de segurança de rede associado a si. As regras em *NSG1* são processadas.
- **VM3**: se *NSG2* tiver uma regra de segurança que negue a porta 80, o tráfego é negado. Se *NSG2* tiver uma regra de segurança que permita a porta 80, é permitido tráfego de saída para a Internet na mesma, uma vez que não existe nenhum grupo de segurança de rede associado a *Subnet2*.
- **VM4**: todo o tráfego de rede é permitido a partir de *VM4,* porque não está associado nenhum grupo de segurança de rede à interface de rede anexada à máquina virtual ou a *Subnet3*.


## <a name="intra-subnet-traffic"></a>tráfego Intra-Subnet

É importante notar que as regras de segurança numa NSG associada a uma sub-rede podem afetar a conectividade entre os VM's dentro dela. Por exemplo, se for adicionada uma regra ao *NSG1* que negue todo o tráfego de entrada e saída, *o VM1* e o *VM2* deixarão de poder comunicar entre si. Outra regra teria de ser acrescentada especificamente para o permitir. 

Pode ver as [regras de segurança em vigor](virtual-network-network-interface.md#view-effective-security-rules) numa interface de rede para ver facilmente as regras agregadas que estão aplicadas à mesma. Também pode utilizar a capacidade [Verificação de fluxo de IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Observador de Rede do Azure para saber se a comunicação é permitida de ou para uma interface de rede. Verificação do fluxo IP indica se uma comunicação é permitida ou negada, e qual regra de segurança de rede permite ou nega o tráfego.

> [!NOTE]
> Os grupos de segurança da rede estão associados a sub-redes ou a máquinas virtuais e serviços de nuvem implantados no modelo de implementação clássico, e a sub-redes ou interfaces de rede no modelo de implementação do Gestor de Recursos. Para saber mais sobre os modelos de implementação do Azure, veja [Understand Azure deployment models](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Compreender os modelos de implementação do Azure).

> [!TIP]
> A menos que tenha uma razão específica para o fazer, recomendamos que associe um grupo de segurança de rede a uma sub-rede, ou a uma interface de rede, mas não a ambas. Uma vez que as regras num grupo de segurança de rede associado a uma sub-rede podem entrar em conflito com as regras num grupo associado a uma interface de rede, poderão ocorrer problemas de comunicação inesperados que exijam resolução.

## <a name="next-steps"></a>Passos seguintes

* Para saber que recursos do Azure podem ser implementados numa rede virtual e associar grupos de segurança de rede aos mesmos, veja [Virtual network integration for Azure services](virtual-network-for-azure-services.md) (Integração da rede virtual para serviços do Azure).
* Se nunca tiver criado um grupo de segurança de rede, pode seguir um [tutorial](tutorial-filter-network-traffic.md) rápido para ganhar experiência na criação de um.
* Se estiver familiarizado com os grupos de segurança de rede e tiver de geri-los, veja [Manage a network security group](manage-network-security-group.md) (Gerir um grupo de segurança de rede). 
* Se estiver com problemas de comunicação e precisar de resolver problemas nos grupos de segurança de rede, veja [Diagnose a virtual machine network traffic filter problem](diagnose-network-traffic-filter-problem.md) (Diagnosticar problemas de filtro de tráfego de rede de uma máquina virtual). 
* Saiba como permitir [que os registos de fluxo de grupos](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de segurança de rede analisem o tráfego de rede de e para recursos que tenham um grupo de segurança de rede associado.