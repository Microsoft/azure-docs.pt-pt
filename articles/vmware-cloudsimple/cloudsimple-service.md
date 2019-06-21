---
title: Solução de VMware pelo serviço CloudSimple
description: Descreve a visão geral do serviço de CloudSimple e conceitos.
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6a4c0bc6070d372a279b74f81ac1f84f565559c3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165232"
---
# <a name="cloudsimple-service-overview"></a>Descrição geral do serviço CloudSimple

Com o serviço de CloudSimple, pode usar a solução de VMware do Azure por CloudSimple. Depois de criar o serviço, pode aprovisionar nós de reserva de nós e criar nuvens privadas. Criar o serviço de CloudSimple em cada região do Azure onde o serviço de CloudSimple está disponível. O serviço define a rede de borda da solução de VMware do Azure por CloudSimple. A rede de borda oferece suporte a serviços que incluem a conectividade de internet para nuvens privadas, Azure ExpressRoute e VPN.

## <a name="gateway-subnet"></a>Sub-rede de gateway

Uma sub-rede de gateway é necessária para cada serviço CloudSimple e é exclusiva para a região no qual é criado. A sub-rede do gateway é utilizada quando criar a rede de borda e necessita de/28 bloco CIDR. O espaço de endereços da sub-rede de gateway tem de ser exclusivo. Ele não pode sobrepor com qualquer rede que se comunica com o ambiente de CloudSimple. As redes que comunicam com CloudSimple incluem redes no local e redes virtuais do Azure. Não é possível eliminar uma sub-rede de gateway depois de criado. A sub-rede do gateway é removida quando o serviço for eliminado.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [criar um serviço de CloudSimple no Azure](quickstart-create-cloudsimple-service.md).
