---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9ce9fa29a29559a1eaaff6173737159f11aa83d8
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "71268894"
---
1. No portal, do lado esquerdo, clique em **+ Criar um recurso** e escreva "Gateway de Rede Virtual" na pesquisa. Localize **Gateway de rede virtual** no resultado da pesquisa e clique na entrada. Na página **Gateway de rede virtual** , clique em **criar**. É aberta a página **Criar gateway de rede virtual**.

   ![Criar campos de página de gateway de rede virtual](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw.png "Criar campos de página de gateway de rede virtual")

   ![Criar campos de página de gateway de rede virtual](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw2.png "Criar campos de página de gateway de rede virtual")
2. Na página **Criar gateway de rede virtual**, preencha os valores para o gateway de rede virtual.

   **Detalhes do projeto**

   - **Assinatura**: selecione a assinatura que você deseja usar na lista suspensa.
   - **Grupo de recursos**: essa configuração é preenchida automática quando você seleciona sua rede virtual nesta página.

   **Detalhes da instância**

   - **Nome**: dê um nome ao gateway. Nomear seu gateway não é o mesmo que nomear uma sub-rede de gateway. É o nome do objeto de gateway que está a criar.
   - **Região**: selecione a região na qual você deseja criar esse recurso. A região do gateway deve ser a mesma que a rede virtual.
   - **Tipo de gateway**: selecione **VPN**. Os gateways de VPN utilizam o tipo de gateway de rede virtual **VPN**. 
   - **Tipo de VPN**: selecione o tipo de VPN especificado para a sua configuração. A maioria das configurações requerem um tipo de VPN baseado em rotas.
   - **SKU**: selecione o SKU de gateway na lista pendente. Os SKUs listados na lista pendente dependem do tipo de VPN que selecionar. Para obter mais informações sobre os SKUs de gateway, veja [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

      **Rede virtual**: escolha a rede virtual à qual pretende adicionar este gateway.

      **Intervalo de endereços de sub-rede de gateway**: esse campo será exibido somente se a rede virtual selecionada não tiver uma sub-rede de gateway. Preencha o intervalo se você ainda não tiver uma sub-rede de gateway. Se possível, torne o intervalo/27 ou maior (/26,/25 etc.)

   **Endereço IP público**: esta definição especifica o objeto de endereço IP público que é associado ao gateway de VPN. O endereço IP público é dinamicamente atribuído a este objeto quando o gateway de VPN é criado. O endereço IP Público só é alterado quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN.

     - **Endereço IP público**: deixe **criar novo** selecionado.
     - **Nome do endereço IP público**: na caixa de texto, digite um nome para sua instância de endereço IP público.
     - **Atribuição**: o gateway de VPN dá suporte apenas ao dinâmico.

   **Modo ativo-ativo**: selecione somente **habilitar modo ativo-ativo** se você estiver criando uma configuração de gateway ativo-ativo. Caso contrário, deixe esta configuração não selecionada.

   Deixe **Configurar o BGP ASN** não selecionado, a não ser que a sua configuração solicite especificamente esta definição. Se precisar desta definição, o ASN predefinido é 65515, apesar de isto poder ser alterado.

3. Clique em **examinar + criar** para executar a validação. Depois que a validação for aprovada, clique em **criar** para implantar o gateway de VPN. Um gateway pode levar até 45 minutos para criar e implantar totalmente. Você pode ver o status da implantação na página Visão geral do seu gateway.

Uma vez criado o gateway, pode visualizar o endereço IP que lhe foi atribuído vendo a rede virtual no portal. O gateway aparece como um dispositivo ligado.