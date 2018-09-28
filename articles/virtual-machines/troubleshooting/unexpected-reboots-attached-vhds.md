---
title: Resolver problemas de reinícios inesperados de VMs com VHDs ligadas em VMs do Azure | Documentos da Microsoft
description: Como resolver problemas de reinícios inesperados de VMs.
keywords: SSH ligação recusada, ssh erro, azure ssh, ligação de SSH falhou
services: virtual-machines
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: cffc6166ab7d0864646421b35fbecafdd80eb27e
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414763"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Resolver problemas de reinícios inesperados de VMs com VHDs ligadas

Se uma Máquina Virtual do Azure (VM) tem um grande número de VHDs ligadas que estão na mesma conta de armazenamento, pode exceder os destinos de escalabilidade para uma conta de armazenamento individuais, fazendo com que a VM reiniciar inesperadamente. Verifique a métrica de minuto para a conta de armazenamento (**TotalRequests**/**TotalIngress**/**TotalEgress**) para picos que excedem o destinos de escalabilidade para uma conta de armazenamento. Ver [as métricas apresentam um aumento do percentthrottlingerror](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) para obter ajuda a determinar se a limitação ocorreu na sua conta de armazenamento.

Em geral, cada entrada individual ou a operação de saída num VHD a partir de uma Máquina Virtual se traduz em **obter a página** ou **colocar página** operações no blob de página subjacente. Portanto, pode usar o IOPS estimado para o seu ambiente para otimizar os VHDs quantos pode ter numa única conta de armazenamento baseado no comportamento específico da sua aplicação. A Microsoft recomenda ter discos de 40 ou menos numa única conta de armazenamento. Ver [metas de desempenho e escalabilidade do armazenamento do Azure](../../storage/common/storage-scalability-targets.md) para obter detalhes sobre destinos de escalabilidade para contas de armazenamento, em particular a taxa de pedidos total e a largura de banda total para o tipo de conta de armazenamento que está a utilizar.

Se está a exceder os destinos de escalabilidade para a sua conta de armazenamento, coloque os VHDs em várias contas de armazenamento para reduzir a atividade em cada conta individual.
