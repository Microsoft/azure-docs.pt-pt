---
title: Solução Azure VMware by CloudSimple - Visão geral dos nódosos
description: Saiba mais sobre os nódosos e conceitos CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65afe26a98a53b00b72a1ea2b49799db2049b727
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024930"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple avisão geral

Os nódosos são os blocos de construção de uma nuvem privada. Um nó é:

* Um hospedeiro de computação de metal nu dedicado onde é instalado um hipervisor VMware ESXi  
* Uma unidade de computação que pode fornecer ou reservar para criar Nuvens Privadas
* Disponível para fornecimento ou reserva numa região onde o serviço CloudSimple está disponível

Cria-se uma Nuvem Privada a partir dos nódosos provisionados. Para criar uma Nuvem Privada, você precisa de um mínimo de três nós do mesmo SKU. Para expandir uma Nuvem Privada, adicione nós adicionais.  Você pode adicionar nós a um cluster existente ou criar um novo cluster, aprovisionando nós no portal Azure e associando-os com o serviço CloudSimple.  Todos os nós provisionados são visíveis sob o serviço CloudSimple.  

## <a name="provisioned-nodes"></a>Nódosos provisionados

Os nódosos provisionados proporcionam capacidade de pagamento. O fornecimento de nós ajuda-o a escalar rapidamente o seu cluster VMware a pedido. Pode adicionar nós conforme necessário ou apagar um nó provisionado para reduzir o seu cluster VMware. Os nódosos provisionados são faturados mensalmente e cobrados à subscrição onde são provisionados.

* Se pagar a sua assinatura Azure com cartão de crédito, o cartão é faturado imediatamente.
* Se for cobrado por fatura, as acusações aparecem na sua próxima fatura.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Solução VMware por CloudSimple acena SKU

Os seguintes tipos de nódisponíveis estão disponíveis para fornecimento ou reserva.

| SKU           | CS28 - Nó                 | CS36 - Nó                 | CS36m - Nó                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Região        | Leste dos EUA, Oeste dos EUA            | Leste dos EUA, Oeste dos EUA            | Europa ocidental                 |
| CPU           | 2x2.2 GHz, 28 Cores (56 HT) | 2x2,3 GHz, 36 Cores (72 HT) | 2x2,3 GHz, 36 Cores (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Cache Disk    | NVMe de 1,6 TB                 | 3.2 TB NVMe                 | 3.2 TB NVMe                 |
| Disco de Capacidade | 5.625 TB Cru                | 11.25 TB Cru                | 15.36 TB Cru                |
| Tipo de armazenamento  | Todos os Flash                   | Todos os Flash                   | Todos os Flash                   |

## <a name="limits"></a>Limites

Os seguintes limites do nó aplicam-se às Nuvens Privadas.

| Recurso | Limite |
|----------|-------|
| Número mínimo de nós para criar uma Nuvem Privada | 3 |
| Número máximo de nós em aglomerado numa nuvem privada | 16 |
| Número máximo de nós numa Nuvem Privada | 64 |
| Número mínimo de nós num novo aglomerado | 3 |

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [fornecer nódosos](create-nodes.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)
