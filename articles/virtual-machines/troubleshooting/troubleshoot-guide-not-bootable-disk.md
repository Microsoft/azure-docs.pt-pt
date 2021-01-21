---
title: Erro de arranque – "isto não é um disco de arranque"
description: Este artigo fornece passos para resolver problemas em que o disco não é bootable em uma Máquina Virtual Azure
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 14da41815e177ece64c72ac27a7cb126e69fdc62
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633193"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Erro de arranque – Este não é um disco bootable

Este artigo fornece passos para resolver problemas em que o disco não é bootable numa Máquina Virtual Azure (VM).

## <a name="symptoms"></a>Sintomas

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, verá que a imagem exibe uma solicitação com a mensagem "Isto não é um disco de arranque. Por favor, insira uma floppy bootable e pressione qualquer tecla para tentar novamente...'.

   Figura 1

   ![A figura 1 mostra a mensagem *"Este não é um disco de arranque. Por favor, insira um floppy bootable e pressione qualquer tecla para tentar novamente..."*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Causa

Esta mensagem de erro significa que o processo de arranque do SISTEMA não conseguiu localizar uma divisória do sistema ativo. Este erro também pode significar que há uma referência em falta na loja De Dados de Configuração de Arranque (BCD), impedindo-a de localizar a partição do Windows.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo

> [!TIP]
> Se tiver uma cópia de segurança recente do VM, poderá tentar [restaurar o VM da cópia de segurança](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema da bota.

1. Criar e Aceder a um VM de reparação.
2. Desajei o estado de partição para ativo.
3. Fixe a partição do disco.
4. **Recomendado:** Antes de reconstruir o VM, ative a recolha da consola em série e do despejo de memória.
5. Reconstruir o VM original.

   > [!NOTE]
   > Ao encontrar este erro de arranque, o SO convidado não está operacional. Vai resolver problemas em modo offline para resolver este problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e Aceder a um VM de reparação

1. Utilize os passos 1-3 dos Comandos de [Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar um VM de reparação.
2. Utilizando ligação de ambiente de trabalho remoto, ligue-se ao VM de reparação.

### <a name="set-partition-status-to-active"></a>Definir o estado de partição para ativo

Os VMs da geração 1 devem verificar primeiro se a partição de SO, que detém a loja BCD, está marcada como *ativa.* Se tiveres uma Geração 2 VM, antecipa-te à [Correção da Partição do Disco,](#fix-the-disk-partition)já que a bandeira do *Status* foi depreciada na geração posterior.

1. Abra uma solicitação de comando elevada *(cmd.exe)*.
2. Introduza a *parte do disco* para lançar a ferramenta DISKPART.
3. Introduza o *disco de lista* para listar os discos do sistema e identifique o OS VHD anexado.
4. Uma vez localizado o OS VHD anexado, *introduza o disco sel #* para selecionar o disco.  Consulte a Figura 2, onde o Disco 1 é o VHD do OS anexado.

   Figura 2

   ![A figura 2 mostra a janela *DISKPART* que mostra a saída do comando do disco de lista, disco 0 e disco 1 apresentados na tabela.  Também mostra a saída do comando sel disco 1, O Disco 1 é o disco selecionado](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. Uma vez selecionado o disco, introduza *a partição* da lista para listar as divisórias do disco selecionado.
6. Assim que a partição da bota for identificada, *insira a partição sel #* para selecionar a partição.  Normalmente, a partição de botas terá cerca de 350 MB de tamanho.  Consulte a Figura 3, onde a partição 1 é a divisória de arranque.

   Figura 3

   ![A figura 3 mostra a janela *DISKPART* com a saída do comando *list partition*. A partição 1 e a partição 2 são apresentadas na tabela. Também mostra a saída do comando de divisão *sel 1*, quando a partição 1 é o disco selecionado.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Introduza a "partição detalhada" para verificar o estado da partição. Ver Figura 4, onde a partição está *Ativa: Não,* ou Figura 5, onde a partição é 'Ative: Sim'.

   Figura 4

   ![A figura 4 mostra a janela *DISKPART* com a saída do comando de partição *detalhe*, quando a Partição 1 está definida para *Ative: No*](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   Figura 5

   ![A figura 5 mostra a janela *DISKPART* com a saída do comando de partição *detalhe*, quando a Partição 1 está definida para *Ative: Yes*.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Se a partição não estiver **Ativa,** entre *ativa* para alterar a bandeira *Ative.*
9. Verifique se a alteração de estado foi feita corretamente digitando *a partição de pormenores*.

   Figura 6

   ![A figura 6 mostra a janela da parte do disco com a saída do comando de partição *detalhe*, quando a Partição 1 está definida para *Ativo: Sim*](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. Introduza a *saída* para fechar a ferramenta DISKPART e guarde as alterações de configuração.

### <a name="fix-the-disk-partition"></a>Fixar a partição do disco

1. Abra uma solicitação de comando elevada (cmd.exe).
2. Utilize o seguinte comando para executar *CHKDSK* no(s) disco(s) e corrigir erros:

   `chkdsk <DRIVE LETTER>: /f`

   A adição da opção de comando '/f' corrigirá quaisquer erros no disco. Certifique-se de que substitui <DRIVE LETTER> a letra do OS VHD anexado.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Recomendado: Antes de reconstruir o VM, ative a recolha de consolas em série e de despejo de memória

Para ativar a recolha de despejo de memória e a Consola em Série, execute o seguinte script:

1. Abra uma sessão de solicitação de comando elevada (Executar como administrador).
2. Execute os seguintes comandos:

   Ativar consola em série

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

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
