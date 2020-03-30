---
title: Não é possível apagar uma rede virtual no Azure . Microsoft Docs
description: Aprenda a resolver o problema em que não pode eliminar uma rede virtual em Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056933"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Resolução de problemas: Falhou em apagar uma rede virtual no Azure

Pode receber erros quando tentar eliminar uma rede virtual no Microsoft Azure. Este artigo fornece medidas de resolução de problemas para ajudá-lo a resolver este problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Orientação na resolução de problemas 

1. [Verifique se está a funcionar um portal](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network)de rede virtual na rede virtual .
2. [Verifique se está a funcionar um gateway de aplicação na rede virtual](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. Verifique se o Serviço de Domínio de [Diretório Ativo Azure está ativado na rede virtual](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Verifique se a rede virtual está ligada a outros recursos.](#check-whether-the-virtual-network-is-connected-to-other-resource)
5. [Verifique se uma máquina virtual ainda está em funcionamento na rede virtual](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Verifique se a rede virtual está presa na migração.](#check-whether-the-virtual-network-is-stuck-in-migration)

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Verifique se um portal de rede virtual está a funcionar na rede virtual

Para remover a rede virtual, primeiro deve remover o gateway da rede virtual.

Para redes virtuais clássicas, vá à página **de visão geral** da rede virtual clássica no portal Azure. Na secção de **ligações VPN,** se o portal estiver em funcionamento na rede virtual, verá o endereço IP do gateway. 

![Verifique se o gateway está em execução](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

Para redes virtuais, vá à página **de visão geral** da rede virtual. Verifique os **dispositivos Ligados** para obter o portal de rede virtual.

![Verifique o dispositivo conectado](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Antes de conseguir remover o portal, retire primeiro quaisquer objetos de **ligação** no portal. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Verifique se está a funcionar um portal de aplicações na rede virtual

Vá à página **de visão geral** da rede virtual. Verifique se os **dispositivos Ligados** estão no gateway da aplicação.

![Verifique o dispositivo conectado](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Se houver um gateway de aplicação, deve removê-lo antes de poder eliminar a rede virtual.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Verifique se o Serviço de Domínio de Diretório Ativo Azure está ativado na rede virtual

Se o Serviço de Domínio de Diretório Ativo estiver ativado e ligado à rede virtual, não pode eliminar esta rede virtual. 

![Verifique o dispositivo conectado](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Para desativar o serviço, consulte Desativar os Serviços de Domínio ativo do [Diretório Azure utilizando o portal Azure](../active-directory-domain-services/delete-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Verifique se a rede virtual está ligada a outros recursos

Verifique se existem ligações de circuito, ligações e epeerings de rede virtuais. Qualquer um destes pode causar um fracasso na eliminação da rede virtual. 

A ordem de eliminação recomendada é a seguinte:

1. Ligações de gateway
2. Gateways
3. IPs
4. Pares de rede virtual
5. Ambiente de Serviço de Aplicativos (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Verifique se uma máquina virtual ainda está em funcionamento na rede virtual

Certifique-se de que nenhuma máquina virtual está na rede virtual.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Verifique se a rede virtual está presa na migração

Se a rede virtual estiver presa num estado de migração, não pode ser eliminada. Executar o seguinte comando para abortar a migração e, em seguida, apagar a rede virtual.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Passos seguintes

- [Rede Virtual Azure](virtual-networks-overview.md)
- [Perguntas mais frequentes (FAQ) da Rede Virtual do Azure](virtual-networks-faq.md)
