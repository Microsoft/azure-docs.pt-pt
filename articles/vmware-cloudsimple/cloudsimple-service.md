---
title: Solução Azure VMware by CloudSimple - Serviço
description: Fornece uma visão geral do serviço e conceitos CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024962"
---
# <a name="cloudsimple-service-overview"></a>Visão geral do serviço CloudSimple

O serviço CloudSimple permite-lhe consumir a Solução Azure VMware pela CloudSimple.  A criação do serviço permite-lhe comprar nós, reservar nós e criar Nuvens Privadas.  Cria o serviço CloudSimple em cada região do Azure onde o serviço CloudSimple está disponível. O serviço define a rede de bordas da Solução Azure VMware pela CloudSimple. A rede edge suporta serviços que incluem VPN, ExpressRoute e conectividade de internet com as suas Nuvens Privadas.

## <a name="gateway-subnet"></a>Sub-rede de gateway

Uma subnet gateway é necessária por serviço CloudSimple e é única para a região em que é criada. A sub-rede gateway é utilizada ao criar a rede de bordas e requer um bloco CIDR /28.  O espaço de endereço da sub-rede gateway deve ser único. Não deve sobrepor-se a qualquer rede que comunique com o ambiente CloudSimple. As redes que comunicam com a CloudSimple incluem redes no local e rede virtual Azure.  Uma sub-rede de gateway não pode ser apagada uma vez criada.  A sub-rede gateway é removida quando o serviço é eliminado.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar um serviço CloudSimple no Azure](quickstart-create-cloudsimple-service.md).
