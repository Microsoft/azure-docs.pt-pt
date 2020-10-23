---
title: 'Gateway Azure VPN: Excluir um portal: portal'
description: Excluir um gateway de rede virtual usando o portal Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/16/2020
ms.author: cherylmc
ms.topic: how-to
ms.openlocfilehash: 7d9ae31b5701707589d79fd5f3d7eb0802038eb9
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148207"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Excluir um gateway de rede virtual usando o portal

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (clássico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Este artigo ajuda-o a eliminar uma porta de entrada de rede virtual. Existem algumas abordagens diferentes que pode tomar quando pretende apagar uma porta de entrada para uma configuração de gateway VPN.

* Se pretender apagar tudo e começar de novo, como no caso de um ambiente de teste, pode eliminar o grupo de recursos. Quando elimina um grupo de recursos, elimina todos os recursos dentro do grupo. Este método só é recomendado se não quiser manter nenhum dos recursos no grupo de recursos. Não é possível eliminar seletivamente apenas alguns recursos usando esta abordagem.

* Se quiser manter alguns dos recursos no seu grupo de recursos, eliminar um gateway de rede virtual torna-se um pouco mais complicado. Antes de poder apagar o gateway de rede virtual, tem primeiro de eliminar quaisquer recursos que dependam do gateway. Os passos que segue dependem do tipo de ligações que criou e dos recursos dependentes para cada ligação.

> [!IMPORTANT]
> Os passos deste artigo aplicam-se ao modelo de implementação Resource Manager. Para eliminar um gateway VPN implantado utilizando o modelo de implementação clássico, utilize os passos no [Delete a gateway:](vpn-gateway-delete-vnet-gateway-classic-powershell.md) artigo clássico.

## <a name="delete-a-vpn-gateway"></a>Eliminar um gateway de VPN

Para eliminar um gateway de rede virtual, tem primeiro de eliminar cada recurso que se refere ao portal de rede virtual. Os recursos devem ser suprimidos numa determinada ordem devido às dependências.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Neste ponto, o gateway de rede virtual é eliminado.

### <a name="to-delete-additional-resources"></a>Para eliminar recursos adicionais

Os seguintes passos ajudam-no a eliminar quaisquer recursos que já não estejam a ser utilizados.

#### <a name="to-delete-the-local-network-gateway"></a>Para eliminar o portal de rede local

1. Em **Todos os recursos,** localize os gateways de rede locais que estavam associados a cada ligação.
1. Na lâmina **de visão geral** para o gateway de rede local, clique em **Eliminar**.

#### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Para eliminar o recurso de endereço IP público para o gateway

1. Em **Todos os recursos**, localize o recurso de endereço IP público que estava associado ao gateway. Se o gateway de rede virtual estiver ativo, verá dois endereços IP públicos.
1. Na página **'Visão Geral'** para o endereço IP público, clique em **Apagar**e, em seguida, **Sim** para confirmar.

#### <a name="to-delete-the-gateway-subnet"></a>Para eliminar a sub-rede gateway

1. Em **Todos os recursos,** localize a rede virtual. 
1. Na lâmina **sub-redes,** clique na **GatewaySubnet**e clique em **Eliminar**. 
1. Clique **em Sim** para confirmar que pretende eliminar a sub-rede gateway.

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="deleterg"></a>Eliminar uma porta de entrada VPN eliminando o grupo de recursos

Se não está preocupado em manter nenhum dos seus recursos no grupo de recursos e apenas quiser recomeçar, pode eliminar um grupo inteiro de recursos. Esta é uma maneira rápida de remover tudo. Os seguintes passos aplicam-se apenas ao modelo de implementação do Gestor de Recursos.

1. Em **Todos os recursos,** localize o grupo de recursos e clique para abrir a lâmina.
1. Clique em **Eliminar**. Na lâmina Delete, veja os recursos afetados. Certifique-se de que pretende eliminar todos estes recursos. Caso contrário, utilize os passos em Eliminar uma porta de entrada VPN no topo deste artigo.
1. Para prosseguir, digite o nome do grupo de recursos que pretende eliminar e, em seguida, clique em **Eliminar**.
