---
title: Resolução de problemas inesperados de VMs com VHDs anexados em VMs Azure Microsoft Docs
description: Como resolver reboots inesperados de VMs.
keywords: ligação ssh recusado, erro ssh, azure ssh, ligação SSH falhou
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 3a06db1afd130d936af868d0d20632c3ec4fbfd2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75358531"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Resolução de problemas inesperados de VMs com VHDs anexados

Se uma Máquina Virtual Azure (VM) tiver um grande número de VHDs anexados que estão na mesma conta de armazenamento, poderá exceder os objetivos de escalabilidade de uma conta de armazenamento individual, fazendo com que o VM reinicie inesperadamente. Verifique as métricas minúsculas da conta de armazenamento **(TotalRequests** / **TotalIngress** / **TotalEgress**) para obter picos que excedam os objetivos de escalabilidade de uma conta de armazenamento. Consulte [as Métricas que mostram um aumento no PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) para assistência na determinação de se ocorreu estrangulamento na sua conta de armazenamento.

Em geral, cada operação individual de entrada ou saída de um VHD de uma Máquina Virtual traduz-se em operações **get page** ou **put page** na bolha de página subjacente. Portanto, pode utilizar o IOPS estimado para o seu ambiente para sintonizar quantos VHDs pode ter numa única conta de armazenamento com base no comportamento específico da sua aplicação. A Microsoft recomenda ter 40 ou menos discos numa única conta de armazenamento. Para obter mais informações sobre os objetivos de escalabilidade das contas de armazenamento padrão, consulte [os objetivos de escalabilidade para contas de armazenamento padrão.](../../storage/common/scalability-targets-standard-account.md) Para obter mais informações sobre os alvos de escalabilidade para contas de armazenamento de blob de página premium, consulte [os alvos de Escalaability para contas de armazenamento de blob de página premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).

Se estiver a exceder os objetivos de escalabilidade da sua conta de armazenamento, coloque os seus VHDs em várias contas de armazenamento para reduzir a atividade em cada conta individual.
