---
title: Erro de paragem do Windows -
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 9e4c4b9c809a626c71b4a7e9235d917b442be160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373365"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Erro de paragem do Windows - #0x000000EF "Processo Crítico Morreu"

Este artigo fornece medidas para resolver questões em que um processo crítico morre durante o arranque de um VM Azure.

## <a name="symptom"></a>Sintoma

Quando utilizar os [diagnósticos boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para visualizar a imagem do VM, verá que a imagem mostra o erro *#0x000000EF* com a mensagem *"Processo Crítico Morreu*".

!["O seu PC teve um problema e precisa de recomeçar. Estamos apenas a recolher informações de erros, e depois podes recomeçar. (##% completo) Se quiser saber mais, pode pesquisar online mais tarde por este erro: 0x0000000EF"](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Causa

Normalmente, isto deve-se a uma falha do processo crítico do sistema durante o arranque. Pode ler mais sobre questões críticas do processo em "[Bug Check 0xEF: CRITICAL_PROCESS_DIED](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)".

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo:

1. Criar e aceder a um VM de reparação.
2. Corrigir qualquer corrupção do OS.
3. **Recomendado**: Antes de reconstruir o VM, ative a recolha de consolas em série e de despejo de memória.
4. Reconstruir o VM.

> [!NOTE]
> Ao encontrar este erro de arranque, o Os convidado não está operacional. Estará a resolver problemas no modo Offline para resolver este problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e aceder a um VM de reparação

1. Utilize [os passos 1-3 dos Comandos de Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) para preparar um VM de reparação.
2. Utilizando a ligação remota de ambiente de trabalho ao VM de reparação.

### <a name="fix-any-os-corruption"></a>Corrigir qualquer Corrupção de Os

1. Abra um pedido de comando elevado.
2. Executar o seguinte comando de verificador de ficheiros do sistema (SFC):

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Onde < drive drive de disco de arranque > é o volume de arranque do VM de reparação (tipicamente "C:") e < DISCO QUEBRADO > será a letra de unidade de acionamento para o disco anexado do VM avariado. Substitua os símbolos superiores/inferiores aos símbolos, bem como o texto contido neles, por exemplo, "< texto aqui >", com a letra adequada.

3. Em seguida, utilize [o passo 5 dos Comandos de Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para remontar o VM e ver se ele arranca.
4. Se o VM ainda não estiver a arrancar, continue a recolher o ficheiro de despejo de memória.

### <a name="collect-the-memory-dump-file"></a>Recolher o ficheiro de despejo de memória

Se o problema persistir após a execução do SFC, será necessária uma análise de um ficheiro de despejo de memória para determinar a causa do problema. Para recolher o ficheiro de despejo de memória, siga estes passos:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Fixe o disco OS a um novo VM de reparação

1. Utilize [os passos 1-3 dos Comandos de Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) para preparar um novo VM de reparação.
2. Utilizando a ligação remota de ambiente de trabalho ao VM de reparação.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localize o ficheiro de despejo e envie um bilhete de apoio

3. No VM de reparação, vá para a pasta das janelas no disco OS anexado. Se a letra do condutor que é atribuída ao disco operativo em anexo for *F,* terá de ir para *F:\Windows*.
4. Localize o ficheiro *memory.dmp* [e,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) em seguida, envie um bilhete de suporte com o ficheiro de despejo de memória.

   > [!NOTE]
   > Se não encontrar o ficheiro de despejo, complete os passos abaixo para ativar a recolha de despejode memória e a Consola Em Série, em seguida, volte a esta secção e repita os passos na tarefa acima para recolher o ficheiro de despejo de memória.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Recomendado: Antes de reconstruir o VM, ative a recolha de consolas em série e de despejo de memória

Para permitir a recolha de despejo de memória e consola em série, execute o seguinte script:

1. Abra uma sessão de solicitação de comando elevada (Corra como administrador).
2. Execute os seguintes comandos:

   Ativar a consola em série

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Substitua qualquer um dos símbolos superiores ou inferiores aos símbolos, bem como o texto que lhes é atribuído, por exemplo, "< texto aqui >".

3. Verifique se o espaço livre no disco OS é tanto quanto o tamanho da memória (RAM) no VM.

Se não houver espaço suficiente no disco OS, deverá alterar o local onde será criado o ficheiro de despejo de memória e encaminhá-lo para qualquer disco de dados ligado ao VM que tenha espaço livre suficiente. Para alterar a localização, substitua "%SystemRoot%" pela letra de unidade (por exemplo, "F:") do disco de dados nos comandos abaixo.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Configuração sugerida para ativar o despejo de OS

**Disco operativo osso avariado**de carga:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Ativar no ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Ativar no ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Descarregar disco operativo quebrado:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Reconstruir o VM original

Utilize [o passo 5 dos Comandos de Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para remontar o VM.
