---
title: 'Gateway Azure VPN: Excluir um portal: portal'
description: Elimine um gateway de rede virtual utilizando o portal Azure no modelo de implementação do Gestor de Recursos.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 09/02/2020
ms.author: cherylmc
ms.topic: how-to
ms.openlocfilehash: 7a7eb87f57676b469203e45e48b6f863cdf894c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400658"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Excluir um gateway de rede virtual usando o portal

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (clássico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Este artigo fornece as instruções para eliminar um gateways Azure VPN implantados usando o modelo de implementação do Gestor de Recursos. Existem algumas abordagens diferentes que pode tomar quando pretende eliminar um gateway de rede virtual para uma configuração de gateway VPN.

- Se pretender apagar tudo e começar de novo, como no caso de um ambiente de teste, pode eliminar o grupo de recursos. Quando elimina um grupo de recursos, elimina todos os recursos dentro do grupo. Este método só é recomendado se não quiser manter nenhum dos recursos no grupo de recursos. Não é possível eliminar seletivamente apenas alguns recursos usando esta abordagem.

- Se quiser manter alguns dos recursos no seu grupo de recursos, eliminar um gateway de rede virtual torna-se um pouco mais complicado. Antes de poder apagar o gateway de rede virtual, tem primeiro de eliminar quaisquer recursos que dependam do gateway. Os passos que segue dependem do tipo de ligações que criou e dos recursos dependentes para cada ligação.

> [!IMPORTANT]
> As instruções abaixo descrevem como eliminar os gateways Azure VPN implantados utilizando o modelo de implementação do Gestor de Recursos. Para eliminar uma porta VPN implantada utilizando o modelo de implementação clássico, utilize o Azure PowerShell como descrito [aqui](vpn-gateway-delete-vnet-gateway-classic-powershell.md).


## <a name="delete-a-vpn-gateway"></a>Eliminar um gateway de VPN

Para eliminar um gateway de rede virtual, tem primeiro de eliminar cada recurso que se refere ao portal de rede virtual. Os recursos devem ser suprimidos numa determinada ordem devido às dependências.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Neste ponto, o gateway de rede virtual é eliminado. Os próximos passos ajudam-no a eliminar quaisquer recursos que já não estejam a ser utilizados.

### <a name="to-delete-the-local-network-gateway"></a>Para eliminar o portal de rede local

1. Em **Todos os recursos,** localize os gateways de rede locais que estavam associados a cada ligação.
2. Na lâmina **de visão geral** para o gateway de rede local, clique em **Eliminar**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Para eliminar o recurso de endereço IP público para o gateway

1. Em **Todos os recursos**, localize o recurso de endereço IP público que estava associado ao gateway. Se o gateway de rede virtual estiver ativo, verá dois endereços IP públicos. 
2. Na página **'Visão Geral'** para o endereço IP público, clique em **Apagar**e, em seguida, **Sim** para confirmar.

### <a name="to-delete-the-gateway-subnet"></a>Para eliminar a sub-rede gateway

1. Em **Todos os recursos,** localize a rede virtual. 
2. Na lâmina **sub-redes,** clique na **GatewaySubnet**e clique em **Eliminar**. 
3. Clique **em Sim** para confirmar que pretende eliminar a sub-rede gateway.

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="deleterg"></a>Eliminar uma porta de entrada VPN eliminando o grupo de recursos

Se não está preocupado em manter nenhum dos seus recursos no grupo de recursos e apenas quiser recomeçar, pode eliminar um grupo inteiro de recursos. Esta é uma maneira rápida de remover tudo. Os seguintes passos aplicam-se apenas ao modelo de implementação do Gestor de Recursos.

1. Em **Todos os recursos,** localize o grupo de recursos e clique para abrir a lâmina.
2. Clique em **Eliminar**. Na lâmina Delete, veja os recursos afetados. Certifique-se de que pretende eliminar todos estes recursos. Caso contrário, utilize os passos em Eliminar uma porta de entrada VPN no topo deste artigo.
3. Para prosseguir, digite o nome do grupo de recursos que pretende eliminar e, em seguida, clique em **Eliminar**.
