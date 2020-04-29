---
title: Restaurar o Estado do Sistema num servidor windows
description: Explicação passo a passo para restaurar o Sistema de Servidores do Windows de uma cópia de segurança no Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 08/18/2017
ms.openlocfilehash: 6d46a091a4e620e26d05735f12a201009663e65d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77602463"
---
# <a name="restore-system-state-to-windows-server"></a>Restaurar o Estado do Sistema para o Servidor windows

Este artigo explica como restaurar as cópias de segurança do Windows Server System State a partir de um cofre dos Serviços de Recuperação do Azure. Para restaurar o System State, deve ter uma cópia de segurança do System State (criada utilizando as instruções no Estado de [Back up System](backup-azure-system-state.md#back-up-windows-server-system-state), e certifique-se de ter instalado a versão mais recente do agente Microsoft [Azure Recovery Services (MARS).](https://aka.ms/azurebackup_agent) Recuperar dados do Estado do Sistema do Servidor do Windows de um cofre dos Serviços de Recuperação do Azure é um processo em duas etapas:

1. Restaurar o Estado do Sistema como ficheiros da Cópia de Segurança Azure. Ao restaurar o System State como ficheiros da Cópia de Segurança Azure, pode:
   * Restaurar o Estado do Sistema para o mesmo servidor onde as cópias de segurança foram tomadas, ou
   * Restaurar o ficheiro System State para um servidor alternativo.

2. Aplique os ficheiros do System State restaurados num Servidor Windows.

## <a name="recover-system-state-files-to-the-same-server"></a>Recover System State files to the same server (Recuperar ficheiros de Estado do Sistema para o mesmo servidor)

Os seguintes passos explicam como reverter a configuração do Windows Server para um estado anterior. Relançar a configuração do servidor de volta para um estado conhecido e estável, pode ser extremamente valioso. Os seguintes passos restauram o Estado do Sistema do servidor a partir de um cofre dos Serviços de Recuperação.

1. Abra o snap-in **Microsoft Azure Backup**. Se não souber onde o snap-in foi instalado, procure no computador ou servidor o **Microsoft Azure Backup**.

    A aplicação de ambiente de trabalho deve aparecer nos resultados da pesquisa.

2. Clique em **Recuperar Dados** para iniciar o assistente.

    ![Recuperar Dados](./media/backup-azure-restore-windows-server/recover.png)

3. No painel **Getting Started,** para restaurar os dados no mesmo servidor ou computador, selecione **Este servidor (`<server name>`)** e clique em **Seguinte**.

    ![Escolha esta opção de servidor para restaurar os dados na mesma máquina](./media/backup-azure-restore-system-state/samemachine.png)

4. No painel do Modo de **Recuperação Select,** escolha o **Estado do Sistema** e, em seguida, clique em **Seguinte**.

    ![Procurar ficheiros](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. No calendário do **painel Select Volume e Data,** selecione um ponto de recuperação.

    Pode restaurar a partir de qualquer ponto de recuperação no tempo. Datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Uma vez que selecione uma data, se estiverem disponíveis vários pontos de recuperação, escolha o ponto de recuperação específico **do** menu time drop-down.

    ![Volume e Data](./media/backup-azure-restore-system-state/select-date.png)

6. Depois de ter escolhido o ponto de recuperação para restaurar, clique em **Next**.

    O Azure Backup monta o ponto de recuperação local e usa-o como volume de recuperação.

7. No painel seguinte, especifique o destino dos ficheiros do System State recuperados e clique em **Navegar** para abrir o Windows Explorer e encontrar os ficheiros e pastas que deseja. A opção, **Criar cópias para que tenha ambas as versões,** cria cópias de ficheiros individuais num arquivo de ficheiros do System State existente em vez de criar a cópia de todo o arquivo do Estado do Sistema.

    ![Opções de recuperação](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Verifique os detalhes da recuperação no painel de **confirmação** e clique em **Recuperar**.

   ![clique em Recuperar para reconhecer a ação de recuperação](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Copie o diretório *Do WindowsImageBackup* no destino Recovery para um volume não crítico do servidor. Normalmente, o volume do Windows OS é o volume crítico.

10. Assim que a recuperação for bem sucedida, siga os passos na secção, [aplique ficheiros do System State restaurados no Servidor do Windows,](backup-azure-restore-system-state.md)para completar o processo de recuperação do Estado do Sistema.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Recuperar ficheiros do Estado do Sistema para um servidor alternativo

Se o seu Servidor Windows estiver corrompido ou inacessível, e pretender restaurá-lo para um estado estável, recuperando o Estado do Sistema do Servidor windows, pode restaurar o Estado do Sistema do servidor corrompido a partir de outro servidor. Utilize os seguintes passos para restaurar o Estado do Sistema num servidor separado.  

A terminologia utilizada nestes passos inclui:

* *Máquina de origem* – A máquina original a partir da qual a cópia de segurança foi tirada e que não está atualmente disponível.
* *Máquina-alvo* – A máquina à qual os dados estão a ser recuperados.
* *Abóbada de amostra* – O cofre dos Serviços de Recuperação ao qual a *máquina Source* e a máquina *Target* estão registadas. <br/>

> [!NOTE]
> As cópias de segurança retiradas de uma máquina não podem ser restauradas a uma máquina que executa uma versão anterior do sistema operativo. Por exemplo, as cópias de segurança retiradas de uma máquina Windows Server 2016 não podem ser restauradas no Windows Server 2012 R2. No entanto, o inverso é possível. Pode utilizar cópias de segurança do Windows Server 2012 R2 para restaurar o Windows Server 2016.
>

1. Abra o encaixe de **backup do Microsoft Azure** na *máquina Target*.
2. Certifique-se de que a *máquina Target* e a *máquina Source* estão registadas no mesmo cofre dos Serviços de Recuperação.
3. Clique em **Recuperar Dados** para iniciar o fluxo de trabalho.
4. Selecione **Outro servidor**

    ![Outro Servidor](./media/backup-azure-restore-system-state/anotherserver.png)

5. Forneça o ficheiro credencial do cofre que corresponde ao *cofre da amostra.* Se o ficheiro credencial do cofre for inválido (ou expirado), descarregue um novo ficheiro credencial de cofre a partir do *cofre sample* no portal Azure. Uma vez fornecido o ficheiro credencial do cofre, aparece o cofre dos Serviços de Recuperação associado ao ficheiro credencial do cofre.

6. No painel Select Backup Server, selecione a *máquina Source* da lista de máquinas apresentadas.
7. No painel do Modo de Recuperação Select, escolha o **Estado do Sistema** e clique em **Seguinte**.

    ![Pesquisa](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. No calendário no painel **Select Volume e Data,** selecione um ponto de recuperação. Pode restaurar a partir de qualquer ponto de recuperação no tempo. Datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Uma vez que selecione uma data, se estiverem disponíveis vários pontos de recuperação, escolha o ponto de recuperação específico **do** menu time drop-down.

    ![Pesquisar itens](./media/backup-azure-restore-system-state/select-date.png)

9. Depois de ter escolhido o ponto de recuperação para restaurar, clique em **Next**.

10. No painel **select System State Recovery Mode,** especifique o destino onde pretende que os ficheiros do System State sejam recuperados e, em seguida, clique em **Seguinte**.

    ![Encriptação](./media/backup-azure-restore-system-state/recover-as-files.png)

    A opção, **Criar cópias para que tenha ambas as versões,** cria cópias de ficheiros individuais num arquivo de ficheiros do System State existente em vez de criar a cópia de todo o arquivo do Estado do Sistema.

11. Verifique os detalhes da recuperação no painel de confirmação e clique em **Recuperar**.

    ![clique no botão Recuperar para confirmar o processo de recuperação](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Copie o diretório *Do WindowsImageBackup* para um volume não\)crítico do servidor (por exemplo, D: . Normalmente, o volume do Windows OS é o volume crítico.

13. Para completar o processo de recuperação, utilize a seguinte secção para [aplicar os ficheiros do System State restaurados num Servidor windows](#apply-restored-system-state-on-a-windows-server).

## <a name="apply-restored-system-state-on-a-windows-server"></a>Apply restored System State on a Windows Server (Aplicar o Estado do Sistema restaurado num servidor do Windows Server)

Depois de ter recuperado o System State como ficheiros utilizando o Agente de Serviços de Recuperação do Azure, utilize o utilitário de backup do Windows Server para aplicar o Estado do Sistema recuperado ao Windows Server. O utilitário de backup do Servidor Windows já está disponível no servidor. Os seguintes passos explicam como aplicar o Estado de Sistema recuperado.

1. Utilize os seguintes comandos para reiniciar o seu servidor no Modo de *Reparação de Serviços de Diretório*. Num pedido de comando elevado:

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

2. Após o reboot, abra o encaixe de backup do Windows Server. Se não souber onde o snap-in foi instalado, procure no computador ou no servidor a cópia de segurança do **Windows Server**.

    A aplicação de ambiente de trabalho aparece nos resultados da pesquisa.

3. No snap-in, selecione **Backup Local**.

    ![selecione Backup Local para restaurar a partir daí](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. Na consola Local Backup, no Painel de **Ações,** clique em **Recuperar** para abrir o Assistente de Recuperação.

5. Selecione a opção, **Uma cópia de segurança armazenada noutro local,** e clique em **Next**.

   ![optar por recuperar para um servidor diferente](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Ao especificar o tipo de localização, selecione **pasta partilhada remota** se a cópia de segurança do Estado do Sistema tiver sido recuperada para outro servidor. Se o seu Estado de Sistema foi recuperado localmente, então selecione **unidades locais.**

    ![selecionar se deve recuperar do servidor local ou de outro](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Introduza o caminho para o diretório *WindowsImageBackup,* ou escolha a unidade local que contém este diretório (por exemplo, D:\WindowsImageBackup), recuperado como parte da recuperação de ficheiros do System State utilizando o Agente de Serviços de Recuperação do Azure e clique em **Next**.

    ![caminho para o arquivo partilhado](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Selecione a versão System State que pretende restaurar e clique em **Next**.

9. No painel Do Tipo de Recuperação Select, selecione **System State** e clique **em Next**.

10. Para a localização da Recuperação do Estado do Sistema, selecione **Localização Original**, e clique **em Seguinte**.

11. Reveja os detalhes de confirmação, verifique as definições de reinicialização e clique em **Recuperar** para aplicar os ficheiros do Estado do Sistema restaurados.

    ![lançar os ficheiros do Estado do Sistema de restauro](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Considerações especiais para a recuperação do Estado do Sistema no servidor de Diretório Ativo

A cópia de segurança do Estado do Sistema inclui dados do Diretório Ativo. Utilize os seguintes passos para restaurar o Ative Directory Domain Service (AD DS) do seu estado atual para um estado anterior.

1. Reiniciar o controlador de domínio no Modo de Restauro de Serviços de Diretório (DSRM).
2. Siga os passos [aqui](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-nonauthoritative-restore) para utilizar cmdlets de backup do Windows Server para recuperar DS AD.

## <a name="troubleshoot-failed-system-state-restore"></a>Problemas falharam no sistema de restauro do Estado

Se o processo anterior de aplicação do System State não estiver concluído com sucesso, utilize o Ambiente de Recuperação do Windows (Win RE) para recuperar o seu Servidor Windows. Os seguintes passos explicam como recuperar usando o Win RE. Utilize esta opção apenas se o Windows Server não arrancar normalmente após a restauração do System State. O processo seguinte apaga os dados não do sistema, tenha cuidado.

1. Insera o seu Servidor Windows no Ambiente de Recuperação do Windows (Win RE).

2. Selecione Troubleshoot a partir das três opções disponíveis.

    ![menu de abertura](./media/backup-azure-restore-system-state/winre-1.png)

3. A partir do ecrã **Opções Avançadas,** selecione **'Comando Prompt'** e forneça o nome de utilizador e palavra-passe do administrador do servidor.

   ![menu de abertura](./media/backup-azure-restore-system-state/winre-2.png)

4. Forneça o nome de utilizador e a palavra-passe do administrador do servidor.

    ![menu de abertura](./media/backup-azure-restore-system-state/winre-3.png)

5. Quando abrir o pedido de comando no modo administrador, corra seguindo o comando para obter as versões de backup do System State.

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![obter versões de backup do System State](./media/backup-azure-restore-system-state/winre-4.png)

6. Executar o seguinte comando para obter todos os volumes disponíveis na cópia de segurança.

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![obter versões de backup do System State](./media/backup-azure-restore-system-state/winre-5.png)

7. O comando seguinte recupera todos os volumes que fazem parte do Backup do Estado do Sistema. Note que este passo recupera apenas os volumes críticos que fazem parte do Estado de Sistema. Todos os dados não-sistema são apagados.

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![obter versões de backup do System State](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>Passos seguintes

* Agora que recuperou os seus ficheiros e pastas, pode [gerir as suas cópias de segurança.](backup-azure-manage-windows-server.md)
