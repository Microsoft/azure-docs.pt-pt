---
title: Criar cópias de segurança do Estado do sistema Windows para o Azure
description: Aprenda a criar cópias de segurança do Estado do sistema do Windows Server e/ou do Windows, computadores para o Azure.
services: backup
author: saurabhsensharma
manager: shivamg
keywords: como realizar uma cópia de segurança; como realizar uma cópia de segurança; cópia de segurança de ficheiros e pastas
ms.service: backup
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: saurse
ms.openlocfilehash: 6d8cbac7eab797662896a96ed588c9d6370cb230
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105204"
---
# <a name="back-up-windows-system-state-in-resource-manager-deployment"></a>Criar cópias de segurança do Estado do sistema Windows na implementação do Resource Manager
Este artigo explica como fazer cópias de segurança do Estado do sistema Windows Server para o Azure. É um tutorial que se destina a explicar as noções básicas.

Se pretender saber mais sobre o Backup do Azure, leia esta [descrição geral](backup-overview.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) que lhe permite aceder a qualquer serviço do Azure.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação
Para criar cópias de segurança do Estado de sistema do Windows Server, terá de criar um cofre dos serviços de recuperação na região onde pretende armazenar os dados. Também precisa de determinar como pretende que o seu armazenamento seja replicado.

### <a name="to-create-a-recovery-services-vault"></a>Para criar um cofre dos Serviços de Recuperação
1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com/) através da sua subscrição do Azure.
2. No menu Hub, clique em **Todos os serviços** e, na lista de recursos, escreva **Serviços de Recuperação** e clique em **cofres dos Serviços de Recuperação**.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-system-state/open-rs-vault-list.png) <br/>

    Se existirem cofres de serviços de recuperação na subscrição, os cofres estão listados.
3. No menu **Cofres dos Serviços de Recuperação**, clique em **Adicionar**.

    ![Passo 2 da Criação do Cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    O painel do cofre dos Serviços de Recuperação Cofre abre-se, pedindo-lhe que forneça um **Nome**, **Subscrição**, **Grupo de recursos** e **Localização**.

    ![Passo 3 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. Para o **Nome**, introduza um nome amigável para identificar o cofre. O nome tem de ser exclusivo para a subscrição do Azure. Escreva um nome que contenha entre 2 e 50 carateres. Tem de começar com uma letra e pode conter apenas letras, números e hífenes.

5. Na secção **Subscrição**, utilize o menu pendente para escolher a subscrição do Azure. Se utilizar apenas uma subscrição, é apresentada essa subscrição e pode avançar para o passo seguinte. Se não tiver a certeza de que subscrição utilizar, utilize a subscrição predefinida (ou sugerida). Terá várias escolhas apenas se a sua conta organizacional estiver associada a várias subscrições do Azure.

6. Na secção **Grupo de recursos**:

    * selecione **Criar novo** se pretender criar um grupo de Recursos.
    Ou
    * selecione **Utilizar existente** e clique no menu pendente para ver a lista de Grupos de recursos disponíveis.

   Para mais informações mais completas sobre os grupos de Recursos, veja a [Azure Resource Manager overview (Descrição geral do Azure Resource Manager)](../azure-resource-manager/resource-group-overview.md).

7. Clique em **Localização** para selecionar a região geográfica do cofre. Esta escolha determina a região geográfica para onde os dados da cópia de segurança são enviados.

8. Na parte inferior do painel do cofre dos Serviços de Recuperação, clique em **Criar**.

    Pode demorar vários minutos até que o cofre dos Serviços de Recuperação seja criado. Monitorize as notificações de estado na área superior direita do portal. Quando o cofre for criado, aparecerá na lista de cofres dos Serviços de Recuperação. Se depois de vários minutos não vir o cofre, clique em **Atualizar**.

    ![Clique no botão Atualizar](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Quando vir o cofre na lista de cofres dos Serviços de Recuperação, está pronto para definir a redundância de armazenamento.

### <a name="set-storage-redundancy-for-the-vault"></a>Definir a redundância de armazenamento do cofre
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

    Por predefinição, o seu cofre tem um armazenamento georredundante. Se utilizar o Azure como um ponto final de armazenamento de cópia de segurança primário, continue a utilizar o **Georredundante**. Se não utilizar o Azure como um ponto final do armazenamento de cópia de segurança primário, escolha **Localmente redundante**, o que reduz os custos de armazenamento do Azure. Leia mais sobre as opções de armazenamento [georredundante](../storage/common/storage-redundancy-grs.md) e [localmente redundante](../storage/common/storage-redundancy-lrs.md) nesta [Descrição geral de redundância de armazenamento](../storage/common/storage-redundancy.md).

Agora que criou um cofre, configure-o para fazer backup de estado do sistema Windows.

## <a name="configure-the-vault"></a>Configurar o cofre
1. Nos painel do cofre dos Serviços de Recuperação (do cofre que acabou de criar), na secção Introdução, clique em **Cópia de Segurança** e, em seguida, no painel **Introdução à Cópia de Segurança**, selecione **Objetivo de cópia de segurança**.

    ![Painel Abrir objetivo de cópia de segurança](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    É aberto o painel **Objetivo de Cópia de Segurança**.

    ![Painel Abrir objetivo de cópia de segurança](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. No menu pendente **Onde está a carga de trabalho em execução?**, selecione **No local**.

    Seleciona **No local** porque o seu Windows Server ou computador Windows é um computador físico que não está no Azure.

3. Do **o que fazer quiser a cópia de segurança?** menu, selecione **estado do sistema**e clique em **OK**.

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

    As credenciais do cofre são transferidas para a pasta Transferências. Depois de as credenciais do cofre serem transferidas, verá um pop-up a perguntar se quer abrir ou guardar as credenciais. Clique em **Guardar**. Se clicar acidentalmente em **Abrir**, deixe a caixa de diálogo que tenta abrir as credenciais do cofre falhar. Não é possível abrir as credenciais do cofre. Avance para o passo seguinte. As credenciais do cofre estão na pasta Transferências.   

    ![as credenciais do cofre terminaram de ser transferidas](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > As credenciais do cofre tem de ser guardadas apenas para uma localização que seja local para o Windows Server no qual pretende usar o agente.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Instalar e registar o agente

> [!NOTE]
> A permissão da cópia de segurança através do portal do Azure ainda não está disponível. Utilize o agente de serviços de recuperação do Microsoft Azure para fazer backup de estado do sistema do Windows Server.
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
* Criar cópias de segurança do Estado do sistema pela primeira vez

Para concluir a cópia de segurança inicial, utilize o agente dos Serviços de Recuperação do Microsoft Azure.

> [!NOTE]
> Pode fazer backup de estado do sistema no Windows Server 2008 R2 por meio do Windows Server 2016. Estado do sistema novamente até não é suportado em SKUs de cliente. Estado do sistema não é mostrado como uma opção para clientes do Windows ou Windows Server 2008 SP2 máquinas.
>
>

### <a name="to-schedule-the-backup-job"></a>Para agendar a tarefa de cópia de segurança

1. Abra o agente dos Serviços de Recuperação do Microsoft Azure. Pode encontrá-lo ao pesquisar na máquina por **Cópia de Segurança do Microsoft Azure**.

    ![Iniciar o agente dos Serviços de Recuperação do Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. No agente dos Serviços de Recuperação, clique em **Agendar Cópia de Segurança**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Na página Introdução do Assistente para Agendar Cópia de Segurança, clique em **Seguinte**.

4. Na página Selecionar Itens para Cópia de Segurança, clique em **Adicionar Itens**.

5. Selecione **estado do sistema** e, em seguida, clique em **OK**.

6. Clique em **Seguinte**.

7. Selecione a frequência de cópia de segurança necessária e a política de retenção para as cópias de segurança do Estado do sistema nas páginas subsequentes.

8. Na página de Confirmação, reveja as informações e, em seguida, clique em **Concluir**.

9. Depois de o assistente ter criado a agenda da cópia de segurança, clique em **Fechar**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Para fazer backup de estado do sistema do Windows Server pela primeira vez

1. Certifique-se de que não há nenhuma atualização pendente para o Windows Server, que requerem um reinício.

2. No agente dos Serviços de Recuperação, clique em **Efetuar Cópia de Segurança Agora** para concluir a propagação inicial através da rede.

    ![Efetuar a cópia de segurança do Windows Server agora](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Selecione **estado do sistema** sobre o **selecione o Item de cópia de segurança** ecrã que é apresentada e clique em **seguinte**.

4. Na página de Confirmação, reveja as definições que o Assistente Efetuar Cópia de Segurança Agora irá utilizar para efetuar uma cópia de segurança da máquina. Em seguida, clique em **Efetuar Cópia de Segurança**.

4. Clique em **Fechar** para fechar o assistente. Se fechar este assistente antes de o processo de cópia de segurança estar concluído, o assistente continua a ser executado em segundo plano.


Depois de concluída a cópia de segurança inicial, o estado **Tarefa concluída** é apresentado na consola de Cópia de Segurança.

  ![IV concluídos](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Tem dúvidas?
Se tiver dúvidas ou se houver alguma funcionalidade que gostaria de ver incluída, [envie-nos comentários](https://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Passos Seguintes
* Obtenha mais detalhes sobre como [efetuar a cópia de segurança das máquinas Windows](backup-configure-vault.md).
* Agora que tenha uma cópia de segurança do Estado de sistema do Windows Server, pode [gerir os cofres e servidores](backup-azure-manage-windows-server.md).
* Se precisar de restaurar uma cópia de segurança, utilize este artigo para [restaurar ficheiros para uma máquina Windows](backup-azure-restore-windows-server.md).
