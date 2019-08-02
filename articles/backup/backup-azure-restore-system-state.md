---
title: 'Backup do Azure: Restaurar o estado do sistema para um Windows Server'
description: Explicação passo a passo para restaurar o estado do sistema do Windows Server de um backup no Azure.
ms.reviewer: saurse
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/18/2017
ms.author: dacurwin
ms.openlocfilehash: 6dc478f569b94450921e56c05b148bae357fef8e
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689133"
---
# <a name="restore-system-state-to-windows-server"></a>Restaurar o estado do sistema para o Windows Server

Este artigo explica como restaurar backups de estado do sistema do Windows Server de um cofre dos serviços de recuperação do Azure. Para restaurar o estado do sistema, você deve ter um backup de estado do sistema (criado usando as instruções em [fazer backup do estado do sistema](backup-azure-system-state.md#back-up-windows-server-system-state)e verificar se você instalou a [versão mais recente do agente de serviços de recuperação do Microsoft Azure (MARS)](https://aka.ms/azurebackup_agent). A recuperação de dados de estado do sistema do Windows Server de um cofre dos serviços de recuperação do Azure é um processo de duas etapas:

1. Restaure o estado do sistema como arquivos do backup do Azure. Ao restaurar o estado do sistema como arquivos do backup do Azure, você pode:
   * Restaure o estado do sistema para o mesmo servidor em que os backups foram feitos, ou
   * Restaure o arquivo de estado do sistema para um servidor alternativo.

2. Aplique os arquivos de estado do sistema restaurados a um Windows Server.


## <a name="recover-system-state-files-to-the-same-server"></a>Recuperar arquivos de estado do sistema para o mesmo servidor
As etapas a seguir explicam como reverter a configuração do Windows Server para um estado anterior. Reverter a configuração do servidor para um estado conhecido e estável pode ser extremamente valioso. As etapas a seguir restauram o estado do sistema do servidor de um cofre dos serviços de recuperação.

1. Abra o snap-in **Microsoft Azure Backup**. Se você não souber onde o snap-in foi instalado, pesquise **backup do Microsoft Azure**no computador ou no servidor.

    O aplicativo de desktop deve aparecer nos resultados da pesquisa.

2. Clique em **recuperar dados** para iniciar o assistente.

    ![Recuperar dados](./media/backup-azure-restore-windows-server/recover.png)

3. No painel de **introdução** , para restaurar os dados para o mesmo servidor ou computador, selecione **este servidor (`<server name>`)** e clique em **Avançar**.

    ![Escolha esta opção de servidor para restaurar os dados no mesmo computador](./media/backup-azure-restore-system-state/samemachine.png)

4. No painel **selecionar modo de recuperação** , escolha **estado do sistema** e clique em **Avançar**.

    ![Procurar arquivos](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. No calendário, no painel **selecionar volume e data** , selecione um ponto de recuperação.

    Você pode restaurar de qualquer ponto de recuperação no tempo. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Depois de selecionar uma data, se vários pontos de recuperação estiverem disponíveis, escolha o ponto de recuperação específico no menu suspenso **hora** .

    ![Volume e data](./media/backup-azure-restore-system-state/select-date.png)

6. Depois de escolher o ponto de recuperação a ser restaurado, clique em **Avançar**.

    O backup do Azure monta o ponto de recuperação local e o usa como um volume de recuperação.

7. No próximo painel, especifique o destino para os arquivos de estado do sistema recuperados e clique em **procurar** para abrir o Windows Explorer e localizar os arquivos e pastas desejados. A opção **criar cópias para que você tenha ambas as versões**, cria cópias de arquivos individuais em um arquivo de estado do sistema existente, em vez de criar a cópia de todo o arquivo de estado do sistema.

    ![Opções de recuperação](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Verifique os detalhes da recuperação no painel de **confirmação** e clique em **recuperar**.

   ![clique em recuperar para confirmar a ação de recuperação](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Copie o diretório *WindowsImageBackup* no destino de recuperação para um volume não crítico do servidor. Normalmente, o volume do sistema operacional Windows é o volume crítico.

10. Depois que a recuperação for bem-sucedida, siga as etapas na seção [aplicar arquivos restaurados do estado do sistema ao Windows Server](backup-azure-restore-system-state.md)para concluir o processo de recuperação do estado do sistema.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Recuperar arquivos de estado do sistema para um servidor alternativo

Se o seu Windows Server estiver corrompido ou inacessível e você quiser restaurá-lo para um estado estável ao recuperar o estado do sistema do Windows Server, você poderá restaurar o estado do sistema do servidor corrompido de outro servidor. Use as etapas a seguir para restaurar o estado do sistema em um servidor separado.  

A terminologia usada nestas etapas inclui:

- *Computador de origem* – o computador original do qual o backup foi feito e que não está disponível no momento.
- *Máquina de destino* – o computador no qual os dados estão sendo recuperados.
- *Cofre de exemplo* – o cofre dos serviços de recuperação no qual o *computador de origem* e o *computador de destino* estão registrados. <br/>

> [!NOTE]
> Os backups extraídos de um computador não podem ser restaurados em um computador que executa uma versão anterior do sistema operacional. Por exemplo, os backups extraídos de um computador Windows Server 2016 não podem ser restaurados para o Windows Server 2012 R2. No entanto, o inverso é possível. Você pode usar backups do Windows Server 2012 R2 para restaurar o Windows Server 2016.
>

1. Abra o snap-in **backup do Microsoft Azure** no *computador de destino*.
2. Verifique se o *computador de destino* e o *computador de origem* estão registrados no mesmo cofre dos serviços de recuperação.
3. Clique em **recuperar dados** para iniciar o fluxo de trabalho.
4. Selecionar **outro servidor**

    ![Outro servidor](./media/backup-azure-restore-system-state/anotherserver.png)

5. Forneça o arquivo de credencial do cofre que corresponde ao *cofre de exemplo*. Se o arquivo de credencial do cofre for inválido (ou expirado), baixe um novo arquivo de credencial do cofre do *cofre de exemplo* no portal do Azure. Depois que o arquivo de credencial do cofre for fornecido, o cofre dos serviços de recuperação associado ao arquivo de credencial do cofre será exibido.

6. No painel Selecionar servidor de backup, selecione o *computador de origem* na lista de computadores exibidos.
7. No painel Selecionar modo de recuperação, escolha **estado do sistema** e clique em **Avançar**.

    ![Pesquisa](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. No calendário, no painel **selecionar volume e data** , selecione um ponto de recuperação. Você pode restaurar de qualquer ponto de recuperação no tempo. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Depois de selecionar uma data, se vários pontos de recuperação estiverem disponíveis, escolha o ponto de recuperação específico no menu suspenso **hora** .

    ![Pesquisar itens](./media/backup-azure-restore-system-state/select-date.png)

9. Depois de escolher o ponto de recuperação a ser restaurado, clique em **Avançar**.

10. No painel **selecionar modo de recuperação do estado do sistema** , especifique o destino onde você deseja que os arquivos de estado do sistema sejam recuperados e clique em **Avançar**.

    ![Encriptação](./media/backup-azure-restore-system-state/recover-as-files.png)

    A opção **criar cópias para que você tenha ambas as versões**, cria cópias de arquivos individuais em um arquivo de estado do sistema existente, em vez de criar a cópia de todo o arquivo de estado do sistema.

11. Verifique os detalhes da recuperação no painel de confirmação e clique em **recuperar**.

    ![Clique no botão recuperar para confirmar o processo de recuperação](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Copie o diretório *WindowsImageBackup* para um volume não crítico do servidor (por exemplo, D:\). Normalmente, o volume do sistema operacional Windows é o volume crítico.

13. Para concluir o processo de recuperação, use a seção a seguir para [aplicar os arquivos de estado do sistema restaurados em um Windows Server](#apply-restored-system-state-on-a-windows-server).




## <a name="apply-restored-system-state-on-a-windows-server"></a>Aplicar o estado do sistema restaurado em um Windows Server

Depois de recuperar o estado do sistema como arquivos usando o agente dos serviços de recuperação do Azure, use o utilitário Backup do Windows Server para aplicar o estado do sistema recuperado ao Windows Server. O utilitário Backup do Windows Server já está disponível no servidor. As etapas a seguir explicam como aplicar o estado do sistema recuperado.

1. Use os comandos a seguir para reinicializar o servidor no *modo de reparo dos serviços de diretório*. Em um prompt de comandos com privilégios elevados:

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. Após a reinicialização, abra o snap-in Backup do Windows Server. Se você não souber onde o snap-in foi instalado, pesquise **backup do Windows Server**no computador ou no servidor.

    O aplicativo de desktop aparece nos resultados da pesquisa.

3. No snap-in, selecione **backup local**.

    ![Selecione o backup local do qual restaurar](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. No console de backup local, no **painel Ações**, clique em **recuperar** para abrir o assistente de recuperação.

5. Selecione a opção, **um backup armazenado em outro local**e clique em **Avançar**.

   ![optar por recuperar em um servidor diferente](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Ao especificar o tipo de local, selecione **pasta compartilhada remota** se o backup de estado do sistema foi recuperado para outro servidor. Se o estado do sistema tiver sido recuperado localmente, selecione **unidades locais**.

    ![Selecione se deseja recuperar do servidor local ou de outro](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Insira o caminho para o diretório *WindowsImageBackup* ou escolha a unidade local que contém esse diretório (por exemplo, D:\WindowsImageBackup), recuperado como parte da recuperação dos arquivos de estado do sistema usando o agente dos serviços de recuperação do Azure e clique em **Avançar** .

    ![caminho para o arquivo compartilhado](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Selecione a versão do estado do sistema que você deseja restaurar e clique em **Avançar**.

9. No painel Selecionar tipo de recuperação, selecione **estado do sistema** e clique em **Avançar**.

10. Para o local da recuperação do estado do sistema, selecione **local original**e clique em **Avançar**.

11. Examine os detalhes de confirmação, verifique as configurações de reinicialização e clique em **recuperar** para aplicar os arquivos de estado do sistema restaurados.

    ![iniciar os arquivos de estado do sistema de restauração](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Considerações especiais sobre a recuperação de estado do sistema no Active Directory Server

O backup de estado do sistema inclui dados de Active Directory. Use as etapas a seguir para restaurar o serviço de Domínio do Active Directory (AD DS) de seu estado atual para um estado anterior.

1. Reinicie o controlador de domínio em Modo de Restauração dos Serviços de Diretório (DSRM).
2. Siga as etapas [aqui](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx) para usar backup do Windows Server cmdlets para recuperar AD DS.


## <a name="troubleshoot-failed-system-state-restore"></a>Solucionar problemas de restauração do estado do sistema com falha

Se o processo anterior de aplicação do estado do sistema não for concluído com êxito, use o ambiente de recuperação do Windows (Win RE) para recuperar o Windows Server. As etapas a seguir explicam como recuperar usando o Win RE. Use esta opção somente se o Windows Server não inicializar normalmente após uma restauração do estado do sistema. O processo a seguir apaga dados que não são do sistema, tome cuidado.

1. Inicialize o Windows Server no ambiente de recuperação do Windows (Win RE).

2. Selecione solucionar problemas nas três opções disponíveis.

    ![menu de abertura](./media/backup-azure-restore-system-state/winre-1.png)

3. Na tela **Opções avançadas** , selecione **prompt de comando** e forneça o nome de usuário e a senha do administrador do servidor.

   ![menu de abertura](./media/backup-azure-restore-system-state/winre-2.png)

4. Forneça o nome de usuário e a senha do administrador do servidor.

    ![menu de abertura](./media/backup-azure-restore-system-state/winre-3.png)

5. Ao abrir o prompt de comando no modo de administrador, execute o comando a seguir para obter as versões de backup do estado do sistema.

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![obter versões de backup do estado do sistema](./media/backup-azure-restore-system-state/winre-4.png)

6. Execute o comando a seguir para obter todos os volumes disponíveis no backup.

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![obter versões de backup do estado do sistema](./media/backup-azure-restore-system-state/winre-5.png)

7. O comando a seguir recupera todos os volumes que fazem parte do backup de estado do sistema. Observe que essa etapa recupera apenas os volumes críticos que fazem parte do estado do sistema. Todos os dados que não são do sistema são apagados.

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![obter versões de backup do estado do sistema](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>Passos Seguintes
* Agora que você recuperou seus arquivos e pastas, você pode [gerenciar seus backups](backup-azure-manage-windows-server.md).
