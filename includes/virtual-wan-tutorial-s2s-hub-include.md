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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489002"
---
1. Localize a WAN virtual que você criou. Na página WAN virtual, na seção **conectividade** , selecione **hubs**.
2. Na página hubs, selecione **+ novo hub** para abrir a página **criar Hub virtual** .

    ![Noções básicas](./media/virtual-wan-tutorial-hub-include/basics.png "Noções básicas")
3. Na guia Criar **noções básicas** de página do **Hub virtual** , preencha os seguintes campos:

    **Detalhes do projeto**

   * Região (anteriormente conhecida como local)
   * Nome
   * Espaço de endereço privado do Hub. O espaço de endereço mínimo é/24 para criar um Hub, o que implica que qualquer intervalo de/25 a/32 produzirá um erro durante a criação.
4. Selecione **Avançar: site a site**.

    ![Site a site](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Site a Site")

5. Na guia **site a site** , preencha os seguintes campos:

   * Selecione **Sim** para criar uma VPN site a site.
   * No momento, o campo número as não é editável no Hub virtual.
   * Selecione o valor **unidades de escala do gateway** na lista suspensa. A unidade de escala permite que você escolha a taxa de transferência agregada do gateway de VPN que está sendo criado no Hub virtual para conectar sites ao. Se você escolher 1 unidade de escala = 500 Mbps, significa que duas instâncias de redundância serão criadas, cada uma com uma taxa de transferência máxima de 500 Mbps. Por exemplo, se você tiver cinco branches, cada um fazendo 10 Mbps na ramificação, precisará de um agregado de 50 Mbps no final da cabeça. O planejamento da capacidade agregada do gateway de VPN do Azure deve ser feito depois de avaliar a capacidade necessária para dar suporte ao número de branches para o Hub.
6. Selecione **revisão + criar** para validar.
7. Selecione **criar** para criar o Hub. Após 30 minutos, **atualize** para exibir o Hub na página **hubs** . Selecione **ir para o recurso** para navegar até o recurso.