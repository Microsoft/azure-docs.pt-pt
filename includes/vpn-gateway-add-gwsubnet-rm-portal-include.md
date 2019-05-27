---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eb2555cdc163ee0f88149248b9e7d83a51d34a1b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150286"
---
1. Na [portal do Azure](http://portal.azure.com), selecione a rede virtual do Resource Manager para o qual pretende criar um gateway de rede virtual.

2. Na **definições** secção da página da rede virtual, selecione **sub-redes** para expandir o **sub-redes** página.

3. Sobre o **sub-redes** página, selecione **sub-rede do Gateway** para abrir o **adicionar sub-rede** página.

   ![Add the gateway subnet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Add the gateway subnet")

4. O **Name** para a sub-rede é automaticamente autofilled com o valor *GatewaySubnet*. Este valor é necessário para o Azure reconheça a sub-rede como a sub-rede do gateway. Ajustar o autofilled **intervalo de endereços** valores para corresponder aos requisitos de configuração, em seguida, selecione **OK** para criar a sub-rede.

   ![Adding the subnet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Adding the subnet")