---
title: Tutorial - Re-apoie o Servidor do Windows para O Azure
description: Este tutorial apresenta detalhes sobre a cópia de segurança do Windows Server no local para um cofre dos Serviços de Recuperação.
ms.topic: tutorial
ms.date: 08/22/2018
ms.custom: mvc
ms.openlocfilehash: d2990b5950cf8812367c3a59c6cace39e4085e2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88261910"
---
# <a name="back-up-windows-server-to-azure"></a>Cópia de Segurança do Windows Server para o Azure

Pode utilizar o Azure Backup para proteger o Windows Server de danos, ataques e desastres. O Azure Backup fornece uma ferramenta simples conhecida como o agente de Serviços de Recuperação do Microsoft Azure (MARS). O agente MARS está instalado no Windows Server para proteger ficheiros e pastas e informações de configuração do servidor através do Estado do Sistema do Windows Server. Este tutorial explica como pode utilizar o Agente MARS para efetuar cópias de segurança do Windows Server para o Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Transferir e configurar o Agente MARS
> * Configurar horas de cópia de segurança e agenda de retenção para as cópias de segurança do servidor
> * Realizar um back-up on-demand

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com>.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Antes de fazer uma cópia de segurança do Windows Server, tem de criar um local para as cópias de segurança ou restaurar pontos, para serem armazenadas. O [cofre dos Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md) é um contentor do Azure que armazena as cópias de segurança do Windows Server. Siga os passos abaixo para criar um cofre dos Serviços de Recuperação no portal do Azure.

1. No menu da esquerda, selecione **All services** (Todos os serviços) e, na lista de serviços, escreva **Recovery Services** (Serviços de Recuperação). Selecione **cofres dos Serviços de Recuperação**.

   ![Cofre de Serviços de Recuperação Aberto](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2. No menu de **cofres dos Serviços de Recuperação,** selecione **Add**.

   ![Fornecer informações para o cofre](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3. No menu do **cofre dos Serviços de Recuperação**,

    * escreva *myRecoveryServicesVault* em **Name** (Nome).
    * O ID da subscrição atual aparece em **Subscription** (Subscrição).
    * Em **Resource group** (Grupo de recursos), selecione **Use existing** (Utilizar existente) *myResourceGroup*. Se *myResourceGroup* não existir, selecione **Create New** (Criar novo) e escreva *myResourceGroup*.
    * No menu pendente **Location** (Localização), escolha *West Europe* (Europa Ocidental).
    * Selecione **Criar** para criar o cofre dos Serviços de Recuperação.

Quando o cofre for criado, aparecerá na lista de cofres dos Serviços de Recuperação.

## <a name="download-recovery-services-agent"></a>Transferir agente dos Serviços de Recuperação

O agente dos Serviços de Recuperação do Microsoft Azure (MARS) cria uma associação entre o Windows Server e o cofre de Serviços de Recuperação. O procedimento seguinte explica como transferir o agente para o servidor.

1. Na lista dos cofres dos Serviços de Recuperação, selecione **myRecoveryServicesVault** para abrir o dashboard do mesmo.

   ![Selecione o cofre para abrir o painel](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2. No menu do painel de instrumentos do cofre, selecione **Backup**.

3. No menu **Objetivo de Cópia de Segurança**:

   * para onde está a sua **On-premises** carga **de trabalho?**
   * para **Pretende efetuar uma cópia de segurança?**, selecione **Ficheiros e pastas** e clique em **Estado do Sistema**

   ![Menu backup Goal](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)

4. Selecione **Preparar Infraestruturas** para abrir o menu **de infraestruturas Prepare.**

5. No menu **de infraestrutura Prepare,** selecione **Agente de Descarregamento para Windows Server ou Cliente do Windows** para descarregar o *MARSAgentInstaller.exe*.

    ![Agente de descarregamento para Windows Server ou Cliente do Windows](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    O instalador abre um browser separado e transfere **MARSAgentInstaller.exe**.

6. Antes de executar o ficheiro descarregado, no menu de infraestrutura **Prepare-se, selecione Baixar** e guardar o ficheiro **'Credenciais de Cofre'.** As credenciais são exigidas para ligar o Agente MARS ao cofre dos Serviços de Recuperação.

    ![Transferir as credenciais do cofre](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)

## <a name="install-and-register-the-agent"></a>Instalar e registar o agente

1. Localize e faça duplo clique o **MARSagentinstaller.exe** transferido.
2. O **Assistente de Configuração do Agente dos Serviços de Recuperação do Microsoft Azure** é apresentado. Ao analisar o assistente, forneça as seguintes informações quando solicitado e selecione **Registar.»**
   * Localização para a pasta cache e da instalação.
   * Informações do servidor proxy se utilizar um servidor proxy para estabelecer ligação à Internet.
   * Os detalhes do seu nome de utilizador e palavra-passe se utilizar um proxy autenticado.

     ![Assistente de configuração de serviços de recuperação do Microsoft Azure](./media/tutorial-backup-windows-server-to-azure/mars-installer.png)

3. No final do assistente, selecione **'Continuar a Registar'** e fornecer o ficheiro **De credenciais de Abóbada** que descarregou no procedimento anterior.

4. Quando solicitado, forneça uma frase de acesso de encriptação para encriptar as cópias de segurança do Windows Server. Guarde a palavra-passe num local seguro, uma vez que a Microsoft não consegue recuperar a palavra-passe se estiver perdida.

5. Selecione **Concluir**.

## <a name="configure-backup-and-retention"></a>Configurar Cópia de Segurança e Retenção

Utilize o agente dos Serviços de Recuperação do Microsoft Azure para agendar quando ocorrerem cópias de segurança do Azure no Windows Server. Execute os seguintes passos no servidor onde transferiu o agente.

1. Abra o agente dos Serviços de Recuperação do Microsoft Azure. Pode encontrá-lo ao pesquisar na máquina por **Cópia de Segurança do Microsoft Azure**.

2. Na consola do agente Serviços de Recuperação, selecione **Agendar Backup** no âmbito do **Painel de Ações**.

    ![Agendar Backup](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. Selecione **Seguinte** para navegar para a página **'Selecionar Itens' para fazer o back up.**

4. Selecione **Adicionar Itens** e a partir da caixa de diálogo que abre, selecione **System State** e ficheiros ou pastas que pretende fazer. Em seguida, selecione **OK**.

5. Selecione **Seguinte**.

6. Na página **'Especificar a Agenda de Cópia de Segurança' (Estado do Sistema),** especifique a hora do dia ou a semana em que as cópias de segurança precisam de ser ativadas para o Estado do Sistema e selecione **Seguinte**.

7. Na página **'Select Retention Policy' (Estado do Sistema),** selecione a Política de Retenção para a cópia de cópia de cópia de segurança para o Estado do Sistema e selecione **Seguinte**.

8. Da mesma forma, selecione o agendamento de cópia de segurança e a política de retenção de ficheiros e pastas selecionados.

9. Na página **'Escolha', 'Escolha', 'Fazer a parte' do tipo** inicial, selecione **automaticamente sobre a rede**e selecione **Seguinte**.

10. Na página **Confirmação,** reveja as informações e **selecione Terminar**.

11. Depois de o assistente terminar de criar o horário de backup, selecione **Close**.

## <a name="perform-an-on-demand-backup"></a>Executar uma cópia de segurança a pedido

Estabeleceu o horário quando os trabalhos de reserva funcionam. No entanto, não fez o back up do servidor. É uma melhor prática de recuperação de desastres executar uma cópia de segurança a pedido para garantir a resiliência dos dados para o seu servidor.

1. Na consola de agentes do Microsoft Azure Recovery Services, selecione **Back Up Now**.

    ![Back Up Now](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2. No assistente **'Back Up Now',** selecione um de **Ficheiros e Pastas** ou **Estado do Sistema** que pretende fazer e selecione **Seguinte**
3. Na página de **Confirmação**, reveja as definições que o assistente **Efetuar Cópia de Segurança Agora** utiliza para efetuar uma cópia de segurança do servidor. Em seguida, selecione **Back Up**.
4. Selecione **Perto** para fechar o assistente. Se fechar este assistente antes de o processo de cópia de segurança estar concluído, o assistente continua a ser executado em segundo plano.
5. Depois de concluída a cópia de segurança inicial, o estado **Tarefa concluída** é apresentado no painel da consola do agente MARS **Tarefas**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou o portal do Azure para:

> [!div class="checklist"]
>
> * Criar um cofre dos Serviços de Recuperação 
> * Transfira o agente dos Serviços de Recuperação do Microsoft Azure
> * Instalar o agente
> * Configurar cópia de segurança do Windows Server
> * Executar uma cópia de segurança a pedido

Continue para o próximo tutorial para recuperar ficheiros a partir do Azure para o Windows Server

> [!div class="nextstepaction"]
> [Restaurar ficheiros do Azure para o Windows Server](./tutorial-backup-restore-files-windows-server.md)
