---
title: Remote Desktop Services não está a começar num Azure VM Microsoft Docs
description: Saiba como resolver problemas com serviços de desktop remotos quando se conecta a uma máquina virtual Microsoft Docs
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
ms.openlocfilehash: 0b00785fed7708986885e9da9102e8f1b4fd4539
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86508887"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Remote Desktop Services não está a começar num Azure VM

Este artigo descreve como resolver problemas quando se liga a uma máquina virtual Azure (VM) e serviços de desktop remoto, ou TermService, não está a iniciar ou falha no arranque.


## <a name="symptoms"></a>Sintomas

Quando tenta ligar-se a um VM, experimenta os seguintes cenários:

- A imagem em VM mostra que o sistema operativo está completamente carregado e à espera de credenciais.

    ![Screenshot do estado de VM](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Visualiza remotamente os registos de eventos no VM utilizando o Observador de Eventos. Vê que os Serviços remotos de Desktop, TermService, não estão a começar ou não começam. O seguinte registo é uma amostra:

    **Nome do registo**: Sistema </br>
    **Fonte**: Gestor de Controlo de Serviços </br>
    **Data**: 12/16/2017 11:19:36</br>
    **ID do evento**: 7022</br>
    **Categoria tarefa**: Nenhuma</br>
    **Nível**: Erro</br>
    **Palavras-chave**: Clássico</br>
    **Utilizador**: N/A</br>
    **Computador**: vm.contoso.com</br>
    **Descrição**: O serviço de serviços de secretária remoto suspensa no arranque. 

    Também pode utilizar a função 'Consola de Acesso série' para procurar estes erros executando a seguinte consulta: 

    ```console
   wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
    ```

## <a name="cause"></a>Causa
 
Este problema ocorre porque os Serviços de Desktop Remoto não estão a funcionar no VM. A causa pode depender dos seguintes cenários: 

- O serviço TermService está definido para **Desativado**. 
- O serviço TermService está a falhar ou a não responder. 
- O TermService não está a começar devido a uma configuração incorreta.

## <a name="solution"></a>Solução

Para resolver este problema, utilize a Consola em Série. Ou [então, reparar o VM offline](#repair-the-vm-offline) ligando o disco de so do VM a um VM de recuperação.

### <a name="use-serial-console"></a>Use consola em série

1. Aceda à [Consola em Série](serial-console-windows.md) selecionando suporte & consola em série de **resolução de**  >  **problemas**. Se a função estiver ativada no VM, pode ligar o VM com sucesso.

2. Criar um novo canal para uma instância CMD. Insira **o CMD** para iniciar o canal e obter o nome do canal.

3. Mude para o canal que executa a instância CMD. Neste caso, deve ser o canal 1:

   ```
   ch -si 1
   ```

4. Selecione **Introduza** novamente e introduza um nome de utilizador válido e senha, ID local ou de domínio, para o VM.

5. Consultar o estado do serviço TermService:

   ```
   sc query TermService
   ```

6. Se o estado de serviço mostrar **parado,** tente iniciar o serviço:

    ```
    sc start TermService
     ``` 

7. Consultar novamente o serviço para garantir que o serviço é iniciado com sucesso:

   ```
   sc query TermService
   ```
8. Se o serviço não arrancar, siga a solução com base no erro que recebeu:

    |  Erro |  Sugestão |
    |---|---|
    |5- ACESSO NEGADO |Consulte [o Serviço de Duração parado devido a um erro de Acesso Negado](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |Consulte [o serviço TermService desativado](#termservice-service-is-disabled).  |  
    |1058 - ERROR_SERVICE_DISABLED  |Consulte [o serviço TermService falha ou pendura](#termservice-service-crashes-or-hangs).  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[Suporte de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.|
    |1067 - ERROR_PROCESS_ABORTED  |Consulte [o serviço TermService falha ou pendura](#termservice-service-crashes-or-hangs).  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Suporte de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |Ver [Serviço de TermService falha devido a falha de logon](#termservice-service-fails-because-of-logon-failure) |
    |1070 - ERROR_SERVICE_START_HANG   | Consulte [o serviço TermService falha ou pendura](#termservice-service-crashes-or-hangs). |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Consulte [o serviço TermService desativado](#termservice-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Suporte de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente. |
    |1753   |[Suporte de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>O serviço TermService está parado devido a um problema de Acesso Negado

1. Ligue-se à [Consola em Série](serial-console-windows.md) e abra uma instância PowerShell.
2. Descarregue a ferramenta Monitor de Processo executando o seguinte script:

   ```
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

4. Reproduza o problema iniciando o serviço que está a dar **acesso negado:** 

   ```
   sc start TermService 
   ```

   Quando falhar, termine o rastreio do Monitor de Processo:

   ```   
   procmon /Terminate 
   ```

5. Recolha o ficheiro **c:\temp\ProcMonTrace.PML**:

    1. [Anexar um disco de dados ao VM](../windows/attach-managed-disk-portal.md
).
    2. Utilize a Consola em Série, pode copiar o ficheiro para a nova unidade. Por exemplo, `copy C:\temp\ProcMonTrace.PML F:\`. Neste comando, F é a letra do condutor do disco de dados anexado.
    3. Desprender a unidade de dados e anexá-la num VM em funcionamento que tenha o Process Monitor ubstakke instalado.

6. Abra **ProcMonTrace.PML** utilizando o Monitor de Processos o VM de funcionamento. Em seguida, o filtro por **Resultado é ACCESS DENIED**, como mostra a seguinte imagem:

    ![Filtro por resultado no Monitor de Processos](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Fixe as chaves de registo, pastas ou ficheiros que estão na saída. Normalmente, este problema é causado quando a conta de inscrição que é usada no serviço não tem permissão da ACL para aceder a estes objetos. Para saber a permissão ACL correta para a conta de entrada, você pode verificar um VM saudável. 

#### <a name="termservice-service-is-disabled"></a>O serviço TermService está desativado

1. Restaurar o serviço ao seu valor de arranque predefinido:

   ```
   sc config TermService start= demand 
   ```

2. Iniciar o serviço:

   ```
   sc start TermService
   ```

3. Consultar novamente o seu estado para se certificar de que o serviço está em execução:

   ```
   sc query TermService 
   ```

4. Tente ligar-se ao VM utilizando o Ambiente de Trabalho Remoto.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>Serviço de TermService falha devido a falha de logon

1. Este problema ocorre se a conta de arranque deste serviço for alterada. Alterou isto de volta ao seu padrão: 

    ```console
    sc config TermService obj= 'NT Authority\NetworkService'
    ```

2. Iniciar o serviço:

    ```console
    sc start TermService
    ```

3. Tente ligar-se ao VM utilizando o Ambiente de Trabalho Remoto.

#### <a name="termservice-service-crashes-or-hangs"></a>O serviço TermService falha ou pendura
1. Se o estado de serviço estiver preso em **Iniciar** ou **Parar,** tente parar o serviço: 

    ```console
    sc stop TermService
    ```

2. Isolar o serviço no seu próprio recipiente 'svchost':

    ```console
    sc config TermService type= own
    ```

3. Iniciar o serviço:

    ```console
    sc start TermService
    ```

4. Se o serviço ainda não estiver a iniciar, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Reparar o VM offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Ligue o disco de SO a um VM de recuperação

1. [Fixe o disco DE A uma VM de recuperação](./troubleshoot-recovery-disks-portal-windows.md).
2. Inicie uma ligação de ambiente de trabalho remoto ao VM de recuperação. Certifique-se de que o disco anexo está sinalizado como **Online** na consola de Gestão de Discos. Note a letra de unidade que é atribuída ao disco de so em anexo.
3. Abra uma instância de ordem elevada **(Executar como administrador**). Em seguida, executar o seguinte script. Assumimos que a letra de unidade que é atribuída ao disco de SO anexado é **F**. Substitua-o pelo valor adequado no seu VM. 

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

4. [Retire o disco de SO e recrie o VM](./troubleshoot-recovery-disks-portal-windows.md). Então verifique se a questão está resolvida.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema.
