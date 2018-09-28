---
title: VM reiniciar ou redimensionar problemas no Azure | Documentos da Microsoft
description: Resolver problemas de implementação do Resource Manager ao reiniciar ou redimensionar uma Máquina Virtual existente no Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 74ba9b8d0ce86a5c663eb9fbb6190e2bcf4513d7
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414736"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Resolver problemas de implementação ao reiniciar ou redimensionar uma VM existente do Windows no Azure
Ao tentar iniciar uma Máquina Virtual do Azure (VM) parada ou redimensionar uma VM do Azure existente, o erro comum que encontrar é uma falha de alocação. Este erro resulta quando o cluster ou a região não tem recursos disponíveis ou não suporta o tamanho da VM pedida.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Registos de atividades de recolha
Para iniciar a resolução de problemas, recolha os registos de atividade para identificar o erro associado com o problema. As ligações seguintes contêm informações detalhadas sobre o processo:

[Ver as operações de implementação](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Ver registos de atividades para gerir recursos do Azure](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: Erro ao iniciar uma VM parada
Tente iniciar uma VM parada, mas ocorre uma falha de alocação.

### <a name="cause"></a>Causa
O pedido para iniciar a VM parada deve ser tentada no cluster original que aloja o serviço em nuvem. No entanto, o cluster não tem espaço livre disponível para satisfazer o pedido.

### <a name="resolution"></a>Resolução
* Parar todas as VMs no conjunto de disponibilidade e, em seguida, reiniciar cada VM.
  
  1. Clique em **grupos de recursos** > *seu grupo de recursos* > **recursos** > *deconjuntodesuadisponibilidade*  >  **Máquinas virtuais** > *sua máquina virtual* > **parar**.
  2. Depois de parar todas as VMs, selecione cada uma das VMs paradas e clique em Iniciar.
* Repita o pedido de reinício num momento posterior.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: Ocorreu um erro ao redimensionar uma VM nova
Tente redimensionar uma VM existente, mas ocorre uma falha de alocação.

### <a name="cause"></a>Causa
O pedido para redimensionar a VM tem de ser tentada no cluster original que aloja o serviço em nuvem. No entanto, o cluster não suporta o tamanho da VM pedida.

### <a name="resolution"></a>Resolução
* Repita o pedido com um tamanho VM mais pequeno.
* Se não é possível alterar o tamanho da VM pedida:
  
  1. Pare todas as VMs no conjunto de disponibilidade.
     
     * Clique em **grupos de recursos** > *seu grupo de recursos* > **recursos** > *deconjuntodesuadisponibilidade*  >  **Máquinas virtuais** > *sua máquina virtual* > **parar**.
  2. Depois de parar todas as VMs, redimensione a VM pretendida para um tamanho maior.
  3. Selecione a VM redimensionada e clique em **iniciar**, e, em seguida, iniciar cada uma das VMs paradas.

## <a name="next-steps"></a>Passos Seguintes
Se tiver problemas ao criar uma nova VM do Windows no Azure, veja [resolver problemas de implementação ao criar uma nova máquina virtual de Windows no Azure](../windows/troubleshoot-deployment-new-vm.md).

