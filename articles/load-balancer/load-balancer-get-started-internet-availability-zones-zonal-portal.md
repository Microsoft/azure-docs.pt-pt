---
title: Criar um balanceador de carga com um front-end da zonal - portal do Azure
titlesuffix: Azure Load Balancer
description: Saiba como criar um balanceador de carga Standard com zonal front-end com o portal do Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: c81ff5ea330c4c0ba26a92a3b5399cfa961e4b2b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57856863"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Criar um balanceador de carga Standard com o front-end zonal através do portal do Azure

Este artigo mostra-se através da criação de um público [Balanceador de carga Standard](https://aka.ms/azureloadbalancerstandard) com uma configuração de IP de front-end zonais. Para compreender o funcionam das zonas de disponibilidade com o Balanceador de carga Standard, veja [zonas de disponibilidade e o Balanceador de carga Standard](load-balancer-standard-availability-zones.md). 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> Suporte para zonas de disponibilidade está disponível para os recursos do Azure selecionados e regiões e famílias de tamanhos VM. Para obter mais informações sobre como começar a utilizar e quais recursos do Azure, regiões e famílias de tamanhos VM pode experimentar com as zonas de disponibilidade, consulte [descrição geral das zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, pode contactar-nos no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abrir um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Criar um balanceador de carga com o endereço IP de front-end zonal

1. Num browser, navegue para o portal do Azure: [ https://portal.azure.com ](https://portal.azure.com) e início de sessão com a sua conta do Azure.
2. No canto superior esquerdo do ecrã, selecione **criar um recurso** > **rede** > **Balanceador de carga.**
3. Na **criar Balanceador de carga** página, em **nome** tipo **myLoadBalancer**.
4. Em **Tipo**, selecione **Público**.
5. Em SKU, selecione **padrão**.
6. Clique em **escolher um endereço IP público**, clique em **criar nova**e, na **Criar endereço IP público** página, em nome, tipo **myPublicIPZonal**, para o SKU, selecione **padrão**, para a zona de disponibilidade, selecione **1**.
    
>[!NOTE] 
> O público IP criado neste passo é de SKU padrão por predefinição.

1. Para **grupo de recursos**, clique em **criar nova**e, em seguida, escreva **myResourceGroupZLB** como o nome do grupo de recursos.
1. Para **localização**, selecione **Europa Ocidental**e, em seguida, clique em **OK**. Em seguida, o balanceador de carga inicia a implementação e demora alguns minutos a concluir a implementação com êxito.

    ![Criar Balanceador de carga Standard com redundância de zona com o portal do Azure](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [zonas de disponibilidade e o Balanceador de carga Standard](load-balancer-standard-availability-zones.md).



