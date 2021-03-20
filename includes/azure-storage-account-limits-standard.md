---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ef9efe389894af7c792e980922ca422e9d05929b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96027046"
---
A tabela seguinte descreve os limites predefinidos para as contas de armazenamento de blob de uso geral da Azure v1, v2, Blob e block blob. O limite *de entrada* refere-se a todos os dados enviados para uma conta de armazenamento. O limite *de saída* refere-se a todos os dados recebidos de uma conta de armazenamento.

> [!NOTE]
> Pode solicitar limites de maior capacidade e entrada. Para solicitar um aumento, contacte [o Suporte Azure](https://azure.microsoft.com/support/faq/).

| Recurso | Limite |
| --- | --- |
| Número de contas de armazenamento por região por subscrição, incluindo contas standard e de armazenamento premium.| 250 |
| Capacidade máxima da conta de armazenamento | 5 PiB <sup>1</sup>|
| Número máximo de recipientes blob, blobs, ações de ficheiros, tabelas, filas, entidades ou mensagens por conta de armazenamento | Sem limite |
| Taxa máxima de pedido<sup>1</sup> por conta de armazenamento | 20.000 pedidos por segundo |
| Entrada máxima<sup>1</sup> por conta de armazenamento (EUA, regiões da Europa) | 10 Gbps |
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

Todas as contas de armazenamento funcionam numa topologia de rede plana, independentemente de quando foram criadas. Para obter mais informações sobre a arquitetura da rede plana Azure Storage e sobre a escalabilidade, consulte [o Microsoft Azure Storage: Um serviço de armazenamento de nuvem altamente disponível com forte consistência](/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets).