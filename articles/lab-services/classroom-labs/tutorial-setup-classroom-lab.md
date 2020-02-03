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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2c28375ce7252e93340f395b97224d292940ce65
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719182"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Tutorial: Configurar um laboratório de sala de aula 
Neste tutorial, vai configurar um laboratório de sala de aula com máquinas virtuais que serão utilizadas pelos alunos na sala de aula.  

Neste tutorial, vai realizar as seguintes ações:

> [!div class="checklist"]
> * Criar um laboratório de sala de aula
> * Adicionar usuários ao laboratório
> * Definir o horário para o laboratório
> * Envie e-mail de convite aos alunos

## <a name="prerequisites"></a>Pré-requisitos
Para criar um laboratório numa conta de laboratório, deve ser membro de um desses papéis na conta do laboratório: Proprietário, Criador de Laboratório ou Colaborador. A conta que usou para criar uma conta de laboratório é automaticamente adicionada ao papel do proprietário.

Um dono de laboratório pode adicionar outros utilizadores ao papel de Criador de **Laboratório.** Por exemplo, um dono de laboratório adiciona professores ao papel de Criador de Laboratório. Depois, os professores criam laboratórios com VMs para as suas aulas. Os alunos usam o link de inscrição que recebem dos professores para se inscreverem no laboratório. Uma vez registados, podem usar VMs nos laboratórios para fazer o trabalho de classe e trabalho em casa. Para obter passos detalhados para adicionar utilizadores ao papel de Criador de Laboratório, consulte [Adicionar um utilizador à função De Criador](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)de Laboratório .


## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue para o [site do Azure Lab Services](https://labs.azure.com). Note que o Internet Explorer 11 ainda não é suportado. 
2. Selecione **Iniciar sessão** e introduza as suas credenciais. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
3. Selecione **Novo laboratório.** 
    
    ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Na janela **Novo Laboratório**, realize as ações abaixo: 
    1. Especifique um **nome** para o seu laboratório e selecione **Next**.  

        ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        Se você selecionar uma imagem do Linux, verá uma opção para habilitar a conexão de área de trabalho remota para ela. Para mais detalhes, consulte [Ativar a ligação remota para o ambiente de trabalho para o Linux](how-to-enable-remote-desktop-linux.md).
    2. Na página de credenciais de **máquina virtual,** especifique credenciais padrão para todos os VMs no laboratório. Especifique o **nome** e a **palavra-passe** para o utilizador e, em seguida, selecione **Next**.  

        ![Nova janela de laboratório](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Registe o nome de utilizador e a palavra-passe. Estas não serão apresentadas novamente.
    3. Na página de políticas do **Laboratório,** insira o número de horas atribuídas para cada utilizador **(quota para cada utilizador**) fora da hora programada para o laboratório e, em seguida, selecione **Terminar**. 

        ![Cota para cada usuário](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Você deve ver a tela a seguir que mostra o status da criação da VM do modelo. A criação do modelo no laboratório demora no máximo 20 minutos. 

    ![Status da criação da VM do modelo](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na página **Modelo,** faça os seguintes passos: Estes passos são **opcionais** para o tutorial.

    2. Ligue-se à VM de modelo através da seleção de **Ligar**. Se for uma VM de modelo do Linux, escolha se deseja se conectar usando SSH ou RDP (se o RDP estiver habilitado).
    1. Selecione **Redefinir a palavra-passe** para redefinir a palavra-passe para o VM. 
    1. Instalar e configurar o software na sua VM de modelo. 
    1. **Parar** a VM.  
    1. Introduzir uma **descrição** do modelo
10. Na página **'Modelo',** **selecione Publicar** na barra de ferramentas. 

    ![Botão Publicar modelo](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Depois de publicar, não é possível anular a publicação. 
8. Na página do **modelo Publicar,** introduza o número de máquinas virtuais que pretende criar em laboratório e, em seguida, selecione **Publicar**. 

    ![Publicar modelo-número de VMs](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Você vê o **estado de publicar** o modelo na página. Este processo pode demorar até uma hora. 

    ![Publicar modelo – progresso](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Mude para a página de **piscina de máquinas virtuais** selecionando máquinas virtuais no menu esquerdo ou selecionando azulejos de máquinas virtuais. Confirme que vê máquinas virtuais que estão em estado **não atribuído.** Estas VMs ainda não estão atribuídas a estudantes. Elas devem estar no estado **Parado**. Pode iniciar uma VM de estudante, ligar à VM, parar a VM e eliminar a VM nesta página. Pode iniciá-las nesta página ou permitir que os seus estudantes iniciem as VMs. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Você executa as seguintes tarefas nesta página (não execute estas etapas para o tutorial. Estas etapas são apenas para suas informações.): 
    
    1. Para alterar a capacidade do laboratório (número de VMs no laboratório), selecione **a capacidade** do Laboratório na barra de ferramentas.
    2. Para iniciar todos os VMs de uma só vez, selecione **Iniciar tudo** na barra de ferramentas. 
    3. Para iniciar um VM específico, selecione a seta para baixo no **Estado**, e, em seguida, selecione **Iniciar**. Também pode iniciar um VM selecionando um VM na primeira coluna e, em seguida, selecionando **Iniciar** na barra de ferramentas.

    Para obter mais informações sobre a criação e gestão de modelos, e a criação e gestão de máquinas virtuais estudantis, consulte os seguintes artigos: 
    
    - [Criar e gerir modelos de laboratório em sala de aula](how-to-create-manage-template.md)
    - [Configurar e gerir a piscina de máquinas virtuais](how-to-set-virtual-machine-passwords.md)

## <a name="add-users-to-the-lab"></a>Adicionar usuários ao laboratório

1. Selecione **Utilizadores** no menu esquerdo. Por predefinição, a opção de **acesso Restrict** está ativada. Quando essa configuração estiver ativada, um usuário não poderá se registrar no laboratório, mesmo se o usuário tiver o link de registro, a menos que o usuário esteja na lista de usuários. Somente os usuários na lista podem se registrar no laboratório usando o link de registro que você envia. Neste procedimento, você adiciona usuários à lista. Em alternativa, pode desligar o **acesso restrito,** o que permite aos utilizadores registarem-se no laboratório desde que tenham a ligação de registo. 
2. Selecione **Adicionar utilizadores** na barra de ferramentas e, em seguida, selecione **Adicionar por endereços de e-mail**. 

    ![Botão Adicionar usuários](../media/how-to-configure-student-usage/add-users-button.png)
1. Na página **adicionar utilizadores,** introduza endereços de e-mail dos utilizadores em linhas separadas ou numa única linha separada por pontos evíocos. 

    ![Adicionar endereços de email do usuário](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selecione **Guardar**. Você vê os endereços de email dos usuários e seus status (registrados ou não) na lista. 

    ![Lista de usuários](../media/how-to-configure-student-usage/users-list-new.png)

    Você verá os nomes dos usuários na lista depois que eles forem registrados no laboratório. 
    
## <a name="set-a-schedule-for-the-lab"></a>Estabeleça um horário para o laboratório
Crie um evento programado para o laboratório para que os VMs no laboratório sejam automaticamente iniciados/parados em momentos específicos. A quota de utilizador especificada anteriormente é o tempo adicional atribuído a cada utilizador fora desta hora programada. 

1. Mude para a página **'Agendas'** e selecione **Adicionar evento agendado** na barra de ferramentas. 

    ![Adicione o botão de agenda na página 'Agendas'](../media/how-to-create-schedules/add-schedule-button.png)
2. Na página **do evento adicionar agendada,** faça os seguintes passos:
    1. Confirme que a **Standard** é selecionada do **tipo Evento**.  
    2. Especifique a **data de início** da aula. 
    4. Especifique a hora de **início** em que pretende que os VMs sejam iniciados.
    5. Especifique o tempo de **paragem** em que os VMs devem ser desligados. 
    6. Especifique o **fuso horário** para os tempos de início e paragem que especificou. 
3. Na mesma página **de eventos agendada,** selecione o horário atual na secção **Repeat.**  

    ![Adicione o botão de agenda na página 'Agendas'](../media/how-to-create-schedules/select-current-schedule.png)
5. Na caixa de diálogo **Repeat,** faça os seguintes passos:
    1. Confirme que **todas as semanas** estão definidas para o campo **Repeat.** 
    2. Selecione os dias em que pretende que o horário faça efeito. No exemplo seguinte, de segunda a sexta-feira é selecionado. 
    3. Selecione uma **data de fim** para o horário.
    8. Selecione **Guardar**. 

        ![Definir agenda de repetição](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Agora, na página **de eventos agendado seleto,** para **Notas (opcional)** , insira qualquer descrição ou notas para a programação. 
4. Na página **de eventos agendada seleção,** selecione **Guardar**. 

    ![Agenda semanal](../media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Navegue até à data de início do calendário para verificar se o horário está definido.
    
    ![Agenda no calendário](../media/how-to-create-schedules/schedule-calendar.png)

    Para obter mais informações sobre a criação e gestão de horários para uma aula, consulte [Criar e gerir o horário para laboratórios de sala de aula.](how-to-create-schedules.md)

## <a name="send-invitation-emails-to-students"></a>Envie e-mails de convite aos alunos

1. Mude para a vista **utilizadores** se ainda não estiver na página e selecione **Convidar tudo** na barra de ferramentas. 

    ![Selecionar alunos](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. No convite Enviar por página de **e-mail,** introduza uma mensagem opcional e, em seguida, selecione **Enviar**. O email inclui automaticamente o link de registro. Pode obter este link de registo **selecionando... (elipse)** na barra de ferramentas e **no link de registo.** 

    ![Enviar link de registro por email](../media/tutorial-setup-classroom-lab/send-email.png)
4. Vê o estado do **convite** na lista de **Utilizadores.** O estado deve alterar-se para **enviar** e, em seguida, para **enviar em &lt;data&gt;** . 

    Para obter mais informações sobre a adição de alunos a uma aula e a gestão do seu uso do laboratório, consulte [como configurar o uso](how-to-configure-student-usage.md)do aluno.

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, criou um laboratório de sala de aula e configurou o laboratório. Para saber como um aluno pode aceder a uma VM no laboratório através da ligação de registo, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Ligar a uma VM no laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)

