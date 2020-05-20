---
title: Start-up os saturação de problemas – capacidade de instalação do Windows Update
description: Passos para resolver problemas em que o Windows Update (KB) obtém um erro e fica sem resposta num VM Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 6359e486-7b02-4c1e-995c-ef6d505f85f4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: bb7b641a7169c6577320f07a964d278ac1727b1c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664698"
---
# <a name="troubleshoot-os-start-up--windows-update-installation-capacity"></a>Start-up os saturação de problemas – capacidade de instalação do Windows Update

Este artigo fornece passos para resolver problemas numa máquina virtual Azure (VM) onde o Windows Update (KB) obtém um erro e fica sem resposta.

## <a name="symptom"></a>Sintoma

Quando utilizar os diagnósticos do Boot para visualizar a imagem do VM, verá que a imagem mostra o Windows Update (KB) em andamento, mas falha ndo com o código de erro: **C01A001D**. A imagem que se segue mostra o Windows Update (KB) preso com a mensagem "Error C01A001D aplicando operação de atualização ##### de ##############":

![O Windows Update (KB) está preso à mensagem: "Error C01A001D aplicando a operação de atualização X de Y (Z)".](./media/troubleshoot-windows-update-installation-capacity/1.png)

## <a name="cause"></a>Causa

Nesta situação, o sistema operativo (OS) não consegue concluir uma instalação do Windows Update (KB), uma vez que não é possível criar um ficheiro central no sistema de ficheiros. Com base neste código de erro, o sistema operativo não consegue escrever ficheiros no disco.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo:

1. Criar e aceder a um VM de reparação.
1. Espaço livre no disco.
1. Ativar a recolha de consolas em série e de despejo de memória.
1. Reconstruir o VM.

> [!NOTE]
> Ao deparar-se com este erro, o Os convidado não está operacional. Problemas resolvem este problema em modo offline para resolver este problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e aceder a um VM de reparação

1. Utilize os passos 1-3 dos Comandos de [Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) para preparar um VM de reparação.
1. Utilizando a ligação remota de ambiente de trabalho, ligue-se ao VM de reparação.

### <a name="free-up-space-on-the-disk"></a>Free Up Space no disco

Para resolver o problema:

- Redimensione o disco até 1 TB se já não estiver no tamanho máximo de 1 TB.
- Faça uma limpeza de disco.
- Desfragmentar a unidade.

1. Verifique se o disco está cheio. Se o tamanho do disco for inferior a 1 TB, expanda-o até um máximo de 1 TB [utilizando powerShell](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk).
1. Se o disco já for de 1 TB, terá de efetuar uma limpeza do disco.
   1. Retire o disco de dados [do VM partido](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk).
   1. Fixe o disco de dados [a um VM funcional](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm).
   1. Utilize a [ferramenta de limpeza](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) do disco para libertar espaço.
1. Uma vez terminada a redimensionamento e a limpeza, desfragmente a unidade utilizando o seguinte comando:

   ```
   defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
   ```
   
Dependendo do nível de fragmentação, a desfragmentação pode levar várias horas.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Ativar a consola em série e a recolha de despejo de memória

**Recomendado**: Antes de reconstruir o VM, ative a consola em série e a recolha de despejo de memória executando o seguinte script:

1. Abra uma sessão de instrução de comando elevado como Administrador.
1. Execute os seguintes comandos:

   **Ativar a consola em série:**
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Verifique se o espaço livre no disco OS é maior do que o tamanho da memória (RAM) no VM.

   Se não houver espaço suficiente no disco OS, altere a localização onde o ficheiro de despejo de memória será criado e consulte esse local para qualquer disco de dados ligado ao VM que tenha espaço livre suficiente. Para alterar a localização, substitua **%SystemRoot%** pela letra de unidade do disco de dados, tais como **F:**, nos seguintes comandos.

   Configuração sugerida para ativar o despejo de OS:

    **Carregue o disco OS quebrado:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM 
   ```
   
   **Ativar no ControlSet001:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **Ativar o ControlSet002:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **Descarregar disco osso quebrado:**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Reconstruir o VM

Utilize [o passo 5 dos Comandos de Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para reconstruir o VM.
