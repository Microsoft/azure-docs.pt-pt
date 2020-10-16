---
title: Erro de arranque de resolução de problemas - erro de leitura de disco ocorreu
description: Este artigo fornece medidas para resolver problemas em que o disco não pode ser lido num VM Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 817c9c5c-6a81-4b42-a6ad-0a0a11858b84
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/01/2020
ms.author: v-miegge
ms.openlocfilehash: f59903ed111be1fe414f4b3ded250d754c91d323
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87069155"
---
# <a name="troubleshoot-boot-error---disk-read-error-occurred"></a>Erro de arranque de resolução de problemas - erro de leitura de disco ocorreu

Este artigo fornece medidas para resolver problemas em que o disco não pode ser lido numa máquina virtual Azure (VM).

## <a name="symptoms"></a>Sintomas

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, verá que a imagem exibe uma solicitação com a mensagem "Ocorreu um erro de leitura de disco. Prima Ctrl+Alt+Del para reiniciar".

   ![Error message: Ocorreu um erro de leitura de disco. Prima Ctrl+Alt+Del para reiniciar.](./media/disk-read-error-occurred/1.png)

## <a name="cause"></a>Causa

Esta mensagem de erro indica que a estrutura do disco está corrompida e ilegível. Se estiver a utilizar um VM de Geração 1, também é possível que a partição do disco que contém os dados de configuração do arranque não esteja definida para **Ative**.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo

1. Criar e Aceder a um VM de reparação.
1. Selecione uma solução:
   - [Definir o estado de partição para ativo](#set-partition-status-to-active)
   - [Fixar a partição do disco](#fix-the-disk-partition)
1. Ativar a recolha de consolas em série e de despejo de memória.
1. Reconstruir o VM.

> [!NOTE]
> Ao encontrar este erro de arranque, o sistema operativo Guest (OS) não está operacional. Vai resolver problemas em modo offline para resolver este problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e aceder a um VM de reparação

1. Utilize os passos 1-3 dos Comandos de [Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar um VM de reparação.
1. Utilizando ligação de ambiente de trabalho remoto, ligue-se ao VM de reparação.

### <a name="set-partition-status-to-active"></a>Definir o estado de partição para ativo

Os VMs de geração 1 devem verificar primeiro que a partição de SO que detém a loja BCD está marcada como **Ative**. Se tiveres uma Geração 2 VM, antecipa-te à [Correção da Partição do Disco,](#fix-the-disk-partition)já que a bandeira do Status foi depreciada na geração posterior.

1. Abra uma solicitação de comando elevada (cmd.exe).
1. Introduza a **parte do disco** para lançar a ferramenta **DISKPART.**
1. Introduza o **disco de lista** para listar os discos do sistema e identifique o disco rígido virtual de SISTEMA anexado (VHD).
1. Uma vez localizado o OS VHD anexado, **introduza o disco sel #** para selecionar o disco. Consulte a seguinte imagem para um exemplo de onde o Disco 1 é o OS VHD anexado.

   ![A janela da parte do disco com a saída do comando **list disk** onde o Disco 0 e o Disco 1 são apresentados na tabela. A janela também mostra a saída do comando **sel disco 1** onde o Disco 1 é o disco selecionado](./media/disk-read-error-occurred/2.png)

1. Uma vez selecionado o disco, introduza **a partição** da lista para listar as divisórias do disco selecionado.
1. Assim que a partição da bota for identificada, **insira a partição sel #** para selecionar a partição. A partição da bota é frequentemente de aproximadamente 350 MB de tamanho.  Veja a seguinte imagem, por exemplo, onde a partição 1 é a divisória de arranque.

   ![A janela da parte do disco com a saída do comando de partição **lista** onde a Partição 1 e a Partição 2 são apresentadas na tabela. A janela também mostra a saída do comando de partição **sel 1** onde a Partição 1 é o disco selecionado.](./media/disk-read-error-occurred/3.png)

1. Introduza **a partição de pormenor** para verificar o estado da partição. Veja as seguintes imagens para exemplos da partição que está a ser definida para **Ative: No** or **Ative: Yes**.

   **Ativo: Não**

   ![A janela da parte do disco com a saída do comando **detail partition** onde a Partição 1 está definida para **Ative: No**.](./media/disk-read-error-occurred/4.png)

   **Ativo: Sim**

   ![A janela da parte do disco com a saída do comando de partição **detail** onde a partição 1 está definida para **Ative: Yes**.](./media/disk-read-error-occurred/5.png)

1. Se a partição não estiver definida como **Ative,** entre **ativa** para alterar a bandeira Ative.
1. Introduza **a partição de pormenor** para verificar se a alteração de estado foi corretamente concluída e verifique se a saída inclui **Ative: Yes**. 
1. Introduza a **saída** para fechar a ferramenta DISKPART e guarde as alterações de configuração.

### <a name="fix-the-disk-partition"></a>Fixe a partição do disco

1. Abra uma solicitação de comando elevada (cmd.exe).
1. Utilize o seguinte comando para executar **CHKDSK** no(s) disco(s) e executar correções de erro:

   `chkdsk <DRIVE LETTER>: /f`

   A adição da opção de comando **/f** corrigirá quaisquer erros no disco. Certifique-se de que substitui **< DRIVE LETTER >** pela letra do OS VHD anexado.

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
