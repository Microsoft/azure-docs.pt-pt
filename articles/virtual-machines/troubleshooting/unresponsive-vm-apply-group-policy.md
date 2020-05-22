---
title: Máquina virtual Azure não responde ao aplicar política
description: Este artigo fornece medidas para resolver problemas em que o ecrã de carga está preso ao aplicar uma apólice durante o arranque num VM Azure.
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
ms.openlocfilehash: 30f833bc49f92dcabfc75f0a1507c6f540bdea24
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749278"
---
# <a name="vm-becomes-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>VM torna-se insensível ao aplicar a política de "política de grupo utilizadores locais & grupos"

Este artigo fornece medidas para resolver problemas em que o ecrã de carga está preso ao aplicar uma apólice durante o arranque num VM Azure.

## <a name="symptoms"></a>Sintomas

Ao utilizar os diagnósticos boot para visualizar uma imagem do VM, o ecrã fica preso a carregar com a mensagem: ' Aplicação da política de*grupo Utilizadores locais e de grupos'.* [Boot diagnostics](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="Screenshot da aplicação da política do grupo Utilizadores locais e carregamento de grupos (Windows Server 2012 R2).":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="Screenshot da aplicação da política do grupo Utilizadores locais e carregamento de grupos (Windows Server 2012).":::

## <a name="cause"></a>Causa

Há bloqueios contraditórios quando a política tenta limpar perfis antigos dos utilizadores.

> [!NOTE]
> Isto aplica-se apenas ao Windows Server 2012 e ao Windows Server 2012 R2.

Aqui está a política problemática:

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>Resolução

### <a name="process-overview"></a>Visão geral do processo

1. [Criar e aceder a um VM de reparação](#step-1-create-and-access-a-repair-vm)
2. [Desativar a política](#step-2-disable-the-policy)
3. [Ativar a consola em série e a recolha de despejo de memória](#step-3-enable-serial-console-and-memory-dump-collection)
4. [Reconstruir o VM](#step-4-rebuild-the-vm)

> [!NOTE]
> Se encontrar este erro de arranque, o Os convidado não está operacional. Tens de resolver problemas no modo Offline.

### <a name="step-1-create-and-access-a-repair-vm"></a>Passo 1: Criar e aceder a um VM de reparação

1. Utilize [os passos 1-3 dos comandos de reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para preparar um VM de reparação.
2. Utilize a ligação remota de ambiente de trabalho à VM de reparação.

### <a name="step-2-disable-the-policy"></a>Passo 2: Desativar a política

1. Na reparação, abra o Editor de Registo.
2. Localize a **HKEY_LOCAL_MACHINE** chave e selecione **File**  >  **Load Hive...** do menu.

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="A screenshot mostra HKEY_LOCAL_MACHINE realçadas e o menu contendo Load Hive.":::

    - A Load Hive permite-lhe carregar as teclas de registo de um sistema offline, neste caso o disco partido ligado ao VM de reparação.
    - As definições em todo o sistema são armazenadas `HKEY_LOCAL_MACHINE` e podem ser abreviadas como "HKLM".
3. No disco anexo, vá ao `\windows\system32\config\SOFTWARE` ficheiro e abra-o.

    1. Será solicitado para um nome. Introduza o SOFTWARE QUEBRADO.<br/>
    2. Para verificar se o BROKENSOFTWARE foi carregado, expanda **HKEY_LOCAL_MACHINE** e procure a tecla BROKENSOFTWARE adicionada.
4. Navegue para BROKENSOFTWARE e verifique se a chave CleanupProfile existe na colmeia carregada.

    1. Se a chave existir, então a política CleanupProfile é definida, o seu valor representa a política de retenção em dias. Continua a apagar a chave.<br/>
    2. Se a chave não existir, a política cleanupProfile não está definida. [Envie um bilhete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), incluindo o ficheiro memory.dmp localizado no diretório windows do disco OS anexo.

5. Eliminar a tecla CleanupProfiles utilizando este comando:

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
6.  Descarregue a colmeia BROKENSOFTWARE utilizando este comando:

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>Passo 3: Ativar a consola em série e a recolha de despejo de memória

Para permitir a recolha de despejo de memória e consola em série, execute este script:

1. Abra uma sessão de solicitação de comando elevada (Corra como administrador).
2. Execute estes comandos:

    **Ativar a consola em série:** 
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
3. Verifique se o espaço livre no disco OS é pelo menos igual ao tamanho da memória do VM (RAM).

    Se não houver espaço suficiente no disco OS, altere a localização do depósito de memória e remeta-o para um disco de dados anexo com espaço livre suficiente. Para alterar a localização, substitua "%SystemRoot%" pela letra de unidade (por exemplo, "F:") do disco de dados nos comandos abaixo.

    **Configuração sugerida para ativar o despejo de OS:**

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

### <a name="step-4-rebuild-the-vm"></a>Passo 4: Reconstruir o VM

Utilize [o passo 5 dos Comandos de Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para remontar o VM.

Se a questão for corrigida, a política foi desativada localmente. Para uma solução permanente, não utilize a política CleanupProfiles em VMs. Utilize um método diferente para efetuar limpezas de perfis.

Não use esta política:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>Passos seguintes

Se encontrar problemas ao aplicar o Windows Update, veja que [o VM não responde com o erro "C01A001D" ao aplicar o Windows Update](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/unresponsive-vm-apply-windows-update).