---
title: A máquina virtual Azure não responde ao aplicar a política
description: Este artigo fornece medidas para resolver problemas em que o ecrã de carga não responde quando se aplica uma política durante o arranque de um VM Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: cbf2fe491e1fe0b553eab04ca7190da0413a3ba6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526015"
---
# <a name="vm-is-unresponsive-when-applying-group-policy-local-users-and-groups-policy"></a>VM não responde ao aplicar a política de utilizadores e grupos locais de política de grupo

Este artigo fornece medidas para resolver problemas em que o ecrã de carga não responde quando uma máquina virtual Azure (VM) aplica uma política durante o arranque.

## <a name="symptoms"></a>Sintomas

Quando está a utilizar [diagnósticos de arranque](./boot-diagnostics.md) para visualizar uma imagem do VM, o ecrã está preso a carregar com a mensagem: "Aplicando a política de utilizadores e grupos locais de política de grupos."

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="Screenshot de Aplicação da Política de Grupo Utilizadores locais e grupos de carregamento de políticas (Windows Server 2012 R2).":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="Screenshot de Aplicação da Política de Grupo Utilizadores locais e grupos de carregamento de políticas (Windows Server 2012 R2).":::

## <a name="cause"></a>Causa

Há bloqueios contraditórios quando a política tenta limpar perfis antigos dos utilizadores.

> [!NOTE]
> Isto aplica-se apenas ao Windows Server 2012 e ao Windows Server 2012 R2.

Eis a política problemática:

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>Resolução

### <a name="process-overview"></a>Visão geral do processo

1. [Criar e aceder a um VM de reparação](#step-1-create-and-access-a-repair-vm)
1. [Desativar a política](#step-2-disable-the-policy)
1. [Ativar a recolha de consolas em série e de despejo de memória](#step-3-enable-serial-console-and-memory-dump-collection)
1. [Reconstruir o VM](#step-4-rebuild-the-vm)

> [!NOTE]
> Se encontrar este erro de arranque, o so convidado não está operacional. Tem de resolver problemas no modo Offline.

### <a name="step-1-create-and-access-a-repair-vm"></a>Passo 1: Criar e aceder a um VM de reparação

1. Utilize [os passos 1-3 dos comandos de reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para preparar um VM de reparação.
2. Utilize ligação de ambiente de trabalho remoto para ligar ao VM de reparação.

### <a name="step-2-disable-the-policy"></a>Passo 2: Desativar a política

1. Na VM de reparação, abra o Editor de Registos.
1. Localize a **chave HKEY_LOCAL_MACHINE** e selecione a Colmeia de Carga de **Ficheiros**no  >  **Load Hive** menu.

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="Screenshot de Aplicação da Política de Grupo Utilizadores locais e grupos de carregamento de políticas (Windows Server 2012 R2)." /v CleanupProfiles /f
    ```
1.  Descarregue a colmeia BROKENSOFTWARE utilizando este comando:

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>Passo 3: Ativar a recolha de consolas em série e de despejo de memória

Para ativar a recolha de lixo de memória e a consola em série, execute este script:

1. Abra uma sessão de pedido de comando elevado. (Executar como administrador.)
1. Executar estes comandos para ativar a consola em série:
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
1. Verifique se o espaço livre no disco de so é pelo menos igual ao tamanho da memória do VM (RAM).

    Se não houver espaço suficiente no disco de so, altere o local de despejo de memória e encaminhe-o para um disco de dados anexado com espaço livre suficiente. Para alterar a localização, substitua "%SystemRoot%" pela letra de unidade (por exemplo, "F:") do disco de dados nos seguintes comandos.

    **Configuração sugerida para permitir o despejo de SO**

    Carregar o disco de OSSO quebrado:

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

### <a name="step-4-rebuild-the-vm"></a>Passo 4: Reconstruir o VM

Utilize [o passo 5 dos comandos de reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para remontar o VM.

Se a questão for corrigida, a política é agora desativada localmente. Para uma solução permanente, não utilize a política de LimpezaProfiles em VMs. Utilize um método diferente para realizar limpezas de perfis.

Não use esta política:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>Passos seguintes

Se tiver problemas quando aplicar o Windows Update, veja que [o VM não responde com o erro "C01A001D" ao aplicar o Windows Update](./unresponsive-vm-apply-windows-update.md).
