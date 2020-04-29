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
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73491594"
---
1. Localize o WAN Virtual que criou. Na página WAN Virtual, sob a secção **conectividade,** selecione **Hubs**.
2. Na página Hubs, selecione **+Novo Hub** para abrir a página **Create virtual hub.**
3. No separador **Criar hub virtual** **Basics,** complete os seguintes campos:

   ![Noções básicas](./media/virtual-wan-tutorial-er-hub-include/hub1.png "Noções básicas")

    **Detalhes do projeto**

   * Região (anteriormente designada por Localização)
   * Nome
   * Espaço de endereços privados hub. O espaço de endereço mínimo é /24 para criar um hub, o que implica que qualquer coisa que varie de /25 a /32 produzirá um erro durante a criação.
4. Selecione o **separador ExpressRoute**.

5. No separador **ExpressRoute,** preencha os seguintes campos:

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * Selecione **Sim** para criar um gateway **ExpressRoute.**
   * Selecione o valor das **unidades** de escala Gateway a partir do dropdown.
6. Selecione **Review + Criar** para validar.
7. Selecione **Criar** para criar o centro. Após 30 minutos, **Refresque** para ver o centro na página **Hubs.** Selecione **Ir ao recurso** para navegar para o recurso.