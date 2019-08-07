---
title: Solução VMware por serviço CloudSimple
description: Descreve a visão geral do serviço e dos conceitos do CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a43fbebb21be82fd751778d6fec95e0ee9c346ad
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812501"
---
# <a name="cloudsimple-service-overview"></a>Visão geral do serviço CloudSimple

Com o serviço CloudSimple, você pode usar a solução VMware do Azure da CloudSimple. Depois de criar o serviço, você pode provisionar nós, reservar nós e criar nuvens privadas. Você cria o serviço CloudSimple em cada região do Azure onde o serviço CloudSimple está disponível. O serviço define a rede de borda da solução do Azure VMware por CloudSimple. A rede de borda dá suporte a serviços que incluem VPN, Azure ExpressRoute e conectividade com a Internet para suas nuvens privadas.

## <a name="gateway-subnet"></a>Sub-rede do gateway

Uma sub-rede de gateway é necessária por serviço CloudSimple e é exclusiva para a região na qual ela é criada. A sub-rede de gateway é usada quando você cria a rede de borda e requer um bloco CIDR/28. O espaço de endereço de sub-rede de gateway deve ser exclusivo. Ele não deve se sobrepor a nenhuma rede que se comunica com o ambiente CloudSimple. As redes que se comunicam com o CloudSimple incluem redes locais e redes virtuais do Azure. Uma sub-rede de gateway não pode ser excluída depois de ser criada. A sub-rede de gateway é removida quando o serviço é excluído.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [criar um serviço CloudSimple no Azure](quickstart-create-cloudsimple-service.md).
