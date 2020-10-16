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
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73491594"
---
1. Localize o WAN virtual que criou. Na página VIRTUAL WAN, na secção **Conectividade,** selecione **Hubs**.
2. Na página Hubs, selecione **+New Hub** para abrir a página **do hub virtual Create.**
3. No **separador 'Criar** página de centro virtual **Basics',** complete os seguintes campos:

   ![Noções básicas](./media/virtual-wan-tutorial-er-hub-include/hub1.png "Noções básicas")

    **Detalhes do projeto**

   * Região (anteriormente designada por Localização)
   * Nome
   * Espaço de endereço privado hub. O espaço mínimo de endereço é /24 para criar um hub, o que implica que qualquer coisa varia entre /25 e /32 produzirá um erro durante a criação.
4. Selecione o **separador ExpressRoute**.

5. No separador **ExpressRoute,** complete os seguintes campos:

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * Selecione **Sim** para criar uma porta de entrada **ExpressRoute.**
   * Selecione o valor das **unidades** de escala Gateway a partir do dropdown.
6. Selecione **Review + Criar** para validar.
7. Selecione **Criar** para criar o hub. Após 30 minutos, **Refresque-se** para ver o hub na página **Hubs.** Selecione **Vá para o recurso** para navegar para o recurso.