---
title: 'Gateway VPN: Modificar as definições do endereço IP do gateway: Portal Azure'
description: Este artigo acompanha-o através da alteração de prefixos de endereço IP para o seu gateway de rede local utilizando o portal Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/16/2020
ms.author: cherylmc
ms.openlocfilehash: af3513c4a4f3b3187e85c65de51ad2e6e2d7279c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983155"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modificar as definições do gateway de rede local com o portal do Azure

Por vezes, as definições para a sua rede local addressPrefix ou GatewayIPAddress mudam. Este artigo mostra-lhe como modificar as definições de gateway de rede local. Também pode modificar estas definições utilizando um método diferente selecionando uma opção diferente da seguinte lista:

Antes de eliminar a ligação, pode querer descarregar a configuração para os seus dispositivos de ligação para obter o PSK definido. Assim, não precisas de o redefinir do outro lado.

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Configuração de gateway de rede local

A imagem abaixo mostra a página de **Configuração** de um recurso de gateway de rede local utilizando o ponto final do endereço IP público:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/ip-address.png" alt-text="Configure o gateway de rede local - endereço IP":::

Esta é a mesma página de configuração com um ponto final FQDN:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/fqdn.png" alt-text="Configure o gateway de rede local - endereço IP":::

## <a name="modify-the-gateway-ip-address"></a><a name="ip"></a>Modificar o endereço IP gateway

Se o dispositivo VPN que pretende ligar foi alterou o respetivo endereço IP público, tem de modificar o gateway de rede local para que essa alteração seja refletida.

1. No recurso Gateway de rede local, na secção **Definições,** clique em **Configuração**.
2. Na caixa **de endereços IP,** modifique o endereço IP.
3. Clique em **Guardar** para guardar as definições.

## <a name="modify-the-gateway-fqdn"></a><a name="fqdn"></a>Modificar o gateway FQDN

Se o dispositivo VPN a que pretende ligar alterou o seu FQDN (Nome de Domínio Totalmente Qualificado), tem de modificar a porta de entrada da rede local para refletir essa alteração.

1. No recurso Gateway de rede local, na secção **Definições,** clique em **Configuração**.
2. Na caixa **FQDN,** modifique o nome de domínio.
3. Clique em **Guardar** para guardar as definições.

> ! [NOTA] Não é possível alterar uma porta de entrada de rede local entre o ponto final do FQDN e o ponto final do endereço IP. Deve eliminar todas as ligações associadas a este gateway de rede local, criar uma nova com o novo ponto final (endereço IP ou FQDN) e, em seguida, recriar as ligações.

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modificar prefixos de endereço IP

### <a name="to-add-additional-address-prefixes"></a>Para adicionar prefixos de endereços adicionais:

1. No recurso Gateway de rede local, na secção **Definições,** clique em **Configuração**.
2. Adicione o espaço de endereço IP na caixa *de intervalo de endereços adicional.*
3. Clique **em Guardar** para guardar as suas definições.

### <a name="to-remove-address-prefixes"></a>Para remover um prefixos de endereço:

1. No recurso Gateway de rede local, na secção **Definições,** clique em **Configuração**.
2. Clique no **'...'** na linha que contém o prefixo que pretende remover.
3. Clique em **Remover**.
4. Clique **em Guardar** para guardar as suas definições.

## <a name="modify-bgp-settings"></a><a name="bgp"></a>Modificar as definições de BGP

### <a name="to-add-or-update-bgp-settings"></a>Para adicionar ou atualizar as definições de BGP:

1. No recurso Gateway de rede local, na secção **Definições,** clique em **Configuração**.
2. Selecione **"Configurar definições de BGP"** para visualizar ou atualizar as configurações do BGP para este gateway de rede local
3. Adicionar ou atualizar o número do sistema autónomo ou o endereço IP do BGP nos campos correspondentes
4. Clique **em Guardar** para guardar as suas definições.

### <a name="to-remove-bgp-settings"></a>Para remover as definições de BGP:

1. No recurso Gateway de rede local, na secção **Definições,** clique em **Configuração**.
2. Desescoda as **"definições de BGP configurantes"** para remover o endereço IP do BGP e do BGP existente
3. Clique **em Guardar** para guardar as suas definições.

## <a name="next-steps"></a>Passos seguintes

Pode verificar a sua ligação de gateway. Ver [Verificar uma ligação de gateway](vpn-gateway-verify-connection-resource-manager.md).
