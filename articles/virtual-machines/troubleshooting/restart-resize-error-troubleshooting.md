---
title: Problemas de reinicialização ou redimensionamento de VM no Azure | Microsoft Docs
description: Solucionar problemas de implantação do Resource Manager com a reinicialização ou o redimensionamento de uma máquina virtual existente no Azure
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965615"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Solucionar problemas de implantação com a reinicialização ou redimensionamento de uma VM do Windows existente no Azure
Quando você tenta iniciar uma VM (máquina virtual) do Azure interrompida ou redimensionar uma VM do Azure existente, o erro comum que você encontra é uma falha de alocação. Esse erro ocorre quando o cluster ou a região não tem recursos disponíveis ou não dá suporte ao tamanho de VM solicitado.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Coletar logs de atividade
Para iniciar a solução de problemas, colete os logs de atividade para identificar o erro associado ao problema. Os links a seguir contêm informações detalhadas sobre o processo:

[Ver as operações de implementação](../../azure-resource-manager/templates/deployment-history.md)

[Exibir logs de atividade para gerenciar recursos do Azure](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: erro ao iniciar uma VM parada
Você tenta iniciar uma VM parada, mas Obtém uma falha de alocação.

### <a name="cause"></a>Causa
A solicitação para iniciar a VM parada deve ser tentada no cluster original que hospeda o serviço de nuvem. No entanto, o cluster não tem espaço livre disponível para atender à solicitação.

### <a name="resolution"></a>Resolução
* Interrompa todas as VMs no conjunto de disponibilidade e reinicie cada VM.
  
  1. Clique **em grupos de recursos** > *seu grupo de recursos* > **recursos** > *seu conjunto de disponibilidade* > **máquinas virtuais** > *sua máquina virtual* > **parar**.
  2. Depois que todas as VMs forem interrompidas, selecione cada uma das VMs interrompidas e clique em Iniciar.
* Tente novamente a solicitação de reinicialização mais tarde.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: erro ao redimensionar uma VM existente
Você tenta redimensionar uma VM existente, mas Obtém uma falha de alocação.

### <a name="cause"></a>Causa
A solicitação para redimensionar a VM deve ser tentada no cluster original que hospeda o serviço de nuvem. No entanto, o cluster não oferece suporte ao tamanho de VM solicitado.

### <a name="resolution"></a>Resolução
* Repita a solicitação usando um tamanho de VM menor.
* Se o tamanho da VM solicitada não puder ser alterado:
  
  1. Pare todas as VMs no conjunto de disponibilidade.
     
     * Clique **em grupos de recursos** > *seu grupo de recursos* > **recursos** > *seu conjunto de disponibilidade* > **máquinas virtuais** > *sua máquina virtual* > **parar**.
  2. Depois que todas as VMs forem interrompidas, redimensione a VM desejada para um tamanho maior.
  3. Selecione a VM redimensionada e clique em **Iniciar**e inicie cada uma das VMs interrompidas.

## <a name="next-steps"></a>Passos seguintes
Se você encontrar problemas ao criar uma nova VM do Windows no Azure, consulte [solucionar problemas de implantação com a criação de uma nova máquina virtual do Windows no Azure](../windows/troubleshoot-deployment-new-vm.md).

