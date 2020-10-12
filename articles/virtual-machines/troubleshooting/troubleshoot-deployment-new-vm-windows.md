---
title: Resolução de problemas da implementação do Windows VM em Azure Microsoft Docs
description: Problemas de implementação do Gestor de Recursos de Resolução de Problemas quando cria uma nova máquina virtual windows no Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: DavidCBerry13
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: daberry
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2b2f21cab4740013eb2de1d69f558c95461c493e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028401"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Problemas de implementação de resolução de problemas ao criar um novo Windows VM em Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Principais questões
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Para outros problemas e questões de implementação de VM, consulte [a resolução de problemas de implementação de problemas de máquina virtual do Windows no Azure](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Recolher registos de atividades
Para iniciar a resolução de problemas, recolha os registos de atividade para identificar o erro associado ao problema. Os seguintes links contêm informações detalhadas sobre o processo a seguir.

[Ver as operações de implementação](../../azure-resource-manager/templates/deployment-history.md)

[Ver registos de atividades para gerir recursos do Azure](../../azure-resource-manager/management/view-activity-logs.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** Se o SISTEMA for generalizado do Windows e for carregado e/ou capturado com a definição generalizada, então não haverá erros. Da mesma forma, se o SISTEMA for especializado no Windows, e for carregado e/ou capturado com a definição especializada, então não haverá erros.

**Erros de upload:**

**N<sup>1</sup>:** Se o SISTEMA for generalizado do Windows e for carregado como especializado, obterá um erro de tempo de provisionamento com o VM preso no ecrã OOBE.

**N<sup>2:</sup>** Se o SISTEMA for especializado no Windows e for carregado como generalizado, obterá um erro de falha de provisionamento com o VM preso no ecrã OOBE porque o novo VM está a funcionar com o nome de computador original, nome de utilizador e senha.

**Resolução**

Para resolver estes dois erros, utilize [o Add-AzVhd para carregar o VHD original,](/powershell/module/az.compute/add-azvhd)disponível no local, com a mesma definição que para o SISTEMA (generalizado/especializado). Para carregar como generalizado, lembre-se de executar sysprep primeiro.

**Erros de captura:**

**N<sup>3:</sup>** Se o SISTEMA for generalizado do Windows e for capturado como especializado, obterá um erro de tempo limite de provisão porque o VM original não é utilizável, uma vez que está marcado como generalizado.

**N<sup>4:</sup>** Se o SISTEMA for especializado no Windows e for capturado como generalizado, obterá um erro de falha de provisionamento porque o novo VM está a funcionar com o nome de computador original, nome de utilizador e senha. Além disso, o VM original não é utilizável porque é marcado como especializado.

**Resolução**

Para resolver estes dois erros, elimine a imagem atual do portal e [recapture-a dos VHDs atuais](../windows/create-vm-specialized.md) com a mesma definição que para o SISTEMA (generalizado/especializado).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Edição: imagem personalizada/galeria/mercado; falha na atribuição
Este erro surge em situações em que o novo pedido de VM é fixado a um cluster que não pode suportar o tamanho VM solicitado, ou não tem espaço livre disponível para acomodar o pedido.

**Causa 1:** O cluster não pode suportar o tamanho VM solicitado.

**Resolução 1:**

* Reda o pedido utilizando um tamanho VM menor.
* Se o tamanho do VM solicitado não puder ser alterado:
  * Pare todos os VMs no conjunto de disponibilidade.
    Clique **em Grupos de Recursos**o seu grupo de  >  *recursos*  >  **Recursos**a sua disponibilidade  >  *definir*  >  **Máquinas Virtuais**a sua máquina  >  *virtual*  >  **Parar**.
  * Depois de todos os VMs pararem, crie o novo VM no tamanho desejado.
  * Inicie primeiro o novo VM e, em seguida, selecione cada um dos VMs parados e clique em **Iniciar**.

**Causa 2:** O cluster não tem recursos livres.

**Resolução 2:**

* Recandidutar o pedido mais tarde.
* Se o novo VM pode fazer parte de um conjunto de disponibilidade diferente
  * Criar um novo VM num conjunto de disponibilidade diferente (na mesma região).
  * Adicione o novo VM à mesma rede virtual.

## <a name="next-steps"></a>Passos seguintes
Se encontrar problemas quando iniciar um VM do Windows parado ou redimensionar um VM do Windows existente em Azure, consulte [problemas de implementação do Gestor de Recursos de Resolução de Problemas com o reinício ou redimensionamento de uma máquina virtual do Windows existente no Azure](restart-resize-error-troubleshooting.md).
