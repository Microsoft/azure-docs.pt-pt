---
title: Não é possível eliminar uma rede virtual no Azure | Documentos da Microsoft
description: Saiba como resolver o problema em que não é possível eliminar uma rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 5d2e10a4c5cd5b5dc1a8fe19cef7bc47f68d3fbe
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235016"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Resolução de problemas: Falha ao eliminar uma rede virtual no Azure

Poderá receber erros ao tentar eliminar uma rede virtual no Microsoft Azure. Este artigo fornece passos de resolução de problemas para o ajudar a resolver este problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Orientação na resolução de problemas 

1. [Verificar se um gateway de rede virtual está em execução na rede virtual](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Verificar se um gateway de aplicação está em execução na rede virtual](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Verifique se o serviço de domínio do Active Directory do Azure está ativado na rede virtual](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Verifique se a rede virtual está ligada a outros recursos](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Verifique se uma máquina virtual ainda está em execução na rede virtual](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Verifique se a rede virtual fica preso em migração](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Verificar se um gateway de rede virtual está em execução na rede virtual

Para remover a rede virtual, tem de remover primeiro o gateway de rede virtual.

Para redes virtuais clássicas, vá para o **descrição geral** página da rede virtual clássica no portal do Azure. Na **ligações VPN** secção, se o gateway estiver em execução na rede virtual, verá o endereço IP do gateway. 

![Verifique se o gateway está em execução](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

Para as redes virtuais, vá para o **descrição geral** página da rede virtual. Verifique **dispositivos ligados** para o gateway de rede virtual.

![Verifique o dispositivo ligado](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Antes de poder remover o gateway, primeiro remova qualquer **ligação** objetos no gateway. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Verificar se um gateway de aplicação está em execução na rede virtual

Vá para o **descrição geral** página da rede virtual. Verifique os **dispositivos ligados** para o gateway de aplicação.

![Verifique o dispositivo ligado](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Se houver um gateway de aplicação, tem de o remover antes de poder eliminar a rede virtual.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Verifique se o serviço de domínio do Active Directory do Azure está ativado na rede virtual

Se o serviço de domínio do Active Directory é ativado e ligado à rede virtual, não é possível eliminar esta rede virtual. 

![Verifique o dispositivo ligado](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Para desativar o serviço, consulte [desativar o Azure Active Directory Domain Services no portal do Azure](../active-directory-domain-services/delete-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Verifique se a rede virtual está ligada a outros recursos

Verificar a existência de ligações de circuito, ligações e peerings de rede virtual. Qualquer uma dessas pode causar uma exclusão de rede virtual efetuar a ativação. 

A ordem de eliminação recomendada é o seguinte:

1. ligações de gateway
2. Gateways
3. IPs
4. Peerings de rede virtual
5. Ambiente de serviço de aplicações (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Verifique se uma máquina virtual ainda está em execução na rede virtual

Certifique-se de que nenhuma máquina virtual está na rede virtual.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Verifique se a rede virtual está bloqueada na migração

Se a rede virtual fica preso num Estado de migração, não pode ser eliminada. Execute o seguinte comando para abortar a migração e, em seguida, elimine a rede virtual.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Passos Seguintes

- [Rede Virtual do Azure](virtual-networks-overview.md)
- [Perguntas mais frequentes (FAQ) da Rede Virtual do Azure](virtual-networks-faq.md)
