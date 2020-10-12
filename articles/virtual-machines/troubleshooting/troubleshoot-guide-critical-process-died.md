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
ms.openlocfilehash: c04f3b27c7214dcf821c7698796bfaea399b947d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86509108"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Erro de paragem do Windows - #0x000000EF "Processo Crítico Morreu"

Este artigo fornece medidas para resolver problemas em que um processo crítico morre durante o arranque de um VM Azure.

## <a name="symptom"></a>Sintoma

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, verá que a imagem mostra o erro *#0x000000EF* com a mensagem Critical *Process Died*.

!["O seu PC teve um problema e precisa de reiniciar. Estamos só a recolher algumas informações de erro, e depois podes recomeçar. (##% completo) Se quiser saber mais, pode pesquisar online mais tarde por este erro: 0x00000EF"](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Causa

Normalmente, isto deve-se a uma falha crítica do sistema durante o arranque. Pode ler mais sobre problemas críticos de processo em "[Bug Check 0xEF: CRITICAL_PROCESS_DIED](/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)".

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo:

1. Criar e Aceder a um VM de reparação.
2. Conserte qualquer corrupção de SO.
3. **Recomendado:** Antes de reconstruir o VM, ative a recolha da consola em série e do despejo de memória.
4. Reconstruir o VM.

> [!NOTE]
> Ao encontrar este erro de arranque, o So convidado não está operacional. Estará a resolver problemas no modo Offline para resolver este problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e Aceder a um VM de reparação

1. Utilize [os passos 1-3 dos Comandos de Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar um VM de reparação.
2. Utilizando ligação de ambiente de trabalho remoto, ligue-se ao VM de reparação.

### <a name="fix-any-os-corruption"></a>Corrija qualquer corrupção de OS

1. Abra um pedido de comando elevado.
2. Executar o seguinte comando Verificador de Ficheiros do Sistema (SFC):

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Onde < DRIVE DRIVE DE DISCO DE ARRANQUE > é o volume de arranque do VM de reparação (normalmente "C:") e < DISCO QUEBRADO drive > será a letra de acionamento do disco anexo a partir do VM quebrado. Substitua o texto superior/inferior ao dos símbolos, bem como o texto contido neles, por exemplo " < texto aqui >", com a carta apropriada.

3. Em seguida, utilize [o passo 5 dos Comandos de Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para remontar o VM e ver se é botas.
4. Se o VM ainda não estiver a arrancar, então continue a recolher o ficheiro de despejo de memória.

### <a name="collect-the-memory-dump-file"></a>Recolher o ficheiro de despejo de memória

Se o problema persistir após a execução do SFC, será necessária uma análise de um ficheiro de despejo de memória para determinar a causa do problema. Para recolher o ficheiro de despejo de memória, siga estes passos:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Fixe o disco DE a um novo VM de reparação

1. Utilize [os passos 1-3 dos Comandos de Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar um novo VM de reparação.
2. Utilizando ligação de ambiente de trabalho remoto, ligue-se ao VM de reparação.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localize o ficheiro de despejo e envie um bilhete de apoio

3. Na vM de reparação, vá à pasta do janela no disco oss anexado. Se a letra do controlador que é atribuída ao disco de oss anexado for *F,* tem de ir para *F:\Windows*.
4. Localize o ficheiro *memory.dmp* e, em seguida, [envie um bilhete de apoio](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o ficheiro de despejo de memória.

   > [!NOTE]
   > Se não conseguir encontrar o ficheiro de despejo, preencha os passos abaixo para permitir a recolha de lixo de memória e consola em série, em seguida, volte a esta secção e repita os passos na tarefa acima para recolher o ficheiro de despejo de memória.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Recomendado: Antes de reconstruir o VM, ative a recolha de consolas em série e de despejo de memória

Para ativar a recolha de despejo de memória e a Consola em Série, execute o seguinte script:

1. Abra uma sessão de solicitação de comando elevada (Executar como administrador).
2. Execute os seguintes comandos:

   Ativar consola em série

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Substitua qualquer símbolo maior ou inferior ao que os símbolos, bem como o texto neles, por exemplo, "< texto aqui >".

3. Verifique se o espaço livre no disco de so é tanto quanto o tamanho da memória (RAM) no VM.

Se não houver espaço suficiente no disco oss, deve alterar o local onde o ficheiro de despejo de memória será criado e encaminhá-lo para qualquer disco de dados ligado ao VM que tenha espaço livre suficiente. Para alterar a localização, substitua "%SystemRoot%" pela letra de unidade (por exemplo, "F:") do disco de dados nos comandos abaixo.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Configuração sugerida para permitir o despejo de OS

**Carregar disco de OS quebrado:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Ativar no ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Ativar no ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Descarregar disco de SO quebrado:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Reconstruir o VM Original

Utilize [o passo 5 dos Comandos de Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para remontar o VM.
