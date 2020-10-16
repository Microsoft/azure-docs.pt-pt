---
title: Não é possível ligar-se remotamente às Máquinas Virtuais Azure porque o DHCP está desativado. Microsoft Docs
description: Saiba como resolver problemas de PDR que é causado pelo serviço ao cliente DHCP é desativado no Microsoft Azure./ Microsoft Docs
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
ms.openlocfilehash: 945f8896a844e7a73107df44d03abc7290f4e3fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86999143"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Não é possível rdp para Azure Virtual Machines porque o serviço dhcp cliente está desativado

Este artigo descreve um problema no qual não é possível evitar o ambiente de trabalho para Azure Windows Virtual Machines (VMs) depois de o serviço dhcp Client ser desativado no VM.


## <a name="symptoms"></a>Sintomas
Não é possível escoar uma ligação RDP a um VM em Azure porque o serviço dhcp cliente está desativado no VM. Quando verifica a imagem no [diagnóstico Boot](../troubleshooting/boot-diagnostics.md) no portal Azure, vê normalmente as botas VM e aguarda credenciais no ecrã de login. Visualiza remotamente os registos de eventos no VM utilizando o Observador de Eventos. O serviço de clientes da DHCP não foi iniciado ou não começou. Segue-se um registo de amostras:

**Nome do registo**: Sistema </br>
**Fonte**: Gestor de Controlo de Serviços </br>
**Data**: 12/16/2015 11:19:36 </br>
**ID do evento**: 7022 </br>
**Categoria tarefa**: Nenhuma </br>
**Nível**: Erro </br>
**Palavras-chave**: Clássico</br>
**Utilizador**: N/A </br>
**Computador**: myvm.cosotos.com</br>
**Descrição**: O serviço de clientes DHCP pendia no arranque.</br>

Para vMs do Gestor de Recursos, pode utilizar a funcionalidade Consola de Acesso série para consultar os registos do evento 7022 utilizando o seguinte comando:

```console
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

Para os VMs clássicos, terá de trabalhar em modo OFFLINE e recolher os registos manualmente.

## <a name="cause"></a>Causa

O Serviço de Clientes DHCP não está a funcionar no VM.

> [!NOTE]
> Este artigo aplica-se apenas ao serviço do Cliente DHCP e não ao Servidor DHCP.

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire uma foto do disco de SO do VM afetado como cópia de segurança. Para mais informações, consulte [Snapshot um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver este problema, utilize o controlo de série para ativar a interface de rede DHCP ou [reinicializar](reset-network-interface.md) o VM.

### <a name="use-serial-control"></a>Utilizar o controlo em série

1. Ligue-se à [Consola em Série e abra a instância CMD](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Se a Consola em Série não estiver ativada no seu VM, consulte [a interface de rede Reset](reset-network-interface.md).
2. Verifique se o DHCP está desativado na interface de rede:

    ```console
    sc query DHCP
    ```

3. Se o DHCP for parado, tente iniciar o serviço

    ```console
    sc start DHCP
    ```

4. Consultar novamente o serviço para se certificar de que o serviço é iniciado com sucesso.

    ```console
    sc query DHCP
    ```

    Tente ligar-se ao VM e ver se o problema está resolvido.
5. Se o serviço não arrancar, utilize a seguinte solução adequada, com base na mensagem de erro que recebeu:

    | Erro  |  Solução |
    |---|---|
    | 5- ACESSO NEGADO  | Consulte o [serviço de clientes DHCP está parado devido a um erro de Acesso Negado](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | Consulte falhas [ou pendes de serviço do cliente DHCP.](#dhcp-client-service-crashes-or-hangs)  |
    | 1058 - ERROR_SERVICE_DISABLED  | Consulte o [serviço de cliente DHCP desativado.](#dhcp-client-service-is-disabled)  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |[Suporte de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.   |
    | 1067 - ERROR_PROCESS_ABORTED |Consulte falhas [ou pendes de serviço do cliente DHCP.](#dhcp-client-service-crashes-or-hangs)   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | [Suporte de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  Veja o [serviço do cliente DHCP falha devido a falha de logon](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | Consulte falhas [ou pendes de serviço do cliente DHCP.](#dhcp-client-service-crashes-or-hangs)  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | Consulte o [serviço de cliente DHCP desativado.](#dhcp-client-service-is-disabled)  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Suporte de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.  |
    |1053 | [Suporte de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>O serviço de clientes DHCP está parado devido a um erro negado pelo Acesso

1. Ligue-se à [Consola em Série](serial-console-windows.md) e abra uma instância PowerShell.
2. Descarregue a ferramenta Monitor de Processo executando o seguinte script:

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Agora inicie um rasto **de procmon:**

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Reproduza o problema iniciando o serviço que está a gerar a mensagem **Access Denied:**

   ```
   sc start DHCP
   ```

   Quando falhar, termine o rastreio do Monitor de Processo:

   ```
   procmon /Terminate
   ```
5. Recolha o ficheiro **c:\temp\ProcMonTrace.PML:**

    1. [Anexar um disco de dados ao VM](../windows/attach-managed-disk-portal.md
).
    2. Utilize a Consola em Série, pode copiar o ficheiro para a nova unidade. Por exemplo, `copy C:\temp\ProcMonTrace.PML F:\`. Neste comando, F é a letra do condutor do disco de dados anexado. Substitua a letra conforme apropriado pelo valor correto.
    3. Desprender a unidade de dados e, em seguida, anexá-la a um VM em funcionamento que tem o Monitor de Processos instalado.

6. Abra **procMonTrace.PML** utilizando o Monitor de Processo no VM de funcionamento. Em seguida, o filtro por **Resultado é ACCESS DENIED**, como mostra a seguinte imagem:

    ![Filtro por resultado no Monitor de Processos](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Fixe as chaves de registo, pastas ou ficheiros que estão na saída. Normalmente, este problema é causado quando a conta de inscrição que é usada no serviço não tem permissão da ACL para aceder a estes objetos. Para determinar a permissão ACL correta para a conta de entrada, pode verificar um VM saudável.

#### <a name="dhcp-client-service-is-disabled"></a>O serviço de cliente DHCP está desativado

1. Restaurar o serviço ao seu valor de arranque predefinido:

   ```
   sc config DHCP start= auto
   ```

2. Iniciar o serviço:

   ```
   sc start DHCP
   ```

3. Consultar novamente o estado de serviço para se certificar de que está a funcionar:

   ```
   sc query DHCP
   ```

4. Tente ligar-se ao VM utilizando o Ambiente de Trabalho Remoto.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>Serviço de cliente DHCP falha devido a falha de logon

1. Como este problema ocorre se a conta de arranque deste serviço foi alterada, reverte a conta para o seu estado de incumprimento:

    ```console
    sc config DHCP obj= 'NT Authority\Localservice'
    ```

2. Iniciar o serviço:

    ```console
    sc start DHCP
    ```

3. Tente ligar-se ao VM utilizando o Ambiente de Trabalho Remoto.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>Serviço de cliente DHCP falha ou pendura

1. Se o estado de serviço estiver preso no estado **de Arranque** ou **Paragem,** tente parar o serviço:

    ```console
    sc stop DHCP
    ```

2. Isolar o serviço no seu próprio recipiente 'svchost':

    ```console
    sc config DHCP type= own
    ```

3. Iniciar o serviço:

    ```console
    sc start DHCP
    ```

4. Se o serviço ainda não arrancar, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Reparar o VM offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Ligue o disco de SO a um VM de recuperação

1. [Fixe o disco DE A uma VM de recuperação](./troubleshoot-recovery-disks-portal-windows.md).
2. Inicie uma ligação de ambiente de trabalho remoto ao VM de recuperação. Certifique-se de que o disco anexo está sinalizado como **Online** na consola de Gestão de Discos. Note a letra de unidade que é atribuída ao disco de so em anexo.
3.  Abra uma instância de ordem elevada **(Executar como administrador**). Em seguida, executar o seguinte script. Este script pressupõe que a letra de unidade que é atribuída ao disco de SO anexado é **F**. Substitua a letra conforme apropriado pelo valor do seu VM.

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

4. [Retire o disco de SO e recrie o VM](./troubleshoot-recovery-disks-portal-windows.md). Então verifique se o problema está resolvido.

## <a name="next-steps"></a>Passos seguintes

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema.
