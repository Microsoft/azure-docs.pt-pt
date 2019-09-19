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
ms.openlocfilehash: d3b54941d38424e71ac800d2e750ac9bbc96cde9
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086871"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Solucionar problemas de reinicializações inesperadas de VMs com VHDs anexados

Se uma VM (máquina virtual) do Azure tiver um grande número de VHDs anexados que estão na mesma conta de armazenamento, você poderá exceder as metas de escalabilidade para uma conta de armazenamento individual, fazendo com que a VM seja reinicializada inesperadamente. Verifique as métricas de minuto para a conta de armazenamento (**TotalRequests**/**TotalIngress**/**TotalEgress**) para picos que excedam as metas de escalabilidade para uma conta de armazenamento. Consulte as [métricas mostram um aumento no PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) para obter assistência para determinar se a limitação ocorreu na sua conta de armazenamento.

Em geral, cada operação individual de entrada ou saída em um VHD de uma máquina virtual se traduz em operações **Get Page** ou **Put Page** no blob de página subjacente. Portanto, você pode usar o IOPS estimado para o seu ambiente para ajustar quantos VHDs você pode ter em uma única conta de armazenamento com base no comportamento específico do seu aplicativo. A Microsoft recomenda ter 40 ou menos discos em uma única conta de armazenamento. Consulte [metas de desempenho e escalabilidade do armazenamento do Azure](../../storage/common/storage-scalability-targets.md) para obter detalhes sobre metas de escalabilidade para contas de armazenamento, em particular a taxa de solicitação total e a largura de banda total para o tipo de conta de armazenamento que você está usando.

Se você estiver excedendo as metas de escalabilidade para sua conta de armazenamento, coloque seus VHDs em várias contas de armazenamento para reduzir a atividade em cada conta individual.
