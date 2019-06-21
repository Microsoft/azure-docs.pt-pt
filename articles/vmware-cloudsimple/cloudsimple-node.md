---
title: Descrição geral de nós para solução de VMware ao CloudSimple - Azure
description: Saiba mais sobre conceitos e CloudSimple nós.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fb82e31d58d9955efc3b147eccf2b82b8768aeee
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165807"
---
# <a name="cloudsimple-nodes-overview"></a>Descrição geral de nós CloudSimple

Um nó é:

* Um dedicado bare-metal de computação onde está instalado o VMware ESXi hipervisor do anfitrião  
* Uma unidade de computação que pode aprovisionar ou de reserva para criar nuvens privadas  
* Disponível para aprovisionar ou reservar numa região em que o serviço de CloudSimple está disponível

Nós são blocos de construção de uma nuvem privada.  Para criar uma nuvem privada, é necessário um mínimo de três nós do mesmo SKU.  Para expandir uma nuvem privada, adicione nós adicionais.  Pode adicionar nós a um cluster existente. Em alternativa, pode criar um novo cluster ao aprovisionar nós no portal do Azure e associá-las com o serviço de CloudSimple.  Aprovisionado de todos os nós são visíveis no serviço CloudSimple.  Criar uma nuvem privada a partir de nós no Portal de CloudSimple aprovisionados.

## <a name="provisioned-nodes"></a>Nós aprovisionado

Nós aprovisionado fornecem a capacidade de pay as you go. Aprovisionamento nós ajuda a dimensionar rapidamente o cluster do VMware a pedido. Pode adicionar nós conforme necessário, ou eliminar um nó aprovisionado para reduzir verticalmente o seu cluster do VMware. aprovisionado os nós são faturados mensalmente e cobrados na subscrição em que estão aprovisionados:

* Se optar pelo pagamento da sua subscrição do Azure com cartão de crédito, cartão é cobrado imediatamente.
* Se é cobrado por fatura, os encargos são apresentados na sua próxima nota fiscal.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Solução de VMware por nós de CloudSimple SKU

Os seguintes nós de tipos estão disponíveis para o aprovisionamento ou de reserva.

| SKU | CS28 - nó | CS36 - nó |
|-----|-------------|-------------|
| CPU | 2x2.2 GHz, 28 núcleos (56 HT) | 2x2.3 GHz, de 36 núcleos (72 HT) |
| RAM | 256 GB | 512 GB |
| Cache Disk |  NVMe 1.6 TB | 3.2-TB NVMe |
| Disco de capacidade | 5.625 TB não processado | 11,25 TB não processado |
| Tipo de armazenamento | Todos os Flash | Todos os Flash |

## <a name="limits"></a>Limits

Os limites de nó seguintes aplicam-se em nuvens privadas.

| Resource | Limite |
|----------|-------|
| Número mínimo de nós para criar uma nuvem privada | 3 |
| Número máximo de nós num Cluster numa nuvem privada | 16 |
| Número máximo de nós numa nuvem privada | 64 |
| Número mínimo de nós num Cluster novo | 3 |

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [aprovisionar nós](create-nodes.md)
* Saiba mais sobre [nuvem privada](cloudsimple-private-cloud.md)