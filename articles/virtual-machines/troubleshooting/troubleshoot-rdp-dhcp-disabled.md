---
title: Não é possível ligar remotamente às Máquinas Virtuais Azure porque o DHCP está desativado. Microsoft Docs
description: Saiba como resolver problemas de RDP causados pelo serviço de clientes DHCP está desativado no Microsoft Azure.[ Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 2c5b0556554d280e57b2df51875e1b057b5fb4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749890"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Não é possível rdp para máquinas virtuais Azure porque o serviço de cliente DHCP está desativado

Este artigo descreve um problema no qual não é possível remotamente desktop para Máquinas Virtuais Do Windows (VMs) depois de o serviço DHCP Client ser desativado no VM.


## <a name="symptoms"></a>Sintomas
Não é possível fazer uma ligação RDP a um VM em Azure porque o serviço De cliente DHCP está desativado no VM. Quando verifica a imagem nos diagnósticos da [Bota](../troubleshooting/boot-diagnostics.md) no portal Azure, vê normalmente as botas VM e aguarda-se por credenciais no ecrã de login. Você vê remotamente os registos do evento no VM utilizando o Espectador de Eventos. Você vê que o Serviço de Clientes DHCP não está iniciado ou não começa. O seguinte um registo de amostra:

**Nome de registo**: Sistema </br>
**Fonte**: Gestor de Controlo de Serviços </br>
**Data**: 12/16/2015 11:19:36 </br>
**ID do evento**: 7022 </br>
**Categoria tarefa**: Nenhum </br>
**Nível**: Erro </br>
**Palavras-chave**: Clássico</br>
**Utilizador**: N/A </br>
**Computador**: myvm.cosotos.com</br>
**Descrição**: O serviço de cliente DHCP pendia no arranque.</br>

Para VMs do Gestor de Recursos, pode utilizar a funcionalidade de consola de acesso em série para consultar os registos do evento 7022 utilizando o seguinte comando:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

Para VMs clássicos, terá de trabalhar em modo OFFLINE e recolher os registos manualmente.

## <a name="cause"></a>Causa

O Serviço de Clientes DHCP não está a funcionar no VM.

> [!NOTE]
> Este artigo aplica-se apenas ao serviço Cliente DHCP e não ao Servidor DHCP.

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire uma foto do disco osso do VM afetado como cópia de segurança. Para mais informações, consulte [snapshot um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver este problema, utilize o controlo de série para ativar o DHCP ou [redefinir](reset-network-interface.md) a interface de rede para o VM.

### <a name="use-serial-control"></a>Utilizar o controlo em série

1. Ligue-se à consola em série e abra a [instância CMD](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Se a Consola série não estiver ativada no seu VM, consulte a [interface de rede Reset](reset-network-interface.md).
2. Verifique se o DHCP está desativado na interface da rede:

        sc query DHCP
3. Se o DHCP for parado, tente iniciar o serviço

        sc start DHCP

4. Consultar novamente o serviço para se certificar de que o serviço é iniciado com sucesso.

        sc query DHCP

    Tente ligar-se ao VM e veja se o problema está resolvido.
5. Se o serviço não arrancar, utilize a seguinte solução adequada, com base na mensagem de erro que recebeu:

    | Erro  |  Solução |
    |---|---|
    | 5- ACESSO NEGADO  | Consulte o [serviço de cliente DHCP parado devido a um erro de Acesso Negado](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | Consulte os [acidentes ou os enforcamentos do serviço do Cliente DHCP.](#dhcp-client-service-crashes-or-hangs)  |
    | 1058 - ERROR_SERVICE_DISABLED  | Ver [Serviço de Cliente DHCP está desativado](#dhcp-client-service-is-disabled).  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |[Suporte de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.   |
    | 1067 - ERROR_PROCESS_ABORTED |Consulte os [acidentes ou os enforcamentos do serviço do Cliente DHCP.](#dhcp-client-service-crashes-or-hangs)   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | [Suporte de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  Ver [Serviço de Cliente DHCP falha devido a falha no logon](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | Consulte os [acidentes ou os enforcamentos do serviço do Cliente DHCP.](#dhcp-client-service-crashes-or-hangs)  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | Ver [Serviço de Cliente DHCP está desativado](#dhcp-client-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Suporte de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.  |
    |1053 | [Suporte de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>Serviço de Cliente DHCP está parado devido a um erro de Acesso Negado

1. Ligue-se à [Consola em Série](serial-console-windows.md) e abra uma instância PowerShell.
2. Descarregue a ferramenta Monitor de Processo executando o seguinte script:

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Agora inicie um traço **de procmon:**

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Reproduzir o problema iniciando o serviço que está a gerar a mensagem **Access Negado:**

   ```
   sc start DHCP
   ```

   Quando falhar, termine o rastreio do Monitor de Processo:

   ```
   procmon /Terminate
   ```
5. Recolher o ficheiro **c:\temp\ProcMonTrace.PML:**

    1. Fixe um disco de [dados ao VM](../windows/attach-managed-disk-portal.md
).
    2. Utilize a Consola em Série pode copiar o ficheiro para a nova unidade. Por exemplo, `copy C:\temp\ProcMonTrace.PML F:\`. Neste comando, F é a letra condutora do disco de dados anexado. Substitua a carta conforme apropriado pelo valor correto.
    3. Desmontar a unidade de dados e, em seguida, anexá-la a um VM de trabalho que tenha o Monitor de Processo sutiques instalados.

6. Abra **procMonTrace.PML** utilizando o Monitor de Processo no VM de trabalho. Em seguida, o filtro por **Resultado é O ACESSO NEGADO,** como mostra a seguinte imagem:

    ![Filtrar por resultado no Monitor de Processos](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Corrija as teclas, pastas ou ficheiros de registo que estejam na saída. Normalmente, este problema é causado quando a conta de entrada que é usada no serviço não tem permissão ACL para aceder a estes objetos. Para determinar a permissão ACL correta para a conta de entrada, pode verificar um VM saudável.

#### <a name="dhcp-client-service-is-disabled"></a>O serviço de cliente DHCP está desativado

1. Restaurar o serviço ao seu valor de arranque padrão:

   ```
   sc config DHCP start= auto
   ```

2. Iniciar o serviço:

   ```
   sc start DHCP
   ```

3. Questione novamente o estado do serviço para se certificar de que está a funcionar:

   ```
   sc query DHCP
   ```

4. Tente ligar-se ao VM utilizando o Remote Desktop.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>Serviço de cliente DHCP falha devido a falha no logon

1. Como este problema ocorre se a conta de arranque deste serviço foi alterada, reverta a conta para o seu estado de incumprimento:

        sc config DHCP obj= 'NT Authority\Localservice'
2. Iniciar o serviço:

        sc start DHCP
3. Tente ligar-se ao VM utilizando o Remote Desktop.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>Serviço de cliente DHCP falha ou enforca

1. Se o estado de serviço estiver preso no estado de **arranque** ou **paragem,** tente parar o serviço:

        sc stop DHCP
2. Isole o serviço no seu próprio recipiente de 'svchost':

        sc config DHCP type= own
3. Iniciar o serviço:

        sc start DHCP
4. Se o serviço ainda não arrancar, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Reparar o VM offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Fixe o disco OS a um VM de recuperação

1. [Fixe o disco OS a um VM](../windows/troubleshoot-recovery-disks-portal.md)de recuperação .
2. Inicie uma ligação remote Desktop ao VM de recuperação. Certifique-se de que o disco em anexo está sinalizado como **Online** na consola de Gestão de Discos. Repare na carta de unidade atribuída ao disco operativo em anexo.
3.  Abra uma instância de instrução de comando elevada **(Executar como administrador).** Em seguida, executar o seguinte script. Este script pressupõe que a letra de unidade atribuída ao disco operativo em anexo é **F**. Substitua a carta conforme apropriado pelo valor do seu VM.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Set default values back on the broken service
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v type /t REG_DWORD /d 16 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v type /t REG_DWORD /d 16 /f

    reg unload HKLM\BROKENSYSTEM
    ```

4. [Desmontar o disco OS e recriar o VM](../windows/troubleshoot-recovery-disks-portal.md). Em seguida, verifique se o problema está resolvido.

## <a name="next-steps"></a>Passos seguintes

Se ainda precisar de ajuda, [contacte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) o suporte para resolver o seu problema.