---
title: Resolver problemas de implementação de VM do Windows no Azure | Documentos da Microsoft
description: Resolver problemas de implementação do Resource Manager, quando cria uma nova máquina virtual de Windows no Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6bdf0816e40e34b184da0340a8dbaffbcd7657e8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710449"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Resolver problemas de implementação ao criar uma nova VM do Windows no Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Principais problemas
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Para outros problemas de implantação de VM e perguntas, consulte [resolver problemas de máquina virtual do Windows implementar no Azure](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Registos de atividades de recolha
Para iniciar a resolução de problemas, recolha os registos de atividade para identificar o erro associado com o problema. As ligações seguintes contêm informações detalhadas sobre o processo a seguir.

[Ver as operações de implementação](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Ver registos de atividades para gerir recursos do Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y** Se o sistema operacional Windows generalizada, e ele é carregado e/ou capturado com a definição generalizada, em seguida, não haverá quaisquer erros. Da mesma forma, se for o sistema operacional Windows especializada, ele é carregado e/ou capturado com a definição especializada, e não haverá quaisquer erros.

**Erros de carregamento:**

**N<sup>1</sup>:** Se o sistema operacional Windows generalizada, e ele é carregado como especializada, obterá um erro de tempo limite de aprovisionamento com a VM presa no ecrã de OOBE.

**N<sup>2</sup>:** Se o sistema operacional Windows especializadas e ser carregado como generalizada, obterá um erro de falha de aprovisionamento com a VM presa no ecrã de OOBE porque a nova VM está em execução com o nome do computador, nome de utilizador e palavra-passe original.

**Resolução**

Para resolver os dois desses erros, utilize [Add-AzVhd para carregar o VHD original](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd), disponível no local, com a mesma definição que para o sistema operacional (generalizado/especializado). Para carregar como generalizada, não se esqueça de executar o sysprep em primeiro lugar.

**Capture erros:**

**N<sup>3</sup>:** Se o sistema operacional Windows generalizada, e ele é capturado como especializada, obterá um erro de tempo limite de aprovisionamento porque a VM original não é utilizável porque está marcada como generalizada.

**N<sup>4</sup>:** Se o sistema operacional é especializada do Windows e, em que é capturado como generalizada, obterá um erro de falha de aprovisionamento, porque a nova VM está em execução com o nome do computador original, nome de utilizador e palavra-passe. Além disso, o original VM não é utilizável porque está marcado como especializada.

**Resolução**

Para resolver os dois desses erros, eliminar a imagem atual do portal, e [recapturá-lo a partir dos VHD atual](../windows/create-vm-specialized.md) com a mesma definição que para o sistema operacional (generalizado/especializado).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problema: Imagem personalizada/Galeria/marketplace; Falha de alocação
Este erro surge em situações quando o novo pedido VM está afixado a um cluster que não suporta o tamanho da VM que está a ser solicitado ou não tem espaço livre disponível para acomodar o pedido.

**Fazer com que 1:** O cluster não suporta o tamanho da VM pedida.

**Resolução 1:**

* Repita o pedido com um tamanho VM mais pequeno.
* Se não é possível alterar o tamanho da VM pedida:
  * Pare todas as VMs no conjunto de disponibilidade.
    Clique em **grupos de recursos** > *seu grupo de recursos* > **recursos** > *deconjuntodesuadisponibilidade*  >  **Máquinas virtuais** > *sua máquina virtual* > **parar**.
  * Depois de parar todas as VMs, crie a nova VM no tamanho pretendido.
  * Em primeiro lugar, a iniciar a nova VM e, em seguida, selecione cada uma das VMs paradas e clique em **iniciar**.

**Causa 2:** O cluster não tem recursos gratuitos.

**Resolução 2:**

* Repita o pedido mais tarde.
* Se a nova VM pode fazer parte de um conjunto de disponibilidade diferente
  * Crie uma nova VM num conjunto (na mesma região) de disponibilidade diferente.
  * Adicione a nova VM a mesma rede virtual.

## <a name="next-steps"></a>Passos Seguintes
Se tiver problemas ao iniciar uma VM parada do Windows ou redimensionar uma VM existente do Windows no Azure, veja [problemas de implementação do Gestor de recursos de resolução de problemas ao reiniciar ou redimensionar uma Máquina Virtual de Windows existente no Azure](restart-resize-error-troubleshooting.md).


