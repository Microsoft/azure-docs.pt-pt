---
title: Resolução de problemas em que o Windows não consegue concluir a configuração do sistema
titlesuffix: Azure Virtual Machines
description: Este artigo fornece medidas para resolver problemas em que o processo Sysprep impede o arranque de um Azure VM.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: 6cb3467fec99bd12810ed058a61de1be7b39cdd0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629594"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>Resolução de problemas em que o Windows não consegue concluir a configuração do sistema

Este artigo fornece medidas para resolver problemas em que o processo Sysprep impede o arranque de uma máquina virtual Azure (VM).

## <a name="symptom"></a>Sintoma

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, verá que a imagem exibe um erro de instalação do Windows enquanto a configuração do Windows está a iniciar os serviços. O erro mostrará a mensagem:

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![A Figura 1 apresenta um erro de instalação do Windows com a mensagem: "O Windows não conseguiu terminar a configuração do sistema. Para tentar retomar a configuração, reinicie o computador. A configuração está a iniciar serviços"](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>Causa

Este erro é causado quando o sistema operativo (OS) não consegue completar o [processo Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview). Este erro ocorrerá quando se tenta uma bota inicial de um VM generalizado. Se encontrar este problema, recrie a imagem generalizada, uma vez que a imagem está num estado intransmissível e não pode ser recuperada.

## <a name="solution"></a>Solução

> [!TIP]
> Se tiver uma cópia de segurança recente do VM, poderá tentar [restaurar o VM da cópia de segurança](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema da bota.

Para corrigir este problema, siga a orientação do [Azure sobre a preparação/captura de uma imagem](../windows/upload-generalized-managed.md) e prepare uma nova imagem generalizada.