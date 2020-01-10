---
title: Metas de escalabilidade para contas de armazenamento de blobs de páginas Premium
titleSuffix: Azure Storage
description: Uma conta de armazenamento de blob de página de desempenho Premium é otimizada para operações de leitura/gravação. Esse tipo de conta de armazenamento faz backup de um disco não gerenciado para uma máquina virtual do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 39153b05b16395cf2dfc0511efa601d1cc4da550
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749702"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Escalabilidade e metas de desempenho para contas de armazenamento de blob de página Premium

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Destinos de escala para contas de blob de páginas Premium

Uma conta de armazenamento de blob de página de desempenho Premium é otimizada para operações de leitura/gravação. Esse tipo de conta de armazenamento faz backup de um disco não gerenciado para uma máquina virtual do Azure.

> [!NOTE]
> A Microsoft recomenda o uso de discos gerenciados com VMs (máquinas virtuais) do Azure, se possível. Para obter mais informações sobre discos gerenciados, consulte [visão geral de armazenamento em disco do Azure para VMs do Windows](../../virtual-machines/windows/managed-disks-overview.md).

As contas de armazenamento de blobs de páginas Premium têm as seguintes metas de escalabilidade:

| Capacidade total da conta                            | Largura de banda total para uma conta de armazenamento com redundância local                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Capacidade do disco: 35 TB <br>Capacidade do instantâneo: 10 TB | Até 50 gigabits por segundo para entrada<sup>1</sup> + saída<sup>2</sup> |

<sup>1</sup> todos os dados (solicitações) que são enviados para uma conta de armazenamento

<sup>2</sup> todos os dados (respostas) que são recebidos de uma conta de armazenamento

Uma conta de blob de páginas Premium é uma conta de uso geral configurada para desempenho premium. As contas de armazenamento de uso geral v2 são recomendadas.

Se você estiver usando contas de armazenamento de blobs de páginas Premium para discos não gerenciados e seu aplicativo exceder as metas de escalabilidade de uma única conta de armazenamento, a Microsoft recomenda a migração para o Managed disks. Para obter mais informações sobre discos gerenciados, consulte [visão geral de armazenamento em disco do Azure para VMs do Windows](../../virtual-machines/windows/managed-disks-overview.md) ou [visão geral armazenamento em disco do Azure para VMs do Linux](../../virtual-machines/linux/managed-disks-overview.md).

Se você não puder migrar para o Managed disks, crie seu aplicativo para usar várias contas de armazenamento e particione seus dados entre essas contas de armazenamento. Por exemplo, se você quiser anexar discos de 51 TB em várias VMs, distribua-os entre duas contas de armazenamento. 35 TB é o limite para uma única conta de armazenamento Premium. Certifique-se de que uma única conta de armazenamento de desempenho premium nunca tenha mais de 35 TB de discos provisionados.

## <a name="see-also"></a>Ver também

- [Escalabilidade e metas de desempenho para contas de armazenamento Standard](../common/scalability-targets-standard-account.md)
- [Metas de escalabilidade para contas de armazenamento de blob de blocos Premium](../blobs/scalability-targets-premium-block-blobs.md)
- [Cotas e limites de assinatura do Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md)
