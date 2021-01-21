---
title: Erro do Gestor de Boot do Windows de resolução de problemas - 0xC000225 "Estado não encontrado"
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
ms.openlocfilehash: a91add684d7b7aaee67e7dd4f4f2d0c6df078132
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632229"
---
# <a name="troubleshoot-windows-boot-manager-error----0xc0000225-status-not-found"></a>Erro do Gestor de Boot do Windows de resolução de problemas - 0xC000225 "Estado não encontrado"
 
Este artigo fornece medidas para resolver problemas onde o código de erro 0xC0000225 ocorre num VM Azure. Este erro indica que o estado ou o objeto não são encontrados.

## <a name="symptoms"></a>Sintomas

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, verá que a imagem de imagem mostra um Erro de Início com o código de estado *0xc0000225*.

O ficheiro associado a este código de erro irá informá-lo quais as medidas a tomar para resolver o problema. Localize o **Arquivo:** texto da secção para determinar o curso de ação adequado.

### <a name="drivers-os-related-or-third-party"></a>Motoristas, OS Relacionados ou Terceiros

Se o ficheiro estiver presente mas se referir a um controlador (como é mostrado) ou estiver relacionado com o SO ou terceiro, siga os passos sob a secção [Reparar o Ficheiro do Sistema](#repair-the-system-file).
 
Na imagem seguinte, o Windows Boot Manager afirma que "o Windows falhou no arranque. Uma recente mudança de hardware ou software pode ser a causa." A imagem também mostra o Estado como "0xc000225", **Arquivo:** como `\windows\System32\drivers\atapi.sys` , e **Info:** como "O sistema operativo não podia ser carregado porque um controlador de sistema crítico está em falta ou contém erros."

![O Windows Boot Manager afirma que o Windows falhou no arranque. Uma recente mudança de hardware ou software pode ser a causa. Também mostra o Estado como "0xc000225", o Ficheiro como '\windows\System32\drivers\atapi.sys', e na secção Informação diz: "O sistema operativo não podia ser carregado porque um controlador de sistema crítico está em falta ou contém erros."](./media/troubleshoot-boot-error-status-not-found/1.png)

### <a name="no-file"></a>Sem Arquivo

Se for apresentado com o código de estado, mas nenhum ficheiro for mostrado, siga os passos por baixo da secção [Adicione a Variável OSDEVICE](#add-the-osdevice-variable).

Na imagem seguinte, o Windows Boot Manager afirma que "o Windows falhou no arranque. Uma recente mudança de hardware ou software pode ser a causa." A imagem também mostra o Estado como "0xc000225", e **Info:** como "A seleção de arranque falhou porque um dispositivo obrigatório é inacessível."

![O Windows Boot Manager afirma que o Windows falhou no arranque. Uma recente mudança de hardware ou software pode ser a causa. Também mostra o Estado como "0xc000225" e na secção Informação diz: "A seleção de arranque falhou porque um dispositivo necessário é inacessível."](./media/troubleshoot-boot-error-status-not-found/2.png)

### <a name="registry-file"></a>Arquivo de Registo

Se se referir a algum dos ficheiros de registo, tais como \windows\system32\config\system, siga os passos sob a secção [Criar um Bilhete de Suporte](#contact-support).
 
Na imagem seguinte, o Windows Boot Manager afirma que "o Windows falhou no arranque. Uma recente mudança de hardware ou software pode ser a causa." A imagem também mostra o Estado como "0xc000225", o Ficheiro como `\windows\System32\config\system` , e **Info:** como "O sistema operativo não podia ser carregado porque o ficheiro de registo do sistema está em falta ou contém erros."

![O Windows Boot Manager afirma que o Windows falhou no arranque. Uma recente mudança de hardware ou software pode ser a causa. Também mostra o Estado como "0xc000225", o Ficheiro como '\windows\System32\config\system', e na secção Informação diz: "O sistema operativo não podia ser carregado porque o ficheiro de registo do sistema está em falta ou contém erros."](./media/troubleshoot-boot-error-status-not-found/3.png)

Na imagem seguinte, o ecrã de recuperação diz que "O seu PC/Dispositivo precisa de ser reparado. O sistema operativo não pôde ser carregado porque o ficheiro de registo do sistema está em falta ou contém erros." A imagem também mostra o código De erro como "0xc0000225" e o Ficheiro como `\windows\System32\config\system` .

![O ecrã de recuperação diz que o seu PC/Dispositivo precisa de ser reparado. O sistema operativo não pôde ser carregado porque o ficheiro de registo do sistema está em falta ou contém erros. Também mostra o código Error como "0xc0000225" e o Ficheiro como '\windows\System32\config\system'.](./media/troubleshoot-boot-error-status-not-found/4.png)

## <a name="causes"></a>Causas

### <a name="missing-binary"></a>Binário desaparecido

Pode encontrar um binário desaparecido ou corrompido no seu ficheiro System **(.sys).**

### <a name="bcd-corruption-or-improper-vhd-migration"></a>Corrupção bcd ou migração VHD imprópria

Neste caso, ou os **Dados de Configuração de Boot (BCD)** são corrompidos, ou o **disco rígido virtual (VHD)** foi migrado do local, mas não estava devidamente preparado. O resultado é que a variável **OSDEVICE** está em falta e terá de ser adicionada.

### <a name="registry-hive-corruption"></a>Corrupção de Colmeia de Registo

Uma corrupção de colmeia de registo pode ser devido a:

- A Colmeia falha
- A Colmeia monta, mas está vazia.
- A Colmeia não foi fechada corretamente
## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo

> [!TIP]
> Se tiver uma cópia de segurança recente do VM, poderá tentar [restaurar o VM da cópia de segurança](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema da bota.

1. Criar e aceder a um VM de reparação.
1. Selecione uma solução:
   - [Reparar o Ficheiro do Sistema](#repair-the-system-file)
   - [Adicione a variável OSDevice](#add-the-osdevice-variable)
   - [Criar um bilhete de apoio](#contact-support)
1. Ativar a recolha de consolas em série e de despejo de memória.
1. Reconstruir o VM.

### <a name="create-and-access-a-repair-vm"></a>Criar e Aceder a um VM de reparação

1. Utilize os passos 1-3 dos Comandos de [Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar um VM de reparação.
1. Utilizando ligação de ambiente de trabalho remoto, ligue-se ao VM de reparação.

### <a name="select-a-solution"></a>Selecione uma solução

1. Abra um pedido de comando elevado.
1. Com base no sintoma que identificou anteriormente, siga os passos na solução correspondente. Pode saltar os passos nas outras soluções, uma vez que não se aplicam à sua questão:

- [Reparar o Ficheiro do Sistema](#repair-the-system-file)
- [Adicione a variável OSDevice](#add-the-osdevice-variable)
- [Criar um bilhete de apoio](#contact-support)

### <a name="repair-the-system-file"></a>Reparar o Ficheiro do Sistema

1. Utilizando o VHD anexado, navegue para a localização do ficheiro do binário mostrado na sua imagem de máquina virtual (VM).
1. Clique no ficheiro com direito, selecione **Propriedades** e, em seguida, selecione o separador **Detalhes** para ver informações no ficheiro.
   1. Note a versão do ficheiro, como mostra a imagem abaixo:

      ![A janela de propriedades para o ficheiro 'cng.sys', com a versão do ficheiro realçada.](./media/troubleshoot-boot-error-status-not-found/5.png)

1. Mude o nome do ficheiro para **< BINARY.SYS >.old,** substituindo **< BINARY.SYS >** pelo nome do ficheiro.

   Para a imagem no degrau acima, o ficheiro **cng.sys** seria renomeado para **cng.sys.old**

   > [!NOTE]
   > Se tentar renomear o ficheiro e receber a mensagem "O ficheiro é corrompido e ilegível", [contacte o suporte para assistência](https://azure.microsoft.com/support/create-ticket/), uma vez que esta solução não funcionará.

1. Agora que o ficheiro corrupto é renomeado, corrija o ficheiro restaurando-o do seu repositório interno.
   1. Lançar uma sessão **cmd.**
   1. Navegue para **\windows\winsxs**.

   1. Procure o binário localizado no início desta secção utilizando o seguinte comando:

      `dir <BINARY WITH ".SYS" EXTENSION>  /s`

      Este comando irá listar todas as versões do ficheiro que a máquina tem, dando-lhe o histórico do caminho desse componente.

      Por exemplo, **dir cng.sys** seria renomeado **dir cng.sys /s**

   1. Escolha a versão mais recente do ficheiro na lista (ou qualquer que prefira) e copie o ficheiro para a pasta **windows\system32** utilizando o caminho anterior e o seguinte comando:

      `copy <drive>:\Windows\WinSxS\<DIRECTORY WHERE FILE IS>\<BINARY WITH ".SYS" EXTENSION> <DRIVE>:\Windows\System32\Drivers\`

      > [!NOTE]
      > Se o binário mais recente não funcionou, experimente uma versão antes dessa, ou qualquer uma das quais sabe que existe um ficheiro estável, como uma versão antes de um patch.

      Por exemplo, se o binário que procura for **cmimcext.sys**, a unidade defeituosa é a unidade **F:**, e acabou de executar uma pesquisa para a versão mais recente, veria a seguinte imagem, onde uma consulta no comando pronta para `dir cmim* /s` localizar a versão mais recente do ficheiro cmimcext.sys.

      ![Uma consulta no comando de "dir cmim* /s" para localizar a versão mais recente do ficheiro cmimcext.sys.](./media/troubleshoot-boot-error-status-not-found/6.png)

      Na imagem de exemplo acima, a consulta foi realizada em **C:**, enquanto a letra de unidade deve ser a da unidade defeituosa, **F:**, que é o disco DE anexado como um disco de dados no VM de reparação.

      O comando resultante para copiar o ficheiro seria: `copy F:\Windows\WinSxS\amd64_xxxxxx\cmimcext.sys F:\Windows\System32\Drivers` .

Uma vez concluída esta tarefa, continue a [ativar a consola em série e a recolha de despejos de memória](#enable-the-serial-console-and-memory-dump-collection).

### <a name="add-the-osdevice-variable"></a>Adicione a variável OSDEVICE

Recolher as informações atuais de configuração de arranque e observar o identificador na partição ativa. Em seguida, utilizará esta informação para adicionar a variável **OSDEVICE,** seguindo as instruções para a geração do seu VM.

Se esta recolha de informações apresentar um erro em que não exista um ficheiro **\boot\bcd,** em vez disso, utilize as instruções na [Reparação do Ficheiro do Sistema.](#repair-the-system-file) 

1. Para a Geração 1 VMs, abra um pedido de comando elevado como administrador e introduza o seguinte comando:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /enum`

   Esta imagem mostra o Windows Boot Loader numa Geração 1 VM, com o atributo identificador em destaque. O atributo identificador realçado mostra uma corda alfanumérica única.

   ![O Windows Boot Loader mostrado numa geração 1 VM com o atributo identificador em destaque. O atributo identificador realçado mostra uma corda alfanumérica única.](./media/troubleshoot-boot-error-status-not-found/7.png)

   Note o identificador do Carregador de Botas do Windows, cujo caminho é **\windows\system32\winload.exe**.

1. Para os VMs da Geração 2, verifique se o disco de SO está online e que as suas letras de unidade de partição foram atribuídas. Quando isto for verificado, recolha as informações de configuração da bota.
   1. Na **pesquisa do Windows,** **digite A Gestão do Disco** e abra a consola de gestão de discos. Utilize esta consola para identificar o número do disco anexado na sua VM de reparação e na partição extensível da Interface de Firmware (EFI) que detém a loja BCD.

   Na imagem a seguir, o disco 2 é o número do disco anexado ao VM de reparação. A imagem também mostra a partição do sistema EFI no Disco 2, que tem 100MB de tamanho e não tem uma carta atribuída.

   ![O disco 2 é apresentado como o número do disco ligado ao VM de reparação. Também mostra a partição do sistema EFI no Disco 2, que é de 100MB e não tem uma carta atribuída a ele.](./media/troubleshoot-boot-error-status-not-found/8.png)

   1. Abra uma solicitação de comando elevada como Administrador e introduza os seguintes comandos:
      1. Abra a **FERRAMENTA DISKPART** utilizando o comando `diskpart` .
      1. Listar todos os discos e, em seguida, selecionar o disco anexo identificado no passo anterior:
      
         ```
         list disk
         sel disk <DISK #>
         ```

         A imagem a seguir mostra os resultados da listagem e seleção de um disco. O disco 0 (127 GB / Online), o disco 1 (32 GB / Online) e o Disco 2 (127 GB / Online) estão listados, tendo o Disco 2 sido selecionado utilizando o comando `sel disk 2` .

         ![Os resultados da listagem e, em seguida, a seleção de um disco. Disco 0 (127 GB | Online), Disco 1 (32 GB | Online) e Disco 2 (127 GB | Online) estão listados, com o Disco 2 a ser selecionado.](./media/troubleshoot-boot-error-status-not-found/9.png)

      1. Listar as divisórias e selecionar a partição do sistema EFI identificada no passo anterior:
      
         ```
         list partition
         sel partition <PARTITION #>
         ```

         A imagem a seguir mostra os resultados da listagem e da seleção de uma partição. Estão listadas a partição 1 (Reservada / 16MB), Partição 2 (Sistema / 100MB) e Partição 3 (Primária / 126 GB), com a Partição 2 a ser selecionada utilizando o comando `sel part 2` .

         ![Os resultados da listagem e, em seguida, a seleção de uma divisória. Estão listadas a partição 1 (Reservada | 16MB), Partição 2 (System | 100MB) e Partição 3 (| 126 GB) estão selecionadas, tendo sido selecionada a Partição 2.](./media/troubleshoot-boot-error-status-not-found/10.png)

      1. Atribua uma carta à partição EFI utilizando o comando `assign` .

         Na imagem a seguir, o `assign` comando e o novo sistema de acionamento **(F:)** são ambos visualizais no File Explorer.

         ![O comando de atribuição e o novo sistema de acionamento (F:) são visualizais no Explorador de Ficheiros.](./media/troubleshoot-boot-error-status-not-found/11.png)

      1. Listar os dados da loja BCD utilizando o seguinte comando:
      
         `bcdedit /store <LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

         Na imagem seguinte, o Windows Boot Loader encontra-se numa geração de 2 VM com o atributo identificador em destaque. O atributo identificador destacado tem um valor de **{predefinido}**.

         ![O Windows Boot Loader é apresentado num VM de Geração 2 com o atributo identificador em destaque. O atributo identificador realçado mostra o padrão como o seu valor.](./media/troubleshoot-boot-error-status-not-found/12.png)

         Note o identificador do Carregador de Botas do Windows, cujo caminho é **\windows\system32\winload.efi**.

1. Note que falta a variável OSDEVICE na partição ativa:

   ![O Windows Boot Manager e os atributos do Windows Boot Loader estão listados na localização de comando, faltando o atributo do Dispositivo OS.](./media/troubleshoot-boot-error-status-not-found/13.png)
   
   Nesta imagem, o Windows Boot Manager e os atributos do Windows Boot Loader estão listados na origem do comando, mas falta o atributo OSDEVICE.

1. Adicione a variável OSDEVICE com base nas seguintes informações:

   Para as divisórias individuais discos OS, adicione `BOOT` .

   > [!NOTE]
   > A pasta de arranque estará na mesma divisória que a pasta do windows **\windows .**
   > - A pasta de arranque para VMs da Geração 1 é **(pasta\boot\bcd)**.
   > - A pasta bootable para VMs da Geração 2 é **EFI\Microsoft\boot\bcd**.

   Para a Geração 1 VMs, insira o seguinte comando:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   Para a Geração 2 VMs, insira o seguinte comando:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   Para vários discos OS de partição, adicione `PARTITION=<LETTER OF WINDOWS FOLDER>:` .

   > [!NOTE]
   > A pasta de boot-able provavelmente estará numa divisória diferente da pasta do windows **\windows pasta**.
   > - A pasta de arranque para VMs da Geração 1 é **(pasta\boot\bcd)**.
   > - A pasta bootable para VMs da Geração 2 é **EFI\Microsoft\boot\bcd**.

   Para a Geração 1 VMs, insira o seguinte comando:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

   Para a Geração 2 VMs, insira o seguinte comando:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {< IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

Uma vez concluída esta tarefa, continue a [ativar a consola em série e a recolha de despejos de memória](#enable-the-serial-console-and-memory-dump-collection).

### <a name="contact-support"></a>Contactar o Suporte

O erro **do Ficheiro de Registo** tem uma solução, mas terá de criar um bilhete de [apoio](https://azure.microsoft.com/support/create-ticket/) para mais assistência.

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
