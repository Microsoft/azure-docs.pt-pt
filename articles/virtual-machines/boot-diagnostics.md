---
title: Diagnóstico de arranque Azure
description: Visão geral dos diagnósticos de arranque da Azure e diagnósticos de arranque geridos
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9ffd5a6397fa01efcf9aece93333dcb5e5b418cc
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042977"
---
# <a name="azure-boot-diagnostics"></a>Diagnóstico de arranque Azure

O diagnóstico de arranque é uma funcionalidade de depuração para máquinas virtuais Azure (VM) que permite o diagnóstico de falhas de arranque VM. O diagnóstico de arranque permite que um utilizador observe o estado do seu VM à medida que está a ser iniciado através da recolha de informações de registos em série e imagens.

## <a name="boot-diagnostics-storage-account"></a>Conta de armazenamento de diagnóstico de arranque
Por predefinição, os diagnósticos de arranque são ativados para todos os VMs criados utilizando o portal Azure e utiliza uma conta de armazenamento gerida. Ao utilizarem uma conta de armazenamento gerida, os utilizadores ganham uma melhoria significativa no tempo de implementação de VM. Por esta razão, recomendamos que os clientes utilizem diagnósticos de arranque com uma conta de armazenamento gerida para todos os VMs.

> [!NOTE]
> Os clientes da Azure não serão cobrados para armazenamento quando optarem por utilizar diagnósticos de arranque com uma conta de armazenamento gerida até 1 de outubro de 2020.

Uma experiência alternativa de diagnóstico de arranque é usar uma conta de armazenamento personalizada. Um utilizador pode criar uma nova conta de armazenamento ou utilizar uma já existente. Para obter mais informações sobre contas de armazenamento personalizadas, consulte [a visão geral da conta de armazenamento.](https://docs.microsoft.com/azure/storage/common/storage-account-overview)

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-portal.png" alt-text="Screenshot para mostrar como ativar diagnósticos de arranque":::

## <a name="boot-diagnostics-view"></a>Vista de diagnósticos de arranque
Localizada na lâmina da máquina virtual, a opção de diagnóstico de arranque encontra-se na secção *de Suporte e Resolução de Problemas* no portal Azure. A seleção de diagnósticos de arranque apresentará uma imagem e informações de registo em série. O registo em série contém mensagens de kernel e a imagem é uma imagem do seu estado atual dos VMs. Baseado em se o VM está a executar Windows ou Linux determina como seria a imagem esperada. Para o Windows, os utilizadores irão ver um fundo de ambiente de trabalho e, para o Linux, os utilizadores irão ver uma solicitação de login.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Screenshot dos diagnósticos de botas Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Screenshot dos diagnósticos de arranque do Windows":::


## <a name="limitations"></a>Limitações
- Os diagnósticos de arranque só estão disponíveis para VMs Azure Resource Manager (ARM). 
- Os diagnósticos de arranque não suportam contas de armazenamento premium, se uma conta de armazenamento premium for utilizada para diagnósticos de arranque os utilizadores receberão um `StorageAccountTypeNotSupported` erro ao iniciar o VM. 
- A Azure Serial Console não suporta atualmente uma conta de armazenamento gerida para diagnósticos de arranque.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a [Consola em Série Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) e como utilizar diagnósticos de arranque para [resolver problemas em máquinas virtuais em Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
