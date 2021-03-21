---
title: Não é possível eliminar uma rede virtual no Azure | Microsoft Docs
description: Saiba como resolver o problema em que não pode eliminar uma rede virtual em Azure.
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
ms.openlocfilehash: b974af343907c98ebd7a318bc60a0e553a07a233
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219356"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Resolução de problemas: Falhou em eliminar uma rede virtual em Azure

Pode receber erros quando tentar eliminar uma rede virtual no Microsoft Azure. Este artigo fornece medidas de resolução de problemas para ajudá-lo a resolver este problema.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Orientação na resolução de problemas 

1. [Verifique se um gateway de rede virtual está a funcionar na rede virtual](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Verifique se um gateway de aplicações está a funcionar na rede virtual](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Verifique se ainda existem casos de contentores Azure na rede virtual](#check-whether-azure-container-instances-still-exist-in-the-virtual-network).
4. [Verifique se o Azure Ative Directory Domain Service está ativado na rede virtual](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
5. [Verifique se a rede virtual está ligada a outros recursos](#check-whether-the-virtual-network-is-connected-to-other-resource).
6. [Verifique se uma máquina virtual ainda está em funcionamento na rede virtual](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
7. [Verifique se a rede virtual está presa na migração.](#check-whether-the-virtual-network-is-stuck-in-migration)

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Verifique se um gateway de rede virtual está em execução na rede virtual

Para remover a rede virtual, tem primeiro de remover o gateway de rede virtual.

Para redes virtuais clássicas, **aceda** à página geral da rede virtual clássica no portal Azure. Na secção **de ligações VPN,** se o gateway estiver em execução na rede virtual, verá o endereço IP do gateway. 

![Verifique se gateway está em execução](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

Para redes virtuais, **aceda** à página geral da rede virtual. Verifique **os dispositivos conectados** para o gateway de rede virtual.

![Screenshot da lista de dispositivos Conectados para uma rede virtual no portal Azure. O gateway de rede Virtual está em destaque na lista.](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Antes de poder remover o gateway, retire primeiro quaisquer objetos **de ligação** no gateway. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Verifique se um gateway de aplicações está em execução na rede virtual

Aceda à página **geral** da rede virtual. Verifique os **dispositivos Connected** para ver o gateway de aplicação.

![Screenshot da lista de dispositivos Conectados para uma rede virtual no portal Azure. O Gateway application está em destaque na lista.](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Se houver um gateway de aplicação, deve removê-lo antes de poder eliminar a rede virtual.

### <a name="check-whether-azure-container-instances-still-exist-in-the-virtual-network"></a>Verifique se ainda existem casos de contentores Azure na rede virtual

1. No portal Azure, aceda à página **geral** do grupo de recursos.
1. No cabeçalho da lista dos recursos do grupo de recursos, selecione **Mostrar tipos ocultos**. O tipo de perfil de rede está escondido no portal Azure por padrão.
1. Selecione o perfil de rede relacionado com os grupos de contentores.
1. Selecione **Eliminar**.

   ![Screenshot da lista de perfis de rede escondidos.](media/virtual-network-troubleshoot-cannot-delete-vnet/container-instances.png)

1. Elimine a sub-rede ou a rede virtual novamente.

Se estes passos não resolverem o problema, use estes [comandos Azure CLI](../container-instances/container-instances-vnet.md#clean-up-resources) para limpar recursos. 

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Verifique se o Azure Ative Directory Domain Service está ativado na rede virtual

Se o Serviço de Domínio do Diretório Ativo estiver ativado e ligado à rede virtual, não é possível eliminar esta rede virtual. 

![Screenshot do ecrã Azure AD Domain Services no portal Azure. Destaca-se o campo Disponível em Rede Virtual/Sub-rede.](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Para desativar o serviço, consulte [Serviços de Domínio do Diretório Ativo de Desativação Azure utilizando o portal Azure](../active-directory-domain-services/delete-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Verifique se a rede virtual está ligada a outros recursos

Verifique se existem ligações de circuitos, ligações e observações virtuais da rede. Qualquer um destes pode fazer com que uma eliminação virtual da rede falhe. 

A ordem de eliminação recomendada é a seguinte:

1. Ligações de gateway
2. Gateways
3. IPs
4. Os espreitadores de rede virtuais
5. Ambiente de Serviço de Aplicações (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Verifique se uma máquina virtual ainda está em funcionamento na rede virtual

Certifique-se de que nenhuma máquina virtual está na rede virtual.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Verifique se a rede virtual está presa na migração

Se a rede virtual estiver presa num estado de migração, não pode ser eliminada. Executar o seguinte comando para abortar a migração e, em seguida, apagar a rede virtual.

```azurepowershell
Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort
```

## <a name="next-steps"></a>Passos seguintes

- [Rede Virtual do Azure](virtual-networks-overview.md)
- [Perguntas mais frequentes (FAQ) da Rede Virtual do Azure](virtual-networks-faq.md)