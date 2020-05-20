---
title: Error Error Descasshoot Windows Boot Manager - 0xC0000225 "Status not found"
description: Passos para resolver problemas em que o código de erro 0xC0000225 ocorre num VM Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: c17899a4-b270-4725-9530-0dcd829b178c
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: 817f9e362e639cbb8f0cc79607c376c0e8216ec7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664992"
---
# <a name="troubleshoot-windows-boot-manager-error----0xc0000225-status-not-found"></a>Error Error Descasshoot Windows Boot Manager - 0xC0000225 "Status not found"
 
Este artigo fornece medidas para resolver problemas em que o código de erro 0xC0000225 ocorre num VM Azure. Este erro indica que o estado ou o objeto não é encontrado.

## <a name="symptoms"></a>Sintomas

Quando utilizar os [diagnósticos boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para visualizar a imagem do VM, verá que a imagem mostra que um Windows não começou a erro com o código de estado *0xc0000225*.

O ficheiro associado a este código de erro informa-o-á quais as medidas a tomar para resolver o problema. Localize o **Ficheiro:** texto da secção para determinar o curso adequado de ação.

### <a name="drivers-os-related-or-third-party"></a>Motoristas, RELACIONADOS OU Terceiros

Se o ficheiro estiver presente, mas se referir a um controlador (como é mostrado) ou estiver relacionado com o OS ou terceiros, siga os passos sob a secção [Reparar o Ficheiro do Sistema](#repair-the-system-file).
 
Na imagem seguinte, o Windows Boot Manager afirma que "o Windows falhou no arranque. Uma recente mudança de hardware ou software pode ser a causa." A imagem também mostra o Status como "0xc00000225", **Arquivo:** como `\windows\System32\drivers\atapi.sys` , e **Informação:** como "O sistema operativo não podia ser carregado porque um controlador crítico do sistema está em falta ou contém erros."

![O Windows Boot Manager afirma que o Windows não começou. Uma recente mudança de hardware ou software pode ser a causa. Também mostra o Status como "0xc0000225", o Ficheiro como '\windows\System32\drivers\atapi.sys', e na secção Info diz: "O sistema operativo não podia ser carregado porque um controlador crítico do sistema está em falta ou contém erros."](./media/troubleshoot-boot-error-status-not-found/1.png)

### <a name="no-file"></a>Sem arquivo

Se for apresentado o código de Estado, mas não for mostrado nenhum ficheiro, siga os passos sob a secção [Adicione a Variável OSDEVICE](#add-the-osdevice-variable).

Na imagem seguinte, o Windows Boot Manager afirma que "o Windows falhou no arranque. Uma recente mudança de hardware ou software pode ser a causa." A imagem mostra ainda o Status como "0xc00000225", e **Informação:** como "A seleção de botas falhou porque um dispositivo obrigatório é inacessível".

![O Windows Boot Manager afirma que o Windows não começou. Uma recente mudança de hardware ou software pode ser a causa. Mostra também o Status como "0xc0000225" e na secção Info afirma: "A seleção do arranque falhou porque um dispositivo obrigatório é inacessível."](./media/troubleshoot-boot-error-status-not-found/2.png)

### <a name="registry-file"></a>Arquivo de Registo

Se se referir a algum dos ficheiros de registo, como \windows\system32\config\system, siga os passos sob a secção [Criar um Bilhete](#contact-support)de Suporte .
 
Na imagem seguinte, o Windows Boot Manager afirma que "o Windows falhou no arranque. Uma recente mudança de hardware ou software pode ser a causa." A imagem mostra ainda o Status como "0xc00000225", o Arquivo como `\windows\System32\config\system` , e **Informação:** como "O sistema operativo não podia ser carregado porque o ficheiro de registo do sistema está em falta ou contém erros."

![O Windows Boot Manager afirma que o Windows não começou. Uma recente mudança de hardware ou software pode ser a causa. Também mostra o Status como "0xc0000225", o Ficheiro como '\windows\System32\config\system', e na secção Info diz: "O sistema operativo não podia ser carregado porque o ficheiro de registo do sistema está em falta ou contém erros."](./media/troubleshoot-boot-error-status-not-found/3.png)

Na imagem seguinte, o ecrã de recuperação indica "O seu PC/Dispositivo precisa de ser reparado. O sistema operativo não pôde ser carregado porque o ficheiro de registo do sistema está em falta ou contém erros." A imagem também mostra o código error como "0xc00000225" e o Ficheiro como `\windows\System32\config\system` .

![O ecrã de recuperação diz :O seu PC/Dispositivo precisa de ser reparado. O sistema operativo não pôde ser carregado porque o ficheiro de registo do sistema está em falta ou contém erros. Também mostra o código error como "0xc00000225" e o Ficheiro como '\windows\System32\config\system'.](./media/troubleshoot-boot-error-status-not-found/4.png)

## <a name="causes"></a>Causas

### <a name="missing-binary"></a>Binário desaparecido

Pode encontrar binário desaparecido ou corrompido no ficheiro System **(.sys).**

### <a name="bcd-corruption-or-improper-vhd-migration"></a>Corrupção do BCD ou migração imprópria de VHD

Neste caso, ou os Dados de Configuração de **Arranque (BCD)** são corrompidos, ou o **disco rígido virtual (VHD)** foi migrado do local, mas não estava devidamente preparado. O resultado é que a variável **OSDEVICE** está em falta e terá de ser adicionada.

### <a name="registry-hive-corruption"></a>Corrupção da colmeia de registo

Uma corrupção de colmeia de registo pode ser devido a:

- A Colmeia falha
- A Colmeia monta, mas está vazia
- A Colmeia não foi fechada corretamente
## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo

1. Criar e aceder a um VM de reparação.
1. Selecione uma Solução:
   - [Reparar o Ficheiro do Sistema](#repair-the-system-file)
   - [Adicione a Variável OSDevice](#add-the-osdevice-variable)
   - [Criar um bilhete de apoio](#contact-support)
1. Ativar a recolha de consolas em série e de despejo de memória.
1. Reconstruir o VM.

### <a name="create-and-access-a-repair-vm"></a>Criar e aceder a um VM de reparação

1. Utilize os passos 1-3 dos Comandos de [Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) para preparar um VM de reparação.
1. Utilizando a ligação remota de ambiente de trabalho, ligue-se ao VM de reparação.

### <a name="select-a-solution"></a>Selecione uma Solução

1. Abra um pedido de comando elevado.
1. Com base no sintoma que identificou anteriormente, siga os passos na solução correspondente. Pode saltar os passos das outras soluções, uma vez que não se aplicarão ao seu problema:

- [Reparar o Ficheiro do Sistema](#repair-the-system-file)
- [Adicione a Variável OSDevice](#add-the-osdevice-variable)
- [Criar um bilhete de apoio](#contact-support)

### <a name="repair-the-system-file"></a>Reparar o Ficheiro do Sistema

1. Utilizando o VHD anexado, navegue até à localização do ficheiro do binário mostrado na sua imagem de máquina virtual (VM).
1. Clique no ficheiro, selecione **Propriedades**e, em seguida, selecione o separador **Detalhes** para ver informações no ficheiro.
   1. Note a versão do ficheiro, como mostra a imagem abaixo:

      ![A janela de propriedades para o ficheiro 'cng.sys', com a versão de ficheiro em destaque.](./media/troubleshoot-boot-error-status-not-found/5.png)

1. Mude o nome do ficheiro para **< BINARY. SYS >.old,** substituindo **< BINARY. A SYS >** com o nome do ficheiro.

   Para a imagem no degrau acima, o ficheiro **cng.sys** seria renomeado para **cng.sys.old**

   > [!NOTE]
   > Se tentar renomear o ficheiro e receber a mensagem "O ficheiro está corrompido e ilegível", [contacte](https://azure.microsoft.com/support/create-ticket/)o suporte de assistência , uma vez que esta solução não funcionará.

1. Agora que o ficheiro corrupto foi renomeado, corrija o ficheiro restaurando-o do seu repositório interno.
   1. Lance uma sessão **cmd.**
   1. Navegue para **\windows\winsxs**.

   1. Procure o binário localizado no início desta secção utilizando o seguinte comando:

      `dir <BINARY WITH ".SYS" EXTENSION>  /s`

      Este comando listará todas as versões do ficheiro que a máquina tem, dando-lhe o histórico de percursos desse componente.
      
      Por exemplo, **dir cng.sys** seria renomeado **dir cng.sys /s**

   1. Escolha a versão mais recente do ficheiro na lista (ou qualquer outra que prefira) e copie o ficheiro para a pasta **windows\system32** utilizando o caminho anterior e o seguinte comando:

      `copy <drive>:\Windows\WinSxS\<DIRECTORY WHERE FILE IS>\<BINARY WITH ".SYS" EXTENSION> <DRIVE>:\Windows\System32\Drivers\`

      > [!NOTE]
      > Se o último binário não funcionou, experimente uma versão antes dessa, ou qualquer uma das quais sabe que há um ficheiro estável, como uma versão antes de um patch.

      Por exemplo, se o binário que procura for **cmimcext.sys**, a unidade defeituosa é a unidade **F:**, e acabou de fazer uma pesquisa para a versão mais recente, veria a seguinte imagem, onde uma consulta no pedido de comando de `dir cmim* /s` localizar a versão mais recente do ficheiro cmimcext.sys.

      ![Uma consulta no comando de "dir cmim* /s" para localizar a versão mais recente do ficheiro cmimcext.sys.](./media/troubleshoot-boot-error-status-not-found/6.png)

      Na imagem de exemplo acima, a consulta foi realizada em **C:**, enquanto a letra de acionamento deve ser a da unidade defeituosa, **F:**, que é o disco OS anexado como um disco de dados no VM de reparação.

      O comando resultante para copiar o ficheiro seria: `copy F:\Windows\WinSxS\amd64_xxxxxx\cmimcext.sys F:\Windows\System32\Drivers` .

Uma vez concluída esta tarefa, continue a [ativar a consola em série e](#enable-the-serial-console-and-memory-dump-collection)a recolha de despejo de memória.

### <a name="add-the-osdevice-variable"></a>Adicione a Variável OSDEVICE

Recolher as informações atuais de instalação de arranque e observar o identificador na partição ativa. Em seguida, utilizará esta informação para adicionar a variável **OSDEVICE,** seguindo as instruções para a geração do seu VM.

Se esta recolha de informação der um erro em que não exista ficheiro **\boot\bcd,** utilize as instruções em [Reparação do Ficheiro do Sistema.](#repair-the-system-file) 

1. Para os VMs da Geração 1, abra um pedido de comando elevado como Administrador e introduza o seguinte comando:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /enum`

   Esta imagem mostra o Windows Boot Loader num VM de Geração 1, com o atributo identificador em destaque. O atributo identificador realçado mostra uma cadeia alfanumérica única.

   ![Windows Boot Loader mostrado numa geração 1 VM com o atributo identificador destacado. O atributo identificador realçado mostra uma cadeia alfanumérica única.](./media/troubleshoot-boot-error-status-not-found/7.png)

   Note o identificador do Carregador de Botas do Windows, cujo caminho é **\windows\system32\winload.exe**.

1. Para os VMs da Geração 2, verifique tanto se o disco de SO está on-line e que as suas cartas de unidade de partição foram atribuídas. Quando isto tiver sido verificado, colete as informações de configuração da bota.
   1. Na **pesquisa do Windows**, escreva **Gestão de Discos** e abra a consola de gestão de discos. Utilize esta consola para identificar o número de disco anexado na sua vm de reparação e na divisória Extensible Firmware Interface (EFI) que detém a loja BCD.

   Na imagem seguinte, o disco 2 é o número do disco ligado ao VM de reparação. A imagem também mostra a Partição do Sistema EFI no Disco 2, que tem 100MB de tamanho e não tem uma letra atribuída.

   ![O disco 2 é mostrado como o número do disco ligado ao VM de reparação. Também mostra a Partição do Sistema EFI no Disco 2, que é de 100MB e não tem uma carta atribuída a ele.](./media/troubleshoot-boot-error-status-not-found/8.png)

   1. Abra um pedido de comando elevado como Administrador e introduza os seguintes comandos:
      1. Abra a **ferramenta DISKPART** utilizando o comando `diskpart` .
      1. Enumerar todos os discos e, em seguida, selecionar o disco anexado identificado no passo anterior:
      
         ```
         list disk
         sel disk <DISK #>
         ```

         A imagem seguinte mostra os resultados da listagem e da seleção de um disco. Disco 0 (127 GB ] Online), Disco 1 (32 GB ] Online) e Disco 2 (127 GB ] Online) estão listados, com o Disco 2 a ser selecionado com o comando `sel disk 2` .

         ![Os resultados da listagem e, em seguida, a seleção de um disco. Disco 0 (127 GB ] Online), Disco 1 (32 GB ] Online) e Disco 2 (127 GB ] Online) estão listados, com o Disco 2 a ser selecionado.](./media/troubleshoot-boot-error-status-not-found/9.png)

      1. Enumerar as divisórias e selecionar a partição do sistema EFI identificada na etapa anterior:
      
         ```
         list partition
         sel partition <PARTITION #>
         ```

         A imagem seguinte mostra os resultados da listagem e da seleção de uma partição. Partição 1 (Reservado / 16MB), Partição 2 (Sistema / 100MB) e Partição 3 (Primária ] 126 GB) estão listados, sendo a Partição 2 selecionada com o comando `sel part 2` .

         ![Os resultados da listagem e, em seguida, a seleção de uma partição. Partição 1 (Reservado / 16MB), Partição 2 (Sistema / 100MB) e Partição 3 (Primárias de 126 GB) estão listadas, com a Partição 2 a ser selecionada.](./media/troubleshoot-boot-error-status-not-found/10.png)

      1. Atribuir uma carta à divisória EFI utilizando o comando `assign` .

         Na seguinte imagem, o comando e o novo SISTEMA de `assign` acionamento **(F:)** são ambos veríveis no File Explorer.

         ![O comando de atribuição e o novo sistema de acionamento (F:) são visível no File Explorer.](./media/troubleshoot-boot-error-status-not-found/11.png)

      1. Enumerar os dados da loja BCD utilizando o seguinte comando:
      
         `bcdedit /store <LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

         Na imagem seguinte, o Windows Boot Loader encontra-se numa Geração 2 VM com o atributo identificador destacado. O atributo identificador destacado tem um valor de **{padrão}**.

         ![O Windows Boot Loader é mostrado num VM da Geração 2 com o atributo identificador em destaque. O atributo identificador destacado mostra o padrão como o seu valor.](./media/troubleshoot-boot-error-status-not-found/12.png)

         Note o identificador do Carregador de Botas do Windows, cujo caminho é **\windows\system32\winload.efi**.

1. Note que falta a variável OSDEVICE na partição ativa:

   ![Os atributos do Windows Boot Manager e do Windows Boot Loader estão listados no pedido de comando, faltando o atributo do Dispositivo OS.](./media/troubleshoot-boot-error-status-not-found/13.png)
   
   Nesta imagem, os atributos do Windows Boot Manager e do Windows Boot Loader estão listados no pedido de comando, mas o atributo DODISPOSITIVO está em falta.

1. Adicione a variável OSDEVICE com base nas seguintes informações:

   Para divisórias únicas Os discos, adicione `BOOT` .

   > [!NOTE]
   > A pasta com arranque estará na mesma divisória que a pasta windows **\windows folder**.
   > - A pasta bootable para VMs da Geração 1 é **(\boot\bcd pasta)**.
   > - A pasta bootable para VMs da Geração 2 é **EFI\Microsoft\boot\bcd**.

   Para os VMs da Geração 1, insira o seguinte comando:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   Para os VMs da Geração 2, insira o seguinte comando:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   Para vários discos OS de partição, adicione `PARTITION=<LETTER OF WINDOWS FOLDER>:` .

   > [!NOTE]
   > A pasta com arranque será provavelmente numa divisória diferente da pasta das **janelas \windows folder**.
   > - A pasta bootable para VMs da Geração 1 é **(\boot\bcd pasta)**.
   > - A pasta bootable para VMs da Geração 2 é **EFI\Microsoft\boot\bcd**.

   Para os VMs da Geração 1, insira o seguinte comando:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

   Para os VMs da Geração 2, insira o seguinte comando:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {< IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

Uma vez concluída esta tarefa, continue a [ativar a consola em série e](#enable-the-serial-console-and-memory-dump-collection)a recolha de despejo de memória.

### <a name="contact-support"></a>Contactar o Suporte

O erro **do Registo** tem uma solução, mas terá de criar um bilhete de [apoio](https://azure.microsoft.com/support/create-ticket/) para mais assistência.

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

   **Colmeia de registo de carga do disco osso quebrado:**

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

   **Descarregar disco operativo quebrado:**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Reconstruir o VM

Utilize [o passo 5 dos Comandos de Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para reconstruir o VM.
