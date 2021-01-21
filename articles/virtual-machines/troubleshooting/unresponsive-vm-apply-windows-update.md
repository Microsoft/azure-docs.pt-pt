---
title: O Azure VM não responde com erro C01A001D ao aplicar a Atualização do Windows
description: Este artigo fornece medidas para resolver problemas em que a atualização do Windows gera um erro e fica sem resposta num VM Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 145d58013b259bf14c26d0840b8cd4299cfe85f0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632627"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>VM não responde com erro "C01A001D" ao aplicar a atualização do Windows

Este artigo fornece medidas para resolver problemas em que o Windows Update (KB) gera um erro e fica sem resposta num VM Azure.

## <a name="symptoms"></a>Sintomas

Ao utilizar [os diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, é apresentado o Windows Update (KB) em curso, mas falha com o código de erro: 'C01A001D'.

![Atualização do Windows sem resposta](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Causa

Um ficheiro central não pode ser criado no sistema de ficheiros. O sistema operativo não consegue escrever ficheiros no disco.

## <a name="resolution"></a>Resolução

### <a name="process-overview"></a>Visão geral do processo

> [!TIP]
> Se tiver uma cópia de segurança recente do VM, poderá tentar [restaurar o VM da cópia de segurança](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema da bota.

1. [Criar e aceder a um VM de reparação.](#create-and-access-a-repair-vm)
2. [Liberte espaço no disco rígido.](#free-up-space-on-the-hard-disk)
3. [Recomendado: Antes de reconstruir o VM, ative a recolha de consolas em série e de despejo de memória](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection).
4. [Reconstruir o VM.](#rebuild-the-vm)

> [!NOTE]
> Quando este erro ocorre, o So convidado não está operacional. Tem de resolver problemas em modo offline para resolver este problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e aceder a um VM de reparação

1. Siga [os passos 1-3 dos Comandos de Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar um VM de reparação.
2. Ligue-se ao VM de reparação utilizando ligação de ambiente de trabalho remoto.

### <a name="free-up-space-on-the-hard-disk"></a>Liberte espaço no disco rígido

Se o disco já não é 1 Tb, tens de o redimensionar. Uma vez que o disco é 1 TB, efetue uma limpeza do disco e uma desfragmentação da unidade.

1. Verifique se o disco está cheio. Se o disco estiver abaixo de 1 Tb, [expanda-o para um máximo de 1 Tb utilizando o PowerShell](../windows/expand-os-disk.md).
2. Uma vez que o disco é 1 Tb, efetue uma limpeza de disco.
    - [Retire o disco de dados do VM quebrado](../windows/detach-disk.md).
    - [Anexar o disco de dados a um VM funcional](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
    - Utilize a [ferramenta De Limpeza de Discos](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) para libertar espaço.
3. Após o redimensionamento e limpeza, desfragmentar a unidade:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    Dependendo do nível de fragmentação, isto pode levar horas.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Recomendado: Antes de reconstruir o VM, ative a recolha de consolas em série e de despejo de memória

1. Abra uma sessão de solicitação de comando elevada (Executar como administrador).
2. Execute os seguintes comandos:

    Ativar consola em série:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Certifique-se de que o espaço livre no disco OS é pelo menos igual ao tamanho da memória VM (RAM).

    Se não houver espaço suficiente no disco de so, altere o local onde o ficheiro de despejo de memória será criado e encaminhe-o para um disco de dados ligado ao VM e com espaço livre suficiente. Para alterar a localização, `%SystemRoot%` substitua-a pela letra de unidade (por exemplo, "F:") do disco de dados nos comandos abaixo:

    **Ativar a configuração sugerida para o despejo de SO:**

    Carregar disco de OS quebrado:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Ativar no ControlSet001:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Ativar no ControlSet002:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Descarregar disco de OSSO quebrado:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>Reconstruir o VM

Utilize [o passo 5 dos comandos de reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para remontar o VM.
