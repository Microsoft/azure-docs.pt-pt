---
title: Visão geral da solução Azure VMware por CloudSimple nós
description: Saiba mais sobre os nós e conceitos do CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 58af69b401400c7b2f663a91de8bf38bc9a296a3
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877689"
---
# <a name="cloudsimple-nodes-overview"></a>Visão geral dos nós do CloudSimple

Os nós são os blocos de construção de uma nuvem privada. Um nó é:

* Um host de computação bare-metal dedicado em que um hipervisor VMware ESXi está instalado  
* Uma unidade de computação que você pode comprar ou reservar para criar nuvens privadas
* Disponível para compra ou reserva em uma região onde o serviço CloudSimple está disponível

Você cria uma nuvem privada a partir dos nós comprados. Para criar uma nuvem privada, você precisa de um mínimo de três nós do mesmo SKU. Para expandir uma nuvem privada, adicione outros nós.  Você pode adicionar nós a um cluster existente ou criar um novo cluster adquirindo nós no portal do Azure e associando-os ao serviço CloudSimple.  Todos os nós adquiridos são visíveis no serviço CloudSimple.  

## <a name="purchased-nodes"></a>Nós comprados

Os nós adquiridos fornecem capacidade pré-paga. A compra de nós ajuda você a dimensionar rapidamente seu cluster VMware sob demanda. Você pode adicionar nós conforme necessário ou excluir um nó comprado para reduzir verticalmente o cluster do VMware. Os nós adquiridos são cobrados mensalmente e cobrados na assinatura em que são comprados.

* Se você pagar pela sua assinatura do Azure por cartão de crédito, o cartão será cobrado imediatamente.
* Se você for cobrado por fatura, os encargos serão exibidos na próxima fatura.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Solução VMware por SKU de nós CloudSimple

Os seguintes tipos de nós estão disponíveis para compra ou reserva.

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

* Saiba como [comprar nós](create-nodes.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)
