---
title: Resolver problemas de implementação de VM do Linux | Documentos da Microsoft
description: Resolver problemas de implementação do Resource Manager, quando cria uma nova máquina virtual do Linux no Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: jeconnoc
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: 9fea914fdf9b025fd5d38219a6bfc81b4a9cc584
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450308"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Resolver problemas de implementação do Resource Manager com a criação de uma nova máquina virtual do Linux no Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Principais problemas
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Para outros problemas de implantação de VM e perguntas, consulte [resolver problemas de implantação problemas de máquina virtual do Linux no Azure](troubleshoot-deploy-vm-linux.md).

## <a name="collect-activity-logs"></a>Registos de atividades de recolha
Para iniciar a resolução de problemas, recolha os registos de atividade para identificar o erro associado com o problema. As ligações seguintes contêm informações detalhadas sobre o processo a seguir.

[Ver as operações de implementação](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Ver registos de atividades para gerir recursos do Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y** Se o sistema operacional seja Linux generalizada, e ele é carregado e/ou capturado com a definição generalizada, em seguida, não haverá quaisquer erros. Da mesma forma, se for o sistema operacional Linux especializada, ele é carregado e/ou capturado com a definição especializada, e não haverá quaisquer erros.

**Erros de carregamento:**

**N<sup>1</sup>:** Se o sistema operacional seja Linux generalizado e ser carregado como especializada, obterá um erro de tempo limite de aprovisionamento porque a VM está bloqueada na fase de aprovisionamento.

**N<sup>2</sup>:** Se o sistema operacional é especializada de Linux, e ele é carregado como generalizada, obterá um erro de falha de aprovisionamento, porque a nova VM está em execução com o nome do computador, nome de utilizador e palavra-passe original.

**Resolução:**

Para resolver os dois desses erros, carregar o VHD original, disponível no local, com a mesma definição que para o sistema operacional (generalizado/especializado). Para carregar como generalizada, não se esqueça de executar - desaprovisionar pela primeira vez.

**Capture erros:**

**N<sup>3</sup>:** Se o sistema operacional seja Linux generalizado e, em que é capturado como especializada, obterá um erro de tempo limite de aprovisionamento porque a VM original não é utilizável porque está marcada como generalizada.

**N<sup>4</sup>:** Se o sistema operacional é especializada de Linux e, em que é capturado como generalizada, obterá um erro de falha de aprovisionamento, porque a nova VM está em execução com o nome do computador, nome de utilizador e palavra-passe original. Além disso, o original VM não é utilizável porque está marcado como especializada.

**Resolução:**

Para resolver os dois desses erros, eliminar a imagem atual do portal, e [recapturá-lo a partir dos VHD atual](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) com a mesma definição que para o sistema operacional (generalizado/especializado).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problema: Custom / Galeria / imagem do marketplace; Falha de alocação
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
Se tiver problemas ao iniciar uma VM do Linux parado ou redimensionar uma VM do Linux existente no Azure, veja [problemas de implementação do Gestor de recursos de resolução de problemas ao reiniciar ou redimensionar uma Máquina Virtual de Linux existente no Azure](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

