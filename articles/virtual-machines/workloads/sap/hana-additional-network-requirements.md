---
title: Requisitos adicionais de rede para SAP HANA em Azure (grandes instâncias) Microsoft Docs
description: Requisitos adicionais de rede para SAP HANA em Azure (grandes instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 308d30118349e020d3b407243f106d9ad8368118
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98198990"
---
# <a name="additional-network-requirements-for-large-instances"></a>Requisitos adicionais de rede para grandes instâncias

Você pode ter requisitos adicionais de rede como parte de uma implantação de grandes casos de SAP HANA em Azure.

## <a name="add-more-ip-addresses-or-subnets"></a>Adicionar mais endereços IP ou sub-redes

Utilize o portal Azure, PowerShell ou o Azure CLI quando adicionar mais endereços IP ou sub-redes.

Adicione a nova gama de endereços IP como uma nova gama para o espaço de endereço de rede virtual, em vez de gerar uma nova gama agregada. Submeta esta alteração à Microsoft. Isto permite-lhe ligar a partir dessa nova gama de endereços IP às unidades de grande instância HANA do seu cliente. Pode abrir um pedido de suporte Azure para obter o novo espaço de endereço de rede virtual adicionado. Depois de receber a confirmação, execute os próximos passos.

Para criar uma sub-rede adicional a partir do portal Azure, consulte [criar uma rede virtual utilizando o portal Azure](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network). Para criar um a partir de PowerShell, consulte [Criar uma rede virtual utilizando o PowerShell](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Adicionar redes virtuais

Depois de inicialmente ligar uma ou mais redes virtuais Azure, é melhor ligar as que acedem a SAP HANA em Azure (grandes instâncias). Primeiro, apresente um pedido de apoio ao Azure. Nesse pedido, incluem-se as informações específicas que identificam a implantação específica do Azure. Inclua também a gama de espaço de endereço IP ou intervalos do espaço de endereço virtual Azure. O SAP HANA na Microsoft Service Management fornece então as informações necessárias para ligar as redes virtuais adicionais e o Azure ExpressRoute. Para cada rede virtual, você precisa de uma chave de autorização única para ligar ao circuito ExpressRoute a grandes instâncias HANA.

## <a name="increase-expressroute-circuit-bandwidth"></a>Aumentar a largura de banda do circuito ExpressRoute

Consulte a SAP HANA sobre a Microsoft Service Management. Se o aconselharem a aumentar a largura de banda do circuito SAP HANA em Azure (grandes instâncias) ExpressRoute, crie um pedido de suporte Azure. (Pode solicitar um aumento para uma largura de banda de circuito único até um máximo de 10 Gbps.) Em seguida, recebe a notificação após a conclusão da operação; não precisa de fazer mais nada para permitir esta velocidade mais alta em Azure.

## <a name="add-an-additional-expressroute-circuit"></a>Adicione um circuito ExpressRoute adicional

Consulte a SAP HANA sobre a Microsoft Service Management. Se o aconselharem a adicionar um circuito ExpressRoute adicional, crie um pedido de suporte Azure (incluindo um pedido para obter informações de autorização para ligar ao novo circuito). Antes de escame o pedido, deve definir o espaço de endereço utilizado nas redes virtuais. O SAP HANA na Microsoft Service Management pode então fornecer autorização.

Quando o novo circuito é criado e o SAP HANA na configuração de Gestão de Serviços da Microsoft está completo, recebe uma notificação com as informações necessárias para prosseguir. Não é possível ligar redes virtuais Azure a este circuito adicional se já estiverem ligadas a outro circuito SAP HANA em Azure (grande exemplo) Circuito ExpressRoute na mesma região de Azure.

## <a name="delete-a-subnet"></a>Eliminar uma sub-rede

Para remover uma sub-rede de rede virtual, pode utilizar o portal Azure, o PowerShell ou o Azure CLI. Se o seu intervalo de endereços IP de rede virtual Azure ou espaço de endereços foi um alcance agregado, não existe seguimento para si com a Microsoft. (Note-se, no entanto, que a rede virtual ainda está a propagar o espaço de endereço de rota BGP que inclui a sub-rede eliminada.) Pode ter definido o intervalo de endereços de rede virtual Azure ou o espaço de endereço como múltiplos intervalos de endereços IP, dos quais um foi atribuído à sua sub-rede eliminada. Certifique-se de que apaga isso do seu espaço de endereço de rede virtual. Em seguida, informe o SAP HANA sobre a Microsoft Service Management para removê-lo das gamas com as quais o SAP HANA em Azure (grandes instâncias) é autorizado a comunicar.

Para obter mais informações, consulte [Eliminar uma sub-rede.](../../../virtual-network/virtual-network-manage-subnet.md#delete-a-subnet)

## <a name="delete-a-virtual-network"></a>Eliminar uma rede virtual

Para obter informações, consulte [Eliminar uma rede virtual.](../../../virtual-network/manage-virtual-network.md#delete-a-virtual-network)

O SAP HANA na Microsoft Service Management remove as autorizações existentes no circuito SAP HANA on Azure (grandes instâncias). Também remove a gama de endereços IP de rede virtual Azure ou espaço de endereço para a comunicação com grandes instâncias HANA.

Depois de remover a rede virtual, abra um pedido de suporte Azure para fornecer a gama de espaço de endereço IP ou intervalos a remover.

Para garantir que remove tudo, elimine a ligação ExpressRoute, o gateway de rede virtual, o gateway público de gateway de rede virtual e a rede virtual.

## <a name="delete-an-expressroute-circuit"></a>Apagar um circuito ExpressRoute

Para remover um HANA SAP adicional em Azure (grandes instâncias) Circuito ExpressRoute, abra um pedido de suporte Azure com SAP HANA na Microsoft Service Management. Solicito que o circuito seja apagado. Dentro da subscrição Azure, pode eliminar ou manter a rede virtual, se necessário. No entanto, deve eliminar a ligação entre o circuito ExpressRoute de grandes instâncias HANA e o gateway de rede virtual ligado.

## <a name="next-steps"></a>Passos seguintes

- [Como instalar e configurar o SAP HANA (instâncias grandes) no Azure](hana-installation.md)
