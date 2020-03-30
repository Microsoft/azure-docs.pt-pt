---
title: Erro de arranque – "isto não é um disco sabotável"
description: Este artigo fornece passos para resolver problemas em que o disco não é saqueado numa Máquina Virtual Azure
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
ms.openlocfilehash: 9f0c6350b89dcfecefcadcc166f7af35abc4b128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80300982"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Erro de arranque – Este não é um disco bootable

Este artigo fornece passos para resolver problemas em que o disco não é saqueado numa Máquina Virtual Azure (VM).

## <a name="symptoms"></a>Sintomas

Quando utilizar os [diagnósticos do Boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para visualizar a imagem do VM, verá que a imagem apresenta um aviso com a mensagem "Este não é um disco de arranque. Por favor, insira um floppy sinuoso e pressione qualquer tecla para tentar novamente...'.

   Figura 1

   ![A figura 1 mostra a mensagem *"Este não é um disco saqueável. Por favor, insira um floppy sinuoso e pressione qualquer tecla para tentar novamente..."*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Causa

Esta mensagem de erro significa que o processo de arranque do SISTEMA operativo não conseguiu localizar uma partição do sistema ativo. Este erro também pode significar que há uma referência em falta na loja De dados de configuração de arranque (BCD), impedindo-a de localizar a divisória do Windows.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo

1. Criar e aceder a um VM de reparação.
2. Detete o estado da partilha para ativo.
3. Fixe a partição do disco.
4. **Recomendado**: Antes de reconstruir o VM, ative a recolha de consolas em série e de despejo de memória.
5. Reconstruir o VM original.

   > [!NOTE]
   > Ao encontrar este erro de arranque, o Os convidado não está operacional. Vais estar a resolver problemas em modo offline para resolver este problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e aceder a um VM de reparação

1. Utilize os passos 1-3 dos Comandos de [Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) para preparar um VM de reparação.
2. Utilizando a ligação remota de ambiente de trabalho ao VM de reparação.

### <a name="set-partition-status-to-active"></a>Definir o estado da partilha para ativo

Os VMs da geração 1 devem primeiro verificar se a partição de SO, que detém a loja BCD, está marcada como *ativa*. Se tiver um VM da Geração 2, avance para [fixar a Partição do Disco,](#fix-the-disk-partition)uma vez que a bandeira de *Status* foi depreciada na geração posterior.

1. Abra um pedido de comando elevado *(cmd.exe)*.
2. Introduza *a peça de disco* para lançar a ferramenta DISKPART.
3. Introduza *o disco da lista* para listar os discos no sistema e identificar o VHD osso anexado.
4. Assim que estiver localizado o VHD osso em anexo, introduza o *disco sel #* para selecionar o disco.  Consulte a Figura 2, onde o disco 1 é o VHD osso anexado.

   Figura 2

   ![A figura 2 mostra a janela *DISKPART* mostrando a saída do comando do disco da lista, disco 0 e disco 1 exibido na tabela.  Também mostra a saída do comando do disco de el 1, o disco 1 é o disco selecionado](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. Uma vez selecionado o disco, introduza a *divisória da lista* para listar as divisórias do disco selecionado.
6. Uma vez identificada a partição da bota, introduza a *partição* de sel # para selecionar a partição.  Normalmente, a partição de botas terá cerca de 350 MB de tamanho.  Consulte a Figura 3, onde a partição 1 é a partição da bota.

   Figura 3

   ![A figura 3 mostra a janela *DISKPART* com a saída do comando de partição *lista*lista*. Partição 1 e Partição 2 estão expostos na tabela. Também mostra a saída do comando *sel divisória 1** quando a partição 1 é o disco selecionado.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Introduza a 'partição de detalhes' para verificar o estado da partição. Consulte a Figura 4, onde a partição está *ativa: Não,* ou figura 5, onde a partição é 'Ativa: Sim'.

   Figura 4

   ![A figura 4 mostra a janela *DISKPART* com a saída do comando *detail partition**, quando a partição 1 está definida para *Ativo: Não*](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   Figura 5

   ![A figura 5 mostra a janela *DISKPART* com a saída do comando de partição *detalhe*, quando a partição 1 está definida para *Ativo: Sim*.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Se a divisória não estiver **ativa,** introduza *ativamente* para alterar a bandeira *Ativa.*
9. Verifique se a alteração de estado foi feita corretamente escrevendo a *partição*de detalhes .

   Figura 6

   ![A figura 6 mostra a janela da parte do disco com a saída do comando *detail partition** quando a partição 1 está definida para *Ativo: Sim*](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. Introduza a *saída* para fechar a ferramenta DISKPART e guarde as alterações de configuração.

### <a name="fix-the-disk-partition"></a>Fixar a Partição do Disco

1. Abra um pedido de comando elevado (cmd.exe).
2. Utilize o seguinte comando para executar *CHKDSK* no disco ou erros:

   `chkdsk <DRIVE LETTER>: /f`

   A adição da opção de comando '/f' corrigirá quaisquer erros no disco. Certifique-se <DRIVE LETTER> de que substitui com a letra do VHD osso anexado.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Recomendado: Antes de reconstruir o VM, ative a recolha de consolas em série e de despejo de memória

Para permitir a recolha de despejo de memória e consola em série, execute o seguinte script:

1. Abra uma sessão de solicitação de comando elevada (Corra como administrador).
2. Execute os seguintes comandos:

   Ativar a consola em série

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

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
