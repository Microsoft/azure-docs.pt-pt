---
title: Erro de paragem do Windows - 0x000000074 Bad System Config Info
description: Este artigo fornece medidas para resolver problemas em que o Windows não pode arrancar e precisa de ser reiniciado devido a informações de configuração do sistema numa Máquina Virtual Azure (VM).
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a1e47f6a-c7d5-4327-a7b0-ad48ed543641
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: 7d1233c97ec80d5a2efa8b53c68e9e07a823165d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977036"
---
# <a name="windows-stop-error---0x00000074-bad-system-config-info"></a>Erro de paragem do Windows - 0x000000074 Bad System Config Info

Este artigo fornece medidas para resolver problemas em que o Windows não pode arrancar e precisa de ser reiniciado devido a informações de configuração do sistema numa Máquina Virtual Azure (VM).

## <a name="symptom"></a>Sintoma

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, verá que a imagem exibe o código de paragem do Windows **#0x00000074** ou **BAD_SYSTEM_CONFIG_INFO**.

*O seu PC teve um problema e precisa de reiniciar. Pode recomeçar.* 
 *Para mais informações http://windows.com/stopcode sobre esta questão e possíveis correções, visite* 
 *Se ligar para uma pessoa de apoio, dê-lhe esta informação:* 
 *Código de paragem: BAD_SYSTEM_CONFIG_INFO*

  ![O código de paragem do Windows 0x00000074, que também é mostrado como "BAD_SYSTEM_CONFIG_INFO". O Windows informa o utilizador de que o seu PC teve um problema e precisa de reiniciar.](./media/windows-stop-error-bad-system-config-info/1.png)

## <a name="cause"></a>Causa

O código de paragem **BAD_SYSTEM_CONFIG_INFO** ocorre se a colmeia de registo **do SISTEMA** parecer estar corrompida. Este erro pode ser causado por qualquer uma destas razões:

- A colmeia do registo não estava bem fechada.
- A colmeia do registo é corrupta.
- Faltam chaves ou valores de registo.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo:

1. Criar e Aceder a um VM de reparação.
1. Verifique se há corrupção de colmeias.
1. Ativar a recolha de consolas em série e de despejo de memória.
1. Reconstruir o VM.

> [!NOTE]
> Ao encontrar este erro, o sistema operativo Guest (OS) não está operacional. Irá resolver problemas em modo offline para resolver este problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e aceder a um VM de reparação

1. Utilize os passos 1-3 dos Comandos de [Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar um VM de reparação.
1. Verifique se há corrupção de colmeias.
1. Utilize ligação de ambiente de trabalho remoto para ligar ao VM de reparação.
1. Copie a `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` pasta e guarde-a na sua divisória de disco saudável ou noutro local seguro. Faça o reforço desta pasta como precaução, uma vez que irá editar ficheiros de registo críticos. 

> [!NOTE]
> Faça uma cópia da `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` pasta como cópia de segurança no caso de precisar de reverter quaisquer alterações que fizer ao registo.

### <a name="check-for-hive-corruption"></a>Verifique a corrupção da colmeia

As instruções abaixo vão ajudá-lo a determinar se a causa foi devido à corrupção da colmeia, ou se a colmeia não foi fechada corretamente. Se a colmeia não foi fechada corretamente, então poderá desbloquear o ficheiro e consertar o seu VM.

1. Na sua VM de reparação, abra a aplicação **do Editor de Registo.** Digite "REGEDIT" na barra de pesquisa do Windows para encontrá-lo.
1. No Editor de Registos, selecione **HKEY_LOCAL_MACHINE** para realçá-lo e, em seguida, selecione **Ficheiro > Load Hive...** do menu.
1. Navegue e `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM` selecione **Abrir**.
1. Quando solicitado para introduzir um nome, **insira BROKENSYSTEM**.

   1. Se a colmeia não abrir, ou se estiver vazia, então a colmeia é corrompida. Se a colmeia tiver sido corrompida, [abra um bilhete de apoio.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

     ![Ocorre um erro indicando que o Editor de Registo não pode carregar a colmeia.](./media/windows-stop-error-bad-system-config-info/2.png)

   1. Se a colmeia abrir normalmente, então a colmeia não estava bem fechada. Continue a passo 5.

1. Para corrigir uma colmeia que não foi fechada corretamente, realce **o BROKENSYSTEM** e, em seguida, selecione **File > Descarregar Colmeia...** para desbloquear o ficheiro.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Ativar a consola em série e a recolha de despejo de memória

**Recomendado:** Antes de reconstruir o VM, ative a coleção de consola em série e de despejo de memória executando o seguinte script:

1. Abra uma sessão de pedido de comando elevado como administrador.
1. Execute os seguintes comandos:

   **Ativar a Consola em Série:**
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Verifique se o espaço livre no disco de so é maior do que o tamanho da memória (RAM) no VM.

   Se não houver espaço suficiente no disco de so, altere o local onde o ficheiro de despejo de memória será criado e encaminhe essa localização para qualquer disco de dados anexado ao VM que tenha espaço livre suficiente. Para alterar a localização, substitua **%SystemRoot%** pela letra de acionamento do disco de dados, como **F:**, nos seguintes comandos.

   Configuração sugerida para permitir o despejo de OS:

   **Colmeia de registo de carga do disco de oss quebrado:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **Ativar no ControlSet001:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Ativar no ControlSet002:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Descarregar disco de SO quebrado:**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Reconstruir o VM

Utilize [o passo 5 dos Comandos de Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para reconstruir o VM.