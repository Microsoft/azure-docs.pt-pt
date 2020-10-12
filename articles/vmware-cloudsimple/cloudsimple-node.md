---
title: Solução Azure VMware by CloudSimple - Visão geral dos nodes
description: Conheça os conceitos CloudSimple, incluindo nós, nós a provisionados, uma Nuvem Privada e Solução VMware por CloudSimple nos nós SKUs.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7de4b5faa997d909089daedab7e48e5d5a6de2e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88140841"
---
# <a name="cloudsimple-nodes-overview"></a>Visão geral dos nóns cloudSimple

Nós são os blocos de construção de uma Nuvem Privada. Um nó é:

* Um hospedeiro de computação de metal nu dedicado onde está instalado um hipervisor VMware ESXi  
* Uma unidade de computação que você pode providenciar ou reservar para criar Nuvens Privadas
* Disponível para fornecimento ou reserva numa região onde o serviço CloudSimple está disponível

Cria-se uma Nuvem Privada a partir dos nós a provisionados. Para criar uma Nuvem Privada, você precisa de um mínimo de três nós do mesmo SKU. Para expandir uma Nuvem Privada, adicione nós adicionais.  Pode adicionar nós a um cluster existente ou criar um novo cluster, fornecendo nós no portal Azure e associando-os ao serviço CloudSimple.  Todos os nós a provisionados são visíveis sob o serviço CloudSimple.  

## <a name="provisioned-nodes"></a>Nódoas a provisionadas

Os nós a provisionados fornecem capacidade de pagamento à medida que vai. Os nós de provisionamento ajudam-no a escalar rapidamente o seu cluster VMware a pedido. Pode adicionar nós conforme necessário ou eliminar um nó provisionado para reduzir o seu cluster VMware. Os nós provisões são faturados mensalmente e cobrados à subscrição onde são a provisionados.

* Se pagar a subscrição do Azure com cartão de crédito, o cartão é cobrado imediatamente.
* Se for cobrado por fatura, as taxas aparecem na sua próxima fatura.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Solução VMware por CloudSimple nosdes SKU

Estão disponíveis os seguintes tipos de nós para provisionamento ou reserva.

| SKU           | CS28 - Nó                 | CS36 - Nó                 | CS36m - Nó                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Region        | Leste dos EUA, Oeste dos EUA            | Leste dos EUA, Oeste dos EUA            | Europa Ocidental                 |
| CPU           | 2x2.2 GHz, 28 Cores (56 HT) | 2x2.3 GHz, 36 Cores (72 HT) | 2x2.3 GHz, 36 Cores (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Disco cache    | 1.6-TB NVMe                 | 3.2-TB NVMe                 | 3.2-TB NVMe                 |
| Disco de capacidade | 5.625 TB Raw                | 11.25 TB Cru                | 15.36 TB Raw                |
| Tipo de armazenamento  | All Flash                   | All Flash                   | All Flash                   |

## <a name="limits"></a>Limites

Os seguintes limites de nó aplicam-se às Nuvens Privadas.

| Recurso | Limite |
|----------|-------|
| Número mínimo de nós para criar uma Nuvem Privada | 3 |
| Número máximo de nós em um aglomerado em uma nuvem privada | 16 |
| Número máximo de nós numa Nuvem Privada | 64 |
| Número mínimo de nós num novo cluster | 3 |

## <a name="next-steps"></a>Passos seguintes

* Saiba como [providenciar os nóns](create-nodes.md)
* Saiba mais sobre [Nuvens Privadas](cloudsimple-private-cloud.md)
