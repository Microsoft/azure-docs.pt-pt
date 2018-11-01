---
title: Criar um público Balanceador de carga Standard com frontend de endereço IP público com redundância de zona com o portal do Azure | Documentos da Microsoft
description: Saiba como criar um público Balanceador de carga Standard com frontend de endereço IP público com redundância de zona com o portal do Azure
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 70514433d11bbe7606d75a3e2c1f6dffc251621f
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740948"
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Criar um público Balanceador de carga Standard com frontend de endereço IP público com redundância de zona com o portal do Azure

Este artigo mostra-se através da criação de um público [Balanceador de carga Standard](https://aka.ms/azureloadbalancerstandard) com um front-end com redundância de zona utilizando um endereço IP público Standard. Um endereço IP de front-end único num Balanceador de carga Standard é com redundância de zona por predefinição.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
 Suporte para zonas de disponibilidade está disponível para os recursos do Azure selecionados e regiões e famílias de tamanhos VM. Para obter mais informações sobre como começar a utilizar e quais recursos do Azure, regiões e famílias de tamanhos VM pode experimentar com as zonas de disponibilidade, consulte [descrição geral das zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, pode contactar-nos no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abrir um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Criar um balanceador de carga com redundância de zona

1. Num browser, navegue para o portal do Azure: [ http://portal.azure.com ](http://portal.azure.com) e início de sessão com a sua conta do Azure.
2. No canto superior esquerdo do ecrã, selecione **criar um recurso** > **rede** > **Balanceador de carga.**
3. Na **criar Balanceador de carga** página, em **nome** tipo **myLoadBalancer**.
4. Em **Tipo**, selecione **Público**.
5. Em SKU, selecione **padrão**.
6. Clique em **endereço IP público**, clique em **criar nova**e, na **Criar endereço IP público** página, em nome, tipo **myPublicIPStandard**.
    >[!NOTE] 
    > O público IP criado neste passo é do Standard SKU e é com redundância de zona por predefinição. 
8. Sob **localização**, selecione **e.u.a. Leste 2**e, em seguida, clique em **OK**. Em seguida, o balanceador de carga inicia a implementação e demora alguns minutos a concluir a implementação com êxito.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [zonas de disponibilidade e o Balanceador de carga Standard](load-balancer-standard-availability-zones.md).



