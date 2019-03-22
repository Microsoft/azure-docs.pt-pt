---
title: Não é a partir de serviços de ambiente de trabalho remoto numa VM do Azure | Documentos da Microsoft
description: Saiba como resolver problemas com os serviços de ambiente de trabalho remoto quando se liga a uma máquina virtual | Documentos da Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 8dc3dcbe3a84a0c35c1e3fc6e367c63393bebb70
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58003136"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Não é a partir de serviços de ambiente de trabalho remoto numa VM do Azure

Este artigo descreve como resolver problemas quando se liga a uma máquina virtual do Azure (VM) e os serviços de ambiente de trabalho remoto ou TermService, não está a iniciar ou não consegue iniciar.

> [!NOTE]  
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [O Azure Resource Manager e clássica](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo descreve com o modelo de implementação do Resource Manager. Recomendamos que utilize este modelo para novas implementações em vez do modelo de implementação clássica.

## <a name="symptoms"></a>Sintomas

Quando tentar ligar a uma VM, ocorrer os seguintes cenários:

- A captura de ecrã da VM mostra que o sistema operacional está totalmente carregado e esperar até que as credenciais.

    ![Captura de ecrã do Estado da VM](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Ver remotamente os registos de eventos na VM com o Visualizador de eventos. Verá que os serviços de ambiente de trabalho remoto, TermService, não está a iniciar ou não conseguir iniciar. O registo seguinte é um exemplo:

    **Nome de registo**:      Sistema </br>
    **origem**:        Gestor de controlo de serviço </br>
    **Data**:          12/16/2017 11:19:36 AM</br>
    **ID de evento**:      7022</br>
    **Categoria de tarefas**: Nenhuma</br>
    **Nível**:         Erro</br>
    **Palavras-chave**:      Clássica</br>
    **Utilizador**:          N/A</br>
    **Computador**: vm.contoso.com</br>
    **Descrição**: O serviço de serviços de ambiente de trabalho remoto suspenso sobre como iniciar. 

    Também pode utilizar a funcionalidade de consola de acesso de série para procurar por estes erros ao executar a seguinte consulta: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Causa
 
Este problema ocorre porque os serviços de ambiente de trabalho remoto não está em execução na VM. A causa pode depender dos seguintes cenários: 

- O serviço de TermService está definido como **desativado**. 
- O serviço de TermService está a falhar ou desligar-se. 
- O TermService não está a iniciar devido uma configuração incorreta.

## <a name="solution"></a>Solução

Para resolver este problema, utilize a consola de série. Ou outro [Repare a VM offline](#repair-the-vm-offline) ao anexar o disco de SO da VM para uma VM de recuperação.

### <a name="use-serial-console"></a>Utilizar a consola de série

1. Acesso a [consola de série](serial-console-windows.md) ao selecionar **suporte e resolução de problemas** > **consola de série**. Se a funcionalidade está ativada na VM, pode ligar a VM com êxito.

2. Crie um novo canal para uma instância CMD. Introduza **CMD** para iniciar o canal e obter o nome do canal.

3. Mude para o canal que executa a instância CMD. Neste caso, deve ser canal 1:

   ```
   ch -si 1
   ```

4. Selecione **Enter** novamente e introduza um nome de utilizador válido e o ID de palavra-passe, local ou num domínio, para a VM.

5. Consulte o estado do serviço TermService:

   ```
   sc query TermService
   ```

6. Se o estado do serviço mostra **parado**, tente iniciar o serviço:

    ```
    sc start TermService
     ``` 

7. Consulte o serviço novamente para certificar-se de que o serviço é iniciado com êxito:

   ```
   sc query TermService
   ```
8. Se o serviço não iniciar, siga a solução com base no erro que recebeu:

    |  Erro |  Sugestão |
    |---|---|
    |5 - ACESSO NEGADO |Ver [TermService serviço está parado devido a um erro de acesso negado](#termService-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |Ver [TermService serviço for desativado](#termService-service-is-disabled).  |  
    |1058 - ERROR_SERVICE_DISABLED  |Ver [TermService serviço falhas ou paradas](#termService-service-crashes-or-hangs).  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[Contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.|
    |1067 - ERROR_PROCESS_ABORTED  |Ver [TermService serviço falhas ou paradas](#termService-service-crashes-or-hangs).  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |Consulte [TermService serviço falha devido a falha de início de sessão](#termService-service-fails-because-of-logon-failure) |
    |1070 - ERROR_SERVICE_START_HANG   | Ver [TermService serviço falhas ou paradas](#termService-service-crashes-or-hangs). |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Ver [TermService serviço for desativado](#termService-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente. |
    |1753   |[Contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>O serviço de TermService está parado devido a um problema de acesso negado

1. Ligar à [consola de série](serial-console-windows.md) e abra uma instância do PowerShell.
2. Transferir a ferramenta de Monitor do processo executando o seguinte script:

   ```
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

4. Reproduza o problema ao iniciar o serviço que está a dar **acesso negado**: 

   ```
   sc start TermService 
   ```

   Quando ocorre uma falha, encerrar o rastreio de Monitor do processo:

   ```   
   procmon /Terminate 
   ```

5. Recolher o arquivo **c:\temp\ProcMonTrace.PML**:

    1. [Anexar um disco de dados para a VM](../windows/attach-managed-disk-portal.md
).
    2. Utilize a consola de série, pode copiar o ficheiro para o novo disco. Por exemplo, `copy C:\temp\ProcMonTrace.PML F:\`. Neste comando, F é a letra de unidade do disco de dados anexados.
    3. Desligar a unidade de dados e anexá-lo num VM que tenha ubstakke de Monitor do processo instalado em funcionamento.

6. Open **ProcMonTrace.PML** com o Monitor de processo a VM em funcionamento. Em seguida, filtrar por **resultado é o acesso NEGADO**, conforme mostrado na captura de ecrã seguinte:

    ![Filtrar por resultado no Monitor do processo](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Corrigi as chaves do Registro, pastas ou ficheiros que estão na saída. Normalmente, esse problema é causado quando a conta de início de sessão que é utilizada no serviço não tem permissão de ACL de acesso esses objetos. Para saber a permissão de ACL correta para a conta de início de sessão, pode verificar uma VM em bom estado. 

#### <a name="termservice-service-is-disabled"></a>O serviço TermService está desativado

1. Restaure o serviço para o valor de arranque predefinido:

   ```
   sc config TermService start= demand 
   ```

2. Inicie o serviço:

   ```
   sc start TermService
   ```

3. Consulte o seu estado novamente para se certificar de que o serviço está em execução:

   ```
   sc query TermService 
   ```

4. Tente ligar à VM com o ambiente de trabalho remoto.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>Serviço de TermService falhar devido a falha de início de sessão

1. Este problema ocorre se a conta de arranque deste serviço tiver sido alterada. Alterado voltar para a predefinição: 

        sc config TermService obj= 'NT Authority\NetworkService'
2. Inicie o serviço:

        sc start TermService
3. Tente ligar à VM com o ambiente de trabalho remoto.

#### <a name="termservice-service-crashes-or-hangs"></a>TermService serviço falhas ou travamentos
1. Se o estado do serviço fica preso em **inicial** ou **parar**, em seguida, tente parar o serviço: 

        sc stop TermService
2. Isole o serviço no seu próprio contentor "svchost":

        sc config TermService type= own
3. Inicie o serviço:

        sc start TermService
4. Se o serviço ainda está a falhar começar, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Repare a VM offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco do SO a uma VM de recuperação

1. [Anexar o disco do SO a uma VM de recuperação](../windows/troubleshoot-recovery-disks-portal.md).
2. Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação. Certifique-se de que o disco ligado é sinalizado de forma **Online** no console de gerenciamento de disco. Tenha em atenção a letra de unidade que está atribuída ao disco do SO anexado.
3. Abra uma instância de linha de comandos elevada (**executar como administrador**). Em seguida, execute o seguinte script. Partimos do princípio de que é a letra de unidade que está atribuída ao disco do SO anexado **F**. Substitua-o com o valor apropriado na sua VM. 

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
   REM Set default values back on the broken service 
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
   ```

4. [Desanexar o disco do SO e recriar a VM](../windows/troubleshoot-recovery-disks-portal.md). Em seguida, verifique se o problema foi resolvido.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, ainda [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema.
