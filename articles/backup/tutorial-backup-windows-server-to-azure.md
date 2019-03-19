---
title: Cópia de Segurança do Windows Server para o Azure
description: Este tutorial apresenta detalhes sobre a cópia de segurança do Windows Server no local para um cofre dos Serviços de Recuperação.
services: backup
author: saurabhsensharma
manager: shivamg
keywords: cópia de segurança do windows server; cópia de segurança do windows server; cópia de segurança e recuperação após desastre
ms.service: backup
ms.topic: tutorial
ms.date: 8/22/2018
ms.author: saurse
ms.custom: mvc
ms.openlocfilehash: f5cfa3de962c51ac7ff43d9b83bdea9f408f5bcf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58097428"
---
# <a name="back-up-windows-server-to-azure"></a>Cópia de Segurança do Windows Server para o Azure


Pode utilizar o Azure Backup para proteger o Windows Server de danos, ataques e desastres. O Azure Backup fornece uma ferramenta simples conhecida como o agente de Serviços de Recuperação do Microsoft Azure (MARS). O agente MARS está instalado no Windows Server para proteger ficheiros e pastas e informações de configuração do servidor através do Estado do Sistema do Windows Server. Este tutorial explica como pode utilizar o Agente MARS para efetuar cópias de segurança do Windows Server para o Azure. Neste tutorial, ficará a saber como: 


> [!div class="checklist"]
> * Transferir e configurar o Agente MARS
> * Configurar horas de cópia de segurança e agenda de retenção para as cópias de segurança do servidor
> * Efetuar um ad hoc a cópia de segurança


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Antes de fazer uma cópia de segurança do Windows Server, tem de criar um local para as cópias de segurança ou restaurar pontos, para serem armazenadas. O [cofre dos Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md) é um contentor do Azure que armazena as cópias de segurança do Windows Server. Siga os passos abaixo para criar um cofre dos Serviços de Recuperação no portal do Azure. 

1. No menu da esquerda, selecione **All services** (Todos os serviços) e, na lista de serviços, escreva **Recovery Services** (Serviços de Recuperação). Clique em **Cofres dos Serviços de Recuperação**.

   ![abrir o cofre dos Serviços de Recuperação](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2. No menu **Cofres dos Serviços de Recuperação**, clique em **Adicionar**.

   ![fornecer informações para o cofre](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3. No menu do **cofre dos Serviços de Recuperação**,

    - escreva *myRecoveryServicesVault* em **Name** (Nome).
    - O ID da subscrição atual aparece em **Subscription** (Subscrição).
    - Em **Resource group** (Grupo de recursos), selecione **Use existing** (Utilizar existente) *myResourceGroup*. Se *myResourceGroup* não existir, selecione **Create New** (Criar novo) e escreva *myResourceGroup*. 
    - No menu pendente **Location** (Localização), escolha *West Europe* (Europa Ocidental).
    - Clique em **Criar** para criar o cofre dos Serviços de Recuperação.
 
Quando o cofre for criado, aparecerá na lista de cofres dos Serviços de Recuperação.

## <a name="download-recovery-services-agent"></a>Transferir agente dos Serviços de Recuperação

O agente dos Serviços de Recuperação do Microsoft Azure (MARS) cria uma associação entre o Windows Server e o cofre de Serviços de Recuperação. O procedimento seguinte explica como transferir o agente para o servidor.

1. Na lista dos cofres dos Serviços de Recuperação, selecione **myRecoveryServicesVault** para abrir o dashboard do mesmo.

   ![fornecer informações para o cofre](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2. No menu do dashboard do cofre, clique em **Cópia de Segurança**.

3. No menu **Objetivo de Cópia de Segurança**:

   * para **Onde está a ser executada a carga de trabalho?**, selecione **No local**, 
   * para **Pretende efetuar uma cópia de segurança?**, selecione **Ficheiros e pastas** e clique em **Estado do Sistema**

   ![fornecer informações para o cofre](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)

4. Clique em **Preparar infraestrutura** para abrir o menu **Preparar a infraestrutura**.

5. No menu **Preparar infraestrutura**, clique em **Transferir Agente para o Windows Server ou um Cliente Windows** para transferir o *MARSAgentInstaller.exe*. 

    ![preparar infraestrutura](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    O instalador abre um browser separado e transfere **MARSAgentInstaller.exe**.
 
6. Antes de executar o ficheiro transferido, no menu Preparar infraestrutura, clique em **Transferir** e guarde o ficheiro **Credenciais do Cofre**. As credenciais são exigidas para ligar o Agente MARS ao cofre dos Serviços de Recuperação.

    ![preparar infraestrutura](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)
 
## <a name="install-and-register-the-agent"></a>Instalar e registar o agente

1. Localize e faça duplo clique o **MARSagentinstaller.exe** transferido.
2. O **Assistente de Configuração do Agente dos Serviços de Recuperação do Microsoft Azure** é apresentado. À medida que avança ao longo do assistente, forneça as seguintes informações quando lhe for pedido e clique em **Registar**.
   - Localização para a pasta cache e da instalação.
   - Informações do servidor proxy se utilizar um servidor proxy para estabelecer ligação à Internet.
   - Os detalhes do seu nome de utilizador e palavra-passe se utilizar um proxy autenticado.

     ![preparar infraestrutura](./media/tutorial-backup-windows-server-to-azure/mars-installer.png) 

3. No final do assistente, clique em **Avançar para o Registo** e forneça o ficheiro **Credenciais do Cofre** transferido no procedimento anterior.
 
4. Quando solicitado, forneça uma frase de acesso de encriptação para encriptar as cópias de segurança do Windows Server. Guarde a frase de acesso numa localização segura visto que a Microsoft não pode recuperar o frase de acesso se a perder.

5. Clique em **Concluir**. 

## <a name="configure-backup-and-retention"></a>Configurar Cópia de Segurança e Retenção

Utilize o agente dos Serviços de Recuperação do Microsoft Azure para agendar quando ocorrerem cópias de segurança do Azure no Windows Server. Execute os seguintes passos no servidor onde transferiu o agente.

1. Abra o agente dos Serviços de Recuperação do Microsoft Azure. Pode encontrá-lo ao pesquisar na máquina por **Cópia de Segurança do Microsoft Azure**.

2.  Na consola do agente dos Serviços de Recuperação, clique em **Agendar Cópia de Segurança** no **Painel Ações**.

    ![preparar infraestrutura](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. Clique em **Seguinte** para navegar para a página **Selecionar Itens para Cópia Segurança**.

4. Clique em **Adicionar Itens** e na caixa de diálogo que se abre, selecione **Estado do Sistema** e os ficheiros ou pastas de que pretende criar cópias de segurança. Em seguida, clique em **OK**.

5. Clique em **Seguinte**.

6. Na página **Especificar Agendamento de Cópia de Segurança (Estado do Sistema)**, especifique a hora do dia ou semana nos quais as cópias de segurança devem ser acionadas para o Estado do Sistema e clique em **Seguinte**.

7. Na página **Selecionar Política de Retenção (Estado do Sistema)** página, selecione a Política de Retenção para a cópia de segurança do Estado do Sistema e clique em **Seguinte**.

8. Da mesma forma, selecione o agendamento de cópia de segurança e a política de retenção de ficheiros e pastas selecionados. 

9. Na página **Escolher Tipo de Cópia de Segurança Inicial**, selecione **Automaticamente através da rede** e clique em **Seguinte**.

10. Na página de **Confirmação**, reveja as informações e clique em **Concluir**.

11. Depois de o assistente ter criado a agenda da cópia de segurança, clique em **Fechar**.

## <a name="perform-an-ad-hoc-back-up"></a>Efetuar um ad hoc a cópia de segurança

Tem de estabelecer o agendamento quando forem executadas tarefas de cópia de segurança. No entanto, não terá efetuado cópia de segurança do servidor. É uma prática de melhor de recuperação após desastre executar uma cópia de segurança a pedido para se certificar da resiliência de dados para o servidor.

1.  Na consola do agente dos Serviços de Recuperação do Microsoft Azure, clique em **Efetuar Cópia de Segurança Agora**.

    ![preparar infraestrutura](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2.  No assistente **Efetuar Cópia de Segurança Agora**, selecione um de **Ficheiros e Pastas** ou **Estado do Sistema** que pretende efetuar cópias de segurança e clique em **Seguinte** 
3. Na página de **Confirmação**, reveja as definições que o assistente **Efetuar Cópia de Segurança Agora** utiliza para efetuar uma cópia de segurança do servidor. Em seguida, clique em **Efetuar Cópia de Segurança**.
4.  Clique em **Fechar** para fechar o assistente. Se fechar este assistente antes de o processo de cópia de segurança estar concluído, o assistente continua a ser executado em segundo plano.
4.  Depois de concluída a cópia de segurança inicial, o estado **Tarefa concluída** é apresentado no painel da consola do agente MARS **Tarefas**.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, utilizou o portal do Azure para: 
 
> [!div class="checklist"] 
> * Criar um cofre dos Serviços de Recuperação  
> * Transfira o agente dos Serviços de Recuperação do Microsoft Azure 
> * Instalar o agente 
> * Configurar cópia de segurança do Windows Server 
> * Executar uma cópia de segurança a pedido 

Continue para o próximo tutorial para recuperar ficheiros a partir do Azure para o Windows Server

> [!div class="nextstepaction"] 
> [Restaurar ficheiros do Azure para o Windows Server](./tutorial-backup-restore-files-windows-server.md) 

