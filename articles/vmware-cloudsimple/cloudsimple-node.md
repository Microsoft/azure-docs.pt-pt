---
title: Visão geral dos nós da solução VMware por CloudSimple – Azure
description: Saiba mais sobre os nós e conceitos do CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 346bd046810ebae5142bc23400419857000d0c8e
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812572"
---
# <a name="cloudsimple-nodes-overview"></a>Visão geral dos nós do CloudSimple

Um nó é:

* Um host de computação bare-metal dedicado no qual VMware ESXi hipervisor está instalado  
* Uma unidade de computação que você pode provisionar ou reservar para criar nuvens privadas  
* Disponível para provisionar ou reservar em uma região onde o serviço CloudSimple está disponível

Os nós são blocos de construção de uma nuvem privada.  Para criar uma nuvem privada, você precisa de um mínimo de três nós do mesmo SKU.  Para expandir uma nuvem privada, adicione outros nós.  Você pode adicionar nós a um cluster existente. Ou, você pode criar um novo cluster Provisionando nós no portal do Azure e associando-os ao serviço CloudSimple.  Todos os nós provisionados são visíveis no serviço CloudSimple.  Você cria uma nuvem privada a partir dos nós provisionados no portal do CloudSimple.

## <a name="provisioned-nodes"></a>Nós provisionados

Os nós provisionados fornecem capacidade de pagamento conforme o uso. O provisionamento de nós ajuda você a dimensionar rapidamente seu cluster VMware sob demanda. Você pode adicionar nós conforme necessário ou excluir um nó provisionado para reduzir verticalmente o cluster do VMware. os nós provisionados são cobrados mensalmente e cobrados para a assinatura onde são provisionados:

* Se você pagar pela sua assinatura do Azure por cartão de crédito, o cartão será cobrado imediatamente.
* Se você for cobrado por fatura, os encargos serão exibidos na próxima fatura.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Solução VMware por SKU de nós CloudSimple

Os seguintes nós de tipos estão disponíveis para provisionamento ou reserva.

| SKU | CS28-nó | CS36-nó |
|-----|-------------|-------------|
| CPU | 2x 2,2 GHz, 28 núcleos (56 HT) | 2x 2,3 GHz, 36 núcleos (72 HT) |
| RAM | 256 GB | 512 GB |
| Disco de cache |  1,6-TB NVMe | 3,2-TB NVMe |
| Disco de capacidade | 5,625 TB brutos | 11,25 TB brutos |
| Tipo de armazenamento | Todos os flash | Todos os flash |

## <a name="limits"></a>Limites

Os limites de nó a seguir se aplicam a nuvens privadas.

| Resource | Limite |
|----------|-------|
| Número mínimo de nós para criar uma nuvem privada | 3 |
| Número máximo de nós em um cluster em uma nuvem privada | 16 |
| Número máximo de nós em uma nuvem privada | 64 |
| Número mínimo de nós em um novo cluster | 3 |

## <a name="next-steps"></a>Passos Seguintes

* Saiba como provisionar [nós](create-nodes.md)
* Saiba mais sobre a [nuvem privada](cloudsimple-private-cloud.md)