---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9132cf438cab518e20e6c2ddfdb7d0928753bd19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60407757"
---
1. No portal, navegue para a rede virtual para a qual pretende criar um gateway de rede virtual.
2. Na secção **Definições** da página da VNet, clique em **Sub-redes** para expandir a página Sub-redes.
3. Na página **Sub-redes**, clique em **+Sub-rede do gateway** na parte superior para abrir a página **Adicionar sub-rede**.

   ![Add the gateway subnet](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Add the gateway subnet")
  
4. O **Nome** da sub-rede é preenchido automaticamente com o valor "GatewaySubnet". O valor de GatewaySubnet é obrigatório para que o Azure reconheça a sub-rede como a sub-rede do gateway. Ajuste os valores de **Intervalo de endereços** de preenchimento automático de modo a corresponder aos seus requisitos de configuração.

   ![Adicionar a subrede do gateway](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Adicionar a subrede do gateway")
  
5. Para criara sub-rede, clique em **OK** na parte inferior da página.
