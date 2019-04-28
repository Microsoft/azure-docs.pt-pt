---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/24/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 35f987f26ce47c19e3d5eb1ca5d2bb32d0c7ad1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60407805"
---
1. No lado esquerdo da página do portal, clique em **+** e escreva "Gateway de Rede Virtual" na pesquisa. Em **Resultados**, localize e clique em **Gateway de rede Virtual**.
2. Na parte inferior da página “Gateway de rede virtual”, clique em **Criar**. É aberta a página **Criar gateway de rede virtual**.
3. Na página **Criar gateway de rede virtual**, preencha os valores do gateway de rede virtual.

    ![Campos da página de criação de gateway de rede virtual](./media/vpn-gateway-add-gateway-portal-include/create-gateway.png "Novo gateway")

   - **Nome**: Nome do seu gateway. Não é o mesmo que atribuir nome a uma sub-rede de gateway. É o nome do objeto de gateway que está a criar.
   - **Tipo de gateway**: Selecione **VPN**. Os gateways de VPN utilizam o tipo de gateway de rede virtual **VPN**. 
   - **Tipo de VPN**: Selecione o tipo VPN especificado para a sua configuração. A maioria das configurações requerem um tipo de VPN baseado em rotas.
   - **SKU**: Selecione o SKU de gateway na lista pendente. Os SKUs listados na lista pendente dependem do tipo de VPN que selecionar. Para obter mais informações sobre os SKUs de gateway, veja [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

     Selecione apenas **Ativar modo ativo-ativo** se estiver a criar uma configuração de gateway ativa-ativa. Caso contrário, deixe esta configuração não selecionada.
   - **Localização**: Poderá ter de deslocar para ver a localização. Ajuste o campo **Localização** para apontar para a localização em que a sua rede virtual se encontra. Por exemplo, E.U.A. Oeste. Se a localização não estiver a apontar para a região onde reside a rede virtual, quando selecionar uma rede virtual no próximo passo, esta não será apresentada na lista pendente.
   - **Rede virtual**: Escolha a rede virtual à qual pretende adicionar este gateway. Clique em **Rede virtual** para abrir a página "Escolher uma rede virtual". Selecione a VNet. Se não vir a VNet, confirme se o campo Localização aponta para a região na qual a rede virtual está localizada.
   - **Intervalo de endereços da sub-rede de gateway**: Só verá esta definição se não tiver criado anteriormente uma sub-rede de gateway da rede virtual. Se tiver criado anteriormente uma sub-rede de gateway válida, esta definição não será apresentada.
   - **Endereço IP público**: Esta definição especifica o objeto de endereço IP público que é associado ao gateway de VPN. O endereço IP público é dinamicamente atribuído a este objeto quando o gateway de VPN é criado. O Gateway de VPN, atualmente, apenas suporta a alocação de endereços IP públicos *dinâmicos*. No entanto, isto não significa que o endereço IP é alterado após ser atribuído ao gateway de VPN. O endereço IP Público só é alterado quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN.

     - Deixe **Criar novo** selecionado.
     - Na caixa de texto, escreva um **Nome** para o seu endereço IP público.

4. Deixe **Configurar o BGP ASN** não selecionado, a não ser que a sua configuração solicite especificamente esta definição. Se precisar desta definição, o ASN predefinido é 65515, apesar de isto poder ser alterado.
5. Verifique as definições. Pode selecionar **Afixar ao dashboard** na parte inferior da página se quiser que o gateway apareça no dashboard. 
6. Clique em **Criar** para começar a criar o gateway de VPN. As definições são validadas e verá o mosaico "A implementar gateway de rede Virtual" no dashboard. A criação de um gateway pode demorar até 45 minutos. Poderá ter de atualizar a página do portal para ver o estado concluído.
   gateway.

Uma vez criado o gateway, visualize o endereço IP que lhe foi atribuído vendo a rede virtual no portal. O gateway aparece como um dispositivo ligado. Pode clicar no dispositivo ligado (o seu gateway de rede virtual) para ver mais informações.