---
title: Soluções de VMware por CloudSimple - serviço
description: Descreve a visão geral do serviço de CloudSimple e conceitos.
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f7b4be0ff3997e27dd5b5321dd44b5006ae52102
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358112"
---
# <a name="cloudsimple-service-overview"></a>Descrição geral do serviço de CloudSimple

O serviço de CloudSimple permite-lhe consumir a solução de VMware do Azure por CloudSimple.  Criação do serviço permite-lhe adquirir nós, reserva de nós e criar nuvens privadas.  Criar o serviço de CloudSimple em cada região do Azure onde o serviço de CloudSimple está disponível.  O serviço define a rede de borda da solução de VMware do Azure por CloudSimple.  A rede de borda oferece suporte a serviços que incluem a VPN, ExpressRoute e de ligação à internet para as nuvens privadas.

## <a name="gateway-subnet"></a>Sub-rede de gateway

Uma sub-rede de gateway é necessária para cada serviço CloudSimple e é exclusiva para a região no qual é criado. A sub-rede do gateway é utilizada quando criar a rede de borda e necessita de/28 bloco CIDR.  O espaço de endereços da sub-rede de gateway tem de ser exclusivo. Ele não pode sobrepor com qualquer rede que se comunica com o ambiente de CloudSimple. As redes que comunicam com CloudSimple incluem redes no local e de rede virtual do Azure.  Não é possível eliminar uma sub-rede de gateway depois de criado.  A sub-rede do gateway é removida quando o serviço for eliminado.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [criar um serviço de CloudSimple no Azure](quickstart-create-cloudsimple-service.md)