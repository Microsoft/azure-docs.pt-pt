---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5642533fe1015e88c3b27e83139bfd26cb0b1a53
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157244"
---
1. No [portal](http://portal.azure.com), navegue para a rede virtual do Gestor de Recursos para a qual pretende criar um gateway de rede virtual.
2. Na secção **Definições** da página da VNet, clique em **Sub-redes** para expandir a página **Sub-redes**.
3. Na página **Sub-redes**, clique em **+Sub-rede do gateway** para abrir a página **Adicionar sub-rede**. 

   ![Add the gateway subnet](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "Add the gateway subnet")
4. O **Nome** da sub-rede é preenchido automaticamente com o valor "GatewaySubnet". Este valor é obrigatório para que o Azure reconheça a sub-rede como a sub-rede do gateway. Ajuste os valores de **Intervalo de endereços** de preenchimento automático de modo a corresponder aos seus requisitos de configuração. Não configure a tabela de rotas ou pontos finais de serviço.

   ![Adding the subnet](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "Adding the subnet")
5. Clique em **OK** na parte inferior da página para criar a sub-rede.
