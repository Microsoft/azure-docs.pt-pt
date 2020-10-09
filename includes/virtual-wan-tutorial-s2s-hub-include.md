---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 64470b42efeea49b7c778d6dffd88465b8445e36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606353"
---
1. Localize o WAN virtual que criou. Na página VIRTUAL WAN, na secção **Conectividade,** selecione **Hubs**.
2. Na página Hubs, selecione **+New Hub** para abrir a página **do hub virtual Create.**

    ![A screenshot mostra o painel de hub virtual Create com o separador Basics selecionado.](./media/virtual-wan-tutorial-hub-include/basics.png "Noções básicas")
3. No **separador 'Criar** página de centro virtual **Basics',** complete os seguintes campos:

    **Detalhes do projeto**

   * Região (anteriormente designada por Localização)
   * Nome
   * Espaço de endereço privado hub. O espaço mínimo de endereço é /24 para criar um hub, o que implica que qualquer coisa varia entre /25 e /32 produzirá um erro durante a criação. Sendo o Azure Virtual WAN um serviço gerido pela Microsoft cria as sub-redes adequadas no centro virtual para os diferentes gateways/serviços (por exemplo, Gateways VPN, Gateways ExpressRoute, User VPN/Point-to-site Gateways, Firewall, Encaminhing etc.). Não é necessário que o utilizador planeie explicitamente o espaço de endereço de sub-rede para os serviços no Virtual Hub, uma vez que a Microsoft o faz como parte do serviço.
4. Selecione **Seguinte: Site-to-site**.

    ![A screenshot mostra o painel de hub virtual Create com o Site para o site selecionado.](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Site a Site")

5. No **separador Site-to-site,** complete os seguintes campos:

   * Selecione **Sim** para criar uma VPN site-to-site.
   * O campo AS Number não é editável no centro virtual neste momento.
   * Selecione o valor das **unidades** de escala Gateway a partir do dropdown. A unidade de escala permite-lhe escolher a produção agregada do gateway VPN que está a ser criado no centro virtual para ligar sites. Se escolher uma unidade de escala de 1 = 500 Mbps, implica que serão criados dois casos de redundância, cada um com uma potência máxima de 500 Mbps. Por exemplo, se tivesse cinco ramos, cada um fazendo 10 Mbps no ramo, precisaria de um agregado de 50 Mbps na extremidade da cabeça. O planeamento da capacidade agregada da gateway Azure VPN deve ser feito após a avaliação da capacidade necessária para suportar o número de balcões no centro.
6. Selecione **Review + Criar** para validar.
7. Selecione **Criar** para criar o hub. Após 30 minutos, **Refresque-se** para ver o hub na página **Hubs.** Selecione **Vá para o recurso** para navegar para o recurso.
