---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 134f1dc7cb6e53c181b2f518055e5cb758fccf31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812714"
---
1. Sob o seu WAN virtual, selecione Hubs e selecione **+New Hub**.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.jpg" alt-text="novo hub":::

1. Na página do hub virtual de criar, preencha os seguintes campos.

   * **Região** - Selecione a região em que pretende implantar o hub virtual.
   * **Nome** - Introduza o nome que pretende chamar ao seu centro virtual.
   * **Espaço de endereço privado hub** - A gama de endereços do hub na notação CIDR.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.jpg" alt-text="novo hub":::

1. No separador Ponto a Local, complete os seguintes campos:

   * **Unidades de escala de gateway** - que representam a capacidade agregada do gateway VPN do utilizador.
   * **Ponto para a configuração do site** - que criou no passo anterior.
   * **Pool de Endereços do Cliente** - para os utilizadores remotos.
   * **IP do servidor DNS personalizado**.

   :::image type="content" source="media/virtual-wan-p2s-hub/hub-with-p2s.png" alt-text="novo hub":::

1. Selecione **Rever + criar**.
1. Na página **de validação passada,** selecione **Criar**.