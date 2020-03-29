---
title: Resolução de problemas reiniciares inesperadas de VMs com VHDs anexados em VMs Azure [ Microsoft Docs
description: Como resolver reinicializações inesperadas de VMs.
keywords: ssh conexão recusada, erro ssh, ssh azul, conexão SSH falhou
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 3a06db1afd130d936af868d0d20632c3ec4fbfd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75358531"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Reinicializações inesperadas de VMs com VHDs anexados

Se uma Máquina Virtual Azure (VM) tiver um grande número de VHDs anexados que estão na mesma conta de armazenamento, poderá exceder os objetivos de escalabilidade para uma conta de armazenamento individual, fazendo com que o VM reinicie inesperadamente. Verifique as métricas minúsculas da conta de armazenamento **(TotalRequests**/**TotalIngress**/**TotalEgress)** para obter picos que excedam os objetivos de escalabilidade para uma conta de armazenamento. As [Métricas mostram um aumento do PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) para assistência na determinação da aceleração na sua conta de armazenamento.

Em geral, cada operação individual de entrada ou saída num VHD de uma Máquina Virtual traduz-se para obter operações de **página** ou página de **colocação** na bolha de página subjacente. Portanto, você pode usar o IOPS estimado para o seu ambiente para afinar quantos VHDs você pode ter em uma única conta de armazenamento com base no comportamento específico da sua aplicação. A Microsoft recomenda ter 40 ou menos discos numa única conta de armazenamento. Para obter mais informações sobre os objetivos de escalabilidade das contas de armazenamento padrão, consulte os objetivos de [escalabilidade para as contas de armazenamento padrão](../../storage/common/scalability-targets-standard-account.md). Para obter mais informações sobre os alvos de escalabilidade para contas de armazenamento de blob de página premium, consulte [os alvos de escalabilidade para contas](../../storage/blobs/scalability-targets-premium-page-blobs.md)de armazenamento de blob de página premium .

Se exceder os alvos de escalabilidade para a sua conta de armazenamento, coloque os seus VHDs em várias contas de armazenamento para reduzir a atividade em cada conta individual.
