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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491594"
---
1. Localize a WAN virtual que você criou. Na página WAN virtual, na seção **conectividade** , selecione **hubs**.
2. Na página hubs, selecione **+ novo hub** para abrir a página **criar Hub virtual** .
3. Na guia Criar **noções básicas** de página do **Hub virtual** , preencha os seguintes campos:

   ![Noções básicas](./media/virtual-wan-tutorial-er-hub-include/hub1.png "Noções básicas")

    **Detalhes do projeto**

   * Região (anteriormente conhecida como local)
   * Nome
   * Espaço de endereço privado do Hub. O espaço de endereço mínimo é/24 para criar um Hub, o que implica que qualquer intervalo de/25 a/32 produzirá um erro durante a criação.
4. Selecione a **guia ExpressRoute**.

5. Na guia **ExpressRoute** , preencha os seguintes campos:

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * Selecione **Sim** para criar um gateway de **ExpressRoute** .
   * Selecione o valor **unidades de escala do gateway** na lista suspensa.
6. Selecione **revisão + criar** para validar.
7. Selecione **criar** para criar o Hub. Após 30 minutos, **atualize** para exibir o Hub na página **hubs** . Selecione **ir para o recurso** para navegar até o recurso.