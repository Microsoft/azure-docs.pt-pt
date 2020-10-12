---
title: Máquina virtual do Windows não pode arrancar devido ao gestor de botas windows
description: Este artigo fornece medidas para resolver problemas em que o Windows Boot Manager impede o arranque de uma Máquina Virtual Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 2457952051f575306de46e3e8145cc26678a1ef8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526544"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Windows VM não pode arrancar devido ao Windows Boot Manager

Este artigo fornece medidas para resolver problemas em que o Windows Boot Manager impede o arranque de uma Máquina Virtual Azure (VM).

## <a name="symptom"></a>Sintoma

O VM fica preso à espera de uma solicitação do utilizador e não é iniciado a menos que instrua manualmente.

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, verá que a imagem exibe o Gestor de Boot do Windows com a mensagem *Escolha um sistema operativo para iniciar ou prima TAB para selecionar uma ferramenta:*

Figura 1
 
![Gestor de boot do Windows afirmando "Escolha um sistema operativo para iniciar ou pressione TAB para selecionar uma ferramenta:"](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Causa

O erro deve-se a um *displaybootmenu* de bandeira BCD no Windows Boot Manager. Quando a bandeira está ativada, o Windows Boot Manager pede ao utilizador, durante o processo de arranque, que selecione o carregador que pretende executar, causando um atraso no arranque. Em Azure, esta funcionalidade pode adicionar ao tempo que leva para iniciar um VM.

## <a name="solution"></a>Solução

Visão geral do processo:

1. Configurar para tempo de arranque mais rápido utilizando a consola em série.
2. Criar e Aceder a um VM de reparação.
3. Configurar para tempo de arranque mais rápido num VM de reparação.
4. **Recomendado:** Antes de reconstruir o VM, ative a recolha da consola em série e do despejo de memória.
5. Reconstruir o VM.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Configurar para tempo de arranque mais rápido usando consola em série

Se tiver acesso à consola em série, há duas formas de conseguir tempos de arranque mais rápidos. Diminua o tempo de espera *do displaybootmenu* ou retire completamente a bandeira.

1. Siga as instruções para aceder ao [Azure Serial Console para](./serial-console-windows.md) que o Windows obtenha acesso à consola baseada em texto.

   > [!NOTE]
   > Se não conseguir aceder à consola em série, avance para criar e aceder a [um VM de reparação](#create-and-access-a-repair-vm).

2. **Opção A**: Reduzir o tempo de espera

   a. O tempo de espera é definido em 30 segundos por defeito, mas pode ser alterado para um tempo mais rápido (por exemplo, 5 segundos).

   b. Utilize o seguinte comando na consola em série para ajustar o valor do tempo limite:

      `bcdedit /set {bootmgr} timeout 5`

3. **Opção B**: Retire a bandeira do BCD

   a. Para evitar que o Menu de Arranque do Ecrã indique completamente, insira o seguinte comando:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Se não foi capaz de utilizar a consola em série para configurar um tempo de arranque mais rápido nos degraus acima, pode, em vez disso, continuar com os seguintes passos. Vai agora resolver problemas em modo offline para resolver este problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e Aceder a um VM de reparação

1. Utilize [os passos 1-3 dos Comandos de Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar um VM de reparação.
2. Utilize ligação de ambiente de trabalho remoto à VM de reparação.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Configurar para tempo de arranque mais rápido num VM de reparação

1. Abra um pedido de comando elevado.
2. Introduza o seguinte para ativar o DisplayBootMenu:

   Utilize este comando para **VMs da Geração 1:**

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   Utilize este comando para **VMs da Geração 2:**

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Substitua qualquer símbolo maior ou inferior ao que os símbolos, bem como o texto neles, por exemplo, "< texto aqui >".

3. Altere o valor de tempo limite para 5 segundos:

   Utilize este comando para **VMs da Geração 1:**

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   Utilize este comando para **VMs da Geração 2:**

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Substitua qualquer símbolo maior ou inferior ao que os símbolos, bem como o texto neles, por exemplo, "< texto aqui >".

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
