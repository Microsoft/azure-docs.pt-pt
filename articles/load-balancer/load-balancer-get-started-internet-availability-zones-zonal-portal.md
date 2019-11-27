---
title: Criar um Load Balancer com um front-end zonal-portal do Azure
titleSuffix: Azure Load Balancer
description: Saiba como criar um Standard Load Balancer com front-end zonal com o portal do Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 82a773b279780bc4eb784fa107d6b15bd0ff2672
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225344"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Criar um Standard Load Balancer com front-end zonal usando portal do Azure

Este artigo percorre como criar um [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) público com uma configuração de IP de front-end zonal. Para entender como as zonas de disponibilidade funcionam com Standard Load Balancer, consulte [zonas de Standard Load Balancer e disponibilidade](load-balancer-standard-availability-zones.md). 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> Suporte para zonas de disponibilidade está disponível para os recursos do Azure selecionados e regiões e famílias de tamanhos VM. Para obter mais informações sobre como começar e quais recursos do Azure, regiões e famílias de tamanho de VM você pode experimentar zonas de disponibilidade, consulte [visão geral do zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, pode contactar-nos no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abrir um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Criar um balanceador de carga com o endereço IP de front-end zonal

1. Em um navegador, navegue até o portal do Azure: [https://portal.azure.com](https://portal.azure.com) e faça logon com sua conta do Azure.
2. No canto superior esquerdo da tela, selecione **criar um recurso** > **rede** > **Load Balancer.**
3. Na página **criar balanceador de carga** , em **nome** , digite **myLoadBalancer**.
4. Em **Tipo**, selecione **Público**.
5. Em SKU, selecione **padrão**.
6. Clique em **escolher um endereço IP público**, clique em **criar novo**e, na página **criar endereço IP público** , em nome, digite **myPublicIPZonal**, para SKU, selecione **padrão**, para zona de disponibilidade, selecione **1**.
    
>[!NOTE] 
> O público IP criado neste passo é de SKU padrão por predefinição.

1. Para **grupo de recursos**, clique em **criar novo**e digite **myResourceGroupZLB** como o nome do grupo de recursos.
1. Para **local**, selecione **Europa Ocidental**e clique em **OK**. Em seguida, o balanceador de carga inicia a implementação e demora alguns minutos a concluir a implementação com êxito.

    ![criar Standard Load Balancer com redundância de zona com o portal do Azure](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre as [zonas de Standard Load Balancer e disponibilidade](load-balancer-standard-availability-zones.md).



