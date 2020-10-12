---
title: VM reinicia ou redimensiona problemas no Azure Microsoft Docs
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
ms.openlocfilehash: 85acd8e26ca10730638332047a37d281358d205f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526559"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Resolver problemas de implementação ao reiniciar ou redimensionar uma VM do Windows existente no Azure
Quando tenta iniciar uma Máquina Virtual Azure (VM) parada ou redimensionar um Azure VM existente, o erro comum que encontra é uma falha de atribuição. Este erro resulta quando o cluster ou a região não dispõem de recursos disponíveis ou não conseguem suportar a dimensão VM solicitada.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Recolher registos de atividades
Para iniciar a resolução de problemas, recolha os registos de atividade para identificar o erro associado ao problema. Os seguintes links contêm informações detalhadas sobre o processo:

[Ver as operações de implementação](../../azure-resource-manager/templates/deployment-history.md)

[Ver registos de atividades para gerir recursos do Azure](../../azure-resource-manager/management/view-activity-logs.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: erro ao iniciar uma VM parada
Tenta-se iniciar um VM parado, mas obtém-se uma falha na atribuição.

### <a name="cause"></a>Causa
O pedido de início do VM parado tem de ser tentado no cluster original que acolhe o serviço de nuvem. No entanto, o cluster não dispõe de espaço livre disponível para satisfazer o pedido.

### <a name="resolution"></a>Resolução
* Pare todos os VMs no conjunto de disponibilidade e, em seguida, reinicie cada VM.
  
  1. Clique **em Grupos de Recursos**o seu grupo de  >  *recursos*  >  **Recursos**a sua disponibilidade  >  *definir*  >  **Máquinas Virtuais**a sua máquina  >  *virtual*  >  **Parar**.
  2. Depois de todas as paragens de VMs, selecione cada um dos VMs parados e clique em Iniciar.
* Re-tentar o pedido de reinício mais tarde.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: erro ao redimensionar uma VM existente
Tenta-se redimensionar um VM existente, mas obtém-se uma falha de alocação.

### <a name="cause"></a>Causa
O pedido de redimensionar o VM tem de ser tentado no cluster original que acolhe o serviço de nuvem. No entanto, o cluster não suporta o tamanho VM solicitado.

### <a name="resolution"></a>Resolução
* Reda o pedido utilizando um tamanho VM menor.
* Se o tamanho do VM solicitado não puder ser alterado:
  
  1. Pare todos os VMs no conjunto de disponibilidade.
     
     * Clique **em Grupos de Recursos**o seu grupo de  >  *recursos*  >  **Recursos**a sua disponibilidade  >  *definir*  >  **Máquinas Virtuais**a sua máquina  >  *virtual*  >  **Parar**.
  2. Depois de todos os VMs pararem, redimensione o VM desejado para um tamanho maior.
  3. Selecione o VM redimensionado e clique em **Iniciar**e, em seguida, inicie cada um dos VMs parados.

## <a name="next-steps"></a>Passos seguintes
Se encontrar problemas quando criar um novo Windows VM em Azure, consulte [problemas de implementação de Resolução de Problemas com a criação de uma nova máquina virtual windows em Azure](./troubleshoot-deployment-new-vm-windows.md).
