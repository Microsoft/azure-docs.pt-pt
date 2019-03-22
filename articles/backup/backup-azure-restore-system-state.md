---
title: 'Cópia de segurança do Azure: Restaurar estado do sistema para um servidor do Windows'
description: Passo a passo obter uma explicação sobre restauração de estado do sistema do Windows Server a partir de uma cópia de segurança no Azure.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 8/18/2017
ms.author: saurse
ms.openlocfilehash: 031b3096d919eb3faadf907f0d30e15d8d8fb5e5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122965"
---
# <a name="restore-system-state-to-windows-server"></a>Restaurar estado do sistema para o Windows Server

Este artigo explica como restaurar cópias de segurança do Estado do sistema do Windows Server a partir de um cofre de serviços de recuperação do Azure. Para restaurar o estado do sistema, tem de ter uma cópia de segurança do Estado do sistema (criado com as instruções em [cópia de segurança do Estado do sistema](backup-azure-system-state.md#back-up-windows-server-system-state)e certifique-se de que instalou o [versão mais recente dos serviços de recuperação do Microsoft Azure (MARS) agente](https://aka.ms/azurebackup_agent). Recuperar dados de estado do sistema do Windows Server a partir de um cofre dos serviços de recuperação do Azure é um processo de dois passos:

1. Restaure estado do sistema como ficheiros de cópia de segurança do Azure. Ao restaurar o estado do sistema como ficheiros de cópia de segurança do Azure, pode:
   * Estado do sistema restauro para o mesmo servidor em que foram executadas as cópias de segurança, ou
   * Ficheiro de estado do sistema de restauro para um servidor alternativo.

2. Aplicam-se os ficheiros de estado do sistema restaurados para um servidor Windows.


## <a name="recover-system-state-files-to-the-same-server"></a>Ficheiros de estado do sistema de recuperação para o mesmo servidor
Os seguintes passos explicam como reverter a configuração do Windows Server para um estado anterior. A reverter a configuração do servidor para um estado conhecido e estável, pode ser extremamente valioso. Os passos seguintes restauram o estado do sistema do servidor de um cofre dos serviços de recuperação. 

1. Abra o snap-in **Microsoft Azure Backup**. Se não souber qual o snap-in foi instalado, procure o computador ou servidor para **Microsoft Azure Backup**.

    A aplicação de ambiente de trabalho deve aparecer nos resultados da pesquisa.

2. Clique em **recuperar dados** para iniciar o assistente.

    ![Recuperar dados](./media/backup-azure-restore-windows-server/recover.png)

3. Sobre o **introdução** painel, para restaurar os dados no mesmo servidor ou computador, selecione **neste servidor (`<server name>`)** e clique em **seguinte**.

    ![Escolha esta opção de servidor para restaurar os dados na mesma máquina](./media/backup-azure-restore-system-state/samemachine.png)

4. Sobre o **selecionar modo de recuperação** painel, escolha **estado do sistema** e, em seguida, clique em **seguinte**.

    ![Procurar ficheiros](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. No calendário na **selecionar Volume e data** painel, selecione uma recuperação do ponto. 

    Pode restaurar a partir de qualquer ponto de recuperação no tempo. As datas em **negrito** indicam a disponibilidade de, pelo menos, um ponto de recuperação. Depois de selecionar uma data, se vários pontos de recuperação disponíveis, selecione o ponto de recuperação específica a partir da **tempo** menu pendente.

    ![Data e de volume](./media/backup-azure-restore-system-state/select-date.png)

6. Depois de escolher o ponto de recuperação para restaurar, clique em **seguinte**.

    O Azure Backup monta o ponto de recuperação local e o usa como um volume de recuperação.

7. No painel seguinte, especifique o destino para os ficheiros recuperados do Estado do sistema e clique em **procurar** para abrir o Explorador do Windows e encontrar os ficheiros e pastas que pretende. A opção **criam cópias para que tenha ambas as versões**, cria cópias dos ficheiros individuais num arquivo de ficheiros de estado do sistema existente em vez de criar a cópia do arquivo de estado do sistema inteiro.

    ![Opções de recuperação](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Verifique os detalhes de recuperação no **confirmação** painel e clique em **recuperar**.

   ![Clique em recuperar para confirmar a ação de recuperação](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Copiar o *WindowsImageBackup* diretório no destino de recuperação para um volume não-críticas do servidor. Normalmente, o volume do SO do Windows é o volume crítico.

10. Assim que a recuperação for concluída com êxito, siga os passos na secção, [aplicar restaurou ficheiros de estado do sistema para o Windows Server](backup-azure-restore-system-state.md), para concluir o processo de recuperação do Estado do sistema.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Ficheiros de estado do sistema de recuperação para um servidor alternativo

Se o Windows Server está danificada ou inacessível e que pretende restaurá-lo para um estado estável por meio da recuperação do Estado do sistema do Windows Server, pode restaurar estado do sistema do servidor danificado de outro servidor. Utilize os seguintes passos para o restauro do Estado do sistema num servidor separado.  

Inclui a terminologia usada nestes passos:

- *Máquina de origem* – a máquina original a partir da cópia de segurança e que está atualmente indisponível.
- *Máquina de destino* – a máquina para que os dados são recuperados.
- *Cofre de exemplo* – o Cofre dos serviços de recuperação a para o qual o *máquina de origem* e *máquina de destino* estão registados. <br/>

> [!NOTE]
> As cópias de segurança obtidas a partir de uma máquina não não possível restaurar um computador que executa uma versão anterior do sistema operativo. Por exemplo, as cópias de segurança criadas a partir de uma máquina do Windows Server 2016 não não possível restaurar para o Windows Server 2012 R2. No entanto, o inverso é possível. Pode utilizar cópias de segurança do Windows Server 2012 R2 para restaurar o Windows Server 2016.
>

1. Abra o **Microsoft Azure Backup** snap-in no *máquina de destino*.
2. Certifique-se de que o *máquina de destino* e o *máquina de origem* estão registados no mesmo cofre dos serviços de recuperação.
3. Clique em **recuperar dados** para iniciar o fluxo de trabalho.
4. Selecione **outro servidor**

    ![Outro servidor](./media/backup-azure-restore-system-state/anotherserver.png)

5. Disponibilize o ficheiro de credenciais de cofre que corresponde da *cofre exemplo*. Se o ficheiro de credenciais do Cofre é inválido (ou expirou), transfira um novo ficheiro de credenciais do cofre do *cofre exemplo* no portal do Azure. Depois do ficheiro de credenciais do Cofre é fornecido, o Cofre de serviços de recuperação associado com o ficheiro de credenciais do Cofre é apresentado.

6. No painel de selecionar o servidor de cópia de segurança, selecione o *máquina de origem* na lista de máquinas apresentadas.
7. No painel selecionar modo de recuperação, selecione **estado do sistema** e clique em **próxima**. 

    ![Pesquisa](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. No calendário na **selecionar Volume e data** painel, selecione uma recuperação do ponto. Pode restaurar a partir de qualquer ponto de recuperação no tempo. As datas em **negrito** indicam a disponibilidade de, pelo menos, um ponto de recuperação. Depois de selecionar uma data, se vários pontos de recuperação disponíveis, selecione o ponto de recuperação específica a partir da **tempo** menu pendente. 

    ![Procurar itens](./media/backup-azure-restore-system-state/select-date.png)

9. Depois de escolher o ponto de recuperação para restaurar, clique em **seguinte**.

10. Sobre o **selecionar modo de recuperação de estado do sistema** painel, especifique o destino onde pretende que os ficheiros de estado do sistema para ser recuperada, em seguida, clique em **próxima**.

    ![Encriptação](./media/backup-azure-restore-system-state/recover-as-files.png)

    A opção **criam cópias para que tenha ambas as versões**, cria cópias dos ficheiros individuais num arquivo de ficheiros de estado do sistema existente em vez de criar a cópia do arquivo de estado do sistema inteiro.

11. Verifique os detalhes de recuperação no painel de confirmação e clique em **recuperar**. 

    ![Clique no botão de recuperação para confirmar o processo de recuperação](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Copiar o *WindowsImageBackup* diretório para um volume não-críticas do servidor (por exemplo d:\). Normalmente, o volume do SO do Windows é o volume crítico.

13. Para concluir o processo de recuperação, utilize a secção seguinte para [aplicam-se os ficheiros de estado do sistema restaurados num servidor Windows](backup-azure-restore-system-state.md#apply-restored-system-state-on-a-windows-server).




## <a name="apply-restored-system-state-on-a-windows-server"></a>Aplicar o estado do sistema restaurado num servidor do Windows

Uma vez tiver recuperado o estado do sistema como arquivos usando o agente de serviços de recuperação do Azure, utilize o utilitário de cópia de segurança do Windows Server para aplicar o estado do sistema recuperados para o Windows Server. O utilitário de cópia de segurança do Windows Server já está disponível no servidor. Os passos seguintes explicam como aplicar o estado do sistema recuperados.

1. Utilize os seguintes comandos para reiniciar o seu servidor no computador *modo de reparação de serviços de diretório*. Na linha de comandos elevada:

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. Após a reinicialização, abra o snap-in cópia de segurança do Windows Server. Se não souber qual o snap-in foi instalado, procure o computador ou servidor para **cópia de segurança do Windows Server**.

    A aplicação de ambiente de trabalho é apresentada nos resultados da pesquisa.

3. No snap-in, selecione **cópia de segurança Local**.

    ![Selecionar cópia de segurança Local para restaurar a partir daí](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. Na consola de cópia de segurança Local, na **painel ações**, clique em **recuperar** para abrir o Assistente de recuperação.

5. Selecione a opção **uma cópia de segurança armazenada noutra localização**e clique em **próxima**.

   ![optar por recuperar para um servidor diferente](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Ao especificar o tipo de localização, selecione **pasta remota partilhada** se a cópia de segurança do Estado do sistema foi recuperada para outro servidor. Se o estado do sistema foi recuperado localmente, em seguida, selecione **unidades locais**. 

    ![Selecione se a recuperação do servidor local ou de outra](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Introduza o caminho para o *WindowsImageBackup* diretório, ou escolher a unidade local que contém este diretório (por exemplo, D:\WindowsImageBackup) recuperado como parte da recuperação de ficheiros de estado do sistema usando os serviços de recuperação do Azure Agente e clique em **seguinte**.

    ![caminho para o ficheiro partilhado](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Selecione a versão de estado do sistema que pretende restaurar e clique em **seguinte**.

9. No painel selecionar tipo de recuperação, selecione **estado do sistema** e clique em **próxima**.

10. Para a localização de recuperação de estado do sistema, selecione **localização Original**e clique em **próxima**.

11. Reveja os detalhes de confirmação, verifique as definições de reinício e clique em **recuperar** para aplicar os ficheiros de estado do sistema restaurados.

    ![iniciar o restauro de ficheiros de estado do sistema](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Considerações especiais sobre recuperação de estado do sistema no servidor do Active Directory

Cópia de segurança do Estado do sistema inclui dados do Active Directory. Utilize os seguintes passos para restaurar o serviço de domínio do Active Directory (AD DS) do seu estado atual para um estado anterior.

1. Reinicie o controlador de domínio no Directory Services Restore modo (DSRM).
2. Siga os passos [aqui](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx) utilizar cmdlets de cópia de segurança do Windows Server para recuperar o AD DS.


## <a name="troubleshoot-failed-system-state-restore"></a>Resolver problemas de restauro do Estado do sistema com falhas

Se o processo anterior de aplicar o estado do sistema não for concluída com êxito, utilize o ambiente de recuperação do Windows (Windows RE) para recuperar o servidor do Windows. Os passos seguintes explicam como recuperar a ganhar RE a utilizar. Utilize esta opção apenas se o servidor do Windows não arrancou normalmente após um restauro do Estado do sistema. O seguinte processo apaga os dados não pertencente ao sistema, tenha cuidado. 

1. Arranque do Windows Server no ambiente de recuperação do Windows (Win RE).

2. Selecione a resolução de problemas entre as três opções disponíveis.

    ![menu de abertura](./media/backup-azure-restore-system-state/winre-1.png)

3. Partir do **opções avançadas** ecrã, selecione **linha de comandos** e forneça o nome de utilizador de administrador de servidor e a palavra-passe.

   ![menu de abertura](./media/backup-azure-restore-system-state/winre-2.png)

4. Forneça o nome de utilizador de administrador de servidor e a palavra-passe.

    ![menu de abertura](./media/backup-azure-restore-system-state/winre-3.png)

5. Quando abrir a linha de comandos no modo de administrador, execute o seguinte comando para obter as versões de cópia de segurança do Estado do sistema.

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![Obtenha versões de cópia de segurança do Estado do sistema](./media/backup-azure-restore-system-state/winre-4.png)

6. Execute o seguinte comando para obter todos os volumes disponíveis na cópia de segurança.

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Obtenha versões de cópia de segurança do Estado do sistema](./media/backup-azure-restore-system-state/winre-5.png)

7. O seguinte comando recupera todos os volumes que fazem parte da cópia de segurança de estado do sistema. Tenha em atenção que este passo recupera apenas os volumes críticos que fazem parte do Estado do sistema. Todos os dados de sistema não é apagado.

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![Obtenha versões de cópia de segurança do Estado do sistema](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>Passos Seguintes
* Agora que recuperar ficheiros e pastas, pode [gerir as cópias de segurança](backup-azure-manage-windows-server.md).
