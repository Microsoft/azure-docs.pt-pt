---
title: Erro de paragem do Windows de resolução de problemas – falha na inicialização do serviço de diretório
description: Resolva problemas em que uma máquina virtual de controlador de domínio Ative Directy (VM) em Azure está presa num loop afirmando que precisa de reiniciar.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3396f8fe-7573-4a15-a95d-a1e104c6b76d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/05/2020
ms.author: v-miegge
ms.openlocfilehash: 118c81dd52951729bfbbb97a510e693861666ee6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665139"
---
# <a name="troubleshoot-windows-stop-error--directory-service-initialization-failure"></a>Erro de paragem do Windows de resolução de problemas – falha na inicialização do serviço de diretório

Este artigo fornece medidas para resolver problemas em que uma máquina virtual de controlador de domínio Ative Directy (VM) em Azure, está presa num loop e afirma que precisa de reiniciar.

## <a name="symptom"></a>Sintoma

Quando utiliza [diagnósticos boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para visualizar a imagem do VM, a imagem mostra que o VM precisa de reiniciar devido a um erro, exibindo o código de paragem **0xC00002E1** no Windows Server 2008 R2, ou **0xC00002E2** no Windows Server 2012 ou mais tarde.

![O ecrã de arranque do Windows Server 2012 afirma "O seu PC teve um problema e precisa de reiniciar. Estamos apenas a recolher algumas informações de erro, e depois recomeçamos para ti."](./media/troubleshoot-directory-service-initialization-failure/1.png)

## <a name="cause"></a>Causa

O código de erro **0xC00002E2** representa **STATUS_DS_INIT_FAILURE**, e o código de erro **0xC00002E1** representa **STATUS_DS_CANT_START**. Ambos os erros ocorrem quando há um problema com o serviço de diretório.

À medida que o Sistema operativo operativo se instala, é então obrigado a reiniciar automaticamente pelo Servidor de Autenticação de Segurança Local **(LSASS.exe),** que autentica os logins dos utilizadores. A autenticação não pode acontecer quando o sistema operativo no VM é um controlador de domínio que não tem acesso a leitura/escrita à sua base de dados local do Diretório Ativo. Devido à falta de acesso ao **Ative Directory (AD),** o LSASS.exe não consegue autenticar e é obrigado a reiniciar o Sistema operativo.

Este erro pode ser causado por qualquer uma das seguintes condições:

- Não há acesso ao disco que detém a base de dados local **(NTDS). DIT**).
- O disco que mantém a base de dados local (NTDS). DIT) esgotou o espaço livre.
- A base de dados local de AD (NTDS. O ficheiro DIT está em falta.
- O VM tem vários discos e a política da Rede de Área de Armazenamento (SAN) está configurada de forma inadequada. A política SAN não está definida para **ONLINEALL**, e os discos não-S estão ligados em modo offline no gestor do disco.
- A base de dados local de AD (NTDS. DIT) ficheiro é corrupto.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo:

1. Criar e aceder a um VM de reparação.
1. Espaço livre no disco.
1. Verifique se a unidade que contém a base de dados ad está anexada.
1. Ativar o modo de restauro de serviços de diretório.
1. **Recomendado**: Antes de reconstruir o VM, ative a recolha de consolas em série e de despejo de memória.
1. Reconstruir o VM.
1. Reconfigurar a Política SAN.

> [!NOTE]
> Ao encontrar este erro, o Os convidado não está operacional. Estará a resolver problemas em modo offline para resolver o problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e aceder a um VM de reparação

1. Utilize [os passos 1-3 dos Comandos de Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para preparar um VM de reparação.
1. Utilizando a ligação remota de ambiente de trabalho ao VM de reparação.

### <a name="free-up-space-on-disk"></a>Liberte espaço no disco

Como o disco está agora ligado a um VM de reparação, verifique se o disco que detém a base de dados interna do Diretório Ativo tem espaço suficiente para executar corretamente.

1. Verifique se o disco está cheio clicando à direita na unidade e selecionando **Propriedades**.
1. Se o disco tiver menos de 300 Mb de espaço livre, [expanda-o para um máximo de 1 Tb utilizando powerShell](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk).
1. Se o disco tiver atingido 1 Tb de espaço usado, efetue uma limpeza do disco.

   1. Utilize o PowerShell para [separar o disco](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell) de dados do VM avariado.
   1. Uma vez separado do VM quebrado, [prenda o disco de dados](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm) a um VM funcional.
   1. Utilize a [ferramenta de limpeza](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) do disco para libertar espaço adicional.

1. **Opcional** - Se for necessário mais espaço, abra uma instância CMD e introduza o `defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g` comando para realizar uma desfragmentação na unidade:

  * No comando, `<LETTER ASSIGNED TO THE OS DISK>` substitua-a com a letra do Disco Os. Por exemplo, se a letra do disco `F:` for, então o comando seria `defrag F: /u /x /g` .

  * Dependendo do nível de fragmentação, a desfragmentação pode levar horas.

Se houver espaço suficiente no disco, continue para a próxima tarefa.

### <a name="check-that-the-drive-containing-the-active-directory-database-is-attached"></a>Verifique se a unidade que contém a base de dados do Diretório Ativo está anexada

1. Abra uma instância CMD elevada e executar os seguintes comandos:

   1. Ficheiro de registo de carga:

      `REG LOAD HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM`

      A designação `f:` pressupõe que o disco é de acionamento `F:` . Utilize a letra de unidade pertencente à unidade que contém o disco OS.

   1. Determine a letra de unidade e a pasta do **NTDS. DIT:**

      ```
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Working Directory"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Database file"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database backup path"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database log files path"
      ```

   1. Descarregar ficheiro de registo:

      `REG UNLOAD HKLM\BROKENSYSTEM`

1. Utilizando o portal Azure, verifique se a unidade onde ntDS. O DIT está configurado, é adicionado ao VM.
1. Utilizando a consola de Gestão de Discos do OS convidado, verifique se o disco que contém NTDS. O DIT está online.
   1. A ferramenta de Gestão de Discos pode ser encontrada em **Ferramentas Administrativas > Gestão informática > Armazenamento,** ou pode ser acedida usando o `diskmgmt.msc` comando numa instância CMD.
1. Se o disco não estiver ligado ao VM, recoloque o disco de dados para corrigir o problema.

   Se o disco estiver normalmente ligado, continue com a tarefa seguinte.

### <a name="enable-directory-services-restore-mode"></a>Ativar o modo de restauro de serviços de diretório

Instale o VM para iniciar o modo de restauro de serviços de **diretório (DSRM)** para contornar a verificação da existência do NTDS. Ficheiro DIT durante o arranque.

1. Antes de continuar, verifique se completou as tarefas anteriores para ligar o disco a um VM de reparação e determinou qual o disco do NTDS. O ficheiro DIT está localizado.
1. Utilizando uma instância CMD elevada, enumere as informações de partição de arranque nessa loja para encontrar o identificador da partição ativa:

   `bcdedit /store <Drive Letter>:\boot\bcd /enum`

   `< Drive Letter >`Substitua-a pela letra determinada nos passos anteriores.

   ![A imagem mostra uma instância CMD elevada depois de entrar no comando 'bcdedit /loja <Drive Letter>:\boot\bcd /enum', que exibe o Windows Boot Manager com o identificador.](./media/troubleshoot-directory-service-initialization-failure/2.png)

1. Ativar a `safeboot DsRepair` bandeira na partição de arranque:

   `bcdedit /store <Drive Letter>:\boot\bcd /set {<Identifier>} safeboot dsrepair`

   Substitua `< Drive Letter >` e `< Identifier >` com os valores determinados nos passos anteriores.

1. Questione novamente as opções de arranque para garantir que a sua mudança foi corretamente definida.

   ![A imagem mostra uma instância CMD elevada após ativar a bandeira safeboot DsRepair.](./media/troubleshoot-directory-service-initialization-failure/3.png)

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Recomendado: antes de reconstruir o VM, ative a recolha de consolas em série e de despejo de memória

Para ativar a recolha de despejode memória e consola em série, execute o seguinte script abrindo uma sessão de solicitação de comando elevada (Executar como administrador) e executar os seguintes comandos.

1. Ativar a consola em série:

  ```
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
  ```

1. Verifique se o espaço livre no disco OS é pelo menos igual ao tamanho da memória (RAM) no VM.

  1. Se não houver espaço suficiente no disco OS, altere a localização onde o ficheiro de despejo de memória será criado e consulte isso para qualquer disco de dados ligado ao VM que tenha espaço livre suficiente.

     Para alterar a localização, `%SystemRoot%` substitua-a pela letra de unidade (por exemplo, `F:` ) do disco de dados nos seguintes comandos.

  #### <a name="the-following-configuration-is-suggested-to-enable-os-dump"></a>Sugere-se a seguinte configuração para permitir o despejo de OS:

  **Disco operativo osso avariado**de carga:

  `REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

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

  `REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-vm"></a>Reconstruir o VM

1. Utilize [o passo 5 dos Comandos de Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) para remontar o VM.

### <a name="reconfigure-the-storage-area-network-policy"></a>Reconfigurar a política da Rede de Área de Armazenamento

1. Ao iniciar o modo DSRM, o único utilizador disponível para iniciar sessão é o administrador de recuperação, que foi utilizado quando o VM foi promovido a um controlador de domínio. Todos os outros utilizadores mostrarão um erro de autenticação.

   1. Se não houver outro DC disponível, deve iniciar sessão localmente `.\administrator` ou `machinename\administrator` a senha DSRM.

1. Estabeleça a política san para que todos os discos estejam online.

   1. Abra uma instância CMD elevada e entre `DISKPART` .
   1. Consulta para a lista dos discos.

      `DISKPART> list disk`

   1. Introduza os seguintes comandos para selecionar o disco que precisa de ser trazido online e alterar a política san:

      ```
      DISKPART> select disk 1
      Disk 1 is now the selected disk.

      DISKPART> attributes disk clear readonly
      Disk attributes cleared successfully.

      DISKPART> attributes disk
      Current Read-only State : No
      Read-only : No
      Boot Disk : No
      Pagefile Disk : No
      Hibernation File Disk : No
      Crashdump Disk : No
      Clustered Disk : No

      DISKPART> online disk
      DiskPart successfully onlined the selected disk.

      DISKPART> san
      SAN Policy : Online All
      ```

1. Uma vez fixada a questão, certifique-se de que a bandeira `DsRepair safeboot` é removida:

   `bcdedit /deletevalue {default} safeboot dsrepair`

1. Reinicie o seu VM.

   > [!NOTE]
   > Se o seu VM acabou de migrar do local e quiser migrar mais controladores de domínio do local para o Azure, deve considerar seguir os passos do artigo abaixo para evitar que esta questão aconteça em futuras migrações:
   >
   > [Como carregar os controladores de domínio hiper-V existentes no local para o Azure utilizando o Azure PowerShell](https://support.microsoft.com/help/2904015)
