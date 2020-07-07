---
title: Restaurar o Estado do Sistema num Servidor windows
description: Explicação passo a passo para restaurar o Estado do Sistema do Servidor do Windows a partir de uma cópia de segurança no Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 08/18/2017
ms.openlocfilehash: 39cac84c4a33c1da209d0a0cc7b0f8ac8ee390a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82610790"
---
# <a name="restore-system-state-to-windows-server"></a>Restaurar o Estado do Sistema para o Servidor do Windows

Este artigo explica como restaurar as cópias de segurança do Windows Server System State a partir de um cofre dos Serviços de Recuperação do Azure. Para restaurar o Estado do Sistema, tem de ter uma cópia de segurança do Estado do Sistema (criada utilizando as instruções no [Estado do Sistema de Reserva](backup-azure-system-state.md#back-up-windows-server-system-state), e certifique-se de que instalou a versão mais recente do agente Microsoft [Azure Recovery Services (MARS).](https://aka.ms/azurebackup_agent) Recuperar os dados do Estado do Sistema do Servidor do Windows a partir de um cofre dos Serviços de Recuperação do Azure é um processo em duas etapas:

1. Restaurar o Estado do Sistema como ficheiros da Azure Backup. Ao restaurar o Estado do Sistema como ficheiros da Azure Backup, pode:
   * Restaurar o Estado do Sistema para o mesmo servidor onde as cópias de segurança foram tomadas, ou
   * Restaurar o ficheiro do Estado do Sistema para um servidor alternativo.

2. Aplique os ficheiros do Estado do Sistema restaurado num Servidor windows.

## <a name="recover-system-state-files-to-the-same-server"></a>Recover System State files to the same server (Recuperar ficheiros de Estado do Sistema para o mesmo servidor)

Os passos seguintes explicam como reverter a configuração do Windows Server para um estado anterior. A configuração do servidor de volta a um estado conhecido e estável pode ser extremamente valiosa. Os passos seguintes restauram o Estado do Sistema do servidor a partir de um cofre dos Serviços de Recuperação.

1. Abra o snap-in **Microsoft Azure Backup**. Se não souber onde o encaixe foi instalado, procure no computador ou servidor para obter o **Microsoft Azure Backup**.

    A aplicação para desktop deve aparecer nos resultados da pesquisa.

2. Clique em **Recuperar Dados** para iniciar o assistente.

    ![Recuperar Dados](./media/backup-azure-restore-windows-server/recover.png)

3. No painel **'Iniciar-se',** para restaurar os dados no mesmo servidor ou computador, selecione **Este servidor `<server name>` ()** e clique em **Seguinte**.

    ![Escolha esta opção de servidor para restaurar os dados na mesma máquina](./media/backup-azure-restore-system-state/samemachine.png)

4. No **painel de modo de recuperação selecionado,** escolha **o Estado do Sistema** e, em seguida, clique em **Seguinte**.

    ![Procurar ficheiros](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. No calendário no **painel De Volume e Data Select,** selecione um ponto de recuperação.

    Pode restaurar a partir de qualquer ponto de recuperação no tempo. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Uma vez selecionada uma data, se estiverem disponíveis vários pontos de recuperação, escolha o ponto de recuperação específico do menu de entrega do **tempo.**

    ![Volume e Data](./media/backup-azure-restore-system-state/select-date.png)

6. Depois de ter escolhido o ponto de recuperação para restaurar, clique em **Seguinte**.

    A Azure Backup monta o ponto de recuperação local, e usa-o como um volume de recuperação.

7. No painel seguinte, especifique o destino para os **ficheiros** do Estado do Sistema recuperado e clique em navegar para abrir o Windows Explorer e encontrar os ficheiros e pastas que pretende. A opção, **Criar cópias para que tenha ambas as versões,** cria cópias de ficheiros individuais num arquivo de ficheiros do Sistema State existente em vez de criar a cópia de todo o arquivo do Estado do Sistema.

    ![Opções de recuperação](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Verifique os detalhes da recuperação no painel **de confirmação** e clique em **Recuperar**.

   ![clique em Recuperar para reconhecer a ação de recuperação](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Copie o diretório *WindowsImageBackup* no destino Recovery para um volume não crítico do servidor. Normalmente, o volume de SISTEMA do Windows é o volume crítico.

10. Assim que a recuperação for bem sucedida, siga os passos na secção, [aplique os ficheiros do Estado do Sistema restaurados no Servidor do Windows](backup-azure-restore-system-state.md), para concluir o processo de recuperação do Estado do Sistema.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Recuperar ficheiros do Estado do Sistema para um servidor alternativo

Se o seu Servidor Windows estiver corrompido ou inacessível e pretender restaurá-lo para um estado estável recuperando o Estado do Sistema do Servidor do Windows, pode restaurar o Estado do Sistema do servidor corrupto a partir de outro servidor. Utilize os seguintes passos para restaurar o Estado do Sistema num servidor separado.  

A terminologia utilizada nestes passos inclui:

* *Máquina de origem* – A máquina original a partir da qual a cópia de segurança foi recolhida e que não está disponível neste momento.
* *Máquina-alvo* – A máquina à qual os dados estão a ser recuperados.
* *Cofre de amostras* – O cofre dos Serviços de Recuperação ao qual estão registados a *máquina Source* e a *máquina Target.* <br/>

> [!NOTE]
> As cópias de segurança retiradas de uma máquina não podem ser restauradas a uma máquina que executa uma versão anterior do sistema operativo. Por exemplo, as cópias de segurança retiradas de uma máquina do Windows Server 2016 não podem ser restauradas no Windows Server 2012 R2. No entanto, o inverso é possível. Pode utilizar cópias de segurança do Windows Server 2012 R2 para restaurar o Windows Server 2016.
>

1. Abra o encaixe de backup do **Microsoft Azure** na *máquina Target*.
2. Certifique-se de que a *máquina Target* e a *máquina Source* estão registadas no mesmo cofre dos Serviços de Recuperação.
3. Clique em **Recuperar Dados** para iniciar o fluxo de trabalho.
4. Selecione **Outro servidor**

    ![Outro servidor](./media/backup-azure-restore-system-state/anotherserver.png)

5. Forneça o ficheiro de credencial do cofre que corresponde ao *cofre da amostra.* Se o ficheiro de credencial do cofre for inválido (ou expirado), descarregue um novo ficheiro de credencial de abóbada do cofre do *cofre do portal* Azure. Uma vez que o ficheiro de credencial do cofre é fornecido, o cofre dos Serviços de Recuperação associado ao ficheiro de credencial do cofre aparece.

6. No painel 'Selecionar backup', selecione a *máquina 'Fonte'* da lista de máquinas visualizadas.
7. No painel de modo de recuperação selecionado, escolha **o Estado do Sistema** e clique em **Seguinte**.

    ![Pesquisar](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. No calendário no painel **de volume e data selecionados,** selecione um ponto de recuperação. Pode restaurar a partir de qualquer ponto de recuperação no tempo. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Uma vez selecionada uma data, se estiverem disponíveis vários pontos de recuperação, escolha o ponto de recuperação específico do menu de entrega do **tempo.**

    ![Pesquisar itens](./media/backup-azure-restore-system-state/select-date.png)

9. Depois de ter escolhido o ponto de recuperação para restaurar, clique em **Seguinte**.

10. No painel **de modo de recuperação do estado do sistema select,** especifique o destino onde deseja que os ficheiros do Estado do Sistema sejam recuperados e, em seguida, clique em **Seguinte**.

    ![Encriptação](./media/backup-azure-restore-system-state/recover-as-files.png)

    A opção, **Criar cópias para que tenha ambas as versões,** cria cópias de ficheiros individuais num arquivo de ficheiros do Sistema State existente em vez de criar a cópia de todo o arquivo do Estado do Sistema.

11. Verifique os detalhes da recuperação no painel de confirmação e clique em **Recuperar**.

    ![clique no botão Recuperar para confirmar o processo de recuperação](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Copie o diretório *WindowsImageBackup* para um volume não crítico do servidor (por exemplo, D: \) . Normalmente, o volume do Windows OS é o volume crítico.

13. Para completar o processo de recuperação, utilize a seguinte secção para [aplicar os ficheiros do Estado do Sistema restaurado num Servidor do Windows](#apply-restored-system-state-on-a-windows-server).

## <a name="apply-restored-system-state-on-a-windows-server"></a>Apply restored System State on a Windows Server (Aplicar o Estado do Sistema restaurado num servidor do Windows Server)

Assim que tiver recuperado o Estado do Sistema como ficheiros que utilizam o Agente de Serviços de Recuperação do Azure, utilize o utilitário backup do Windows Server para aplicar o Estado do Sistema recuperado ao Windows Server. O utilitário de backup do Windows Server já está disponível no servidor. Os seguintes passos explicam como aplicar o Estado do Sistema recuperado.

1. Utilize os seguintes comandos para reiniciar o seu servidor no *Modo de Reparação de Serviços de Diretório*. Num pedido de comando elevado:

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

2. Após o reboot, abra o encaixe de backup do Windows Server. Se não souber onde o encaixe foi instalado, procure no computador ou servidor para obter **cópia de segurança do Servidor do Windows**.

    A aplicação de ambiente de trabalho aparece nos resultados da pesquisa. Se não aparecer, ou encontrar erros ao abrir a aplicação, tem de instalar as **Funcionalidades de Backup**do Servidor do Windows e componentes dependentes abaixo, que estão disponíveis no **'Add Features Wizard'** no **Server Manager**.

3. No snap-in, selecione **Local Backup**.

    ![selecione Backup Local para restaurar a partir daí](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. Na consola De backup local, no **Painel de Ações,** clique em **Recuperar** para abrir o Assistente de Recuperação.

5. Selecione a opção, **Uma cópia de segurança armazenada noutro local**e clique em **Seguinte**.

   ![optar por recuperar para um servidor diferente](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Ao especificar o tipo de localização, selecione **a pasta partilhada remotamente** se a cópia de segurança do Estado do Sistema tiver sido recuperada para outro servidor. Se o seu Estado do Sistema foi recuperado localmente, então selecione **unidades locais**.

    ![selecionar se para a recuperação de servidor local ou outro](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Introduza o caminho para o diretório *WindowsImageBackup,* ou escolha a unidade local que contém este diretório (por exemplo, D:\WindowsImageBackup), recuperado como parte da recuperação de ficheiros do Estado do Sistema utilizando o Agente de Serviços de Recuperação do Azure e clique **em Seguinte**.

    ![caminho para o arquivo compartilhado](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Selecione a versão 'Estado do Sistema' que pretende restaurar e clique em **Seguinte**.

9. No painel Select Recovery Type, selecione **'Estado do Sistema'** e clique em **Seguinte**.

10. Para a localização da Recuperação do Estado do Sistema, selecione **Original Location**, e clique em **Seguinte**.

11. Reveja os detalhes de confirmação, verifique as definições de reinicialização e clique em **Recuperar** para aplicar os ficheiros do Estado do Sistema restaurado.

    ![lançar os ficheiros do Estado do Sistema de restauração](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Considerações especiais para a recuperação do Estado do Sistema no servidor Ative Directory

A cópia de segurança do Estado do Sistema inclui dados do Ative Directory. Utilize os seguintes passos para restaurar o Serviço de Domínio do Diretório Ativo (DS AD) do seu estado atual para um estado anterior.

1. Reinicie o controlador de domínio no Modo de Restauro de Serviços de Diretório (DSRM).
2. Siga os passos [aqui](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-nonauthoritative-restore) para utilizar cmdlets de backup do Windows Server para recuperar DS AD.

## <a name="troubleshoot-failed-system-state-restore"></a>Resolução de problemas falhou restauro do Estado do Sistema

Se o processo anterior de aplicação do Estado do Sistema não estiver concluído com sucesso, utilize o Ambiente de Recuperação do Windows (Win RE) para recuperar o seu Servidor Windows. Os seguintes passos explicam como recuperar usando Win RE. Utilize esta opção apenas se o Windows Server não arrancar normalmente após a restauração do Estado do Sistema. O processo a seguir apaga os dados não-sistema, tenha cuidado.

1. Iniciar o seu Servidor Windows no Ambiente de Recuperação do Windows (Ganhar RE).

2. Selecione Troubleshoot das três opções disponíveis.

    ![menu de abertura](./media/backup-azure-restore-system-state/winre-1.png)

3. A partir do ecrã **de Opções Avançadas,** selecione **o Pedido de Comando** e forneça o nome de utilizador e a palavra-passe do administrador do servidor.

   ![menu de abertura](./media/backup-azure-restore-system-state/winre-2.png)

4. Forneça o nome de utilizador e a palavra-passe do administrador do servidor.

    ![menu de abertura](./media/backup-azure-restore-system-state/winre-3.png)

5. Quando abrir o pedido de comando no modo de administrador, corra seguindo o comando para obter as versões de backup do Estado do Sistema.

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![obter versões de backup do Estado do Sistema](./media/backup-azure-restore-system-state/winre-4.png)

6. Executar o seguinte comando para obter todos os volumes disponíveis na cópia de segurança.

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![obter versões de backup do Estado do Sistema](./media/backup-azure-restore-system-state/winre-5.png)

7. O comando seguinte recupera todos os volumes que fazem parte da Cópia de Segurança do Estado do Sistema. Note que este passo recupera apenas os volumes críticos que fazem parte do Estado do Sistema. Todos os dados não-sistema são apagados.

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![obter versões de backup do Estado do Sistema](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>Passos seguintes

* Agora que recuperou os seus ficheiros e pastas, pode [gerir as suas cópias de segurança.](backup-azure-manage-windows-server.md)
