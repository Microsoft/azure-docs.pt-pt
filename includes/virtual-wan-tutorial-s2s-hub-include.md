---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3bd9489adaf46e604393fc7059d37443bdd5ec3e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73489002"
---
1. Localize o WAN Virtual que criou. Na página WAN Virtual, sob a secção **conectividade,** selecione **Hubs**.
2. Na página Hubs, selecione **+Novo Hub** para abrir a página **Create virtual hub.**

    ![Noções básicas](./media/virtual-wan-tutorial-hub-include/basics.png "Noções básicas")
3. No separador **Criar hub virtual** **Basics,** complete os seguintes campos:

    **Detalhes do projeto**

   * Região (anteriormente designada por Localização)
   * Nome
   * Espaço de endereços privados hub. O espaço de endereço mínimo é /24 para criar um hub, o que implica que qualquer coisa que varie de /25 a /32 produzirá um erro durante a criação.
4. Selecione **Seguinte: Site-a-site**.

    ![Site a Site](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Site a Site")

5. No separador **Site-a-local,** preencha os seguintes campos:

   * Selecione **Sim** para criar uma VPN site-to-site.
   * O campo AS Number não é editável no centro virtual neste momento.
   * Selecione o valor das **unidades** de escala Gateway a partir do dropdown. A unidade de escala permite-lhe escolher a entrada agregada do gateway VPN que está a ser criada no centro virtual para ligar os sites. Se escolher uma unidade de escala = 500 Mbps, implica que serão criados dois casos de redundância, cada um com uma potência máxima de 500 Mbps. Por exemplo, se tiver cinco ramos, cada um fazendo 10 Mbps no ramo, precisará de um agregado de 50 Mbps na extremidade da cabeça. O planeamento da capacidade agregada do gateway Azure VPN deve ser feito após a avaliação da capacidade necessária para suportar o número de balcões no centro.
6. Selecione **Review + Criar** para validar.
7. Selecione **Criar** para criar o centro. Após 30 minutos, **Refresque** para ver o centro na página **Hubs.** Selecione **Ir ao recurso** para navegar para o recurso.