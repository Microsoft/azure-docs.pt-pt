---
title: Apoiar o estado do sistema Windows para o Azure
description: Aprenda a fazer o back up do estado do sistema dos computadores Windows Server e/ou Windows para o Azure.
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: ea38b76d9a8b7b8ccc1898ed9450177da2cb2458
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87003839"
---
# <a name="back-up-windows-system-state-to-azure"></a>Apoiar o estado do sistema Windows para o Azure

Este artigo explica como fazer o back up do seu sistema Windows Server para OZure. Pretende-se que te faça passar pelo básico.

Se pretender saber mais sobre o Backup do Azure, leia esta [descrição geral](backup-overview.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) que lhe permite aceder a qualquer serviço do Azure.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy-for-the-vault"></a>Definir a redundância de armazenamento do cofre

Ao criar um cofre dos Serviços de Recuperação, certifique-se de que a redundância de armazenamento está configurada conforme pretende.

1. No painel **Cofres dos Serviços de Recuperação**, clique em novo cofre.

    ![Selecione o novo cofre da lista de cofres dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Ao selecionar o cofre, o painel **cofre dos Serviços de Recuperação** estreita e o painel Definições (*que tem o nome do cofre na parte superior*), e o painel de detalhes do cofre abre.

    ![Ver a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. No painel de Definições do novo cofre, utilize o diapositivo vertical para deslocar para baixo para a secção Gerir e clique em **Infraestrutura de Cópia de Segurança**.
    É aberto o painel Infraestrutura de Cópia de Segurança.
3. No painel Infraestrutura de Cópia de Segurança, clique em **Configuração de Cópia de Segurança** para abrir o painel **Configuração de Cópia de Segurança**.

    ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Escolha a opção de replicação de armazenamento adequada para o cofre.

    ![opções de configuração de armazenamento](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Por predefinição, o seu cofre tem um armazenamento georredundante. Se utilizar o Azure como um ponto final de armazenamento de cópia de segurança primário, continue a utilizar o **Georredundante**. Se não utilizar o Azure como um ponto final do armazenamento de cópia de segurança primário, escolha **Localmente redundante**, o que reduz os custos de armazenamento do Azure. Leia mais sobre as opções de armazenamento [georredundante](../storage/common/storage-redundancy.md) e [localmente redundante](../storage/common/storage-redundancy.md) nesta [Descrição geral de redundância de armazenamento](../storage/common/storage-redundancy.md).

Agora que criaste um cofre, configura-o para apoiar o Windows System State.

## <a name="configure-the-vault"></a>Configurar o cofre

1. Nos painel do cofre dos Serviços de Recuperação (do cofre que acabou de criar), na secção Introdução, clique em **Cópia de Segurança** e, em seguida, no painel **Introdução à Cópia de Segurança**, selecione **Objetivo de cópia de segurança**.

    ![Painel Abrir objetivo de cópia de segurança](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    É aberto o painel **Objetivo de Cópia de Segurança**.

    ![Painel Abrir objetivo de cópia de segurança](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. No menu pendente **Onde está a carga de trabalho em execução?**, selecione **No local**.

    Seleciona **No local** porque o seu Windows Server ou computador Windows é um computador físico que não está no Azure.

3. A partir do menu O que pretende **System State** **fazer?** **OK**

    ![Configurar ficheiros e pastas](./media/backup-azure-system-state/backup-goal-system-state.png)

    Depois de clicar em OK, aparece uma marca de verificação junto a **Objetivo de cópia de segurança** e é aberto o painel **Preparar infraestrutura**.

    ![Objetivo de cópia de segurança configurado, em seguida, preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. No painel **Preparar infraestrutura**, clique em **Transferir Agente para o Windows Server ou um Cliente Windows**.

    ![preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Se estiver a utilizar o Windows Server Essential, opte por transferir o agente para o Windows Server Essential. Um menu de pop-up pede-lhe para executar ou guardar MARSAgentInstaller.exe.

    ![Caixa de diálogo MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. No menu de pop-up de transferência, clique em **Guardar**.

    Por predefinição, o ficheiro **MARSagentinstaller.exe** é guardado na pasta Transferências. Quando o instalador concluir, verá um pop-up a perguntar se quer executar o instalador ou abrir a pasta.

    ![preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Ainda não tem de instalar o agente. Pode instalar o agente depois de transferir as credenciais do cofre.

6. No painel **Preparar infraestrutura**, clique em **Transferir**.

    ![transferir as credenciais do cofre](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    As credenciais do cofre são transferidas para a pasta Transferências. Depois de as credenciais do cofre serem transferidas, verá um pop-up a perguntar se quer abrir ou guardar as credenciais. Clique em **Save** (Guardar). Se clicar acidentalmente em **Abrir**, deixe a caixa de diálogo que tenta abrir as credenciais do cofre falhar. Não é possível abrir as credenciais do cofre. Avance para o passo seguinte. As credenciais do cofre estão na pasta Transferências.

    ![as credenciais do cofre terminaram de ser transferidas](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > As credenciais do cofre devem ser guardadas apenas para um local que seja local para o Servidor do Windows no qual pretende utilizar o agente.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Instalar e registar o agente

> [!NOTE]
> A permissão da cópia de segurança através do portal do Azure ainda não está disponível. Utilize o Agente de Serviços de Recuperação do Microsoft Azure para fazer o sistema do Windows Server State.
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
     > Se perder ou se esquecer da frase de acesso, a Microsoft não o pode ajudar a recuperar os dados de cópia de segurança. Guarde o ficheiro numa localização segura. É necessário para restaurar uma cópia de segurança.
     >
     >

O agente está agora instalado e a máquina está registada no cofre. Está pronto para configurar e agendar a cópia de segurança.

## <a name="back-up-windows-server-system-state"></a>Fazer cópia de segurança do Estado do Sistema Windows Server

A cópia de segurança inicial inclui duas tarefas:

* Agendar a cópia de segurança
* Back up System State pela primeira vez

Para concluir a cópia de segurança inicial, utilize o agente dos Serviços de Recuperação do Microsoft Azure.

> [!NOTE]
> Pode fazer o back up System State no Windows Server 2008 R2 através do Windows Server 2016. O back up do Estado do Sistema não é suportado em SKUs clientes. O Estado do Sistema não é apresentado como uma opção para clientes Windows ou máquinas SP2 do Windows Server 2008.
>
>

### <a name="to-schedule-the-backup-job"></a>Para agendar a tarefa de cópia de segurança

1. Abra o agente dos Serviços de Recuperação do Microsoft Azure. Pode encontrá-lo ao pesquisar na máquina por **Cópia de Segurança do Microsoft Azure**.

    ![Iniciar o agente dos Serviços de Recuperação do Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. No agente dos Serviços de Recuperação, clique em **Agendar Cópia de Segurança**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Na página Introdução do Assistente para Agendar Cópia de Segurança, clique em **Seguinte**.

4. Na página Selecionar Itens para Cópia de Segurança, clique em **Adicionar Itens**.

5. Selecione **o Estado do Sistema** e, em seguida, clique em **OK**.

6. Clique em **Seguinte**.

7. Selecione a frequência de backup necessária e a política de retenção para as cópias de segurança do Estado do Sistema nas páginas seguintes.

8. Na página de Confirmação, reveja as informações e, em seguida, clique em **Concluir**.

9. Depois de o assistente ter criado a agenda da cópia de segurança, clique em **Fechar**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Para fazer o back up do Sistema do Servidor do Windows pela primeira vez

1. Certifique-se de que não existem atualizações pendentes para o Windows Server que exijam um reboot.

2. No agente dos Serviços de Recuperação, clique em **Efetuar Cópia de Segurança Agora** para concluir a propagação inicial através da rede.

    ![Back-up do Windows Server agora](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Selecione **o Estado do Sistema** no ecrã de artigo de **reserva** selecionado que aparece e clique em **Seguinte**.

4. Na página de Confirmação, reveja as definições que o Assistente Efetuar Cópia de Segurança Agora irá utilizar para efetuar uma cópia de segurança da máquina. Em seguida, clique em **Efetuar Cópia de Segurança**.

5. Clique em **Fechar** para fechar o assistente. Se fechar este assistente antes de o processo de cópia de segurança estar concluído, o assistente continua a ser executado em segundo plano.
    > [!NOTE]
    > O Agente MARS ativa o SFC /check-inonly como parte dos pré-verificações antes de cada cópia de segurança do estado do sistema. Isto é para garantir que os ficheiros com sistemas com a versão como parte do Sistema têm as versões corretas correspondentes à versão do Windows. Saiba mais sobre o System File Checker (SFC) [neste artigo](/windows-server/administration/windows-commands/sfc).
    >

Depois de concluída a cópia de segurança inicial, o estado **Tarefa concluída** é apresentado na consola de Cópia de Segurança.

  ![IV concluídos](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Perguntas?

Se tiver dúvidas ou se houver alguma funcionalidade que gostaria de ver incluída, [envie-nos comentários](https://feedback.azure.com/forums/258995-azure-backup).

## <a name="next-steps"></a>Passos seguintes

* Obtenha mais detalhes sobre como [efetuar a cópia de segurança das máquinas Windows](backup-windows-with-mars-agent.md).
* Agora que fez o back up do seu Estado do Sistema do Servidor do Windows, pode [gerir os seus cofres e servidores](backup-azure-manage-windows-server.md).
* Se precisar de restaurar uma cópia de segurança, utilize este artigo para [restaurar ficheiros para uma máquina Windows](backup-azure-restore-windows-server.md).
