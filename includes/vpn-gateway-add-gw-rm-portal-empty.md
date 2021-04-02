---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6cb30b96f9c2094e6d690b565edf487d6508d520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92487121"
---
1. A partir do [portal Azure](https://portal.azure.com), em **Recursos de Busca, serviços e docs (G+/)** tipo gateway de rede **virtual**. Localize **o gateway de rede virtual** nos resultados da pesquisa e selecione-o.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-empty/search.png" alt-text="Campo de pesquisa" lightbox="./media/vpn-gateway-add-gw-rm-portal-empty/search-expand.png":::

1. Na página **de gateway de rede Virtual,** selecione + **Adicionar**. É aberta a página **Criar gateway de rede virtual**.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-empty/add.png" alt-text="página de gateways de rede virtual":::
1. No **separador Básicos,** preencha os valores do seu gateway de rede virtual.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-empty/gateway.png" alt-text="Campos gateway":::

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-empty/gateway-vnet.png" alt-text="Campos de gateway adicionais":::

   **Detalhes do projeto**

   * **Subscrição**: Selecione a subscrição que pretende utilizar a partir do dropdown.
   * **Grupo de Recursos**: Esta definição é preenchida automaticamente quando seleciona a sua rede virtual nesta página.

   **Detalhes da instância**

   * **Nome**: dê um nome ao gateway. Nomear o seu portal não é o mesmo que nomear uma sub-rede de gateway. É o nome do objeto de gateway que está a criar.
   * **Região**: Selecione a região em que pretende criar este recurso. A região para o portal deve ser a mesma que a rede virtual.
   * **Tipo de gateway**: selecione **VPN**. Os gateways de VPN utilizam o tipo de gateway de rede virtual **VPN**.
   * **Tipo de VPN**: selecione o tipo de VPN especificado para a sua configuração. A maioria das configurações requerem um tipo de VPN baseado em rotas.
   * **SKU**: selecione o SKU de gateway na lista pendente. Os SKUs listados na lista pendente dependem do tipo de VPN que selecionar. Para obter mais informações sobre os SKUs de gateway, veja [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   * **Geração**: Para obter informações sobre a Geração Gateway VPN, consulte [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   * **Rede virtual**: A partir do dropdown, selecione a rede virtual à qual pretende adicionar este gateway.
   * **Intervalo de endereços de sub-rede gateway**: Este campo só aparece se o seu VNet não tiver uma sub-rede de gateway. Se possível, faça o intervalo /27 ou maior (/26,/25 etc.). Não recomendamos a criação de um intervalo inferior ao de /28. Se já tem uma sub-rede gateway, pode ver os detalhes do GatewaySubnet navegando para a sua rede virtual. Clique **em Subnetas** para ver o alcance. Se quiser alterar o alcance, pode eliminar e recriar o GatewaySubnet.

   **Endereço IP público**

   Esta definição especifica o objeto de endereço IP público que fica associado ao gateway VPN. O endereço IP público é dinamicamente atribuído a este objeto quando o gateway de VPN é criado. O endereço IP Público só é alterado quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN.

     * **Endereço IP público**: Deixe **criar novos selecionados.**
     * **Nome do endereço IP público**: Na caixa de texto, escreva um nome para a sua instância de endereço IP público.
     * **Atribuição**: Gateway VPN suporta apenas Dynamic.
     * **Ativar o modo ativo**: Selecione **Apenas active-active mode** se estiver a criar uma configuração de gateway ativa ativa. Caso contrário, deixe esta definição **Desativada**.
     * Deixe **o Configure BGP** como **desativado,** a menos que a sua configuração exija especificamente esta definição. Se precisar desta definição, o ASN predefinido é 65515, apesar de isto poder ser alterado.
1. Selecione **Review + criar** para executar validação.
1. Assim que a validação passar, **selecione Criar** para implementar o gateway VPN. Um gateway pode demorar até 45 minutos para criar e implantar totalmente. Pode ver o estado de implantação na página 'Vista Geral' para o seu gateway.

Uma vez criado o gateway, pode visualizar o endereço IP que lhe foi atribuído vendo a rede virtual no portal. O gateway aparece como um dispositivo ligado.
