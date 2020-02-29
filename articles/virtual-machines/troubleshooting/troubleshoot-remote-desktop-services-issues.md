---
title: Os Serviços de Ambiente de Trabalho Remoto não estão a começar num Azure VM  Microsoft Docs
description: Saiba como resolver problemas com os Serviços de Desktop Remotoquando se conecta a uma máquina virtual  Microsoft Docs
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
ms.openlocfilehash: 4b314fbdb9cbc0c0b797cbee8e92ee4702bbea81
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919469"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Os Serviços de Ambiente de Trabalho Remoto não estão a começar num VM Azure

Este artigo descreve como resolver problemas quando se conecta a uma máquina virtual Azure (VM) e serviços de ambiente de trabalho remoto, ou TermService, não está a começar ou não consegue começar.


## <a name="symptoms"></a>Sintomas

Quando tenta ligar-se a um VM, experimenta os seguintes cenários:

- A imagem vM mostra que o sistema operativo está completamente carregado e à espera de credenciais.

    ![Screenshot do estado vm](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Ver remotamente os registos de eventos na VM com o Visualizador de eventos. Você vê que os Serviços de Ambiente de Trabalho Remoto, TermService, não estão a começar ou não começam. O seguinte tronco é uma amostra:

    **Nome de registo**: Sistema </br>
    **Fonte**: Gestor de Controlo de Serviços </br>
    **Data**: 12/16/2017 11:19:36</br>
    **ID do evento**: 7022</br>
    **Categoria tarefa**: Nenhum</br>
    **Nível**: Erro</br>
    **Palavras-chave**: Clássico</br>
    **Utilizador**: N/A</br>
    **Computador**: vm.contoso.com</br>
    **Descrição**: O serviço de Serviços de Ambiente de Trabalho Remoto pendurado no arranque. 

    Também pode utilizar a funcionalidade Consola de Acesso Em Série para procurar estes erros executando a seguinte consulta: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Causa
 
Este problema ocorre porque os Serviços de Ambiente de Trabalho Remoto não estão a funcionar no VM. A causa pode depender dos seguintes cenários: 

- O serviço TermService está definido para **deficientes**. 
- O serviço TermService está a falhar ou a não responder. 
- O TermService não está a começar devido a uma configuração incorreta.

## <a name="solution"></a>Solução

Para resolver este problema, utilize a Consola em Série. Ou então [repare o VM offline](#repair-the-vm-offline) ligando o disco osso do VM a um VM de recuperação.

### <a name="use-serial-console"></a>Utilizar consola em série

1. Aceda à [Consola em Série](serial-console-windows.md) selecionando Suporte e **Resolução de Problemas** > **consola em série**. Se a funcionalidade estiver ativada no VM, pode ligar o VM com sucesso.

2. Crie um novo canal para uma instância CMD. Introduza a **CMD** para iniciar o canal e obter o nome do canal.

3. Mude para o canal que executa a instância CMD. Neste caso, deve ser o canal 1:

   ```
   ch -si 1
   ```

4. Selecione **Entrar** novamente e introduzir um nome de utilizador e senha válidos, id local ou de domínio, para o VM.

5. Consulta do estado do serviço TermService:

   ```
   sc query TermService
   ```

6. Se o estado de serviço mostrar **parado,** tente iniciar o serviço:

    ```
    sc start TermService
     ``` 

7. Consultar novamente o serviço para se certificar de que o serviço é iniciado com sucesso:

   ```
   sc query TermService
   ```
8. Se o serviço não arrancar, siga a solução com base no erro que recebeu:

    |  Erro |  Sugestão |
    |---|---|
    |5 - ACESSO NEGADO |Ver [O serviço TermService está parado devido a um erro de Acesso Negado](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |Ver [serviço TermService está desativado](#termservice-service-is-disabled).  |  
    |1058 - ERROR_SERVICE_DISABLED  |Ver [Serviço TermService falha ou fica pendurado](#termservice-service-crashes-or-hangs).  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[Suporte de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.|
    |1067 - ERROR_PROCESS_ABORTED  |Ver [Serviço TermService falha ou fica pendurado](#termservice-service-crashes-or-hangs).  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Suporte de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |Ver [Serviço TermService falha devido a falha no logon](#termservice-service-fails-because-of-logon-failure) |
    |1070 - ERROR_SERVICE_START_HANG   | Ver [Serviço TermService falha ou fica pendurado](#termservice-service-crashes-or-hangs). |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Ver [serviço TermService está desativado](#termservice-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Suporte de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente. |
    |1753   |[Suporte de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>Serviço termservice está parado devido a um problema de Acesso Negado

1. Ligue-se à [Consola em Série](serial-console-windows.md) e abra uma instância PowerShell.
2. Descarregue a ferramenta Monitor de Processo executando o seguinte script:

   ```
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

4. Reproduzir o problema iniciando o serviço que está a dar **acesso negado:** 

   ```
   sc start TermService 
   ```

   Quando ocorre uma falha, encerrar o rastreio de Monitor do processo:

   ```   
   procmon /Terminate 
   ```

5. Recolha o ficheiro **c:\temp\ProcMonTrace.PML**:

    1. Fixe um disco de [dados ao VM](../windows/attach-managed-disk-portal.md
).
    2. Utilize a consola de série, pode copiar o ficheiro para o novo disco. Por exemplo, `copy C:\temp\ProcMonTrace.PML F:\`. Neste comando, F é a letra de unidade do disco de dados anexados.
    3. Desmontar a unidade de dados e anexá-la num VM de trabalho que tenha o Monitor de Processo sutiques instalado.

6. Abra **procMonTrace.PML** utilizando o Monitor de Processo do VM de trabalho. Em seguida, o filtro por **Resultado é O ACESSO NEGADO,** como mostra a seguinte imagem:

    ![Filtrar por resultado no Monitor de Processos](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Corrigi as chaves do Registro, pastas ou ficheiros que estão na saída. Normalmente, esse problema é causado quando a conta de início de sessão que é utilizada no serviço não tem permissão de ACL de acesso esses objetos. Para saber a permissão ACL correta para a conta de entrada, pode verificar um VM saudável. 

#### <a name="termservice-service-is-disabled"></a>O serviço TermService está desativado

1. Restaure o serviço para o valor de arranque predefinido:

   ```
   sc config TermService start= demand 
   ```

2. Inicie o serviço:

   ```
   sc start TermService
   ```

3. Questione novamente o seu estado para se certificar de que o serviço está em funcionamento:

   ```
   sc query TermService 
   ```

4. Tente ligar-se à VM utilizando o Remote Desktop.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>Serviço termservice falha devido a falha no logon

1. Este problema ocorre se a conta de arranque deste serviço for alterada. Mudou isto de volta ao seu padrão: 

        sc config TermService obj= 'NT Authority\NetworkService'
2. Inicie o serviço:

        sc start TermService
3. Tente ligar-se à VM utilizando o Remote Desktop.

#### <a name="termservice-service-crashes-or-hangs"></a>Serviço termService falha ou enforca
1. Se o estado de serviço estiver preso em **Iniciar** ou **Parar,** tente parar o serviço: 

        sc stop TermService
2. Isole o serviço no seu próprio contentor "svchost":

        sc config TermService type= own
3. Inicie o serviço:

        sc start TermService
4. Se o serviço ainda não tiver iniciado, suporte de [contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Repare a VM offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco do SO a uma VM de recuperação

1. [Fixe o disco OS a um VM](../windows/troubleshoot-recovery-disks-portal.md)de recuperação .
2. Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação. Certifique-se de que o disco em anexo está sinalizado como **Online** na consola de Gestão de Discos. Tenha em atenção a letra de unidade que está atribuída ao disco do SO anexado.
3. Abra uma instância de instrução de comando elevada **(Executar como administrador).** Em seguida, execute o seguinte script. Assumimos que a carta de unidade atribuída ao disco operativo em anexo é **F**. Substitua-o pelo valor adequado no seu VM. 

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

4. [Desmontar o disco OS e recriar o VM](../windows/troubleshoot-recovery-disks-portal.md). Em seguida, verifique se a questão está resolvida.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, [contacte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) o suporte para resolver o seu problema.
