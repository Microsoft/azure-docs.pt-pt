---
title: Requisitos de rede adicionais para SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Requisitos de rede adicionais para SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6fc4e797bd74c28fc741bf2a3928b46f0984b1b9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099930"
---
# <a name="additional-network-requirements-for-large-instances"></a>Requisitos de rede adicionais para instâncias grandes

Você pode ter requisitos de rede adicionais como parte de uma implantação de grandes instâncias do SAP HANA no Azure.

## <a name="add-more-ip-addresses-or-subnets"></a>Adicionar mais endereços IP ou sub-redes

Use o portal do Azure, o PowerShell ou o CLI do Azure ao adicionar mais endereços IP ou sub-redes.

Adicione o novo intervalo de endereços IP como um novo intervalo ao espaço de endereço de rede virtual, em vez de gerar um novo intervalo agregado. Envie essa alteração para a Microsoft. Isso permite que você se conecte a partir desse novo intervalo de endereços IP para as unidades do SAP HANA em instâncias grandes em seu cliente. Você pode abrir uma solicitação de suporte do Azure para obter o novo espaço de endereço de rede virtual adicionado. Depois de receber a confirmação, execute as próximas etapas.

Para criar uma sub-rede adicional do portal do Azure, consulte [criar uma rede virtual usando o portal do Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). Para criar um do PowerShell, consulte [criar uma rede virtual usando o PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Adicionar redes virtuais

Depois de conectar inicialmente uma ou mais redes virtuais do Azure, talvez você queira conectar outras que acessam SAP HANA no Azure (instâncias grandes). Primeiro, envie uma solicitação de suporte do Azure. Nessa solicitação, inclua as informações específicas que identificam a implantação específica do Azure. Inclua também o intervalo de espaço de endereço IP ou os intervalos do espaço de endereço de rede virtual do Azure. O SAP HANA no gerenciamento de serviços da Microsoft fornece as informações necessárias para conectar as redes virtuais adicionais e o Azure ExpressRoute. Para cada rede virtual, você precisa de uma chave de autorização exclusiva para se conectar ao circuito do ExpressRoute para instâncias grandes do HANA.

## <a name="increase-expressroute-circuit-bandwidth"></a>Aumentar a largura de banda do circuito do ExpressRoute

Consulte SAP HANA no gerenciamento de serviços da Microsoft. Se eles o aconselham a aumentar a largura de banda do SAP HANA no circuito do ExpressRoute do Azure (instâncias grandes), crie uma solicitação de suporte do Azure. (Você pode solicitar um aumento para uma largura de banda de circuito único até um máximo de 10 Gbps.) Em seguida, você receberá uma notificação depois que a operação for concluída; Você não precisa fazer mais nada para habilitar essa velocidade mais alta no Azure.

## <a name="add-an-additional-expressroute-circuit"></a>Adicionar um circuito de ExpressRoute adicional

Consulte SAP HANA no gerenciamento de serviços da Microsoft. Se eles recomendarem que você adicione um circuito de ExpressRoute adicional, crie uma solicitação de suporte do Azure (incluindo uma solicitação para obter informações de autorização para se conectar ao novo circuito). Antes de fazer a solicitação, você deve definir o espaço de endereço usado nas redes virtuais. SAP HANA no gerenciamento de serviços da Microsoft pode fornecer autorização.

Quando o novo circuito é criado e o SAP HANA na configuração de gerenciamento de serviços da Microsoft for concluído, você receberá uma notificação com as informações necessárias para continuar. Você não poderá conectar as redes virtuais do Azure a esse circuito adicional se elas já estiverem conectadas a outro SAP HANA no circuito de ExpressRoute do Azure (instância grande) na mesma região do Azure.

## <a name="delete-a-subnet"></a>Excluir uma sub-rede

Para remover uma sub-rede de rede virtual, você pode usar o portal do Azure, o PowerShell ou o CLI do Azure. Se o intervalo de endereços IP ou o espaço de endereço de sua rede virtual do Azure era um intervalo agregado, não há nenhum acompanhamento para você com a Microsoft. (No entanto, observe que a rede virtual ainda está propagando o espaço de endereço de rota BGP que inclui a sub-rede excluída.) Você pode ter definido o intervalo de endereços da rede virtual do Azure ou o espaço de endereço como vários intervalos de endereços IP, dos quais um foi atribuído à sua sub-rede excluída. Certifique-se de excluí-lo de seu espaço de endereço de rede virtual. Em seguida, informe SAP HANA sobre o gerenciamento de serviços da Microsoft para removê-lo dos intervalos que SAP HANA no Azure (instâncias grandes) tem permissão para se comunicar com o.

Para obter mais informações, consulte [excluir uma sub-rede](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Excluir uma rede virtual

Para obter informações, consulte [excluir uma rede virtual](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network).

SAP HANA no gerenciamento de serviços da Microsoft remove as autorizações existentes na SAP HANA no circuito do ExpressRoute do Azure (instâncias grandes). Ele também remove o intervalo de endereços IP da rede virtual do Azure ou o espaço de endereço para a comunicação com instâncias grandes do HANA.

Depois de remover a rede virtual, abra uma solicitação de suporte do Azure para fornecer o intervalo de espaço de endereço IP ou os intervalos a serem removidos.

Para garantir que você remova tudo, exclua a conexão do ExpressRoute, o gateway de rede virtual, o IP público do gateway de rede virtual e a rede virtual.

## <a name="delete-an-expressroute-circuit"></a>Excluir um circuito do ExpressRoute

Para remover um SAP HANA adicional no circuito do ExpressRoute do Azure (instâncias grandes), abra uma solicitação de suporte do Azure com SAP HANA no gerenciamento de serviços da Microsoft. Solicite que o circuito seja excluído. Na assinatura do Azure, você pode excluir ou manter a rede virtual, conforme necessário. No entanto, você deve excluir a conexão entre o circuito ExpressRoute de instâncias grandes HANA e o gateway de rede virtual vinculado.

## <a name="next-steps"></a>Passos seguintes

- [Como instalar e configurar o SAP HANA (instâncias grandes) no Azure](hana-installation.md)
