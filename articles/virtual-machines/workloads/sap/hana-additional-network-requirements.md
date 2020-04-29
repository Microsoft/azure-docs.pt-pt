---
title: Requisitos adicionais de rede para o SAP HANA no Azure (grandes instâncias) / Microsoft Docs
description: Requisitos adicionais de rede para O SAP HANA no Azure (grandes instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 66bbd8b462ac35756be0fae6eba940ba0aba6c4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77614568"
---
# <a name="additional-network-requirements-for-large-instances"></a>Requisitos adicionais de rede para grandes instâncias

Pode ter requisitos adicionais de rede como parte de uma implementação de grandes instâncias de SAP HANA em Azure.

## <a name="add-more-ip-addresses-or-subnets"></a>Adicione mais endereços IP ou subredes

Utilize o portal Azure, powerShell ou o Azure CLI quando adicionar mais endereços IP ou subredes.

Adicione a nova gama de endereços IP como uma nova gama para o espaço de endereços de rede virtual, em vez de gerar uma nova gama agregada. Envie esta alteração para a Microsoft. Isto permite-lhe ligar a partir dessa nova gama de endereços IP às grandes unidades de instância HANA no seu cliente. Pode abrir um pedido de suporte do Azure para adicionar o novo espaço de endereço de rede virtual. Depois de receber a confirmação, execute os próximos passos.

Para criar uma subrede adicional do portal Azure, consulte [Criar uma rede virtual utilizando o portal Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). Para criar um a partir do PowerShell, consulte [Criar uma rede virtual utilizando powerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Adicionar redes virtuais

Depois de ligar inicialmente uma ou mais redes virtuais Azure, é melhor ligar outras que acedem ao SAP HANA no Azure (grandes instâncias). Primeiro, submeta um pedido de apoio azure. Neste pedido, incluem-se as informações específicas que identificam a implantação específica do Azure. Incluem também a gama de espaços de endereçoIP ou gamas do espaço de endereços da rede virtual Azure. O SAP HANA na Microsoft Service Management fornece então as informações necessárias para ligar as redes virtuais adicionais e o Azure ExpressRoute. Para cada rede virtual, você precisa de uma chave de autorização única para ligar ao circuito ExpressRoute a grandes instâncias HANA.

## <a name="increase-expressroute-circuit-bandwidth"></a>Aumentar largura de banda do circuito ExpressRoute

Consulte a SAP HANA sobre a Microsoft Service Management. Se o aconselharem a aumentar a largura de banda do circuito SAP HANA no Circuito Azure (grandes instâncias), crie um pedido de apoio Azure. (Pode solicitar um aumento para uma largura de banda de circuito único até um máximo de 10 Gbps.) Em seguida, recebe notificação após a operação estar concluída; não é preciso fazer mais nada para permitir esta velocidade mais alta em Azure.

## <a name="add-an-additional-expressroute-circuit"></a>Adicione um circuito adicional expressRoute

Consulte a SAP HANA sobre a Microsoft Service Management. Se o aconselharem a adicionar um circuito expressRoute adicional, crie um pedido de suporte Azure (incluindo um pedido para obter informações de autorização para ligar ao novo circuito). Antes de fazer o pedido, deve definir o espaço de endereço utilizado nas redes virtuais. O SAP HANA na Microsoft Service Management pode então fornecer autorização.

Quando o novo circuito é criado e o SAP HANA na configuração de Gestão de Serviços da Microsoft está completo, recebe uma notificação com as informações que precisa de proceder. Não é possível ligar redes virtuais Azure a este circuito adicional se já estiverem ligadas a outro circuito SAP HANA no circuito Azure (grande instância) ExpressRoute na mesma região de Azure.

## <a name="delete-a-subnet"></a>Eliminar uma sub-rede

Para remover uma subrede de rede virtual, pode utilizar o portal Azure, PowerShell ou o Azure CLI. Se o seu alcance de endereço IP ou endereço de rede virtual Azure fosse um intervalo agregado, não existe seguimento para si com a Microsoft. (Note-se, no entanto, que a rede virtual continua a propagar o espaço de endereço de rota BGP que inclui a sub-rede eliminada.) Pode ter definido a gama de endereços ou endereços da rede virtual Azure como múltiplas gamas de endereços IP, das quais uma foi atribuída à sua sub-rede eliminada. Certifique-se de que apaga a partir do seu espaço de endereço de rede virtual. Em seguida, informe o SAP HANA na Microsoft Service Management para removê-lo das gamas com as quais o SAP HANA no Azure (grandes instâncias) é autorizado a comunicar.

Para mais informações, consulte [Eliminar uma sub-rede](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Eliminar uma rede virtual

Para obter informações, consulte [Eliminar uma rede virtual](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network).

SAP HANA na Microsoft Service Management remove as autorizações existentes no circuito SAP HANA no circuito Azure (grandes instâncias) ExpressRoute. Também remove a gama de endereços IP da rede virtual Azure ou espaço de endereço para a comunicação com grandes instâncias HANA.

Depois de remover a rede virtual, abra um pedido de suporte Azure para fornecer a gama ou gamas de endereçoip para remover.

Para garantir que remove tudo, elimine a ligação ExpressRoute, o portal de rede virtual, o IP público de gateway da rede virtual e a rede virtual.

## <a name="delete-an-expressroute-circuit"></a>Eliminar um circuito ExpressRoute

Para remover um circuito Adicional SAP HANA no circuito ExpressRoute (grandes instâncias), abra um pedido de suporte Azure com o SAP HANA na Microsoft Service Management. Peça que o circuito seja apagado. Dentro da subscrição do Azure, pode eliminar ou manter a rede virtual, se necessário. No entanto, deve eliminar a ligação entre o circuito DE grandes instâncias HANA ExpressRoute e o gateway de rede virtual ligado.

## <a name="next-steps"></a>Passos seguintes

- [Como instalar e configurar o SAP HANA (instâncias grandes) no Azure](hana-installation.md)
