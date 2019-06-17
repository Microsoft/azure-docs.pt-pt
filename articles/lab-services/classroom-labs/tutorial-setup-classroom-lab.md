---
title: Configurar um laboratório de sala de aula com o Azure Lab Services | Microsoft Docs
description: Neste tutorial, vai configurar um laboratório para utilizar numa sala de aula.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 803fe6eff8804dbd407642386865fe975c8db524
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123272"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Tutorial: Configurar um laboratório de sala de aula 
Neste tutorial, vai configurar um laboratório de sala de aula com máquinas virtuais que serão utilizadas pelos alunos na sala de aula.  

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Criar um laboratório de sala de aula
> * Adicionar utilizadores ao laboratório
> * Enviar a ligação de registo para os alunos

## <a name="prerequisites"></a>Pré-requisitos
Para configurar um laboratório de sala de aula numa conta de laboratório, tem de ser um membro de uma destas funções na conta de laboratório: Proprietário, criador do laboratório ou contribuinte. A conta que utilizou para criar uma conta de laboratório é automaticamente adicionada à função de proprietário.

Proprietário de um laboratório pode adicionar outros utilizadores para o **criador do laboratório** função. Por exemplo, o proprietário de um laboratório adiciona professores para a função de criador do laboratório. Em seguida, os professores criar laboratórios com VMs para suas classes. Os estudantes utilizam a ligação de registo que ele recebe de professores se registrar para o laboratório. Assim que estão registadas, eles podem usar as VMs nos laboratórios para fazer a classe de trabalho e trabalho doméstico. Para obter passos detalhados para adicionar utilizadores à função de criador do laboratório, consulte [adicionar um utilizador à função de criador do laboratório](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue para o [site do Azure Lab Services](https://labs.azure.com). Tenha em atenção que Internet Explorer 11 ainda não é suportado. 
2. Selecione **Iniciar sessão** e introduza as suas credenciais. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
3. Na janela **Novo Laboratório**, realize as ações abaixo: 
    1. Especifique um **nome** para o seu laboratório. 
    2. Especifique o número máximo **número de máquinas virtuais** no laboratório. Pode aumentar ou decreate o número de VMs, depois de criar o laboratório ou num laboratório existente. Para obter mais informações, consulte [atualizar o número de VMs num laboratório](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Selecione **Guardar**.

        ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na página **Selecionar especificações da máquina virtual**, execute os passos seguintes:
    1. Selecione um **tamanho** para máquinas virtuais (VM) criadas no laboratório. Atualmente, **pequeno**, **médio**, **média (virtualização)** , **grandes**, e **GPU** tamanhos são permitido.
    3. Selecione a **imagem de VM** a ser utilizado para criar as VM no laboratório. Se selecionar uma imagem do Linux, verá uma opção para ativar a ligação de ambiente de trabalho remoto para o mesmo. Para obter detalhes, consulte [ativar a ligação de ambiente de trabalho remoto para Linux](how-to-enable-remote-desktop-linux.md).
    4. Selecione **Seguinte**.

        ![Especificar as especificações de VM](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Na página **Definir credenciais** , especifique as credenciais predefinidas para todas as VM do laboratório. 
    1. Especifique o **nome de utilizador** de todas as VM do laboratório.
    2. Especifique a **palavra-passe** do utilizador. 

        > [!IMPORTANT]
        > Registe o nome de utilizador e a palavra-passe. Estas não serão apresentadas novamente.
    3. Selecione **Criar**. 

        ![Definir credenciais](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na página **Configurar modelo**, verá o estado do processo de criação do laboratório. A criação do modelo no laboratório demora no máximo 20 minutos. 

    ![Configurar modelo](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Depois de concluída a configuração do modelo, verá a página seguinte: 

    ![Configurar a página do modelo após a conclusão](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Sobre o **modelo de configurar** página, efetue os seguintes passos: Essas etapas são **opcional** para o tutorial.
    2. Ligue-se à VM de modelo através da seleção de **Ligar**. Se for um modelo VM do Linux, escolher se pretende ligar com SSH ou RDP (se estiver ativado RDP).
    1. Selecione **Repor palavra-passe** para repor a palavra-passe para a VM. 
    1. Instalar e configurar o software na sua VM de modelo. 
    1. **Parar** a VM.  
    1. Introduzir uma **descrição** do modelo
9. Selecione **Seguinte** na página do modelo. 
10. Na página **Publicar o modelo**, execute as ações seguintes. 
    1. Para publicar o modelo imediatamente, selecione **publicar**.  

        > [!WARNING]
        > Depois de publicar, não é possível anular a publicação. 
    2. Para publicar mais tarde, selecione **Guardar para utilização posterior**. Pode publicar o modelo de VM depois de concluído o assistente. Para obter detalhes sobre como configurar e publicar depois de concluído o assistente, consulte [publicar o modelo](how-to-create-manage-template.md#publish-the-template-vm) secção a [como gerir laboratórios de sala de aula](how-to-manage-classroom-labs.md) artigo.

        ![Publicar modelo](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Verá o **progresso da publicação** do modelo. Este processo pode demorar até uma hora. 

    ![Publicar modelo – progresso](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Verá a página seguinte, quando o modelo for publicado com êxito. Selecione **Done** (Concluído).

    ![Publicar modelo – êxito](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Verá o **dashboard** para o seu laboratório. 
    
    ![Dashboard de laboratório de sala de aula](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Mude para o **máquinas virtuais** página ao selecionar máquinas virtuais no menu da esquerda ou ao selecionar o mosaico de máquinas virtuais. Confirme que vê as máquinas virtuais que estão na **não atribuído** estado. Estas VMs ainda não estão atribuídas a estudantes. Elas devem estar no estado **Parado**. Pode iniciar uma VM de estudante, ligar à VM, parar a VM e eliminar a VM nesta página. Pode iniciá-las nesta página ou permitir que os seus estudantes iniciem as VMs. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="add-users-to-the-lab"></a>Adicionar utilizadores ao laboratório

1. Selecione **utilizadores** no menu da esquerda. Por predefinição, o **restringir o acesso** opção está ativada. Quando esta definição está ativado, um utilizador não é possível registar com o laboratório, mesmo que o utilizador tem a ligação de registo, a menos que o utilizador está na lista de utilizadores. Apenas os utilizadores da lista podem registar com o laboratório, utilizando a ligação de registo que envia. Neste procedimento, pode adicionar utilizadores à lista. Em alternativa, pode desativar **restringir o acesso**, que permite aos utilizadores para se registar com o laboratório, desde que tenham uma ligação de registo. 
2. Selecione **adicionar utilizadores** na barra de ferramentas. 

    ![Adicionar botão de utilizadores](../media/how-to-configure-student-usage/add-users-button.png)
1. Sobre o **adicionar utilizadores** página, introduza os endereços de e-mail dos utilizadores em linhas separadas ou numa única linha, separados por ponto e vírgula. 

    ![Endereços de e-mail do utilizador](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selecione **Guardar**. Ver os endereços de e-mail dos utilizadores e os respetivos Estados (registados ou não) na lista. 

    ![Lista de utilizadores](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-for-users"></a>Definir quotas para os utilizadores
Pode definir quotas de por utilizador, utilizando os seguintes passos: 

1. Selecione **utilizadores** no menu da esquerda, se a página não estiver ativada. 
2. Selecione **Quota por utilizador:** na barra de ferramentas. 
3. Sobre o **Quota por utilizador** , especifique o número de horas que pretende dar a cada usuário (para estudantes): 
    1. **0 horas (apenas para o agendamento)** . Os utilizadores podem utilizar as respetivas VMs apenas durante a hora agendada ou quando, como o proprietário de laboratório ativa de VMs para os mesmos.

        ![Horas de zero - apenas hora agendada](../media/how-to-configure-student-usage/zero-hours.png)
    1. **Número total de horas de laboratório por utilizador**. Os utilizadores podem utilizar as respetivas VMs para o número de horas (especificado para este campo) definido **além da data agendada**. Se selecionar esta opção, introduza o **número de horas** na caixa de texto. 

        ![Número de horas por utilizador](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. Selecione **Guardar**. 
5. Veja os valores alterados na barra de ferramentas agora: **Quota de por utilizador: &lt;número de horas&gt;** . 

    ![Quota de por utilizador](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="set-a-schedule-for-the-lab"></a>Definir uma agenda para o laboratório
Se tiver configurado a definição de quota para **0 horas (apenas para o agendamento)** , tem de definir uma agenda para o laboratório. Neste tutorial, defina a agenda para ser um agendamento semanal recorrente.

1. Mude para o **agendas** página e selecione **adicionar agenda** na barra de ferramentas. 

    ![Adicionar botão de agenda na página de agendas](../media/how-to-create-schedules/add-schedule-button.png)
2. Sobre o **adicionar agenda** página, mude para **semanal** na parte superior. 
3. Para **agendar dias (obrigatório)** , selecione os dias em que pretende que a agenda para entrar em vigor. No exemplo a seguir, de segunda a sexta-feira está selecionada. 
4. Para o **partir** , insira o **agendar a data de início** ou escolher uma data, selecionando o **calendário** botão. Este campo é obrigatório. 
5. Para **data de fim da agenda**, introduza ou selecione uma data de término no qual as VMs devem ser encerradas. 
6. Para **hora de início**, selecione a hora em que pretende que as VMs para ser iniciado. A hora de início é necessária se a hora de paragem não está definida. Selecione **remover Iniciar evento** se pretender especificar a hora de paragem. Se o **hora de início** é desativada, selecione **evento de início de adicionar** junto à lista pendente para ativá-la. 
7. Para **hora de paragem**, selecione a hora em que pretende que as VMs sejam encerradas. A hora de paragem é necessária se a hora de início não está definida. Selecione **evento stop do Remove** se pretender especificar a hora de início. Se o **hora de paragem** é desativada, selecione **evento stop do Add** junto à lista pendente para ativá-la.
8. Para **fuso de horário (obrigatório)** , selecione o fuso horário para o início e parar de vezes especificado.  
9. Para **notas**, introduza qualquer descrição ou notas para a agenda. 
10. Selecione **Guardar**. 

    ![Agendamento semanal](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="send-an-email-with-the-registration-link"></a>Enviar um e-mail com a ligação de registo

1. Mude para o **utilizadores** ver se ainda não está na página. 
2. Na lista, selecione específicos ou todos os utilizadores. Para selecionar utilizadores específicos, marque as caixas de seleção na primeira coluna da lista. Para selecionar todos os utilizadores, selecione a caixa de verificação na frente o título da primeira coluna (**nome**) ou selecione todas as caixas de verificação para todos os utilizadores na lista. Pode ver o estado do **estado de convite** nesta lista.  Na imagem seguinte, o estado de convite para todos os estudantes é definido como **convite não enviado**. 

    ![Selecionar estudantes](../media/tutorial-setup-classroom-lab/select-students.png)
1. Selecione o **ícone de e-mail (envelope)** em uma das linhas (ou) select **Enviar convite** na barra de ferramentas. Também pode passar o mouse sobre um nome para estudantes na lista para ver o ícone de e-mail. 

    ![Enviar ligação de registo por e-mail](../media/tutorial-setup-classroom-lab/send-email.png)
4. Sobre o **ligação de registo de enviar por e-mail** página, siga estes passos: 
    1. Tipo de uma **mensagem opcional** que pretende enviar para os alunos. O e-mail inclui automaticamente a ligação de registo. 
    2. Sobre o **ligação de registo de enviar por e-mail** página, selecione **enviar**. Verá o estado de convite para a alteração **Enviar convite** e clique em **convite enviado**. 
        
        ![Convites enviados](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, criou um laboratório de sala de aula e configurou o laboratório. Para saber como um aluno pode aceder a uma VM no laboratório através da ligação de registo, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Ligar a uma VM no laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)

