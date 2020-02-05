---
title: Soluções Azure VMware (AVS) - Visão geral dos nós
description: Saiba mais sobre os nódosos e conceitos AVS.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 08d8fc3e6f1f2f83cf3c4fee3fdafb0bd07e336c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024930"
---
# <a name="avs-nodes-overview"></a>Visão geral dos avs

Os nódosos são os blocos de construção de uma Nuvem Privada AVS. Um nó é:

* Um hospedeiro de computação de metal nu dedicado onde é instalado um hipervisor VMware ESXi  
* Uma unidade de computação que pode comprar ou reservar para criar Nuvens Privadas AVS
* Disponível para compra ou reserva numa região onde o serviço AVS está disponível

Cria-se uma Nuvem Privada AVS a partir dos nódosos comprados. Para criar uma Nuvem Privada AVS, você precisa de um mínimo de três nós do mesmo SKU. Para expandir uma Nuvem Privada AVS, adicione nós adicionais. Pode adicionar nós a um cluster existente ou criar um novo cluster comprando nós no portal Azure e associando-os ao serviço AVS. Todos os nós adquiridos são visíveis no serviço AVS. 

## <a name="provisioned-nodes"></a>Nódosos provisionados

Os nódosos provisionados proporcionam capacidade de pagamento. O fornecimento de nós ajuda-o a escalar rapidamente o seu cluster VMware a pedido. Pode adicionar nós conforme necessário ou apagar um nó provisionado para reduzir o seu cluster VMware. Os nódosos provisionados são faturados mensalmente e cobrados à subscrição onde são provisionados.

* Se pagar a sua assinatura Azure com cartão de crédito, o cartão é faturado imediatamente.
* Se for cobrado por fatura, as acusações aparecem na sua próxima fatura.

## <a name="vmware-solution-by-avs-nodes-sku"></a>Solução VMware por AVS nodes SKU

Os seguintes tipos de nódisponíveis estão disponíveis para fornecimento ou reserva.

| SKU           | CS28 - Nó                 | CS36 - Nó                 | CS36m - Nó                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Região        | Leste dos EUA, Oeste dos EUA            | Leste dos EUA, Oeste dos EUA            | Europa Ocidental                 |
| CPU           | 2x2.2 GHz, 28 Cores (56 HT) | 2x2,3 GHz, 36 Cores (72 HT) | 2x2,3 GHz, 36 Cores (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Disco de Cache    | NVMe de 1,6 TB                 | 3.2 TB NVMe                 | 3.2 TB NVMe                 |
| Disco de Capacidade | 5.625 TB Cru                | 11.25 TB Cru                | 15.36 TB Cru                |
| Tipo de armazenamento  | Todos os Flash                   | Todos os Flash                   | Todos os Flash                   |

## <a name="limits"></a>Limites

Os seguintes limites do nó aplicam-se às Nuvens Privadas AVS.

| Recurso | Limite |
|----------|-------|
| Número mínimo de nós para criar uma Nuvem Privada AVS | 3 |
| Número máximo de nós em aglomerado numa Nuvem Privada AVS | 16 |
| Número máximo de nós numa Nuvem Privada AVS | 64 |
| Número mínimo de nós num novo aglomerado | 3 |

## <a name="next-steps"></a>Passos seguintes

* Saiba como [comprar nódosos](create-nodes.md)
* Saiba mais sobre [as Nuvens Privadas aVS](cloudsimple-private-cloud.md)
* Aprenda a [fornecer nódosos](create-nodes.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)
