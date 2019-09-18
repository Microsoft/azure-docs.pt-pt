---
title: Não é possível excluir uma rede virtual no Azure | Microsoft Docs
description: Saiba como solucionar o problema no qual não é possível excluir uma rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 2d427a8b40fcb537801ce76aae6bc32fcda3a307
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056933"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Solução Falha ao excluir uma rede virtual no Azure

Você pode receber erros ao tentar excluir uma rede virtual em Microsoft Azure. Este artigo fornece etapas de solução de problemas para ajudá-lo a resolver esse problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Orientação na resolução de problemas 

1. [Verifique se um gateway de rede virtual está em execução na rede virtual](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Verifique se um gateway de aplicativo está em execução na rede virtual](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Verifique se Azure Active Directory serviço de domínio está habilitado na rede virtual](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Verifique se a rede virtual está conectada a outro recurso](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Verifique se uma máquina virtual ainda está em execução na rede virtual](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Verifique se a rede virtual está presa na migração](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Verificar se um gateway de rede virtual está em execução na rede virtual

Para remover a rede virtual, primeiro você deve remover o gateway de rede virtual.

Para redes virtuais clássicas, vá para a página **visão geral** da rede virtual clássica no portal do Azure. Na seção **conexões VPN** , se o gateway estiver em execução na rede virtual, você verá o endereço IP do gateway. 

![Verificar se o gateway está em execução](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

Para redes virtuais, vá para a página **visão geral** da rede virtual. Verifique os **dispositivos conectados** para o gateway de rede virtual.

![Verificar o dispositivo conectado](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Antes de remover o gateway, primeiro remova todos os objetos de **conexão** no gateway. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Verificar se um gateway de aplicativo está em execução na rede virtual

Vá para a página **visão geral** da rede virtual. Verifique os **dispositivos conectados** para o gateway de aplicativo.

![Verificar o dispositivo conectado](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Se houver um gateway de aplicativo, você deverá removê-lo antes de poder excluir a rede virtual.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Verifique se Azure Active Directory serviço de domínio está habilitado na rede virtual

Se o serviço de Domínio do Active Directory estiver habilitado e conectado à rede virtual, você não poderá excluir essa rede virtual. 

![Verificar o dispositivo conectado](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Para desabilitar o serviço, consulte [desabilitar Azure Active Directory Domain Services usando o portal do Azure](../active-directory-domain-services/delete-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Verifique se a rede virtual está conectada a outro recurso

Verifique se há links de circuito, conexões e emparelhamentos de rede virtual. Qualquer um deles pode causar falha na exclusão da rede virtual. 

A ordem de exclusão recomendada é a seguinte:

1. Conexões de gateway
2. Gateways
3. IPs
4. Emparelhamentos de rede virtual
5. Ambiente do Serviço de Aplicativo (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Verificar se uma máquina virtual ainda está em execução na rede virtual

Certifique-se de que nenhuma máquina virtual esteja na rede virtual.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Verificar se a rede virtual está presa na migração

Se a rede virtual estiver presa em um estado de migração, ela não poderá ser excluída. Execute o comando a seguir para anular a migração e, em seguida, exclua a rede virtual.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Passos Seguintes

- [Rede Virtual do Azure](virtual-networks-overview.md)
- [Perguntas mais frequentes (FAQ) da Rede Virtual do Azure](virtual-networks-faq.md)
