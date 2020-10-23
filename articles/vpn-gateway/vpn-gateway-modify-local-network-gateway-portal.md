---
title: 'Gateway VPN: Modificar as definições do endereço IP do gateway: Portal Azure'
description: Este artigo acompanha-o através da alteração de prefixos de endereço IP para o seu gateway de rede local utilizando o portal Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/16/2020
ms.author: cherylmc
ms.openlocfilehash: 4ff4f1238764d7bdab6e74d29254a6388ea76d78
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143136"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modificar as definições do gateway de rede local com o portal do Azure

Por vezes, as definições para a sua rede local addressPrefix ou GatewayIPAddress mudam. Este artigo mostra-lhe como modificar as definições de gateway de rede local. Também pode modificar estas definições utilizando um método diferente selecionando uma opção diferente da seguinte lista:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Configuração de gateway de rede local

A imagem abaixo mostra a página de **Configuração** de um recurso de gateway de rede local utilizando o ponto final do endereço IP público:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/settings.png" alt-text="Definições de endereços IP" lightbox="./media/vpn-gateway-modify-local-network-gateway-portal/settings-expand.png":::

Esta é a página de configuração com um ponto final FQDN:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/name.png" alt-text="Definições de endereços IP":::

## <a name="to-modify-the-gateway-ip-address-or-fqdn"></a><a name="ip"></a>Para modificar o endereço IP do gateway ou FQDN

> [!NOTE]
> Não é possível alterar uma porta de entrada de rede local entre o ponto final do FQDN e o ponto final do endereço IP. Deve eliminar todas as ligações associadas a este gateway de rede local, criar uma nova com o novo ponto final (endereço IP ou FQDN) e, em seguida, recriar as ligações.
>

Se o dispositivo VPN ao qual pretende ligar alterou o seu endereço IP público, modifique o gateway de rede local utilizando os seguintes passos:

1. No recurso Gateway de rede local, na secção **Definições,** selecione **Configuração**.
2. Na caixa **de endereços IP,** modifique o endereço IP.
3. Selecione **Guardar** para guardar as definições.

Se o dispositivo VPN ao qual pretende ligar alterou o seu FQDN (Nome de Domínio Totalmente Qualificado), modifique o gateway de rede local utilizando os seguintes passos:

1. No recurso Gateway de rede local, na secção **Definições,** selecione **Configuração**.
2. Na caixa **FQDN,** modifique o nome de domínio.
3. Selecione **Guardar** para guardar as definições.

## <a name="to-modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Para modificar prefixos de endereço IP

Para adicionar prefixos de endereços adicionais:

1. No recurso Gateway de rede local, na secção **Definições,** selecione **Configuração**.
2. Adicione o espaço de endereço IP na caixa *de intervalo de endereços adicional.*
3. **Selecione Guardar** para guardar as suas definições.

Para remover um prefixos de endereço:

1. No recurso Gateway de rede local, na secção **Definições,** selecione **Configuração**.
2. Selecione o **'...'** na linha que contém o prefixo que pretende remover.
3. Selecione **Remover**.
4. **Selecione Guardar** para guardar as suas definições.

## <a name="to-modify-bgp-settings"></a><a name="bgp"></a>Para modificar as definições de BGP

Para adicionar ou atualizar as definições de BGP:

1. No recurso Gateway de rede local, na secção **Definições,** selecione **Configuração**.
2. Selecione **"Configurar definições de BGP"** para visualizar ou atualizar as configurações do BGP para este gateway de rede local
3. Adicionar ou atualizar o número do sistema autónomo ou o endereço IP do BGP nos campos correspondentes
4. **Selecione Guardar** para guardar as suas definições.

Para remover as definições de BGP:

1. No recurso Gateway de rede local, na secção **Definições,** selecione **Configuração**.
2. Desescolh as **"definições de BGP configurantes"** para remover o endereço IP do BGP e do BGP existentes
3. **Selecione Guardar** para guardar as suas definições.

## <a name="next-steps"></a>Passos seguintes

Pode verificar a sua ligação de gateway. Ver [Verificar uma ligação de gateway](vpn-gateway-verify-connection-resource-manager.md).
