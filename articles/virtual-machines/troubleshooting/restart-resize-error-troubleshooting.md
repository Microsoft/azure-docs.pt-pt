---
title: VM reiniciando ou redimensionando problemas em Azure Microsoft Docs
description: Problemas de implementação do Gestor de Recursos de Resolução de Problemas com o reinício ou redimensionamento de uma máquina virtual existente em Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a6532558107463311c4225b9855bc4cd3f19eed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75965615"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Resolver problemas de implementação ao reiniciar ou redimensionar uma VM do Windows existente no Azure
Quando tenta iniciar uma Máquina Virtual Azure parada (VM), ou redimensionar um VM Azure existente, o erro comum que encontra é uma falha de atribuição. Este erro resulta quando o cluster ou a região não dispõem de recursos disponíveis ou não suportam a dimensão vm solicitada.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Recolher registos de atividade
Para iniciar a resolução de problemas, recolha os registos de atividade para identificar o erro associado ao problema. Os seguintes links contêm informações detalhadas sobre o processo:

[Ver as operações de implementação](../../azure-resource-manager/templates/deployment-history.md)

[Ver registos de atividade para gerir recursos Do Azure](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: erro ao iniciar uma VM parada
Tentas começar um VM parado, mas tens uma falha na atribuição.

### <a name="cause"></a>Causa
O pedido para iniciar o VM parado tem de ser tentado no cluster original que acolhe o serviço de nuvem. No entanto, o cluster não dispõe de espaço livre disponível para satisfazer o pedido.

### <a name="resolution"></a>Resolução
* Pare todos os VMs no conjunto de disponibilidade e, em seguida, reinicie cada VM.
  
  1. Clique em **Grupos** > de Recursos*o seu grupo* > de recursos**Recursos O** > *seu conjunto* > de disponibilidade**máquinas** > virtuais para**parar**a*máquina* > virtual .
  2. Depois de todas as paragens de VMs, selecione cada um dos VMs parados e clique em Iniciar.
* Tente o pedido de reinício mais tarde.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: erro ao redimensionar uma VM existente
Tentare redimensionar um VM existente, mas obtém uma falha de atribuição.

### <a name="cause"></a>Causa
O pedido de redimensionar o VM tem de ser tentado no cluster original que acolhe o serviço de nuvem. No entanto, o cluster não suporta o tamanho de VM solicitado.

### <a name="resolution"></a>Resolução
* Tente novamente o pedido utilizando um tamanho VM menor.
* Se o tamanho do VM solicitado não puder ser alterado:
  
  1. Pare todos os VMs no conjunto de disponibilidade.
     
     * Clique em **Grupos** > de Recursos*o seu grupo* > de recursos**Recursos O** > *seu conjunto* > de disponibilidade**máquinas** > virtuais para**parar**a*máquina* > virtual .
  2. Depois de todos os VMs pararem, redimensionar o VM desejado para um tamanho maior.
  3. Selecione o VM redimensionado e clique **em Iniciar**, e, em seguida, inicie cada um dos VMs parados.

## <a name="next-steps"></a>Passos seguintes
Se encontrar problemas quando criar um novo Windows VM em Azure, consulte problemas de implementação da [Troubleshoot com a criação](../windows/troubleshoot-deployment-new-vm.md)de uma nova máquina virtual Windows em Azure .

