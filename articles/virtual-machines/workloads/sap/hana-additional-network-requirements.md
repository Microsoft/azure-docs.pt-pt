---
title: Requisitos de rede adicionais do SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Requisitos de rede adicionais para o SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2f4c74076f2763addc1f2b971d8ea0ca29ce6fd0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60203980"
---
# <a name="additional-network-requirements-for-large-instances"></a>Requisitos de rede adicionais para as instâncias grandes

Pode ter requisitos de rede adicionais como parte de uma implementação de instâncias grandes do SAP HANA no Azure.

## <a name="add-more-ip-addresses-or-subnets"></a>Adicione mais sub-redes ou endereços IP

Utilize o portal do Azure, PowerShell ou a CLI do Azure ao adicionar mais sub-redes ou endereços IP.

Adicione o novo intervalo de endereços IP como um novo intervalo ao espaço de endereços de rede virtual, em vez de gerar um novo intervalo de agregados. Envie esta alteração para a Microsoft. Isto permite-lhe ligar a partir desse novo intervalo de endereços IP para as unidades de instância grande do HANA no cliente. É possível abrir um pedido de suporte do Azure para obter o novo espaço de endereços de rede virtual adicionado. Depois de receber a confirmação, execute os passos seguintes.

Para criar uma sub-rede adicional no portal do Azure, veja [criar uma rede virtual com o portal do Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). Para criar uma do PowerShell, veja [criar uma rede virtual com o PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Adicionar redes virtuais

Depois de ligar inicialmente um ou mais redes virtuais do Azure, pode querer ligar outros adicionais que aceder ao SAP HANA no Azure (instâncias grandes). Primeiro, submeta um pedido de suporte do Azure. Na solicitação, inclua informações específicas, identificar a implementação do Azure específica. Também pode inclua o intervalo de espaço de endereços IP ou intervalos de espaço de endereços de rede virtual do Azure. SAP HANA no gerenciamento de serviços da Microsoft, em seguida, fornece as informações necessárias, tem de se ligar a redes virtuais adicionais e o Azure ExpressRoute. Para cada rede virtual, precisa de uma chave de autorização exclusivo para ligar ao circuito do ExpressRoute para instâncias grandes do HANA.

## <a name="increase-expressroute-circuit-bandwidth"></a>Aumentar a largura de banda de circuito de ExpressRoute

Consulte o SAP HANA no gerenciamento de serviços da Microsoft. Se eles aconselha a aumentar a largura de banda do SAP HANA no circuito do ExpressRoute do Azure (instâncias grandes), criam um pedido de suporte do Azure. (Pode pedir um aumento de uma largura de banda do circuito único até um máximo de 10 Gbps). Em seguida, receber notificação depois da operação foi concluída; não precisa de fazer mais nada para ativar esta velocidade superior no Azure.

## <a name="add-an-additional-expressroute-circuit"></a>Adicionar um circuito do ExpressRoute adicional

Consulte o SAP HANA no gerenciamento de serviços da Microsoft. Se, deve adicionar um circuito do ExpressRoute adicional, criam um pedido de suporte do Azure (incluindo um pedido para obter informações de autorização para ligar para o novo circuito). Antes de efetuar o pedido, tem de definir o espaço de endereços utilizado nas redes virtuais. SAP HANA no gerenciamento de serviços da Microsoft, em seguida, pode fornecer autorização.

Quando é criado o novo circuito e o SAP HANA na configuração de gestão do serviço Microsoft estiver concluído, receberá uma notificação com as informações que precisa continuar. Não é possível ligar redes virtuais do Azure para este circuito adicional, se já estiverem ligados ao outro SAP HANA no circuito de ExpressRoute do Azure (instância grande) na mesma região do Azure.

## <a name="delete-a-subnet"></a>Eliminar uma sub-rede

Para remover uma sub-rede de rede virtual, pode utilizar o portal do Azure, PowerShell ou a CLI do Azure. Se a sua rede virtual do Azure intervalo ou endereço de espaço de endereços IP foi um intervalo de agregados, não há nenhum siga para com a Microsoft. (Entretanto, observe que a rede virtual ainda é a propagar o espaço de endereços de rotas BGP que inclui a sub-rede eliminada.) Possa ter definido o espaço de endereço ou intervalo de endereços do rede virtual do Azure como vários intervalos de endereços IP, de que uma foi atribuída à sua sub-rede foi eliminada. Certifique-se de que excluí-lo do seu espaço de endereço de rede virtual. Em seguida, informe o SAP HANA no gerenciamento de serviços da Microsoft para o remover dos intervalos de SAP HANA no Azure (instâncias grandes) tem permissão para comunicar com.

Para obter mais informações, consulte [eliminar uma sub-rede](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Eliminar uma rede virtual

Para obter informações, consulte [eliminar uma rede virtual](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network).

SAP HANA no gerenciamento de serviços da Microsoft remove as autorizações existentes sobre o SAP HANA no circuito do ExpressRoute do Azure (instâncias grandes). Esta operação também remove a rede virtual do Azure intervalo ou endereço de espaço de endereços IP para a comunicação com instâncias grandes do HANA.

Depois de remover a rede virtual, abra um pedido de suporte do Azure para fornecer o intervalo de espaço de endereços IP ou intervalos de ser removidos.

Para garantir que remova tudo, elimine a ligação do ExpressRoute, o gateway de rede virtual, o IP público do gateway de rede virtual e a rede virtual.

## <a name="delete-an-expressroute-circuit"></a>Eliminar um circuito do ExpressRoute

Para remover um SAP HANA adicionais no circuito do ExpressRoute do Azure (instâncias grandes), abra um pedido de suporte do Azure com o SAP HANA no gerenciamento de serviços da Microsoft. Solicite que o circuito ser eliminado. A subscrição do Azure, pode eliminar ou manter a rede virtual, conforme necessário. No entanto, tem de eliminar a ligação entre o circuito de ExpressRoute de instâncias grandes do HANA e o gateway de rede virtual ligado.

## <a name="next-steps"></a>Passos Seguintes

- [Como instalar e configurar o SAP HANA (instâncias grandes) no Azure](hana-installation.md)
