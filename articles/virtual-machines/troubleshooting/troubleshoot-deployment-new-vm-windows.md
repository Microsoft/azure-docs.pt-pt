---
title: Solucionar problemas de implantação de VM do Windows no Azure | Microsoft Docs
description: Solucionar problemas de implantação do Resource Manager ao criar uma nova máquina virtual do Windows no Azure
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981396"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Solucionar problemas de implantação ao criar uma nova VM do Windows no Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Principais problemas
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Para outros problemas de implantação de VM e perguntas, consulte [solucionar problemas de implantação de máquina virtual do Windows no Azure](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Coletar logs de atividade
Para iniciar a solução de problemas, colete os logs de atividade para identificar o erro associado ao problema. Os links a seguir contêm informações detalhadas sobre o processo a ser seguido.

[Ver as operações de implementação](../../azure-resource-manager/templates/deployment-history.md)

[Exibir logs de atividade para gerenciar recursos do Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** Se o sistema operacional for Windows generalizado e ele for carregado e/ou capturado com a configuração generalizada, não haverá erros. Da mesma forma, se o sistema operacional for Windows especializado e ele for carregado e/ou capturado com a configuração especializada, não haverá erros.

**Erros de upload:**

**N<sup>1</sup>:** Se o sistema operacional for Windows generalizado e ele for carregado como especializado, você receberá um erro de tempo limite de provisionamento com a VM paralisada na tela do OOBE.

**N<sup>2</sup>:** Se o sistema operacional for Windows especializado e ele for carregado como generalizado, você receberá um erro de falha de provisionamento com a VM paralisada na tela do OOBE porque a nova VM está em execução com o nome do computador, nome de usuário e senha originais.

**Resolução**

Para resolver esses dois erros, use [Add-AzVhd para carregar o VHD original](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd), disponível no local, com a mesma configuração do sistema operacional (generalizado/especializado). Para carregar como generalizado, lembre-se de executar o Sysprep primeiro.

**Erros de captura:**

**N<sup>3</sup>:** Se o sistema operacional for Windows generalizado e ele for capturado como especializado, você obterá um erro de tempo limite de provisionamento porque a VM original não é utilizável, pois está marcada como generalizada.

**N<sup>4</sup>:** Se o sistema operacional for Windows especializado e ele for capturado como generalizado, você receberá um erro de falha de provisionamento porque a nova VM está em execução com o nome do computador, o nome de usuário e a senha originais. Além disso, a VM original não é utilizável porque está marcada como especializada.

**Resolução**

Para resolver esses erros, exclua a imagem atual do portal e [recapture-a dos VHDs atuais](../windows/create-vm-specialized.md) com a mesma configuração que para o sistema operacional (generalizado/especializado).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problema: imagem personalizada/de galeria/do Marketplace; falha de alocação
Esse erro ocorre em situações em que a nova solicitação de VM é fixada em um cluster que não dá suporte ao tamanho da VM que está sendo solicitada ou não tem espaço livre disponível para acomodar a solicitação.

**Causa 1:** O cluster não dá suporte ao tamanho de VM solicitado.

**Resolução 1:**

* Repita a solicitação usando um tamanho de VM menor.
* Se o tamanho da VM solicitada não puder ser alterado:
  * Pare todas as VMs no conjunto de disponibilidade.
    Clique **em grupos de recursos** > *seu grupo de recursos* > **recursos** > *seu conjunto de disponibilidade* > **máquinas virtuais** > *sua máquina virtual* > **parar**.
  * Depois que todas as VMs forem interrompidas, crie a nova VM no tamanho desejado.
  * Inicie a nova VM primeiro e, em seguida, selecione cada uma das VMs paradas e clique em **Iniciar**.

**Causa 2:** O cluster não tem recursos gratuitos.

**Resolução 2:**

* Repita a solicitação mais tarde.
* Se a nova VM puder fazer parte de um conjunto de disponibilidade diferente
  * Crie uma nova VM em um conjunto de disponibilidade diferente (na mesma região).
  * Adicione a nova VM à mesma rede virtual.

## <a name="next-steps"></a>Passos seguintes
Se você encontrar problemas ao iniciar uma VM do Windows parada ou redimensionar uma VM do Windows existente no Azure, consulte [solucionar problemas de implantação do Resource Manager com a reinicialização ou redimensionamento de uma máquina virtual do Windows existente no Azure](restart-resize-error-troubleshooting.md).


