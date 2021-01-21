---
title: A máquina virtual não responde ao aplicar a política de configuração da política de auditoria
description: Este artigo fornece medidas para resolver problemas em que a máquina virtual (VM) fica sem resposta ao aplicar a política de configuração da política de auditoria, o que impede o arranque de um VM Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3f6383b5-81fa-49ea-9434-2fe475e4cbef
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: f286542c91ba473d13595d8e8299b1bbd8c93856
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632610"
---
# <a name="virtual-machine-is-unresponsive-while-applying-audit-policy-configuration-policy"></a>A máquina virtual não responde ao aplicar a política de configuração da política de auditoria

Este artigo fornece medidas para resolver problemas em que a máquina virtual (VM) fica sem resposta ao aplicar a política de configuração da política de auditoria, o que impede o arranque de um VM Azure.

## <a name="symptom"></a>Sintoma

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, verá que a imagem mostra que o sistema operativo (OS) não respondeu durante uma bota com a mensagem Aplicando a política de **configuração da política de auditoria**.

  ![O arranque do SISTEMA com a mensagem: "Aplicar a política de configuração da política de auditoria"](./media/vm-unresponsive-applying-audit-configuration-policy/1.png)

  ![O arranque do SISTEMA no Windows Server 2012 com a mensagem: "Aplicar a política de configuração da política de auditoria"](./media/vm-unresponsive-applying-audit-configuration-policy/2.png)

## <a name="cause"></a>Causa

Há bloqueios contraditórios quando a política tenta limpar perfis antigos dos utilizadores.

> [!NOTE]
> Isto aplica-se apenas ao Windows Server 2012 e ao Windows Server 2012 R2.

Eis a política problemática: Configuração do *computador\Políticas\Modelos administrativos\Sistema/Perfis do utilizador\Eliminar os perfis dos utilizadores mais antigos do que um número especificado de dias no reinício do sistema.*

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo

> [!TIP]
> Se tiver uma cópia de segurança recente do VM, poderá tentar [restaurar o VM da cópia de segurança](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema da bota.

1. Criar e Aceder a um VM de reparação.
1. Desative a apólice.
1. Ativar a recolha de consolas em série e de despejo de memória.
1. Reconstruir o VM.
1. Recolha o ficheiro de despejo de memória e envie um bilhete de apoio.

### <a name="create-and-access-a-repair-vm"></a>Criar e aceder a um VM de reparação

1. Utilize os passos 1-3 dos Comandos de [Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar um VM de reparação.
1. Utilizando ligação de ambiente de trabalho remoto, ligue-se ao VM de reparação.

### <a name="disable-the-policy"></a>Desativar a política

1. Na VM de reparação, abra o **Editor de Registos.**
1. Localize a **chave HKEY_LOCAL_MACHINE** e selecione **'Ficheiro > Carregar Colmeia'** a partir do menu.

   ![A navegação dentro do Editor de Registo para carregar uma colmeia.](./media/vm-unresponsive-applying-audit-configuration-policy/3.png)

   - Pode utilizar a Colmeia de Carga para carregar as chaves de registo de um sistema offline. Neste caso, o sistema é o disco partido ligado à VM de reparação.
   - As definições em todo o sistema são armazenadas no **HKEY_LOCAL_MACHINE** e podem ser abreviadas como **HKLM**.

1. No disco anexo, abra o `\windows\system32\config\SOFTWARE` ficheiro.

   - Quando lhe pedirem um nome, insira **BROKENSOFTWARE**.
   - Para verificar se **brokensoftware** foi carregado, expanda **HKEY_LOCAL_MACHINE** e procure a chave **BROKENSOFTWARE** adicionada.

1. Vá ao **BROKENSOFTWARE** e verifique se a tecla **CleanupProfiles** existe na colmeia carregada.

   - Se a chave existir, a política **de LimpezaProfiles** é definida. O seu valor representa a política de retenção medida em dias.
   - Se a chave não existir, a política **de LimpezaProfiles** não está definida. Nesta situação, avance para [apresentar um bilhete de apoio com um ficheiro de despejo de memória](#collect-the-memory-dump-file-and-submit-a-support-ticket).

1. Elimine a tecla **CleanupProfiles** utilizando este comando:

   `reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f`

1. Descarregue a **colmeia BROKENSOFTWARE** utilizando este comando:

   `reg unload HKLM\BROKENSOFTWARE`

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Ativar a consola em série e a recolha de despejo de memória

**Recomendado:** Antes de reconstruir o VM, ative a coleção de consola em série e de despejo de memória executando o seguinte script:

1. Abra uma sessão de pedido de comando elevado como administrador.
1. Enuda os dados da loja BCD e determine o identificador de carregador de arranque, que utilizará no passo seguinte.

   1. Para uma Geração 1 VM, insira o seguinte comando e note o identificador listado:

      `bcdedit /store <BOOT PARTITON>:\boot\bcd /enum`

      - No comando, `<BOOT PARTITON>` substitua-a pela letra da partição no disco anexo que contém a pasta de arranque.

        ![A Figura 4 mostra a saída da listagem da loja BCD numa Geração 1 VM, que lista no Windows Boot Loader o número do identificador.](./media/vm-unresponsive-applying-audit-configuration-policy/4.png)

   1. Para uma Geração 2 VM, insira o seguinte comando e note o identificador listado:

      `bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

      - No comando, `<LETTER OF THE EFI SYSTEM PARTITION>` substitua-se pela letra da Partição do Sistema EFI.
      - Pode ser útil lançar a consola de Gestão de Discos para identificar a partição do sistema adequada rotulada como **Partição do Sistema EFI**.
      - O identificador pode ser um GUID único ou pode ser o **bootmgr** padrão .

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
   
### <a name="rebuild-the-virtual-machine"></a>Reconstruir a máquina virtual

1. Utilize [o passo 5 dos Comandos de Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para reconstruir o VM.

1. Teste se as suas botas VM normalmente para ver se o problema corrigiu o problema.

   - Se o problema não tiver sido corrigido, continue a [recolher um ficheiro de despejo e a enviar um bilhete de apoio](#collect-the-memory-dump-file-and-submit-a-support-ticket).
   - Se a questão tiver sido corrigida, não serão necessários mais passos.

Se a questão foi corrigida, a apólice é agora desativada localmente. Para uma solução permanente, não utilize a política de LimpezaProfiles em VMs, uma vez que eliminará automaticamente os perfis do utilizador. Utilize um método diferente para executar limpezas de perfis, como uma tarefa ou script programados.

**Não use esta política:** 
 *Modelos de administração da máquina\System\User Profiles\Eliminar os perfis do utilizador mais antigos do que um número especificado de dias no reinício do sistema.*

### <a name="the-issue-should-now-be-fixed"></a>A questão deve agora ser corrigida

Teste o seu VM para se certificar de que está a funcionar normalmente. Se ainda estiver a passar por problemas, então pode continuar na secção seguinte para mais assistência.

### <a name="collect-the-memory-dump-file-and-submit-a-support-ticket"></a>Recolha o ficheiro de despejo de memória e envie um bilhete de apoio

Para resolver este problema, bastaria primeiro recolher o ficheiro de despejo de memória para o acidente e, em seguida, contactar o suporte com o ficheiro de despejo de memória. Para recolher o ficheiro de despejo, siga estes passos:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Fixe o disco DE a um novo VM de reparação

1. Utilize os passos 1-3 dos Comandos de [Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar um novo VM de reparação.
1. Utilizando ligação de ambiente de trabalho remoto, ligue-se ao VM de reparação.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localize o ficheiro de despejo e envie um bilhete de apoio

1. Na vM de reparação, vá à pasta do janela no disco oss anexado. Se a letra do controlador atribuída ao disco de sos anexado estiver rotulada como *F,* então tem de ir a `F:\Windows` .
1. Localize o `memory.dmp` ficheiro e, em seguida, [envie um bilhete de apoio](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o ficheiro de despejo de memória.
1. Se tiver problemas em localizar o `memory.dmp` ficheiro, utilize [chamadas de interrupção não mascarada (NMI) na consola em série.](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) Siga o guia para [gerar um ficheiro de despejo de falhas utilizando chamadas NMI aqui](/windows/client-management/generate-kernel-or-complete-crash-dump).