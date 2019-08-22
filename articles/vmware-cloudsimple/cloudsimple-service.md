---
title: Solução do Azure VMware por CloudSimple-Service
description: Fornece uma visão geral do serviço e dos conceitos do CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877664"
---
# <a name="cloudsimple-service-overview"></a>Visão geral do serviço CloudSimple

O serviço CloudSimple permite que você consuma a solução do Azure VMware pelo CloudSimple.  A criação do serviço permite que você compre nós, Reserve nós e crie nuvens privadas.  Você cria o serviço CloudSimple em cada região do Azure onde o serviço CloudSimple está disponível. O serviço define a rede de borda da solução do Azure VMware por CloudSimple. A rede de borda dá suporte a serviços que incluem conectividade VPN, ExpressRoute e Internet para suas nuvens privadas.

## <a name="gateway-subnet"></a>Sub-rede do gateway

Uma sub-rede de gateway é necessária por serviço CloudSimple e é exclusiva para a região na qual ela é criada. A sub-rede de gateway é usada ao criar a rede de borda e requer um bloco CIDR/28.  O espaço de endereço de sub-rede de gateway deve ser exclusivo. Ele não deve se sobrepor a nenhuma rede que se comunica com o ambiente CloudSimple. As redes que se comunicam com o CloudSimple incluem redes locais e rede virtual do Azure.  Uma sub-rede de gateway não pode ser excluída depois de ser criada.  A sub-rede de gateway é removida quando o serviço é excluído.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar um serviço CloudSimple no Azure](quickstart-create-cloudsimple-service.md).
