---
title: Apoiar o estado do sistema Windows para o Azure
description: Saiba como fazer o back up do estado do sistema dos computadores windows server para o Azure.
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: 1b3573d757d2f7b1ffec9ae718aa791488960f3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91332899"
---
# <a name="back-up-windows-system-state-to-azure"></a>Apoiar o estado do sistema Windows para o Azure

Este artigo explica como fazer o back up do seu sistema Windows Server para OZure. Pretende-se que te faça passar pelo básico.

Se pretender saber mais sobre o Backup do Azure, leia esta [descrição geral](backup-overview.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) que lhe permite aceder a qualquer serviço do Azure.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy-for-the-vault"></a>Definir a redundância de armazenamento do cofre

Ao criar um cofre dos Serviços de Recuperação, certifique-se de que a redundância de armazenamento está configurada conforme pretende.

1. Do painel de cofres dos **Serviços de Recuperação,** selecione o novo cofre.

    ![Selecione o novo cofre da lista de cofres dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Quando seleciona o cofre, o painel de **abóbada dos Serviços de Recuperação** estreita-se e o painel de Definições (*que tem o nome do cofre na parte superior)* e o painel de detalhes do cofre abrem-se.

    ![Ver a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. No painel de Definições do novo cofre, utilize o slide vertical para deslocar para baixo para a secção 'Gerir' e selecione **Infraestrutura de backup**.
    O painel de infraestruturas de reserva abre.
3. No painel de infraestruturas de cópia de segurança, selecione **a configuração de backup** para abrir o painel de **configuração de backup.**

    ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Escolha a opção de replicação de armazenamento adequada para o cofre.

    ![Escolhas de configuração de armazenamento](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration-for-vault.png)

    Por predefinição, o seu cofre tem um armazenamento georredundante. Se utilizar o Azure como um ponto final de armazenamento de cópia de segurança primário, continue a utilizar o **Georredundante**. Se não utilizar o Azure como um ponto final do armazenamento de cópia de segurança primário, escolha **Localmente redundante**, o que reduz os custos de armazenamento do Azure. Leia mais sobre opções de armazenamento [geo-redundantes,](../storage/common/storage-redundancy.md#geo-redundant-storage) [redundantes locais](../storage/common/storage-redundancy.md#locally-redundant-storage) e [redundantes](../storage/common/storage-redundancy.md#zone-redundant-storage) nesta [visão geral de redundância de armazenamento](../storage/common/storage-redundancy.md).

Agora que criaste um cofre, configura-o para apoiar o Windows System State.

## <a name="configure-the-vault"></a>Configurar o cofre

1. No painel de abóbada dos Serviços de Recuperação (para o cofre que acabou de criar), na secção Iniciar, selecione **Backup**, em seguida, no painel **"Começar com Cópia de Segurança",** selecione **o objetivo backup**.

    ![Abrir as definições de cópia de segurança](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    O painel de reserva do Goal abre.a ver com o objetivo de **reserva.**

    ![Abra o painel de objetivos de backup](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. No menu pendente **Onde está a carga de trabalho em execução?**, selecione **No local**.

    Escolhes **As instalações** porque o teu Windows Server ou computador Windows é uma máquina física que não está no Azure.

3. A partir do **menu O que pretende fazer?** Menu, selecione System **State**, e selecione **OK**.

    ![Configurar ficheiros e pastas](./media/backup-azure-system-state/backup-goal-system-state.png)

    Depois de selecionar **OK,** aparece uma marca de verificação ao lado **do objetivo Backup**, e o painel de **infraestrutura** Prepare-se.

    ![Objetivo de cópia de segurança configurado, em seguida, preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. No painel **de infraestruturas Prepare,** selecione **Agente de Descarregamento para Windows Server ou Cliente Do Windows**.

    ![Preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Se estiver a utilizar o Windows Server Essential, então opte por descarregar o agente para Windows Server Essential. Um menu de pop-up pede-lhe para executar ou guardar MARSAgentInstaller.exe.

    ![Caixa de diálogo MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. No menu pop-up de descarregamento, **selecione Save**.

    Por predefinição, o ficheiro **MARSagentinstaller.exe** é guardado na pasta Transferências. Quando o instalador estiver concluído, verá um pop-up a perguntar se pretende executar o instalador ou abrir a pasta.

    ![O instalador MARS está completo](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Ainda não tem de instalar o agente. Pode instalar o agente depois de ter descarregado as credenciais do cofre.

6. No painel **de infraestruturas** Prepare,selecione **Baixar**.

    ![transferir as credenciais do cofre](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    As credenciais do cofre descarregam para a sua pasta **Downloads.** Depois de as credenciais do cofre terminarem de ser descarregadas, verá um pop-up a perguntar se quer abrir ou guardar as credenciais. Selecione **Guardar**. Se selecionar acidentalmente **Abrir,** deixe o diálogo que tenta abrir as credenciais do cofre, falhe. Não conseguirá abrir as credenciais do cofre. Continue para o próximo passo. As credenciais do cofre estão na pasta **Downloads.**

    ![as credenciais do cofre terminaram de ser transferidas](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > As credenciais do cofre devem ser guardadas apenas para um local local para o Servidor do Windows no qual pretende utilizar o agente.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Instalar e registar o agente

> [!NOTE]
> A ajuda de cópias de segurança através do portal Azure não está disponível. Utilize o Agente de Serviços de Recuperação do Microsoft Azure para fazer o sistema do Windows Server State.
>

1. Localize e faça duplo clique em **MARSagentinstaller.exe** na pasta Transferências (ou noutra localização guardada).

    O instalador fornece uma série de mensagens à medida que extrai, instala e regista o agente dos Serviços de Recuperação.

    ![executar as credenciais de instalador do agente dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Conclua o Assistente de Configuração do Agente dos Serviços de Recuperação do Microsoft Azure. Para concluir o assistente, tem de:

   * Selecionar uma localização para a pasta cache e da instalação.
   * Fornecer as informações do servidor proxy se utilizar um servidor proxy para estabelecer ligação à Internet.
   * Forneça os detalhes do seu nome de utilizador e palavra-passe se utilizar um proxy autenticado.
   * Fornecer as credenciais do cofre transferidas
   * Guarde a frase de acesso de encriptação numa localização segura.

     > [!NOTE]
     > Se perder ou esquecer a palavra-passe, a Microsoft não pode ajudar a recuperar os dados de backup. Guarde o ficheiro numa localização segura. É necessário restaurar uma reserva.
     >
     >

O agente está agora instalado e a máquina está registada no cofre. Está pronto para configurar e agendar a cópia de segurança.

## <a name="back-up-windows-server-system-state"></a>Fazer cópia de segurança do Estado do Sistema Windows Server

A cópia de segurança inicial inclui duas tarefas:

* Agendar a cópia de segurança
* Back up System State pela primeira vez

Para concluir a cópia de segurança inicial, utilize o agente dos Serviços de Recuperação do Microsoft Azure.

> [!NOTE]
> Pode fazer o back up System State no Windows Server 2008 R2 através do Windows Server 2016. O sistema de back-up não é suportado em SKUs clientes. O Sistema State não é apresentado como uma opção para clientes Windows ou máquinas SP2 do Windows Server 2008.
>
>

### <a name="to-schedule-the-backup-job"></a>Para agendar a tarefa de cópia de segurança

1. Abra o agente dos Serviços de Recuperação do Microsoft Azure. Pode encontrá-lo ao pesquisar na máquina por **Cópia de Segurança do Microsoft Azure**.

    ![Iniciar o agente dos Serviços de Recuperação do Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. No agente serviços de recuperação, selecione **Agendar Backup**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Na página **'Iniciar'** do Assistente de Cópia de Segurança da Agenda, selecione **Seguinte**.

4. Na **página 'Selecionar Itens' para cópia de segurança,** selecione **Adicionar Itens**.

5. Selecione **o Estado do Sistema** e, em seguida, selecione **OK**.

6. Selecione **Seguinte**.

7. Selecione a frequência de backup necessária e a política de retenção para as cópias de segurança do Estado do Sistema nas páginas seguintes.

8. Na página Confirmação, reveja as informações e, em seguida, **selecione Terminar**.

9. Depois de o assistente terminar de criar o horário de backup, selecione **Close**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Para fazer o back up do Sistema do Servidor do Windows pela primeira vez

1. Certifique-se de que não existem atualizações pendentes para o Windows Server que exijam um reboot.

2. No agente Serviços de Recuperação, selecione **Back Up Now** para completar a sementeira inicial sobre a rede.

    ![Back-up do Windows Server agora](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Selecione **o Estado do Sistema** no ecrã de **itens de cópia de segurança** selecionado que aparece e selecione **Seguinte**.

4. Na página de Confirmação, reveja as definições que o Assistente Efetuar Cópia de Segurança Agora irá utilizar para efetuar uma cópia de segurança da máquina. Em seguida, selecione **Back Up**.

5. Selecione **Perto** para fechar o assistente. Se fechar este assistente antes de o processo de cópia de segurança estar concluído, o assistente continua a ser executado em segundo plano.
    > [!NOTE]
    > O Agente MARS dispara `SFC /verifyonly` como parte dos pré-controlos antes de cada cópia de segurança do estado do sistema. Isto é para garantir que os ficheiros com sistemas com a versão como parte do Sistema têm as versões corretas correspondentes à versão do Windows. Saiba mais sobre o System File Checker (SFC) [neste artigo](/windows-server/administration/windows-commands/sfc).
    >

Depois de concluída a cópia de segurança inicial, o estado **Tarefa concluída** é apresentado na consola de Cópia de Segurança.

  ![IV concluídos](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Perguntas?

Se tiver dúvidas, [envie-nos feedback.](https://feedback.azure.com/forums/258995-azure-backup)

## <a name="next-steps"></a>Passos seguintes

* Obtenha mais detalhes sobre como [efetuar a cópia de segurança das máquinas Windows](backup-windows-with-mars-agent.md).
* Agora que fez o back up do seu Estado do Sistema do Servidor do Windows, pode [gerir os seus cofres e servidores](backup-azure-manage-windows-server.md).
* Se precisar de restaurar uma cópia de segurança, utilize este artigo para [restaurar ficheiros para uma máquina Windows](backup-azure-restore-windows-server.md).
