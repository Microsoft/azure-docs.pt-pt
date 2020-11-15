---
title: Start start-up de SISTEMA de Resolução de Problemas – Capacidade de instalação do Windows Update
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
ms.openlocfilehash: f83a1820eb931fa075681da7a9661b304059cd2a
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94635710"
---
# <a name="troubleshoot-os-start-up--windows-update-installation-capacity"></a>Start start-up de SISTEMA de Resolução de Problemas – Capacidade de instalação do Windows Update

Este artigo fornece medidas para resolver problemas numa máquina virtual Azure (VM) onde o Windows Update (KB) obtém um erro e fica sem resposta.

## <a name="symptom"></a>Sintoma

Quando utilizar diagnósticos boot para visualizar a imagem do VM, verá que a imagem mostra o Windows Update (KB) em andamento, mas falhando com o código de erro: **C01A001D**. A imagem a seguir mostra o Windows Update (KB) preso com a mensagem "Error C01A001D applying update operation ##### of #######)":

![O Windows Update (KB) está preso à mensagem: "Erro C01A001D aplicando a operação de atualização X de Y (Z)".](./media/troubleshoot-windows-update-installation-capacity/1.png)

## <a name="cause"></a>Causa

Nesta situação, o sistema operativo (OS) não consegue completar uma instalação do Windows Update (KB), uma vez que não é possível criar um ficheiro principal no sistema de ficheiros. Com base neste código de erro, o sistema operativo não consegue gravar ficheiros para o disco.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo:

1. Criar e aceder a um VM de reparação.
1. Espaço livre no disco.
1. Ativar a recolha de consolas em série e de despejo de memória.
1. Reconstruir o VM.

> [!NOTE]
> Ao encontrar este erro, o SO convidado não está operacional. Resolver problemas em modo offline para resolver este problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e Aceder a um VM de reparação

1. Utilize os passos 1-3 dos Comandos de [Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar um VM de reparação.
1. Utilizando ligação de ambiente de trabalho remoto, ligue-se ao VM de reparação.

### <a name="free-up-space-on-the-disk"></a>Espaço livre no disco

Para resolver o problema:

- Redimensione o disco até 1 TB se ainda não estiver no tamanho máximo de 1 TB.
- Faça uma limpeza de disco.
- Des-fragmentar a unidade.

1. Verifique se o disco está cheio. Se o tamanho do disco for inferior a 1 TB, expanda-o até um máximo de 1 TB [utilizando o PowerShell](../windows/expand-os-disk.md).
1. Se o disco já estiver em 1 TB, terá de efetuar uma limpeza de disco.
   1. Utilize a [ferramenta De Limpeza de Discos](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) para libertar espaço.
1. Uma vez terminada a redimensionamento e a limpeza, desa fragmentar a unidade utilizando o seguinte comando:

   ```
   defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
   ```
   
Dependendo do nível de fragmentação, a deses fragmentação pode demorar várias horas.

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

   Se não houver espaço suficiente no disco de so, altere o local onde o ficheiro de despejo de memória será criado e encaminhe essa localização para qualquer disco de dados anexado ao VM que tenha espaço livre suficiente. Para alterar a localização, substitua **%SystemRoot%** pela letra de acionamento do disco de dados, como **F:** , nos seguintes comandos.

   Configuração sugerida para permitir o despejo de OS:

    **Carregue o disco de oss quebrado:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM 
   ```
   
   **Ativar no ControlSet001** :

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
   
   **Descarregar disco de OS quebrado:**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Reconstruir o VM

Utilize [o passo 5 dos Comandos de Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para reconstruir o VM.
