---
title: Serviços de Área de Trabalho Remota não está iniciando em uma VM do Azure | Microsoft Docs
description: Saiba como solucionar problemas com Serviços de Área de Trabalho Remota quando você se conecta a uma máquina virtual | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 9f7957fb0e6e888367c1f8ded1abfb3828697cbb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087100"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Serviços de Área de Trabalho Remota não está iniciando em uma VM do Azure

Este artigo descreve como solucionar problemas quando você se conecta a uma VM (máquina virtual) do Azure e Serviços de Área de Trabalho Remota, ou o TermService, não está iniciando ou não é iniciado.

> [!NOTE]  
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Azure Resource Manager e clássico](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo descreve como usar o modelo de implantação do Gerenciador de recursos. Recomendamos que você use esse modelo para novas implantações em vez do modelo de implantação clássico.

## <a name="symptoms"></a>Sintomas

Ao tentar se conectar a uma VM, você enfrenta os seguintes cenários:

- A captura de tela da VM mostra que o sistema operacional está totalmente carregado e aguardando credenciais.

    ![Captura de tela do status da VM](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Ver remotamente os registos de eventos na VM com o Visualizador de eventos. Você vê que o Serviços de Área de Trabalho Remota, o TermService, não está iniciando ou não é iniciado. O seguinte log é um exemplo:

    **Nome do log**:      Sistema </br>
    **Origem**:        Gerenciador de controle de serviço </br>
    **Data**:          12/16/2017 11:19:36 AM</br>
    **ID do evento**:      7022</br>
    **Categoria da tarefa**: Nenhum</br>
    **Nível**:         Erro</br>
    **Palavras-chave**:      Clássica</br>
    **Usuário**:          N/A</br>
    **Computador**: VM.contoso.com</br>
    **Descrição**: O serviço de Serviços de Área de Trabalho Remota parou ao iniciar. 

    Você também pode usar o recurso de console de acesso serial para procurar esses erros executando a seguinte consulta: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Causa
 
Esse problema ocorre porque Serviços de Área de Trabalho Remota não está em execução na VM. A causa pode depender dos seguintes cenários: 

- O serviço do TermService está definido como **desabilitado**. 
- O serviço do TermService está falhando ou não respondendo. 
- O TermService não está iniciando devido a uma configuração incorreta.

## <a name="solution"></a>Solução

Para solucionar esse problema, use o console serial. Ou então, [Repare a VM offline](#repair-the-vm-offline) anexando o disco do sistema operacional da VM a uma VM de recuperação.

### <a name="use-serial-console"></a>Usar o console serial

1. Acesse o [console serial](serial-console-windows.md) selecionando **suporte &**  > solução de problemas**console serial**. Se o recurso estiver habilitado na VM, você poderá conectar a VM com êxito.

2. Crie um novo canal para uma instância CMD. Insira **cmd** para iniciar o canal e obter o nome do canal.

3. Alterne para o canal que executa a instância CMD. Nesse caso, deve ser o canal 1:

   ```
   ch -si 1
   ```

4. Selecione **Enter** novamente e insira um nome de usuário e senha, local ou ID de domínio válidos para a VM.

5. Consulte o status do serviço do TermService:

   ```
   sc query TermService
   ```

6. Se o status do serviço mostrar **parado**, tente iniciar o serviço:

    ```
    sc start TermService
     ``` 

7. Consulte o serviço novamente para certificar-se de que o serviço foi iniciado com êxito:

   ```
   sc query TermService
   ```
8. Se o serviço não for iniciado, siga a solução com base no erro que você recebeu:

    |  Erro |  Sugestão |
    |---|---|
    |5 - ACESSO NEGADO |Consulte [o serviço do TermService foi interrompido devido a um erro de acesso negado](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |Consulte o [serviço do TermService está desabilitado](#termservice-service-is-disabled).  |  
    |1058 - ERROR_SERVICE_DISABLED  |Consulte [falhas ou suspensões do serviço do TermService](#termservice-service-crashes-or-hangs).  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[Entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema rapidamente.|
    |1067 - ERROR_PROCESS_ABORTED  |Consulte [falhas ou suspensões do serviço do TermService](#termservice-service-crashes-or-hangs).  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema rapidamente.|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |Veja falha [no serviço do TermService devido à falha de logon](#termservice-service-fails-because-of-logon-failure) |
    |1070 - ERROR_SERVICE_START_HANG   | Consulte [falhas ou suspensões do serviço do TermService](#termservice-service-crashes-or-hangs). |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Consulte o [serviço do TermService está desabilitado](#termservice-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema rapidamente. |
    |1753   |[Entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema rapidamente.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>O serviço do TermService foi interrompido devido a um problema de acesso negado

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

4. Reproduza o problema iniciando o serviço que está concedendo **acesso negado**: 

   ```
   sc start TermService 
   ```

   Quando ocorre uma falha, encerrar o rastreio de Monitor do processo:

   ```   
   procmon /Terminate 
   ```

5. Colete o arquivo **c:\temp\ProcMonTrace.PML**:

    1. [Anexar um disco de dados para a VM](../windows/attach-managed-disk-portal.md
).
    2. Utilize a consola de série, pode copiar o ficheiro para o novo disco. Por exemplo, `copy C:\temp\ProcMonTrace.PML F:\`. Neste comando, F é a letra de unidade do disco de dados anexados.
    3. Desanexe a unidade de dados e anexe-a em uma VM em funcionamento que tenha o Process Monitor ubstakke instalado.

6. Abra **ProcMonTrace. PML** usando o Process Monitor na VM de trabalho. Em seguida, filtrar por **resultado é o acesso NEGADO**, conforme mostrado na captura de ecrã seguinte:

    ![Filtrar por resultado no Monitor do processo](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Corrigi as chaves do Registro, pastas ou ficheiros que estão na saída. Normalmente, esse problema é causado quando a conta de início de sessão que é utilizada no serviço não tem permissão de ACL de acesso esses objetos. Para saber a permissão de ACL correta para a conta de entrada, você pode verificar em uma VM íntegra. 

#### <a name="termservice-service-is-disabled"></a>O serviço do TermService está desabilitado

1. Restaure o serviço para o valor de arranque predefinido:

   ```
   sc config TermService start= demand 
   ```

2. Inicie o serviço:

   ```
   sc start TermService
   ```

3. Consulte seu status novamente para verificar se o serviço está em execução:

   ```
   sc query TermService 
   ```

4. Tente se conectar à VM usando Área de Trabalho Remota.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>Falha no serviço do TermService devido à falha de logon

1. Esse problema ocorre se a conta de inicialização desse serviço tiver sido alterada. Foi alterado novamente para seu padrão: 

        sc config TermService obj= 'NT Authority\NetworkService'
2. Inicie o serviço:

        sc start TermService
3. Tente se conectar à VM usando Área de Trabalho Remota.

#### <a name="termservice-service-crashes-or-hangs"></a>Falhas ou suspensões do serviço do TermService
1. Se o status do serviço estiver preso no **início** ou na **interrupção**, tente parar o serviço: 

        sc stop TermService
2. Isole o serviço no seu próprio contentor "svchost":

        sc config TermService type= own
3. Inicie o serviço:

        sc start TermService
4. Se o serviço ainda estiver falhando no início, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Repare a VM offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco do SO a uma VM de recuperação

1. [Anexar o disco do SO a uma VM de recuperação](../windows/troubleshoot-recovery-disks-portal.md).
2. Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação. Certifique-se de que o disco ligado é sinalizado de forma **Online** no console de gerenciamento de disco. Tenha em atenção a letra de unidade que está atribuída ao disco do SO anexado.
3. Abra uma instância de linha de comandos elevada (**executar como administrador**). Em seguida, execute o seguinte script. Supomos que a letra da unidade atribuída ao disco do sistema operacional anexado é **F**. Substitua-o pelo valor apropriado em sua VM. 

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

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte

Se você ainda precisar de ajuda, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema.
