---
title: Solução Azure VMware by CloudSimple - Serviço
description: Conheça o serviço CloudSimple com a sua visão geral. A criação do serviço permite-lhe adquirir nós, reservar nós e criar Nuvens Privadas.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ec77209c0995bf5aec0957c8d4d2269f27d743bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88142133"
---
# <a name="cloudsimple-service-overview"></a>Visão geral do serviço CloudSimple

O serviço CloudSimple permite-lhe consumir Azure VMware Solution by CloudSimple.  A criação do serviço permite-lhe adquirir nós, reservar nós e criar Nuvens Privadas.  Cria o serviço CloudSimple em cada região do Azure onde o serviço CloudSimple está disponível. O serviço define a rede de bordas da Azure VMware Solution by CloudSimple. A rede edge suporta serviços que incluem VPN, ExpressRoute e conectividade internet com as suas Nuvens Privadas.

## <a name="gateway-subnet"></a>Sub-rede de gateway

Uma sub-rede de gateway é necessária por serviço CloudSimple e é única na região em que é criada. A sub-rede gateway é utilizada ao criar a rede de arestas e requer um bloco CIDR /28.  O espaço de endereço da sub-rede gateway deve ser único. Não deve sobrepor-se a qualquer rede que comunique com o ambiente CloudSimple. As redes que comunicam com o CloudSimple incluem redes no local e rede virtual Azure.  Uma sub-rede de gateway não pode ser apagada uma vez criada.  A sub-rede gateway é removida quando o serviço é eliminado.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar um serviço CloudSimple no Azure.](quickstart-create-cloudsimple-service.md)
