---
title: O Azure VM não responde com erro c01A001D ao aplicar a tualização do Windows
description: Este artigo fornece passos para resolver problemas em que a atualização do Windows gera um erro e torna-se insensível num VM Azure.
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
ms.openlocfilehash: 16c8eed3377c2191b4345ec59ec1eba8be01369d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633960"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>VM não responde com erro "C01A001D" ao aplicar atualização do Windows

Este artigo fornece passos para resolver problemas em que o Windows Update (KB) gera um erro e fica sem resposta num VM Azure.

## <a name="symptoms"></a>Sintomas

Ao utilizar [os diagnósticos boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para visualizar a imagem do VM, o Windows Update (KB) em andamento é apresentado, mas falha com o código de erro: 'C01A001D'.

![Atualização do Windows sem resposta](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Causa

Um ficheiro central não pode ser criado no sistema de ficheiros. O sistema operativo não consegue escrever ficheiros no disco.

## <a name="resolution"></a>Resolução

### <a name="process-overview"></a>Visão geral do processo

1. [Criar e aceder a um VM de reparação.](#create-and-access-a-repair-vm)
2. [Liberte espaço no disco rígido.](#free-up-space-on-the-hard-disk)
3. [Recomendado: Antes de reconstruir o VM, ative](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection)a recolha de consolas em série e de despejo de memória.
4. [Reconstruir o VM.](#rebuild-the-vm)

> [!NOTE]
> Quando este erro ocorre, o Os convidado não está operacional. É preciso resolver problemas em modo offline para resolver este problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e aceder a um VM de reparação

1. Siga [os passos 1-3 dos Comandos de Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) para preparar um VM de reparação.
2. Ligue-se ao VM de reparação utilizando a ligação remota de ambiente de trabalho.

### <a name="free-up-space-on-the-hard-disk"></a>Liberte espaço no disco rígido

Se o disco já não tiver 1 Tb, tem de o redimensionar. Uma vez que o disco seja de 1 TB, efetue uma limpeza do disco e uma desfragmentação da unidade.

1. Verifique se o disco está cheio. Se o disco estiver abaixo de 1 Tb, [expanda-o para um máximo de 1 Tb utilizando powerShell](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json).
2. Uma vez que o disco seja 1 Tb, efetue uma limpeza do disco.
    - [Retire o disco de dados do VM partido](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk).
    - [Fixe o disco de dados a um VM funcional](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm).
    - Utilize a [ferramenta de limpeza](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) do disco para libertar espaço.
3. Após redimensionamento e limpeza, desfragmente a unidade:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    Dependendo do nível de fragmentação, isto pode levar horas.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Recomendado: Antes de reconstruir o VM, ative a recolha de consolas em série e de despejo de memória

1. Abra uma sessão de solicitação de comando elevada (Corra como administrador).
2. Execute os seguintes comandos:

    Ativar a consola em série:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Certifique-se de que o espaço livre no disco OS é pelo menos igual ao tamanho da memória VM (RAM).

    Se não houver espaço suficiente no disco OS, altere o local onde o ficheiro de despejo de memória será criado e remeta-o para um disco de dados ligado ao VM e com espaço livre suficiente. Para alterar a `%SystemRoot%` localização, substitua-a pela letra de unidade (por exemplo"F:") do disco de dados nos comandos abaixo:

    **Ativar a configuração sugerida de despejo de OS:**

    Disco operativo osso avariado de carga:

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

    Descarregar disco operativo quebrado:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>Reconstruir o VM

Utilize [o passo 5 dos comandos de reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para remontar o VM.
