---
title: 'Gateway Azure VPN: Apagar um portal: portal'
description: Elimine um portal de rede virtual utilizando o portal Azure no modelo de implementação do Gestor de Recursos.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/23/2018
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: fba213be2de3b747d3ba962674a03c0bc7df3ed5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863670"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Eliminar um portal de rede virtual utilizando o portal

> [!div class="op_single_selector"]
> * [Portal Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (clássico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Este artigo fornece as instruções para a apagar um gateways Azure VPN implantados utilizando o modelo de implementação do Gestor de Recursos. Existem algumas abordagens diferentes que pode tomar quando pretende eliminar uma porta de entrada de rede virtual para uma configuração de gateway VPN.

- Se quiser apagar tudo e recomeçar, como no caso de um ambiente de teste, pode eliminar o grupo de recursos. Ao eliminar um grupo de recursos, elimina todos os recursos dentro do grupo. Este método só é recomendado se não quiser manter nenhum dos recursos no grupo de recursos. Não é possível eliminar seletivamente apenas alguns recursos usando esta abordagem.

- Se quiser manter alguns dos recursos no seu grupo de recursos, apagar um portal de rede virtual torna-se um pouco mais complicado. Antes de poder eliminar o portal de rede virtual, tem primeiro de eliminar quaisquer recursos que dependam do portal. Os passos que segue dependem do tipo de ligações que criou e dos recursos dependentes para cada ligação.

> [!IMPORTANT]
> As instruções abaixo descrevem como eliminar os gateways VpN Azure implantados utilizando o modelo de implementação do Gestor de Recursos. Para eliminar um gateway VPN implantado utilizando o modelo de implementação clássico, utilize o Azure PowerShell como descrito [aqui](vpn-gateway-delete-vnet-gateway-classic-powershell.md).


## <a name="delete-a-vpn-gateway"></a>Eliminar um gateway de VPN

Para eliminar um portal de rede virtual, tem primeiro de eliminar cada recurso que diz respeito ao portal da rede virtual. Os recursos devem ser suprimidos numa determinada ordem devido a dependências.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Neste ponto, o portal de rede virtual é eliminado. Os próximos passos ajudam-no a eliminar quaisquer recursos que já não estejam a ser utilizados.

### <a name="to-delete-the-local-network-gateway"></a>Para eliminar o portal da rede local

1. Em **Todos os recursos,** localize os portões de rede locais que estavam associados a cada ligação.
2. Na lâmina **de visão geral** para o gateway da rede local, clique em **Eliminar**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Para eliminar o recurso de endereço IP público para o gateway

1. Em **Todos os recursos,** localize o recurso de endereço IP público que estava associado ao portal. Se o portal de rede virtual estiver ativo, verá dois endereços IP públicos. 
2. Na página **'Visão Geral'** do endereço IP público, clique em **Eliminar**e, em seguida, **sim** para confirmar.

### <a name="to-delete-the-gateway-subnet"></a>Para eliminar a sub-rede de gateway

1. Em **Todos os recursos,** localize a rede virtual. 
2. Na lâmina **sub-redes,** clique na **GatewaySubnet**e, em seguida, clique em **Eliminar**. 
3. Clique **sim** para confirmar que pretende eliminar a sub-rede de gateway.

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="deleterg"></a>Eliminar um gateway VPN eliminando o grupo de recursos

Se não está preocupado em manter nenhum dos seus recursos no grupo de recursos e apenas quer recomeçar, pode eliminar todo um grupo de recursos. Esta é uma maneira rápida de remover tudo. Os seguintes passos aplicam-se apenas ao modelo de implementação do Gestor de Recursos.

1. Em **Todos os recursos,** localize o grupo de recursos e clique para abrir a lâmina.
2. Clique em **Apagar**. Na lâmina Delete, veja os recursos afetados. Certifique-se de que pretende eliminar todos estes recursos. Caso contrário, utilize os passos em Eliminar um gateway VPN na parte superior deste artigo.
3. Para prosseguir, digite o nome do grupo de recursos que pretende eliminar e, em seguida, clique em **Apagar**.
