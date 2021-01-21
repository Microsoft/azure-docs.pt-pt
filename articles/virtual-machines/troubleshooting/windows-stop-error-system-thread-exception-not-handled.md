---
title: Erro de paragem do Windows - 0x000000007E exceção do fio do sistema não manuseado
description: Este artigo fornece medidas para resolver problemas em que o SO convidado encontra um problema e deseja reiniciar o seu Azure VM. A mensagem dirá que "uma exceção do fio do sistema não foi tratada".
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: beb658474e49f9b47900d8481dab0424dae8c5be
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661396"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Erro de paragem do Windows - 0x000000007E exceção do fio do sistema não manuseado

Este artigo fornece medidas para resolver problemas em que o sistema operativo convidado (os hóspedes) encontra um problema e tenta reiniciar a sua máquina virtual Azure (VM). A mensagem de erro que é exibida diz: "Uma exceção do fio do sistema não foi tratada."

## <a name="symptoms"></a>Sintomas

Quando utilizar [diagnósticos de arranque](./boot-diagnostics.md) para visualizar uma imagem da saída VM, verá que o Windows precisa de reiniciar com o código de paragem "SYSTEM THREAD EXCEPTION NOT HANDLED" ou com o código de erro "0x00000000E".

![Screenshot mostrando Windows preso durante o arranque com um "O seu PC teve um problema e precisa de reiniciar. Vamos recomeçar por si." mensagem de erro e um código de paragem "SYSTEM THREAD EXCEPTION NOT HANDLED".](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Causa

A causa não pode ser determinada até que um ficheiro de despejo de memória seja analisado. Continue a recolher o ficheiro de despejo de memória.

## <a name="solution"></a>Solução

> [!TIP]
> Se tiver uma cópia de segurança recente do VM, poderá tentar [restaurar o VM da cópia de segurança](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema da bota.


Para resolver este problema, primeiro precisa de recolher o ficheiro de despejo de memória para a falha e depois enviar o ficheiro para o suporte da Microsoft. Para recolher o ficheiro de despejo, siga as instruções nas duas secções seguintes.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Fixe o disco DE a um novo VM de reparação

1. Para preparar um VM de reparação, siga os passos 1-3 dos comandos de [reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md).
1. Ligue-se ao VM de reparação utilizando a Ligação de Ambiente de Trabalho Remoto.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localize o ficheiro de despejo e envie um bilhete de apoio

1. Na vM de reparação, aceda à pasta Do Windows no disco oss anexado. Por exemplo, se a letra de unidade atribuída ao disco de SO anexado estiver marcada como *F,* vá a `F:\Windows` .
1. Procure o ficheiro *.dmp memória* e, em seguida, envie um bilhete [de apoio](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o ficheiro de despejo de memória anexado.
1. Se tiver dificuldade em localizar a *memória.dmp* ficheiro, siga o guia para [gerar um ficheiro de despejo de falhas utilizando chamadas de interrupção não mascarada (NMI).](/windows/client-management/generate-kernel-or-complete-crash-dump)

Para obter mais informações sobre chamadas NMI, consulte as chamadas NMI no guia do utilizador [da Consola em Série Azure.](./serial-console-windows.md#use-the-serial-console-for-nmi-calls)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Resolução de problemas Erros de arranque da Máquina Virtual Azure](./boot-error-troubleshoot.md)
