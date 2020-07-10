---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/08/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 509568b143c9fbbf236139ca83cb55b0ef39beb0
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145938"
---
A tabela seguinte descreve os limites predefinidos para as contas de armazenamento de blob de uso geral da Azure v1, v2, Blob e block blob. O limite *de entrada* refere-se a todos os dados enviados para uma conta de armazenamento. O limite *de saída* refere-se a todos os dados recebidos de uma conta de armazenamento.

| Recurso | Limite |
| --- | --- |
| Número de contas de armazenamento por região por subscrição, incluindo contas standard e de armazenamento premium.| 250 |
| Capacidade máxima da conta de armazenamento | 5 PiB <sup>1</sup>|
| Número máximo de recipientes blob, blobs, ações de ficheiros, tabelas, filas, entidades ou mensagens por conta de armazenamento | Sem limite |
| Taxa máxima de pedido<sup>1</sup> por conta de armazenamento | 20.000 pedidos por segundo |
| Entrada máxima<sup>1</sup> por conta de armazenamento (EUA, regiões da Europa) | 10 Gbps |
| Entrada máxima<sup>1</sup> por conta de armazenamento (regiões que não os EUA e a Europa) | 5 Gbps se ra-GRS/GRS estiver ativado, 10 Gbps para LRS/ZRS<sup>2</sup> |
| Saída máxima para as contas de armazenamento v2 e Blob (todas as regiões) | 50 Gbps |
| Saída máxima para contas de armazenamento v1 para fins gerais (regiões dos EUA) | 20 Gbps se ra-GRS/GRS estiver ativado, 30 Gbps para LRS/ZRS<sup>2</sup> |
| Saída máxima para as contas de armazenamento v1 para fins gerais (regiões não americanas) | 10 Gbps se ra-GRS/GRS estiver ativado, 15 Gbps para LRS/ZRS<sup>2</sup> |
| Número máximo de regras de rede virtuais por conta de armazenamento | 200 |
| Número máximo de regras de endereço IP por conta de armazenamento | 200 |

<sup>1</sup> As contas padrão de armazenamento Azure suportam limites de capacidade mais elevados e limites mais elevados para a entrada por pedido. Para solicitar um aumento dos limites de conta, contacte [o Suporte Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Se a sua conta de armazenamento tiver acesso de leitura ativado com armazenamento geo-redundante (RA-GRS) ou armazenamento de geo-zona redundante (RA-GZRS), então os alvos de saída para a localização secundária são idênticos aos da localização primária. Para obter mais informações, consulte [a replicação do Azure Storage](../articles/storage/common/storage-redundancy.md).

> [!NOTE]
> A Microsoft recomenda que utilize uma conta de armazenamento v2 para a maioria dos cenários. Pode facilmente atualizar uma conta de armazenamento V1 ou Azure Blob para uma conta V2 para fins gerais, sem tempo de inatividade e sem necessidade de copiar dados. Para obter mais informações, consulte [upgrade para uma conta de armazenamento v2 para fins gerais.](../articles/storage/common/storage-account-upgrade.md)

Se as necessidades da sua aplicação excederem os objetivos de escalabilidade de uma única conta de armazenamento, pode construir a sua aplicação para utilizar várias contas de armazenamento. Em seguida, pode dividir os seus objetos de dados através dessas contas de armazenamento. Para obter informações sobre os preços de volume, consulte [os preços de armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/).

Todas as contas de armazenamento funcionam numa topologia de rede plana, independentemente de quando foram criadas. Para obter mais informações sobre a arquitetura da rede plana Azure Storage e sobre a escalabilidade, consulte [o Microsoft Azure Storage: Um serviço de armazenamento de nuvem altamente disponível com forte consistência](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
