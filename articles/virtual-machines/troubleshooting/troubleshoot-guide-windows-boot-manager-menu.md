---
title: Máquina virtual do Windows não pode arrancar devido ao gestor de boot sacar janelas
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
ms.openlocfilehash: 5d2fb62870e2c41af635627f5d692f08c67f8394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373352"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Windows VM não pode arrancar devido ao Windows Boot Manager

Este artigo fornece passos para resolver problemas em que o Windows Boot Manager impede o arranque de uma Máquina Virtual Azure (VM).

## <a name="symptom"></a>Sintoma

O VM está preso à espera de um pedido de utilizador e não arranca a menos que seja instruído manualmente.

Quando utilizar os [diagnósticos do Boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para visualizar a imagem do VM, verá que a imagem mostra o Windows Boot Manager com a mensagem *Escolha um sistema operativo para iniciar ou prima TAB para selecionar uma ferramenta:*.

Figura 1
 
![Gestor de arranque do Windows afirmando "Escolha um sistema operativo para iniciar ou prima TAB para selecionar uma ferramenta:"](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Causa

O erro deve-se a um menu de *exibição* de bandeira BCD no Windows Boot Manager. Quando a bandeira está ativada, o Windows Boot Manager solicita ao utilizador, durante o processo de arranque, que selecione qual o carregador que pretende executar, causando um atraso no arranque. Em Azure, esta funcionalidade pode aumentar o tempo que leva para iniciar um VM.

## <a name="solution"></a>Solução

Visão geral do processo:

1. Configure para um tempo de arranque mais rápido utilizando a consola em série.
2. Criar e aceder a um VM de reparação.
3. Configure para tempo de arranque mais rápido num VM de reparação.
4. **Recomendado**: Antes de reconstruir o VM, ative a recolha de consolas em série e de despejo de memória.
5. Reconstruir o VM.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Configure para tempo de arranque mais rápido usando a consola em série

Se tiveres acesso à consola em série, existem duas maneiras de conseguires tempos de arranque mais rápidos. Ou diminui o tempo de espera do *menu de visualização* ou remova completamente a bandeira.

1. Siga as instruções para aceder à [Consola Em Série Azure para windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) para ter acesso à consola baseada em texto.

   > [!NOTE]
   > Se não conseguir aceder à consola em série, salte para criar e aceder a [um VM de reparação](#create-and-access-a-repair-vm).

2. **Opção A:** Reduzir o tempo de espera

   a. O tempo de espera é definido a 30 segundos por defeito, mas pode ser alterado para um tempo mais rápido (por exemplo, 5 segundos).

   b. Utilize o seguinte comando na consola em série para ajustar o valor do tempo de paragem:

      `bcdedit /set {bootmgr} timeout 5`

3. **Opção B:** Retire a bandeira bcd

   a. Para evitar que o menu de arranque do display se insira completamente, introduza o seguinte comando:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Se não foi capaz de utilizar a consola em série para configurar um tempo de arranque mais rápido nos degraus acima, pode, em vez disso, continuar com os seguintes passos. Agora vai supor problemas em modo offline para resolver este problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e aceder a um VM de reparação

1. Utilize [os passos 1-3 dos Comandos de Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) para preparar um VM de reparação.
2. Utilize a ligação remota de ambiente de trabalho ao VM de reparação.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Configure para tempo de arranque mais rápido em um VM de reparação

1. Abra um pedido de comando elevado.
2. Introduza o seguinte para ativar o DisplayBootMenu:

   Utilize este comando para **VMs da Geração 1:**

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   Utilize este comando para **VMs da Geração 2:**

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Substitua qualquer um dos símbolos superiores ou inferiores aos símbolos, bem como o texto que lhes é atribuído, por exemplo, "< texto aqui >".

3. Alterar o valor do tempo de saída para 5 segundos:

   Utilize este comando para **VMs da Geração 1:**

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   Utilize este comando para **VMs da Geração 2:**

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Substitua qualquer um dos símbolos superiores ou inferiores aos símbolos, bem como o texto que lhes é atribuído, por exemplo, "< texto aqui >".

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