---
title: Solução de VMware pelo serviço CloudSimple
description: Descreve a visão geral do serviço de CloudSimple e conceitos.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0bebc68129ee2ff79241bcefec1ce0c3ca0b472d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595320"
---
# <a name="cloudsimple-service-overview"></a>Descrição geral do serviço CloudSimple

Com o serviço de CloudSimple, pode usar a solução de VMware do Azure por CloudSimple. Depois de criar o serviço, pode aprovisionar nós de reserva de nós e criar nuvens privadas. Criar o serviço de CloudSimple em cada região do Azure onde o serviço de CloudSimple está disponível. O serviço define a rede de borda da solução de VMware do Azure por CloudSimple. A rede de borda oferece suporte a serviços que incluem a conectividade de internet para nuvens privadas, Azure ExpressRoute e VPN.

## <a name="gateway-subnet"></a>Sub-rede de gateway

Uma sub-rede de gateway é necessária para cada serviço CloudSimple e é exclusiva para a região no qual é criado. A sub-rede do gateway é utilizada quando criar a rede de borda e necessita de/28 bloco CIDR. O espaço de endereços da sub-rede de gateway tem de ser exclusivo. Ele não pode sobrepor com qualquer rede que se comunica com o ambiente de CloudSimple. As redes que comunicam com CloudSimple incluem redes no local e redes virtuais do Azure. Não é possível eliminar uma sub-rede de gateway depois de criado. A sub-rede do gateway é removida quando o serviço for eliminado.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar um serviço de CloudSimple no Azure](quickstart-create-cloudsimple-service.md).
