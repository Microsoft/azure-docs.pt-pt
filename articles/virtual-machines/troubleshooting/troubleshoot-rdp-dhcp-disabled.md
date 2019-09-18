---
title: Não é possível ligar remotamente às máquinas de virtuais do Azure porque está desativado o DHCP | Documentos da Microsoft
description: Saiba como resolver problemas relacionados com RDP problema seja causado pelo serviço de cliente DHCP está desabilitado no Microsoft Azure. | Documentos da Microsoft
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
ms.openlocfilehash: ef44931cc3b36bcab64a2de840d9264c1b8fdedb
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058027"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Não é possível RDP para máquinas de virtuais do Azure, porque o serviço de cliente DHCP é desativado

Este artigo descreve um problema em que não é possível o ambiente de trabalho remoto para máquinas de virtuais de Windows do Azure (VMs) depois do serviço de cliente DHCP está desativado na VM.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="symptoms"></a>Sintomas
Não pode fazer uma ligação RDP uma VM no Azure, porque o serviço de cliente DHCP está desativado na VM. Quando dá entrada a captura de ecrã do [diagnósticos de arranque](../troubleshooting/boot-diagnostics.md) no portal do Azure, verá a VM arranca normalmente e aguarda que as credenciais no ecrã de início de sessão. Ver remotamente os registos de eventos na VM com o Visualizador de eventos. Verá que o serviço de cliente DHCP não estiver iniciado ou não conseguir iniciar. Registo de um exemplo a seguir:

**Nome do log**: Sistema </br>
**Origem**: Gerenciador de controle de serviço </br>
**Data**: 12/16/2015 11:19:36 AM </br>
**ID do evento**: 7022 </br>
**Categoria da tarefa**: Nenhum </br>
**Nível**: Erro </br>
**Palavras-chave**: Clássica</br>
**Usuário**: N/A </br>
**Computador**: myvm.cosotos.com</br>
**Descrição**: O serviço cliente DHCP parou ao iniciar.</br>

Para VMs do Resource Manager, pode utilizar a funcionalidade de consola de acesso de série a consulta para o evento registos 7022 com o seguinte comando:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

Para VMs clássicas, terá de trabalhar no modo OFFLINE e recolher os registos manualmente.

## <a name="cause"></a>Causa

O serviço de cliente DHCP não está em execução na VM.

> [!NOTE]
> Este artigo aplica-se apenas para o serviço de cliente DHCP e não o servidor DHCP.

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire um instantâneo do disco do SO da VM afetado como uma cópia de segurança. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver este problema, utilize o controlo Serial para ativar o DHCP ou [interface de rede de reposição](reset-network-interface.md) para a VM.

### <a name="use-serial-control"></a>Utilizar o controlo de série

1. Ligar à [consola de série e Abrir instância CMD](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Se a consola de série não estiver ativada na sua VM, consulte [interface de rede de reposição](reset-network-interface.md).
2. Verifique se o DHCP está desabilitado na interface de rede:

        sc query DHCP
3. Se o DHCP está parado, tente iniciar o serviço

        sc start DHCP

4. Consulte o serviço novamente para certificar-se de que o serviço é iniciado com êxito.

        sc query DHCP

    Tente ligar à VM e veja se o problema está resolvido.
5. Se o serviço não iniciar, utilize a seguinte solução apropriada, com base na mensagem de erro que recebeu:

    | Erro  |  Solução |
    |---|---|
    | 5 - ACESSO NEGADO  | Ver [serviço de cliente DHCP está parado devido a um erro de acesso negado](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | Ver [serviço de cliente DHCP falhas ou paradas](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 - ERROR_SERVICE_DISABLED  | Ver [serviço de cliente DHCP é desativado](#dhcp-client-service-is-disabled).  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |[Contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para seu problema resolvido rapidamente.   |
    | 1067 - ERROR_PROCESS_ABORTED |Ver [serviço de cliente DHCP falhas ou paradas](#dhcp-client-service-crashes-or-hangs).   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | [Contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para seu problema resolvido rapidamente.  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  Consulte [serviço de cliente DHCP falhar devido a falha de início de sessão](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | Ver [serviço de cliente DHCP falhas ou paradas](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | Ver [serviço de cliente DHCP é desativado](#dhcp-client-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para seu problema resolvido rapidamente.  |
    |1053 | [Contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para seu problema resolvido rapidamente.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>O serviço de cliente DHCP está parado devido a um erro de acesso negado

1. Ligar à [consola de série](serial-console-windows.md) e abra uma instância do PowerShell.
2. Transferir a ferramenta de Monitor do processo executando o seguinte script:

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Inicie agora uma **procmon** rastreio:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Reproduza o problema ao iniciar o serviço que está a gerar a **acesso negado** mensagem:

   ```
   sc start DHCP
   ```

   Quando ocorre uma falha, encerrar o rastreio de Monitor do processo:

   ```
   procmon /Terminate
   ```
5. Recolher a **c:\temp\ProcMonTrace.PML** ficheiro:

    1. [Anexar um disco de dados para a VM](../windows/attach-managed-disk-portal.md
).
    2. Utilize a consola de série, pode copiar o ficheiro para o novo disco. Por exemplo, `copy C:\temp\ProcMonTrace.PML F:\`. Neste comando, F é a letra de unidade do disco de dados anexados. Substitua a letra conforme adequado com o valor correto.
    3. Desligar a unidade de dados e, em seguida, anexá-lo para um VM que tenha ubstakke de Monitor do processo instalado em funcionamento.

6. Open **ProcMonTrace.PML** com o Monitor de processo sobre a VM em funcionamento. Em seguida, filtrar por **resultado é o acesso NEGADO**, conforme mostrado na captura de ecrã seguinte:

    ![Filtrar por resultado no Monitor do processo](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Corrigi as chaves do Registro, pastas ou ficheiros que estão na saída. Normalmente, esse problema é causado quando a conta de início de sessão que é utilizada no serviço não tem permissão de ACL de acesso esses objetos. Para determinar a permissão de ACL correta para a conta de início de sessão, pode verificar uma VM em bom estado.

#### <a name="dhcp-client-service-is-disabled"></a>O serviço de cliente DHCP é desativado

1. Restaure o serviço para o valor de arranque predefinido:

   ```
   sc config DHCP start= auto
   ```

2. Inicie o serviço:

   ```
   sc start DHCP
   ```

3. Consultar o estado de serviço novamente para garantir que ela está em execução:

   ```
   sc query DHCP
   ```

4. Tente ligar à VM com o ambiente de trabalho remoto.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>Serviço de cliente DHCP falhar devido a falha de início de sessão

1. Uma vez que este problema ocorre se a conta de arranque deste serviço tiver sido alterada, reverta a conta para o estado do mesmo padrão:

        sc config DHCP obj= 'NT Authority\Localservice'
2. Inicie o serviço:

        sc start DHCP
3. Tente ligar à VM com o ambiente de trabalho remoto.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>Falhas de serviço de cliente DHCP ou travamentos

1. Se o estado do serviço fica preso no **inicial** ou **parar** de estado, tente parar o serviço:

        sc stop DHCP
2. Isole o serviço no seu próprio contentor "svchost":

        sc config DHCP type= own
3. Inicie o serviço:

        sc start DHCP
4. Se o serviço ainda não for iniciado, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Repare a VM offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco do SO a uma VM de recuperação

1. [Anexar o disco do SO a uma VM de recuperação](../windows/troubleshoot-recovery-disks-portal.md).
2. Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação. Certifique-se de que o disco ligado é sinalizado de forma **Online** no console de gerenciamento de disco. Tenha em atenção a letra de unidade que está atribuída ao disco do SO anexado.
3.  Abra uma instância de linha de comandos elevada (**executar como administrador**). Em seguida, execute o seguinte script. Este script presume que é a letra de unidade que está atribuída ao disco do SO anexado **F**. Substitua a letra conforme adequado com o valor na sua VM.

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

4. [Desanexar o disco do SO e recriar a VM](../windows/troubleshoot-recovery-disks-portal.md). Em seguida, verifique se o problema é resolvido.

## <a name="next-steps"></a>Passos Seguintes

Se precisar de ajuda, ainda [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) conseguir resolver o seu problema.