---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52ab0413dffeee59cb9d34c6276a0c806a4d0322
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780208"
---
A sub-rede de gateway contém os endereços IP reservados que os serviços de gateway de rede virtual usam. Crie uma sub-rede de gateway.

1. No portal, navegue para a rede virtual para a qual pretende criar um gateway de rede virtual.
2. Na página rede virtual, clique em **sub-redes** para expandir a página **VNet1-sub-redes** .
3. Clique em **+ sub-rede de gateway** na parte superior para abrir a página **Adicionar sub-rede** .

   ![Add the gateway subnet](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Add the gateway subnet")
4. O **nome** da sua sub-rede é preenchido automaticamente com o valor necessário ' GatewaySubnet '. Ajuste o intervalo de endereços preenchidos automaticamente **(bloco CIDR)** para corresponder ao seguinte valor:

   **Intervalo de endereços (bloco CIDR)** : 10.1.255.0/27

   ![Add the gateway subnet](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet1.png "Add the gateway subnet")
5. Deixe o restante das configurações como os padrões de **nenhum** ou **0 selecionado**. Em seguida, clique em **OK** para criar a sub-rede de gateway.