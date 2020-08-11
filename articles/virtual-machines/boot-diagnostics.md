---
title: Diagnóstico de arranque Azure
description: Visão geral dos diagnósticos de arranque da Azure e diagnósticos de arranque geridos
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: e2ba5d909a3aa43921f52295d2f7216aac76bc32
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067091"
---
# <a name="azure-boot-diagnostics"></a>Diagnóstico de arranque Azure

O diagnóstico de arranque é uma funcionalidade de depuração para máquinas virtuais Azure (VM) que permite o diagnóstico de falhas de arranque VM. O diagnóstico de arranque permite que um utilizador observe o estado do seu VM à medida que está a ser iniciado através da recolha de informações de registos em série e imagens.

## <a name="boot-diagnostics-view"></a>Vista de diagnóstico de arranque
Localizada na lâmina da máquina virtual, a opção de diagnóstico de arranque encontra-se na secção *de Suporte e Resolução de Problemas* no portal Azure. A seleção de diagnósticos de arranque apresentará uma imagem e informações de registo em série. O registo em série contém mensagens de kernel e a imagem é uma imagem do seu estado atual dos VMs. Baseado em se o VM está a executar Windows ou Linux determina como seria a imagem esperada. Para o Windows, os utilizadores irão ver um fundo de ambiente de trabalho e, para o Linux, os utilizadores irão ver uma solicitação de login.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Screenshot dos diagnósticos de botas Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Screenshot dos diagnósticos de arranque do Windows":::


## <a name="limitations"></a>Limitações
- O diagnóstico de arranque só está disponível para VMs do Azure Resource Manager. 
- Os diagnósticos de arranque não suportam contas de armazenamento premium, se uma conta de armazenamento premium for utilizada para diagnósticos de arranque os utilizadores receberão um `StorageAccountTypeNotSupported` erro ao iniciar o VM. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a [Consola em Série Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) e como utilizar diagnósticos de arranque para [resolver problemas em máquinas virtuais em Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
