---
title: 'Elimine um gateway de rede virtual: Portal do Azure: O Resource Manager | Documentos da Microsoft'
description: Elimine um gateway de rede virtual com o portal do Azure no modelo de implementação do Resource Manager.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/23/2018
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: 387b4e982772f22453876e1ea8b9e7c4039601c4
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790583"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Eliminar um gateway de rede virtual com o portal

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (clássico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Este artigo fornece as instruções para eliminar um gateways de VPN do Azure implementadas com o modelo de implementação do Resource Manager. Existem duas abordagens diferentes, que pode tomar para eliminar um gateway de rede virtual para uma configuração de gateway VPN.

- Se pretender eliminar tudo e começar de novo, como no caso de um ambiente de teste, pode eliminar o grupo de recursos. Quando elimina um grupo de recursos, elimina todos os recursos dentro do grupo. Este método só é recomendado se não quiser manter qualquer um dos recursos no grupo de recursos. Não é possível eliminar seletivamente apenas a alguns recursos, o uso dessa abordagem.

- Se quiser manter alguns dos recursos no grupo de recursos, a eliminar um gateway de rede virtual fica um pouco mais complicada. Antes de poder eliminar o gateway de rede virtual, tem primeiro de eliminar todos os recursos que são dependentes no gateway. Os passos que segue dependem do tipo de ligações que criou e os recursos dependentes para cada ligação.

> [!IMPORTANT]
> As instruções abaixo descrevem como eliminar gateways de VPN do Azure implementadas com o modelo de implementação do Resource Manager. Para eliminar um gateway VPN implementado usando o modelo de implementação clássica, tente utilizar o Azure PowerShell, tal como descrito [aqui](vpn-gateway-delete-vnet-gateway-classic-powershell.md).


## <a name="delete-a-vpn-gateway"></a>Eliminar um gateway de VPN

Para eliminar um gateway de rede virtual, tem primeiro de eliminar cada recurso que diz respeito ao gateway de rede virtual. Recursos tem de ser eliminados numa determinada ordem devido a dependências.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Neste momento, o gateway de rede virtual é eliminado. Os passos seguintes ajudar a eliminar todos os recursos que já não estão a ser utilizados.

### <a name="to-delete-the-local-network-gateway"></a>Para eliminar o gateway de rede local

1. Na **todos os recursos**, localize os gateways de rede local que foram associados a cada conexão.
2. Sobre o **descrição geral** painel para o gateway de rede local, clique em **eliminar**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Para eliminar o recurso de endereço IP público para o gateway

1. Na **todos os recursos**, localize o recurso de endereço IP público que foi associado ao gateway. Se o gateway de rede virtual foi ativo-ativo, verá dois endereços IP públicos. 
2. Na **descrição geral** para o endereço IP público, clique em **eliminar**, em seguida, **Sim** para confirmar.

### <a name="to-delete-the-gateway-subnet"></a>Para eliminar a sub-rede do gateway

1. Na **todos os recursos**, localize a rede virtual. 
2. Na **sub-redes** painel, clique no **GatewaySubnet**, em seguida, clique em **eliminar**. 
3. Clique em **Sim** para confirmar que pretende eliminar a sub-rede do gateway.

## <a name="deleterg"></a>Eliminar um gateway de VPN, eliminando o grupo de recursos

Se não estiver preocupado manter qualquer um dos seus recursos no grupo de recursos e pretender começar de novo, pode eliminar um grupo de recursos inteiro. Esta é uma forma rápida de remover tudo. Os seguintes passos aplicam-se apenas para o modelo de implementação do Resource Manager.

1. Na **todos os recursos**, localize o grupo de recursos e clique para abrir o painel.
2. Clique em **Eliminar**. No painel eliminação, ver os recursos afetados. Certifique-se de que pretende eliminar todos esses recursos. Se não for, utilize os passos em eliminar um gateway de VPN na parte superior deste artigo.
3. Para continuar, escreva o nome do grupo de recursos que pretende eliminar, em seguida, clique em **eliminar**.
