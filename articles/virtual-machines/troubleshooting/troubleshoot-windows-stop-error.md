---
title: Erro de paragem do Windows - Estado Sem Memória
description: Este artigo fornece medidas para resolver problemas em que o Windows não começa e apresenta o estado "Status No Memory".
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: d29c7e49-4578-43c8-b829-831da4e48932
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 33b4c59e14301e496d0eddafa7bdfdf201b7aa29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87005910"
---
# <a name="windows-stop-error---status-no-memory"></a>Erro de paragem do Windows - Estado Sem Memória

Este artigo fornece medidas para resolver problemas em que o Windows não começa e exibe o código de estado ou erro #0xC0000017, também conhecido como "Status No Memory".

## <a name="symptom"></a>Sintoma

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem da máquina virtual (VM), verá que a imagem exibe o código de erro: `0xC0000017` . Dependendo da versão do Windows que está a executar, poderá ver este código apresentado no **Windows Boot Manager** ou no ecrã **Recovery**.

   **Gestor de boots do Windows**

   ![O Gestor de Botas do Windows afirma que "o Windows falhou no arranque. Uma recente mudança de hardware ou software pode ser a causa". Deslocando-se para baixo, você vê o código de estado "0xC0000017" bem como informações afirmando que "A aplicação ou sistema operativo não poderia ser carregado porque um ficheiro necessário está em falta ou contém erros".](./media/troubleshoot-windows-stop-error/1.png)

   **Tela de recuperação**
 
   ![O ecrã de recuperação indica que "o seu PC/Dispositivo precisa de ser reparado. Não há memória suficiente disponível para criar um dispositivo ramdisk". Também deverá ver o código de erro "0xC0000017".](./media/troubleshoot-windows-stop-error/2.png)

## <a name="cause"></a>Causa

O disco do sistema operativo está cheio, demasiado fragmentado, ou o sistema operativo (OS) não consegue aceder à memória ou ao ficheiro de página, ou ambos.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo:

1. Criar e Aceder a um VM de reparação
1. Espaço livre no disco
1. Limpe a má memória da Loja BCD
1. Restaurar o Ficheiro página para a sua localização predefinida
1. Ativar a recolha de consolas em série e de despejo de memória
1. Reconstruir o VM

> [!NOTE]
> Ao encontrar este erro, o SO convidado não está operacional. Vai resolver problemas em modo offline para resolver este problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e aceder a um VM de reparação

1. Utilize [os passos 1-3 dos Comandos de Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar um VM de reparação.
1. Utilizando ligação de ambiente de trabalho remoto, ligue-se ao VM de reparação.

### <a name="for-generation-2-vms-assign-a-letter-to-the-extensible-firmware-interface-efi-partition"></a>Para a Geração 2 VMs, atribua uma carta à partição extensível da Interface de Firmware (EFI):

Se estiver a utilizar uma Geração 2 VM, a partição EFI do disco anexo pode não ter uma carta atribuída a ele. Você precisa seguir os passos abaixo para atribuir uma carta à partição antes de prosseguir com este guia de resolução de problemas.

1. Na pesquisa do Windows, introduza `diskmgmt` e abra a consola de **Gestão de Discos.**
1. Identifique o disco partido ligado ao VM de reparação. Tipicamente, este disco é listado em último na consola, e tem o maior valor numérico.
1. Note se nesse disco existe uma partição que detém a **partição do sistema EFI,** que também não tem um valor de letra atribuído a ele (como unidade *F:*). Se todas as divisórias forem atribuídas, pode saltar à frente para libertar espaço no disco. Caso contrário, continue a atribuir uma carta a este disco.

   ![A consola de Gestão de Discos, com o disco em anexo "Disco 2", bem como a partição não atribuída que é de 100 MB e é a "Partição do Sistema EFI".](./media/troubleshoot-windows-stop-error/3.png)

1. Abra uma solicitação de comando elevada como administrador e introduza `diskpart` para lançar a ferramenta **DISKPART.**
1. Introduza os seguintes comandos:

   ```
   list disk
   sel disk <NUMBER OF THE ATTACHED DISK>
   list partition
   sel partition <NUMBER OF THE SYSTEM (EFI) PARTION>
   assign
   ```
   
   - No comando, `<NUMBER OF THE ATTACHED DISK>` substitua-o pelo número do disco que identificou no passo 2.
   - No comando, `<NUMBER OF THE SYSTEM PARTION>` substitua-o pelo número de partição da partição do sistema EFI. Esta partição ainda não foi atribuída uma carta, mas deve ser do tipo **Sistema** e ter cerca de 100MB de tamanho.

   > [!NOTE]
   > Comparar as divisórias na consola de Gestão de Discos com as listadas na ferramenta DISKPART, pode ser útil para determinar qual o número de partição correspondente à partição do sistema EFI no disco anexo.

1. Feche a janela da linha de comandos.

### <a name="free-up-space-on-the-disk"></a>Liberte espaço no disco

Agora que o disco avariado está ligado ao VM de reparação, deve verificar se o SO do disco tem espaço suficiente para funcionar corretamente. 

1. Verifique se o disco está cheio clicando à direita na unidade do disco anexo e selecionando **propriedades**.
1. Se o disco tiver **menos de 300 Mb de espaço livre,** [expanda-o para um máximo de 1 Tb utilizando o PowerShell](../windows/expand-os-disk.md).
1. Uma vez que o tamanho do disco é **1 Tb,** você precisará realizar uma limpeza de disco. Pode utilizar a [ferramenta De Limpeza de Discos](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) para libertar espaço.
1. Abra uma instância de pedido de comando elevado (executar como administrador) e efetue uma deses fragmentação na unidade:

   ``
   defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g
   ``
   
   - Dependendo do nível de fragmentação, a deses fragmentação pode demorar horas.
   - No comando, `<LETTER ASSIGNED TO THE OS DISK>` substitua-a pela letra do disco DE (tal como unidade *F:*).

### <a name="clean-out-bad-memory-from-the-boot-configuration-data-bcd-store"></a>Limpe a memória errada da loja De Dados de Configuração de Arranque (BCD)

1. Abra uma solicitação de comando elevada (executar como administrador).
1. Consultar o ficheiro de configuração da bota para sinalizar más memórias com o seguinte comando:

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /enum {badmemory}
   ``
   
   - No comando, `<LETTER ASSIGNED TO THE OS DISK>` substitua-a pela letra do disco DE (tal como unidade *F:*).

1. Se a consulta não mostrar blocos de memória errada, avance para a próxima tarefa. Caso contrário, continue a pisar o passo 4.
1. Se forem identificados blocos de memória deficientes, estes blocos estão a impedir a criação de um ramdisk e devem ser eliminados com o seguinte comando:

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /deletevalue {badmemory} badmemorylist
   ``
   
   - No comando, `<LETTER ASSIGNED TO THE OS DISK>` substitua-a pela letra do disco DE (tal como unidade *F:*).

### <a name="restore-the-page-file-to-its-default-location"></a>Restaurar o ficheiro da página para a sua localização predefinida

O ficheiro de página armazena dados que não podem ser guardados pela memória de acesso aleatório (RAM) do seu computador como uma forma de transbordo/backup. É possível que o ficheiro esteja hospedado num VHD em vez da unidade temporária, que é a localização Azure padrão. Se for verdade, o ficheiro pode não estar acessível e deve ser restaurado para o local padrão.

Antes de tomar quaisquer medidas, deve criar uma cópia da pasta **\windows\system32\config** num disco saudável. Este passo garante que pode desfazer quaisquer alterações indesejadas. Vais trabalhar em ficheiros importantes do sistema, por isso esta precaução é altamente recomendada.

1. Na pesquisa do Windows, **insira o Regedit** e abra a aplicação Editor de Registo.
1. No Editor de Registo, destaque a chave **HKEY_LOCAL_MACHINE** e selecione **File > Load Hive...** do menu.

   ![O menu de carga hive do editor de registo.](./media/troubleshoot-windows-stop-error/4.png)

1. No diálogo 'Carregar Hive', selecione **\windows\system32\config\SYSTEM** e clique em Abrir.
   1. Será solicitado um nome, que deve introduzir **BROKENSYSTEM**. Este nome ajudará a diferenciar as colmeias afetadas enquanto está a resolver problemas.
   1. Expanda **HKEY_LOCAL_MACHINE** para ver a nova chave BROKENSYSTEM que adicionou.
1. Utilizando o Editor de Registo, determine de que Controlo a máquina está a arrancar.
   1. Navegue para **HKEY_LOCAL_MACHINE >>  >> DE SISTEMAS BROKEN Select**.
   1. Nas teclas listadas, note o valor dos dados da Corrente. Por exemplo, se este valor for **de 1** ou **0x00000001 (1)**, então o conjunto de controlo será ControlSet001.
1. Verifique o local onde a criação do PageFile está configurada.
   1. Enquanto em HKEY_LOCAL_MACHINE\BROKENSYSTEM, expanda o diretório correspondente ao número do Conjunto de Controlo identificado no passo 4, tal como **o ControlSet001**.
   1. Navegue para **Control >> Session Manager >> Memory Management** e note a localização da chave **'Ficheiros', existente.**
   1. Esta chave deve estar na localização Azure padrão da unidade temporária. Se não estiver lá e estiver num VHD noutro local, como a unidade do disco de dados ou a unidade de sistema operativo, terá de ser eliminado.
   1. Navegue por esse local no Explorador de Ficheiros e, em seguida, elimine o ficheiro **pagefile.sys.**

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Ativar a consola em série e a recolha de despejo de memória

**Recomendado:** Antes de reconstruir o VM, ative a coleção de consola em série e de despejo de memória executando o seguinte script:

Para ativar a recolha de despejo de memória e a Consola em Série, execute o seguinte script:

1. Abra uma sessão de pedido de comando elevado como administrador.
1. Enuste os dados da loja BCD e determine o identificador de carregador de arranque, que utilizará no passo seguinte.

   1. Para uma Geração 1 VM, insira o seguinte comando e note o identificador listado:
   
      ``
      bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
      ``
   
   - No comando, `<BOOT PARTITON>` substitua-a pela letra da partição no disco anexo que contém a pasta de arranque.

      ![A saída da listagem da loja BCD numa Geração 1 VM, que lista no Windows Boot Loader o número do identificador.](./media/troubleshoot-windows-stop-error/5.png)

   1. Para uma Geração 2 VM, insira o seguinte comando e note o identificador listado:
   
      ``
      bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
      ``
   
   - No comando, `<LETTER OF THE EFI SYSTEM PARTITION>` substitua-se pela letra da Partição do Sistema EFI.
   - Pode ser útil lançar a consola de Gestão de Discos para identificar a partição do sistema adequada rotulada como **Partição do Sistema EFI**.
   - O identificador pode ser um GUID único ou pode ser o **bootmgr**padrão .

1. Executar os seguintes comandos para ativar a Consola em Série:

   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
   
   - No comando, `<VOLUME LETTER WHERE THE BCD FOLDER IS>` substitua-a pela letra da pasta BCD.
   - No comando, `<BOOT LOADER IDENTIFIER>` substitua-o pelo identificador que encontrou no passo anterior.

1. Verifique se o espaço livre no disco de so é maior do que o tamanho da memória (RAM) no VM.

   Se não houver espaço suficiente no disco de so, altere o local onde o ficheiro de despejo de memória será criado e encaminhe essa localização para qualquer disco de dados anexado ao VM que tenha espaço livre suficiente. Para alterar a localização, substitua **%SystemRoot%** pela letra de acionamento do disco de dados, tal como a unidade **F:** nos seguintes comandos.

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
