---
title: 'Gateway de VPN do Azure: excluir um gateway: Portal'
description: Exclua um gateway de rede virtual usando o portal do Azure no modelo de implantação do Gerenciador de recursos.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/23/2018
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: e8c7a9c7b6d38c5fee4f57f65dd61a71f9723c07
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146337"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Excluir um gateway de rede virtual usando o portal

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (clássico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Este artigo fornece as instruções para excluir um gateway de VPN do Azure implantado usando o modelo de implantação do Gerenciador de recursos. Há algumas abordagens diferentes que você pode tomar quando deseja excluir um gateway de rede virtual para uma configuração de gateway de VPN.

- Se você quiser excluir tudo e começar novamente, como no caso de um ambiente de teste, você poderá excluir o grupo de recursos. Quando você exclui um grupo de recursos, ele exclui todos os recursos dentro do grupo. Esse método só é recomendado se você não quiser manter nenhum dos recursos no grupo de recursos. Você não pode excluir seletivamente apenas alguns recursos usando essa abordagem.

- Se você quiser manter alguns dos recursos em seu grupo de recursos, a exclusão de um gateway de rede virtual se tornará um pouco mais complicada. Antes de excluir o gateway de rede virtual, você deve primeiro excluir todos os recursos que dependem do gateway. As etapas a serem seguidas dependem do tipo de conexão que você criou e dos recursos dependentes de cada conexão.

> [!IMPORTANT]
> As instruções a seguir descrevem como excluir gateways de VPN do Azure implantados usando o modelo de implantação do Resource Manager. Para excluir um gateway de VPN implantado usando o modelo de implantação clássico, use Azure PowerShell conforme descrito [aqui](vpn-gateway-delete-vnet-gateway-classic-powershell.md).


## <a name="delete-a-vpn-gateway"></a>Eliminar um gateway de VPN

Para excluir um gateway de rede virtual, você deve primeiro excluir cada recurso que pertença ao gateway de rede virtual. Os recursos devem ser excluídos em uma determinada ordem devido a dependências.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Neste ponto, o gateway de rede virtual é excluído. As próximas etapas ajudarão você a excluir todos os recursos que não estão mais sendo usados.

### <a name="to-delete-the-local-network-gateway"></a>Para excluir o gateway de rede local

1. Em **todos os recursos**, localize os gateways de rede local que foram associados a cada conexão.
2. Na folha **visão geral** do gateway de rede local, clique em **excluir**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Para excluir o recurso de endereço IP público para o gateway

1. Em **todos os recursos**, localize o recurso de endereço IP público que estava associado ao gateway. Se o gateway de rede virtual estava ativo-ativo, você verá dois endereços IP públicos. 
2. Na página **visão geral** do endereço IP público, clique em **excluir**e em **Sim** para confirmar.

### <a name="to-delete-the-gateway-subnet"></a>Para excluir a sub-rede de gateway

1. Em **todos os recursos**, localize a rede virtual. 
2. Na folha **sub-redes** , clique no **GatewaySubnet**e, em seguida, clique em **excluir**. 
3. Clique em **Sim** para confirmar que deseja excluir a sub-rede de gateway.

## <a name="deleterg"></a>Excluir um gateway de VPN excluindo o grupo de recursos

Se você não estiver preocupado em manter qualquer um dos seus recursos no grupo de recursos e quiser apenas recomeçar, poderá excluir um grupo de recursos inteiro. Essa é uma maneira rápida de remover tudo. As etapas a seguir se aplicam somente ao modelo de implantação do Gerenciador de recursos.

1. Em **todos os recursos**, localize o grupo de recursos e clique para abrir a folha.
2. Clique em **Eliminar**. Na folha excluir, exiba os recursos afetados. Certifique-se de que você deseja excluir todos esses recursos. Caso contrário, use as etapas em excluir um gateway de VPN na parte superior deste artigo.
3. Para continuar, digite o nome do grupo de recursos que você deseja excluir e clique em **excluir**.
