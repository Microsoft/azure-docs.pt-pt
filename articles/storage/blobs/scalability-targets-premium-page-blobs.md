---
title: Metas de escalabilidade para contas de armazenamento de blob de página premium
titleSuffix: Azure Storage
description: Uma conta de armazenamento de blob de página de desempenho premium é otimizada para operações de leitura/escrita. Este tipo de conta de armazenamento confirma um disco não gerido para uma máquina virtual Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 23a176bfa5e3861dbc4ad5c03ea54fc847d3f56b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96922539"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Metas de escalabilidade e desempenho para contas de armazenamento de blob de página premium

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Metas de escala para contas blob de página premium

Uma conta de armazenamento de página blob de desempenho premium é otimizada para operações de leitura/escrita. Este tipo de conta de armazenamento confirma um disco não gerido para uma máquina virtual Azure.

> [!NOTE]
> A Microsoft recomenda a utilização de discos geridos com máquinas virtuais Azure (VMs) se possível. Para obter mais informações sobre discos geridos, consulte [a visão geral do armazenamento do disco Azure para VMs](../../virtual-machines/managed-disks-overview.md).

As contas de armazenamento de blob de página premium têm os seguintes alvos de escalabilidade:

| Capacidade total da conta                            | Largura de banda total para uma conta de armazenamento localmente redundante                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Capacidade do disco: 4 TB (disco individual)/ 35 TB (total acumulado de todos os discos) <br>Capacidade instantânea: 10 TB | Até 50 gigabits por segundo para entrada<sup>1</sup> + saída<sup>2</sup> |

<sup>1</sup> Todos os dados (pedidos) enviados para uma conta de armazenamento

<sup>2</sup> Todos os dados (respostas) que são recebidos de uma conta de armazenamento

Uma conta blob de página premium é uma conta de uso geral configurada para desempenho premium. Recomenda-se a utilização geral das contas de armazenamento v2.

Se estiver a utilizar contas de armazenamento de blob de página premium para discos não geridos e a sua aplicação exceder os objetivos de escalabilidade de uma única conta de armazenamento, então a Microsoft recomenda migrar para discos geridos. Para obter mais informações sobre discos geridos, consulte [a visão geral do armazenamento do disco Azure para VMs](../../virtual-machines/managed-disks-overview.md).

Se não conseguir migrar para discos geridos, então construa a sua aplicação para utilizar várias contas de armazenamento e dividir os seus dados através dessas contas de armazenamento. Por exemplo, se pretender anexar discos de 51-TB através de vários VMs, espalhe-os por duas contas de armazenamento. 35 TB é o limite para uma única conta de armazenamento premium. Certifique-se de que uma única conta de armazenamento de desempenho premium nunca tenha mais de 35 TB de discos a provisionados.

## <a name="see-also"></a>Ver também

- [Metas de escalabilidade e desempenho para contas de armazenamento padrão](../common/scalability-targets-standard-account.md)
- [Metas de escalabilidade para contas de armazenamento de blocos premium](../blobs/scalability-targets-premium-block-blobs.md)
- [Limites e quotas de subscrição do Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md)
