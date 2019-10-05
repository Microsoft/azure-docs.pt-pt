---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ed828a09843648b129b701c9afd22aac9e2fa9b0
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970795"
---
1. No portal, do lado esquerdo, clique em **+ Criar um recurso** e escreva "Gateway de Rede Virtual" na pesquisa. Localize **Gateway de rede virtual** no resultado da pesquisa e clique na entrada. Na página **Gateway de rede virtual** , clique em **criar**. É aberta a página **Criar gateway de rede virtual**.

   ![Create virtual network gateway page fields](./media/vpn-gateway-add-gw-rm-portal-include/p2sgw.png "Create virtual network gateway page fields")

   ![Create virtual network gateway page fields](./media/vpn-gateway-add-gw-rm-portal-include/p2sgw2.png "Create virtual network gateway page fields")
2. Na página **Criar gateway de rede virtual**, preencha os valores para o gateway de rede virtual.

   **Detalhes do projeto**

   - **Subscrição**: Selecione a assinatura que você deseja usar na lista suspensa.
   - **Grupo de recursos**: Essa configuração é preenchida automática quando você seleciona sua rede virtual nesta página.

   **Detalhes da instância**

   - **Nome**: Nomeie seu gateway. Nomear seu gateway não é o mesmo que nomear uma sub-rede de gateway. É o nome do objeto de gateway que está a criar.
   - **Região**: Selecione a região na qual você deseja criar esse recurso. A região do gateway deve ser a mesma que a rede virtual.
   - **Tipo de gateway**: Selecione **VPN**. Os gateways de VPN utilizam o tipo de gateway de rede virtual **VPN**. 
   - **Tipo de VPN**: Selecione o tipo de VPN que é especificado para sua configuração. A maioria das configurações requerem um tipo de VPN baseado em rotas.
   - **SKU**: Selecione o SKU de gateway no menu suspenso. Os SKUs listados na lista pendente dependem do tipo de VPN que selecionar. Para obter mais informações sobre os SKUs de gateway, veja [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

      **Rede virtual**: Escolha a rede virtual à qual pretende adicionar este gateway.

      **Intervalo de endereços da sub-rede do gateway**: Esse campo só aparecerá se a VNet não tiver uma sub-rede de gateway. Se possível, torne o intervalo/27 ou maior (/26,/25 etc.). Não é recomendável criar um intervalo menor que/28. Se você já tiver uma sub-rede de gateway, poderá exibir detalhes do GatewaySubnet navegando até sua rede virtual. Clique em **sub-redes** para exibir o intervalo. Se você quiser alterar o intervalo, poderá excluir e recriar o GatewaySubnet.

   **Endereço IP público**: Essa configuração especifica o objeto de endereço IP público que é associado ao gateway de VPN. O endereço IP público é dinamicamente atribuído a este objeto quando o gateway de VPN é criado. O endereço IP Público só é alterado quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN.

     - **Endereço IP público**: Deixe **Criar novo** selecionado.
     - **Nome do endereço IP público**: Na caixa de texto, digite um nome para sua instância de endereço IP público.
     - **Atribuição**: O gateway de VPN dá suporte apenas ao dinâmico.

   **Modo ativo-ativo**: Selecione apenas **Ativar modo ativo-ativo** se estiver a criar uma configuração de gateway ativa-ativa. Caso contrário, deixe esta configuração não selecionada.

   Deixe **Configurar o BGP ASN** não selecionado, a não ser que a sua configuração solicite especificamente esta definição. Se precisar desta definição, o ASN predefinido é 65515, apesar de isto poder ser alterado.

3. Clique em **examinar + criar** para executar a validação. Depois que a validação for aprovada, clique em **criar** para implantar o gateway de VPN. Um gateway pode levar até 45 minutos para criar e implantar totalmente. Você pode ver o status da implantação na página Visão geral do seu gateway.

Uma vez criado o gateway, pode visualizar o endereço IP que lhe foi atribuído vendo a rede virtual no portal. O gateway aparece como um dispositivo ligado.
