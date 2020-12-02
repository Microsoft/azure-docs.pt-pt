---
title: Erro de paragem do Windows - Avaria de hardware
description: Este artigo fornece medidas para resolver problemas em que as máquinas virtuais do Windows Server 2008 falham com uma mensagem de erro que afirma que houve uma avaria no hardware.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: v-mibufo
ms.openlocfilehash: 18622d60f3a33658fadfd28c53c93a07b4b438a9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488651"
---
# <a name="windows-stop-error---hardware-malfunction"></a>Erro de paragem do Windows - Avaria de hardware

Este artigo fornece medidas para resolver problemas em que as máquinas virtuais do Windows Server 2008 falham com uma mensagem de erro que afirma que houve uma avaria no hardware.

## <a name="symptoms"></a>Sintomas

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, verá que a imagem exibe um ecrã azul com a mensagem:

**\*\*\* Avaria no hardware**

**Ligue para o seu fornecedor para obter apoio**

**\*\*\* O sistema parou \*\*\***

#### <a name="blue-screen"></a>Tela azul

![A imagem mostra uma falha de mau funcionamento do hardware do ecrã azul.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-1.png)

#### <a name="serial-console"></a>Consola de série

![A imagem mostra a mensagem "Hardware Malfunction" na funcionalidade "Serial Console" se a Consola em Série tiver sido ativada.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-2.png)

## <a name="cause"></a>Causa

Este ecrã aparecerá quando o SO convidado não foi configurado corretamente e foi enviado um Interrupção Não-Mascarada (NMI). A mensagem de erro indica que um programa de modo kernel gerou uma exceção, que o manipulador não apanhou. Pode identificar qual a exceção gerada recolhendo um depósito de memória.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo 

1. Configurar a chave de registo de interrupção não mascarada (NMI) 
2. Criar e Aceder a um VM de reparação 
3. Ativar a recolha de consolas em série e de despejo de memória 
4. Reconstruir o VM 

### <a name="set-up-the-non-maskable-interrupt-nmi-registry-key"></a>Configurar a chave de registo de interrupção não mascarada (NMI)

1. Utilizando o portal Azure, reinicie o VM de modo a que as botas Guest OS sejam normalmente. 
2. Uma vez restaurado algum acesso ao VM, abra uma solicitação de comando elevada (executada como administrador). 
3. Configurar a chave de registo do NMI com o seguinte comando:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```
    [Ver mais informações sobre o comando REG ADD](/windows-server/administration/windows-commands/reg-add)
4. *(Opcional)* Coleção de despejo de memória de configuração:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f  
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 

    ```
5. *(Opcional)* Configurar o acesso à consola em série:

    ```
    BCDEDIT /ems {current} on, or bcdedit /ems '{current}' on if you are using PowerShell
    BCDEDIT /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
    [Ver mais informações sobre o comando BCDEDIT](/windows-server/administration/windows-commands/bcdedit)
6. Reiniciar o VM com o seguinte comando:

    ```
    SHUTDOWN /r /t 0 /f 
    ```
    [Ver mais informações sobre o comando SHUTDOWN](/windows-server/administration/windows-commands/shutdown)

> [!IMPORTANT]
> A questão deve agora ser corrigida!

> [!NOTE]
> Depois de reiniciar, teste o seu VM para se certificar de que está a funcionar normalmente. Se ainda estiver com problemas, pode continuar na secção seguinte para mais instruções.

> [!TIP]
> Recomenda-se a configuração da chave de registo de interrupção não mascarada (NMI) na secção acima, no entanto, se o seu VM não arrancar normalmente depois, então as alterações previstas para o registo do So convidado podem não ter ocorrido. Se for esse o caso, pode seguir as instruções abaixo para adicionar manualmente as definições de registo.

### <a name="create-and-access-a-repair-vm"></a>Criar e aceder a um VM de reparação

1. Utilize os passos 1-3 dos Comandos de [Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar um VM de reparação.
2. Ligue-se ao VM de reparação utilizando ligação de ambiente de trabalho remoto.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Ativar a recolha de consolas em série e de despejo de memória

Antes de reconstruir o VM, é aconselhável ativar a recolha de despejos de memória e a Consola em Série. Para tal, execute o seguinte script: 

1. Abra uma sessão de solicitação de comando elevada (executar como administrador). 
2. Enuste os dados da loja BCD e determine o identificador de carregador de arranque, que utilizará no passo seguinte. 
    1. Para uma Geração 1 VM, insira o seguinte comando e note o identificador listado: 
 
        ```
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```
        No comando, `<BOOT PARTITON>` substitua-a pela letra da partição no disco anexo que contém a pasta de arranque. 

        ![A imagem mostra a saída da listagem da loja BCD numa Geração 1 VM, que lista sob o Windows Boot Loader o número do identificador.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-3.png)
    2. Para uma Geração 2 VM, insira o seguinte comando e note o identificador listado:
    
        ```
        BCDEDIT /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum 
        ```
        * No comando, `<LETTER OF THE EFI SYSTEM PARTITION>` substitua-se pela letra da Partição do Sistema EFI.
        * Pode ser útil lançar a consola de Gestão de Discos para identificar a partição do sistema adequada rotulada como *Partição do Sistema EFI*.
        * O identificador pode ser um GUID único ou pode ser o *bootmgr* padrão .
3. Executar os seguintes comandos para ativar a Consola em Série:

    ```
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON  
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 

    ```
    * No comando, `<VOLUME LETTER WHERE THE BCD FOLDER IS>` substitua-a pela letra da pasta BCD.
    * No comando, `<BOOT LOADER IDENTIFIER>` substitua-o pelo identificador que encontrou no passo anterior.
4. Verifique se o espaço livre no disco de so é maior do que o tamanho da memória (RAM) no VM. 
    1. Se não houver espaço suficiente no disco de so, deve alterar o local onde o ficheiro de despejo de memória será criado. Em vez de criar o ficheiro no disco OS, pode encaminhá-lo para qualquer outro disco de dados ligado ao VM que tenha espaço livre suficiente. Para alterar a localização, substitua **%SystemRoot%** pela letra de acionamento (por exemplo **F:**) do disco de dados nos comandos listados abaixo. 
    2. Introduza os comandos abaixo (configuração de despejo sugerida):

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
### <a name="rebuild-the-virtual-machine"></a>Reconstruir a máquina virtual

* Utilize [o passo 5 dos Comandos de Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para reconstruir o VM.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Resolução de problemas Azure Erros de Arranque de Máquina Virtual](./boot-error-troubleshoot.md)