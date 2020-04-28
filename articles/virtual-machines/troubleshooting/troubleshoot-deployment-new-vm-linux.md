---
title: Implantação de Linux VM de troubleshoot Microsoft Docs
description: Problemas de implementação do Gestor de Recursos de Troubleshoot quando cria uma nova máquina virtual Linux em Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: 98c3a6b14230e30ccbb103be741595696a20c236
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75981407"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Problemas de implementação do Gestor de Recursos de Troubleshoot com a criação de uma nova máquina virtual Linux em Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Principais questões
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Para outras questões e problemas de implementação de VM, veja [Resolver problemas de implementação de máquina virtual do Linux no Azure](troubleshoot-deploy-vm-linux.md).

## <a name="collect-activity-logs"></a>Recolher registos de atividade
Para iniciar a resolução de problemas, recolha os registos de atividade para identificar o erro associado ao problema. Os seguintes links contêm informações detalhadas sobre o processo a seguir.

[Ver as operações de implementação](../../azure-resource-manager/templates/deployment-history.md)

[Ver registos de atividade para gerir recursos Do Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Se o Sistema Operativo For generalizado pelo Linux, e for carregado e/ou capturado com a definição generalizada, então não haverá erros. Da mesma forma, se o SISTEMA é especializado em Linux, e é carregado e/ou capturado com a configuração especializada, então não haverá erros.

**Erros de upload:**

**N<sup>1:</sup>** Se o Sistema operativo Linux for generalizado e for carregado como especializado, terá um erro de tempo de fornecimento porque o VM está preso na fase de provisionamento.

**N<sup>2:</sup>** Se o SISTEMA for especializado em Linux, e for carregado como generalizado, terá um erro de falha de provisionamento porque o novo VM está a funcionar com o nome de computador original, nome de utilizador e senha.

**Resolução:**

Para resolver estes dois erros, faça o upload do VHD original, disponível nas instalações, com a mesma definição que para o SISTEMA (generalizado/especializado). Para fazer o upload como generalizado, lembre-se de executar a desprovisionamento primeiro.

**Erros de captura:**

**N<sup>3:</sup>** Se o Sistema operativo Linux for generalizado, e for capturado como especializado, terá um erro de tempo de fornecimento porque o VM original não é utilizável, uma vez que está marcado como generalizado.

**N<sup>4:</sup>** Se o SISTEMA for especializado em Linux, e for capturado como generalizado, terá um erro de falha de provisionamento porque o novo VM está a funcionar com o nome de computador original, nome de utilizador e senha. Além disso, o VM original não é utilizável porque é marcado como especializado.

**Resolução:**

Para resolver estes dois erros, elimine a imagem atual do portal e [recapture-a dos VHDs atuais](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) com a mesma definição que para o SISTEMA (generalizado/especializado).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Edição: Imagem personalizada/ galeria/ marketplace; falha de atribuição
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
Se encontrar problemas quando iniciar um VM Linux parado ou redimensionar um VM Linux existente em Azure, consulte problemas de implementação do [Troubleshoot Resource Manager com o reinício ou redimensionamento de uma máquina virtual Linux existente em Azure.](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

