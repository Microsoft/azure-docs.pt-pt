---
title: Configurar um laboratório de sala de aula com o Azure Lab Services | Microsoft Docs
description: Neste tutorial, você usa a Azure Lab Services para criar um laboratório de sala de aula com máquinas virtuais que são usadas por alunos da sua turma.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 1155646d31ddb8a0a3abce025acde5c4cb645f54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336739"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Tutorial: Configurar um laboratório de sala de aula 
Neste tutorial, vai configurar um laboratório de sala de aula com máquinas virtuais que serão utilizadas pelos alunos na sala de aula.  

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Criar um laboratório de sala de aula
> * Adicionar utilizadores ao laboratório
> * Definir horário para o laboratório
> * Enviar e-mail de convite aos alunos

## <a name="prerequisites"></a>Pré-requisitos
Neste tutorial, cria-se um laboratório com máquinas virtuais para a sua aula. Para criar um laboratório numa conta de laboratório, deve ser membro de uma destas funções na conta do laboratório: Proprietário, Criador de Laboratório ou Colaborador. A conta que usou para criar uma conta de laboratório é automaticamente adicionada à função de proprietário. Então, pode usar a conta de utilizador que usou para criar uma conta de laboratório para criar um laboratório em sala de aula. 

Aqui está o fluxo de trabalho típico ao utilizar os Serviços Azure Lab:

1. Um criador de conta de laboratório adiciona outros utilizadores ao papel **de Criador de Laboratório.** Por exemplo, o criador/administrador da conta de laboratório adiciona educadores ao papel de Criador de **Laboratório** para que possam criar laboratórios para as suas aulas. 
2. Depois, os educadores criam laboratórios com VMs para as suas aulas e enviam links de inscrição para os alunos da turma. 
3. Os alunos usam o link de inscrição que recebem dos educadores para se inscreverem no laboratório. Uma vez registados, podem usar VMs nos laboratórios para fazer o trabalho de classe e o trabalho em casa. 

## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula
Neste passo, cria-se um laboratório para a sua aula em Azure. 

1. Navegue para o [site do Azure Lab Services](https://labs.azure.com). Note que o Internet Explorer 11 ainda não está suportado. 
2. Selecione **Iniciar sessão** e introduza as suas credenciais. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
3. Selecione **Novo laboratório**. 
    
    ![Screenshot que mostra "Azure Lab Services" com o botão "Novo laboratório" selecionado.](./media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Na janela **Novo Laboratório**, realize as ações abaixo: 
    1. Especifique um **nome** para o seu laboratório e selecione **Next**.  

        ![Criar um laboratório de sala de aula](./media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. Na página **de credenciais de máquina Virtual,** especifique credenciais padrão para todos os VMs no laboratório. Especifique o **nome** e a **palavra-passe** para o utilizador e, em seguida, selecione **Seguinte**.  

        ![Nova janela do laboratório](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Registe o nome de utilizador e a palavra-passe. Estas não serão apresentadas novamente.
    3. Na página de políticas do **Laboratório,** **selecione Terminar**. 

        ![Quota para cada utilizador](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Deve ver o seguinte ecrã que mostra o estado da criação do modelo VM. Esta operação leva até 20 minutos. 

    ![Estado da criação do modelo VM](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na página **Do Modelo,** faça os seguintes passos: Estes passos são **opcionais** para o tutorial.

    1. Ligue-se à VM de modelo através da seleção de **Ligar**. Se for um VM de modelo Linux, escolha se pretende ligar usando SSH ou RDP (se rdp estiver ativado).
    3. Instale e configuure o software necessário para a sua classe no modelo VM. 
    4. **Pare** o modelo VM.  

    > [!NOTE]
    > Os VMs do modelo incorrem em **custos** ao correr, por isso certifique-se de que o modelo VM é desligado quando não precisa que esteja a funcionar. 

## <a name="publish-the-template-vm"></a>Publicar a VM do modelo
Neste passo, publica-se o modelo VM. Ao publicar o modelo VM, a Azure Lab Services cria VMs no laboratório utilizando o modelo. Todas as máquinas virtuais têm a mesma configuração do modelo.

1. Na página **Modelo,** **selecione Publicar** na barra de ferramentas. 

    ![Publicar botão de modelo](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Depois de publicar, não é possível anular a publicação. 
2. Na página do **modelo publicar,** insira o número de máquinas virtuais que pretende criar no laboratório e, em seguida, selecione **Publicar**. 

    ![Modelo de publicação - número de VMs](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Vê o **estado de publicação** do modelo na página. Este processo pode demorar até uma hora. 

    ![Publicar modelo – progresso](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Aguarde até que a publicação esteja concluída e, em seguida, mude para a página da piscina das **máquinas Virtuais** selecionando **máquinas virtuais** no menu esquerdo ou selecionando o azulejo **das máquinas Virtuais.** Confirme que vê máquinas virtuais que estão em estado **não atribuído.** Estas VMs ainda não estão atribuídas a estudantes. Elas devem estar no estado **Parado**. Pode iniciar uma VM de estudante, ligar à VM, parar a VM e eliminar a VM nesta página. Pode iniciá-las nesta página ou permitir que os seus estudantes iniciem as VMs. 

    ![Máquinas virtuais no estado parado](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > Quando um educador liga um VM estudantil, a quota para o aluno não é afetada. A quota para um utilizador especifica o número de horas de laboratório disponíveis para o utilizador fora do horário de aula programado. Para obter mais informações sobre quotas, consulte [Definição de quotas para utilizadores.](how-to-configure-student-usage.md?#set-quotas-for-users)

## <a name="set-a-schedule-for-the-lab"></a>Estabeleça um horário para o laboratório
Crie um evento programado para o laboratório para que os VMs no laboratório sejam automaticamente iniciados/parados em momentos específicos. A quota de utilizador (padrão: 10 horas) especificada anteriormente é o tempo adicional atribuído a cada utilizador fora desta hora programada. 

1. Mude para a página **Agendas** e **selecione Adicionar evento agendado** na barra de ferramentas. 

    ![Screenshot que mostra o botão "Adicionar evento programado" na página "Agendas".](./media/how-to-create-schedules/add-schedule-button.png)
2. Na página de **eventos agendada add,** faça os seguintes passos:
    1. Confirme que **a Standard** está selecionada do **tipo Evento**.  
    2. Selecione a **data de início** para a aula. 
    4. Selecione a **hora de início** em que deseja que os VMs sejam iniciados.
    5. Selecione o **tempo de paragem** em que os VMs devem ser desligados. 
    6. Selecione o **fuso horário** para os tempos de início e de paragem especificados. 
3. Na mesma página **de eventos agendada,** selecione o horário atual na secção **Repetição.**  

    ![Adicione o botão de agenda na página Agendas](./media/how-to-create-schedules/select-current-schedule.png)
5. Na caixa de diálogo **Repeat,** faça os seguintes passos:
    1. Confirme que **todas as semanas** estão definidas para o campo **Repetição.** 
    2. Selecione os dias em que deseja que o horário entre em vigor. No exemplo seguinte, Monday-Friday é selecionada. 
    3. Selecione uma **data de fim** para o horário.
    8. Selecione **Guardar**. 

        ![Definir horário de repetição](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Agora, na página de **eventos agendada add,** para **Notas (opcional)**, insira qualquer descrição ou notas para o horário. 
4. Na página de **eventos agendada para adicionar,** selecione **Save**. 

    ![Horário semanal](./media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Navegue até à data de início do calendário para verificar se o horário está definido.
    
    ![Horário no calendário](./media/how-to-create-schedules/schedule-calendar.png)

    Para obter mais informações sobre a criação e gestão de horários para uma aula, consulte [Criar e gerir a programação dos laboratórios de sala de aula.](how-to-create-schedules.md)


## <a name="add-users-to-the-lab"></a>Adicionar utilizadores ao laboratório

1. Selecione **Utilizadores** no menu esquerdo. Por predefinição, a opção **de acesso Restrict** está ativada. Quando esta definição estiver acesa, um utilizador não pode registar-se no laboratório mesmo que o utilizador tenha o link de registo, a menos que o utilizador esteja na lista de utilizadores. Apenas os utilizadores da lista podem registar-se no laboratório utilizando o link de registo que envia. Neste procedimento, adicione utilizadores à lista. Em alternativa, pode desativar **o acesso Restrict**, que permite aos utilizadores registarem-se no laboratório desde que tenham o link de registo. 
2. **Selecione Adicionar utilizadores** na barra de ferramentas e, em seguida, selecione **Adicionar por endereço de e-mail**. 

    ![Adicione botão de utilizadores](./media/how-to-configure-student-usage/add-users-button.png)
1. Na página **'Adicionar' aos utilizadores,** introduza endereços de e-mail dos utilizadores em linhas separadas ou numa única linha separada por pontos de ligação. 

    ![Adicionar endereços de e-mail do utilizador](./media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selecione **Guardar**. Veja os endereços de e-mail dos utilizadores e os seus status (registados ou não) na lista. 

    ![Lista de utilizadores](./media/how-to-configure-student-usage/users-list-new.png)

    Verá os nomes dos utilizadores na lista depois de estarem registados no laboratório. 
    

## <a name="send-invitation-emails-to-users"></a>Enviar e-mails de convite para utilizadores

1. Mude para a visualização **do Utilizadores** se ainda não estiver na página e selecione **Convidar todos** na barra de ferramentas. 

    ![Selecione alunos](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. Na página de **envio por e-mail,** introduza uma mensagem opcional e, em seguida, selecione **Enviar**. O e-mail inclui automaticamente o link de registo. Pode obter este link de registo selecionando **... (elipse)** na barra de ferramentas e **no link de registo.** 

    ![Enviar link de registo por e-mail](./media/tutorial-setup-classroom-lab/send-email.png)
4. Vê o estado do **convite** na lista **de Utilizadores.** O estado deve ser alterado para **Enviar** e, em seguida, para **Enviar na &lt; &gt; data**. 

    Para obter mais informações sobre a adição de alunos a uma aula e gerir o seu uso do laboratório, consulte [Como configurar o uso dos alunos.](how-to-configure-student-usage.md)

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criaste um laboratório para a tua aula em Azure. Para saber como um aluno pode aceder a uma VM no laboratório através da ligação de registo, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Ligar a uma VM no laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)

