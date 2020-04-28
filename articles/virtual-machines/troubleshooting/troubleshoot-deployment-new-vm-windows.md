---
title: Implantação de Windows VM em Azure Microsoft Docs
description: Problemas de implementação do Gestor de Recursos de Resolução de Problemas quando cria uma nova máquina virtual do Windows no Azure
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
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0bc363b87a9f5b2f013c0bae75a07d79a3a7a830
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75981396"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Problemas de implementação de problemas ao criar um novo Windows VM em Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Principais questões
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Para outros problemas e questões de implementação de VM, consulte [a Troubleshoot implementando problemas de máquinas virtuais do Windows no Azure](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Recolher registos de atividade
Para iniciar a resolução de problemas, recolha os registos de atividade para identificar o erro associado ao problema. Os seguintes links contêm informações detalhadas sobre o processo a seguir.

[Ver as operações de implementação](../../azure-resource-manager/templates/deployment-history.md)

[Ver registos de atividade para gerir recursos Do Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** Se o SISTEMA for generalizado pelo Windows e for carregado e/ou capturado com a definição generalizada, não haverá erros. Da mesma forma, se o SISTEMA for especializado no Windows, e for carregado e/ou capturado com a definição especializada, então não haverá erros.

**Erros de upload:**

**N<sup>1:</sup>** Se o SISTEMA for generalizado pelo Windows e for carregado como especializado, terá um erro de tempo de fornecimento com o VM preso no ecrã OOBE.

**N<sup>2:</sup>** Se o SISTEMA for especializado no Windows e for carregado como generalizado, terá um erro de falha de provisionamento com o VM preso no ecrã OOBE porque o novo VM está a funcionar com o nome de computador, nome de utilizador e senha original.

**Resolução**

Para resolver estes dois erros, utilize [add-AzVhd para carregar o VHD original,](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd)disponível no local, com a mesma definição que para o OS (generalizado/especializado). Para fazer o upload como generalizado, lembre-se de executar sysprep primeiro.

**Erros de captura:**

**N<sup>3:</sup>** Se o Sistema operativo for generalizado pelo Windows, e for capturado como especializado, terá um erro de tempo de fornecimento porque o VM original não é utilizável, uma vez que está marcado como generalizado.

**N<sup>4:</sup>** Se o SISTEMA for especializado no Windows e for capturado como generalizado, terá um erro de falha de provisionamento porque o novo VM está a funcionar com o nome de computador, nome de utilizador e senha original. Além disso, o VM original não é utilizável porque é marcado como especializado.

**Resolução**

Para resolver estes dois erros, elimine a imagem atual do portal e [recapture-a dos VHDs atuais](../windows/create-vm-specialized.md) com a mesma definição que para o SISTEMA (generalizado/especializado).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Edição: Imagem personalizada/galeria/marketplace; falha de atribuição
Este erro surge em situações em que o novo pedido vm é fixado a um cluster que não pode suportar a dimensão VM que está a ser solicitada, ou não tem espaço livre disponível para acomodar o pedido.

**Causa 1:** O cluster não pode suportar o tamanho de VM solicitado.

**Resolução 1:**

* Tente novamente o pedido utilizando um tamanho VM menor.
* Se o tamanho do VM solicitado não puder ser alterado:
  * Pare todos os VMs no conjunto de disponibilidade.
    Clique em **Grupos** > de Recursos*o seu grupo* > de recursos**Recursos O** > *seu conjunto* > de disponibilidade**máquinas** > virtuais para**parar**a*máquina* > virtual .
  * Depois de todos os VMs pararem, crie o novo VM no tamanho desejado.
  * Inicie primeiro o novo VM e, em seguida, selecione cada um dos VMs parados e clique em **Iniciar**.

**Causa 2:** O cluster não tem recursos livres.

**Resolução 2:**

* Tente o pedido mais tarde.
* Se o novo VM pode fazer parte de um conjunto de disponibilidade diferente
  * Crie um novo VM num conjunto de disponibilidade diferente (na mesma região).
  * Adicione o novo VM à mesma rede virtual.

## <a name="next-steps"></a>Passos seguintes
Se encontrar problemas quando iniciar um Windows VM parado ou redimensionar um Windows VM existente em Azure, consulte problemas de implementação do [Troubleshoot Resource Manager com o reinício ou redimensionamento de uma máquina virtual do Windows existente no Azure.](restart-resize-error-troubleshooting.md)


