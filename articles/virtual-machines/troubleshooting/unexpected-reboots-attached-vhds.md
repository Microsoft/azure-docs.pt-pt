---
title: Solucionar problemas de reinicializações inesperadas de VMs com VHDs anexados em VMs do Azure | Microsoft Docs
description: Como solucionar problemas de reinicializações inesperadas de VMs.
keywords: conexão SSH recusada, erro de SSH, Azure SSH, falha na conexão SSH
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 3a06db1afd130d936af868d0d20632c3ec4fbfd2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358531"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Solucionar problemas de reinicializações inesperadas de VMs com VHDs anexados

Se uma VM (máquina virtual) do Azure tiver um grande número de VHDs anexados que estão na mesma conta de armazenamento, você poderá exceder as metas de escalabilidade para uma conta de armazenamento individual, fazendo com que a VM seja reinicializada inesperadamente. Verifique as métricas de minuto para a conta de armazenamento (**TotalRequests**/**TotalIngress**/**TotalEgress**) para picos que excedam as metas de escalabilidade para uma conta de armazenamento. Consulte as [métricas mostram um aumento no PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) para obter assistência para determinar se a limitação ocorreu na sua conta de armazenamento.

Em geral, cada operação individual de entrada ou saída em um VHD de uma máquina virtual se traduz em operações **Get Page** ou **Put Page** no blob de página subjacente. Portanto, você pode usar o IOPS estimado para o seu ambiente para ajustar quantos VHDs você pode ter em uma única conta de armazenamento com base no comportamento específico do seu aplicativo. A Microsoft recomenda ter 40 ou menos discos em uma única conta de armazenamento. Para obter mais informações sobre metas de escalabilidade para contas de armazenamento padrão, consulte [metas de escalabilidade para contas de armazenamento padrão](../../storage/common/scalability-targets-standard-account.md). Para obter mais informações sobre metas de escalabilidade para contas de armazenamento de blob de página Premium, consulte [metas de escalabilidade para contas de armazenamento de blob de páginas Premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).

Se você estiver excedendo as metas de escalabilidade para sua conta de armazenamento, coloque seus VHDs em várias contas de armazenamento para reduzir a atividade em cada conta individual.
