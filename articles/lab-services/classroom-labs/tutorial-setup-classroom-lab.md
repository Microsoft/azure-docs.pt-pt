---
title: Configurar um laboratório de sala de aula com o Azure Lab Services | Microsoft Docs
description: Neste tutorial, você usa o Azure Lab Services para criar um laboratório de sala de aula com máquinas virtuais que são usadas por alunos da sua turma.
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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: b3a9c95233c2016dc35f4fcade3e2634e121d252
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591782"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Tutorial: Configurar um laboratório de sala de aula 
Neste tutorial, vai configurar um laboratório de sala de aula com máquinas virtuais que serão utilizadas pelos alunos na sala de aula.  

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Criar um laboratório de sala de aula
> * Adicione utilizadores ao laboratório
> * Definir o horário para o laboratório
> * Envie e-mail de convite aos alunos

## <a name="prerequisites"></a>Pré-requisitos
Neste tutorial, criaste um laboratório com máquinas virtuais para a tua turma. Para criar um laboratório numa conta de laboratório, deve ser membro de um desses papéis na conta do laboratório: Proprietário, Criador de Laboratório ou Colaborador. A conta que usou para criar uma conta de laboratório é automaticamente adicionada ao papel do proprietário. Então, pode usar a conta de utilizador que usou para criar uma conta de laboratório para criar um laboratório de sala de aula. 

Aqui está o fluxo de trabalho típico ao utilizar os Serviços de Laboratório Azure:

1. Um criador de conta de laboratório adiciona outros utilizadores ao papel de **Criador de Laboratório.** Por exemplo, o criador/administrador da conta de laboratório adiciona educadores ao papel de Criador de **Laboratório** para que possam criar laboratórios para as suas aulas. 
2. Depois, os educadores criam laboratórios com VMs para as suas aulas e enviam links de inscrição aos alunos da turma. 
3. Os alunos usam o link de inscrição que recebem dos educadores para se inscreverem no laboratório. Uma vez registados, podem usar VMs nos laboratórios para fazer o trabalho de classe e trabalho em casa. 

## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula
Neste passo, cria-se um laboratório para a sua turma em Azure. 

1. Navegue para o [site do Azure Lab Services](https://labs.azure.com). Note que o Internet Explorer 11 ainda não é suportado. 
2. Selecione **Iniciar sessão** e introduza as suas credenciais. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
3. Selecione **Novo laboratório.** 
    
    ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Na janela **Novo Laboratório**, realize as ações abaixo: 
    1. Especifique um **nome** para o seu laboratório e selecione **Next**.  

        ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. Na página de credenciais de **máquina virtual,** especifique credenciais padrão para todos os VMs no laboratório. Especifique o **nome** e a **palavra-passe** para o utilizador e, em seguida, selecione **Next**.  

        ![Nova janela de laboratório](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Registe o nome de utilizador e a palavra-passe. Estas não serão apresentadas novamente.
    3. Na página de políticas do **Laboratório,** selecione **Terminar**. 

        ![Quota para cada utilizador](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Deve ver o seguinte ecrã que mostra o estado da criação vM do modelo. Esta operação leva até 20 minutos. 

    ![Estado da criação vm do modelo](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na página **Modelo,** faça os seguintes passos: Estes passos são **opcionais** para o tutorial.

    1. Ligue-se à VM de modelo através da seleção de **Ligar**. Se for um VM de modelo Linux, escolha se pretende ligar-se utilizando SSH ou RDP (se o RDP estiver ativado).
    3. Instale e configure o software necessário para a sua aula no modelo VM. 
    4. **Pare** o modelo VM.  

## <a name="publish-the-template-vm"></a>Publicar a VM do modelo
Neste passo, publica o modelo VM. Ao publicar o modelo VM, o Azure Lab Services cria VMs no laboratório utilizando o modelo. Todas as máquinas virtuais têm a mesma configuração do modelo.

1. Na página **'Modelo',** **selecione Publicar** na barra de ferramentas. 

    ![Publicar botão de modelo](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Depois de publicar, não é possível anular a publicação. 
2. Na página do **modelo Publicar,** introduza o número de máquinas virtuais que pretende criar em laboratório e, em seguida, selecione **Publicar**. 

    ![Publicar modelo - número de VMs](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Você vê o **estado de publicar** o modelo na página. Este processo pode demorar até uma hora. 

    ![Publicar modelo – progresso](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Aguarde até que a publicação esteja completa e, em seguida, mude para a página de **piscina de máquinas virtuais** selecionando **máquinas virtuais** no menu esquerdo ou selecionando azulejos de **máquinas virtuais.** Confirme que vê máquinas virtuais que estão em estado **não atribuído.** Estas VMs ainda não estão atribuídas a estudantes. Elas devem estar no estado **Parado**. Pode iniciar uma VM de estudante, ligar à VM, parar a VM e eliminar a VM nesta página. Pode iniciá-las nesta página ou permitir que os seus estudantes iniciem as VMs. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > Quando um educador liga um Estudante VM, a quota para o aluno não é afetada. A quota para um utilizador especifica o número de horas de laboratório disponíveis para o utilizador fora do horário de aula programado. Para obter mais informações sobre quotas, consulte [As quotas definidas para os utilizadores.](how-to-configure-student-usage.md?#set-quotas-for-users)

## <a name="set-a-schedule-for-the-lab"></a>Estabeleça um horário para o laboratório
Crie um evento programado para o laboratório para que os VMs no laboratório sejam automaticamente iniciados/parados em momentos específicos. A quota de utilizador (predefinido: 10 horas) especificada anteriormente é o tempo adicional atribuído a cada utilizador fora desta hora programada. 

1. Mude para a página **'Agendas'** e selecione **Adicionar evento agendado** na barra de ferramentas. 

    ![Adicione o botão de agenda na página 'Agendas'](../media/how-to-create-schedules/add-schedule-button.png)
2. Na página **do evento adicionar agendada,** faça os seguintes passos:
    1. Confirme que a **Standard** é selecionada do **tipo Evento**.  
    2. Selecione a data de **início** para a aula. 
    4. Selecione a hora de **início** em que pretende que os VMs sejam iniciados.
    5. Selecione o tempo de **paragem** em que os VMs devem ser desligados. 
    6. Selecione o **fuso horário** para os tempos de início e paragem que especificou. 
3. Na mesma página **de eventos agendada,** selecione o horário atual na secção **Repeat.**  

    ![Adicione o botão de agenda na página 'Agendas'](../media/how-to-create-schedules/select-current-schedule.png)
5. Na caixa de diálogo **Repeat,** faça os seguintes passos:
    1. Confirme que **todas as semanas** estão definidas para o campo **Repeat.** 
    2. Selecione os dias em que pretende que o horário faça efeito. No exemplo seguinte, de segunda a sexta-feira é selecionado. 
    3. Selecione uma **data de fim** para o horário.
    8. Selecione **Guardar**. 

        ![Definir agenda de repetição](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Agora, na página **de eventos agendado seleto,** para **Notas (opcional)**, insira qualquer descrição ou notas para a programação. 
4. Na página **de eventos agendada seleção,** selecione **Guardar**. 

    ![Horário semanal](../media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Navegue até à data de início do calendário para verificar se o horário está definido.
    
    ![Agenda no calendário](../media/how-to-create-schedules/schedule-calendar.png)

    Para obter mais informações sobre a criação e gestão de horários para uma aula, consulte [Criar e gerir o horário para laboratórios de sala de aula.](how-to-create-schedules.md)


## <a name="add-users-to-the-lab"></a>Adicione utilizadores ao laboratório

1. Selecione **Utilizadores** no menu esquerdo. Por predefinição, a opção de **acesso Restrict** está ativada. Quando esta definição está aberta, um utilizador não pode registar-se no laboratório mesmo que o utilizador tenha o link de registo, a menos que o utilizador esteja na lista de utilizadores. Apenas os utilizadores da lista podem registar-se no laboratório utilizando o link de registo que envia. Neste procedimento, adiciona os utilizadores à lista. Em alternativa, pode desligar o **acesso restrito,** o que permite aos utilizadores registarem-se no laboratório desde que tenham a ligação de registo. 
2. Selecione **Adicionar utilizadores** na barra de ferramentas e, em seguida, selecione **Adicionar por endereço de e-mail**. 

    ![Adicione o botão dos utilizadores](../media/how-to-configure-student-usage/add-users-button.png)
1. Na página **adicionar utilizadores,** introduza endereços de e-mail dos utilizadores em linhas separadas ou numa única linha separada por pontos evíocos. 

    ![Adicionar endereços de e-mail do utilizador](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selecione **Guardar**. Veja os endereços de e-mail dos utilizadores e os seus status (registados ou não) na lista. 

    ![Lista de utilizadores](../media/how-to-configure-student-usage/users-list-new.png)

    Verá os nomes dos utilizadores na lista depois de estarem registados no laboratório. 
    

## <a name="send-invitation-emails-to-users"></a>Enviar e-mails de convite aos utilizadores

1. Mude para a vista **utilizadores** se ainda não estiver na página e selecione **Convidar tudo** na barra de ferramentas. 

    ![Selecione alunos](../media/tutorial-setup-classroom-lab/invite-all-button.png)
1. No convite Enviar por página de **e-mail,** introduza uma mensagem opcional e, em seguida, selecione **Enviar**. O e-mail inclui automaticamente o link de registo. Pode obter este link de registo **selecionando... (elipse)** na barra de ferramentas e **no link de registo.** 

    ![Enviar link de registo por e-mail](../media/tutorial-setup-classroom-lab/send-email.png)
4. Vê o estado do **convite** na lista de **Utilizadores.** O estado deve alterar-se para **enviar** e, em seguida, para **enviar a &lt; &gt; data**. 

    Para obter mais informações sobre a adição de alunos a uma aula e a gestão do seu uso do laboratório, consulte [como configurar o uso](how-to-configure-student-usage.md)do aluno.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou um laboratório para a sua aula em Azure. Para saber como um aluno pode aceder a uma VM no laboratório através da ligação de registo, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Ligar a uma VM no laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)

