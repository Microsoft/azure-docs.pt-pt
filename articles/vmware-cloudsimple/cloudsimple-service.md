---
title: Azure VMware Solutions (AVS) - Serviço
description: Fornece uma visão geral do serviço e conceitos AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d09c8c34093e7d33122f934138ff9fdf4842508e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024962"
---
# <a name="avs-service-overview"></a>Visão geral do serviço AVS

O serviço AVS permite-lhe consumir a Solução Azure VMware pela AVS. A criação do serviço permite-lhe comprar nós, reservar nós e criar Nuvens Privadas AVS. Cria o serviço AVS em cada região do Azure onde o serviço AVS está disponível. O serviço define a rede de bordas da Azure VMware Solution pela AVS. A rede edge suporta serviços que incluem VPN, ExpressRoute e conectividade de internet com as suas Nuvens Privadas AVS.

## <a name="gateway-subnet"></a>Sub-rede de gateway

Uma subnet gateway é necessária por serviço AVS e é única para a região em que é criada. A sub-rede de gateway é usada ao criar a rede de borda e requer um bloco CIDR/28. O espaço de endereço de sub-rede de gateway deve ser exclusivo. Não deve sobrepor-se a qualquer rede que comunique com o ambiente AVS. As redes que comunicam com a AVS incluem redes no local e rede virtual Azure. Uma sub-rede de gateway não pode ser apagada uma vez criada. A sub-rede gateway é removida quando o serviço é eliminado.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [criar um serviço AVS no Azure.](quickstart-create-cloudsimple-service.md)
