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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: a5f8c8c00a9f63558043167c5cf8269f9e139d54
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584898"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Tutorial: Configurar um laboratório de sala de aula 
Neste tutorial, vai configurar um laboratório de sala de aula com máquinas virtuais que serão utilizadas pelos alunos na sala de aula.  

Neste tutorial, vai realizar as seguintes ações:

> [!div class="checklist"]
> * Criar um laboratório de sala de aula
> * Adicionar usuários ao laboratório
> * Definir agenda para o laboratório
> * Enviar email de convite para alunos

## <a name="prerequisites"></a>Pré-requisitos
Para configurar um laboratório de sala de aula em uma conta de laboratório, você deve ser membro de uma dessas funções na conta de laboratório: proprietário, criador de laboratório ou colaborador. A conta usada para criar uma conta de laboratório é adicionada automaticamente à função proprietário.

Um proprietário de laboratório pode adicionar outros usuários à função de **criador de laboratório** . Por exemplo, um proprietário de laboratório adiciona professores à função de criador de laboratório. Em seguida, os professores criam laboratórios com VMs para suas classes. Os alunos usam o link de registro que eles recebem dos professores para se registrarem no laboratório. Depois de registrados, eles podem usar VMs nos laboratórios para fazer a classe funcionar e trabalhar em casa. Para obter etapas detalhadas para adicionar usuários à função de criador de laboratório, consulte [Adicionar um usuário à função criador de laboratório](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue para o [site do Azure Lab Services](https://labs.azure.com). Observe que o Internet Explorer 11 ainda não tem suporte. 
2. Selecione **Iniciar sessão** e introduza as suas credenciais. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
3. Selecione **novo laboratório**. 
    
    ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Na janela **Novo Laboratório**, realize as ações abaixo: 
    1. Especifique um **nome** para o laboratório e selecione **Avançar**.  

        ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        Se você selecionar uma imagem do Linux, verá uma opção para habilitar a conexão de área de trabalho remota para ela. Para obter detalhes, consulte [habilitar conexão de área de trabalho remota para Linux](how-to-enable-remote-desktop-linux.md).
    2. Na página **credenciais da máquina virtual** , especifique as credenciais padrão para todas as VMs no laboratório. Especifique o **nome** e a **senha** para o usuário e, em seguida, selecione **Avançar**.  

        ![Nova janela de laboratório](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Registe o nome de utilizador e a palavra-passe. Estas não serão apresentadas novamente.
    3. Na página **políticas de laboratório** , insira o número de horas alocadas para cada usuário (**cota para cada usuário**) fora da hora agendada para o laboratório e selecione **concluir**. 

        ![Cota para cada usuário](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Você deve ver a tela a seguir que mostra o status da criação da VM do modelo. A criação do modelo no laboratório demora no máximo 20 minutos. 

    ![Status da criação da VM do modelo](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na página **modelo** , execute as seguintes etapas: essas etapas são **opcionais** para o tutorial.

    2. Ligue-se à VM de modelo através da seleção de **Ligar**. Se for uma VM de modelo do Linux, escolha se deseja se conectar usando SSH ou RDP (se o RDP estiver habilitado).
    1. Selecione **Redefinir senha** para redefinir a senha para a VM. 
    1. Instalar e configurar o software na sua VM de modelo. 
    1. **Parar** a VM.  
    1. Introduzir uma **descrição** do modelo
10. Na página **modelo** , selecione **publicar** na barra de ferramentas. 

    ![Botão Publicar modelo](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Depois de publicar, não é possível anular a publicação. 
8. Na página **Publicar modelo** , insira o número de máquinas virtuais que você deseja criar no laboratório e, em seguida, selecione **publicar**. 

    ![Publicar modelo-número de VMs](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Você verá o **status da publicação** do modelo na página. Este processo pode demorar até uma hora. 

    ![Publicar modelo – progresso](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Alterne para a página do **pool de máquinas virtuais** selecionando máquinas virtuais no menu à esquerda ou selecionando máquinas virtuais bloco. Confirme que você vê as máquinas virtuais que estão em estado não **atribuído** . Estas VMs ainda não estão atribuídas a estudantes. Elas devem estar no estado **Parado**. Pode iniciar uma VM de estudante, ligar à VM, parar a VM e eliminar a VM nesta página. Pode iniciá-las nesta página ou permitir que os seus estudantes iniciem as VMs. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Você executa as seguintes tarefas nesta página (não execute estas etapas para o tutorial. Estas etapas são apenas para suas informações.): 
    
    1. Para alterar a capacidade do laboratório (número de VMs no laboratório), selecione **capacidade do laboratório** na barra de ferramentas.
    2. Para iniciar todas as VMs de uma só vez, selecione **Iniciar tudo** na barra de ferramentas. 
    3. Para iniciar uma VM específica, selecione a seta para baixo no **status**e, em seguida, selecione **Iniciar**. Você também pode iniciar uma VM selecionando uma VM na primeira coluna e, em seguida, selecionando **Iniciar** na barra de ferramentas.

    Para obter mais informações sobre como criar e gerenciar modelos e configurar e gerenciar máquinas virtuais de aluno, consulte os seguintes artigos: 
    
    - [Criar e gerenciar modelos de laboratório de sala de aula](how-to-create-manage-template.md)
    - [Configurar e gerenciar o pool de máquinas virtuais](how-to-set-virtual-machine-passwords.md)

## <a name="add-users-to-the-lab"></a>Adicionar usuários ao laboratório

1. Selecione **usuários** no menu à esquerda. Por padrão, a opção de **acesso restrito** está habilitada. Quando essa configuração estiver ativada, um usuário não poderá se registrar no laboratório, mesmo se o usuário tiver o link de registro, a menos que o usuário esteja na lista de usuários. Somente os usuários na lista podem se registrar no laboratório usando o link de registro que você envia. Neste procedimento, você adiciona usuários à lista. Como alternativa, você pode desativar o **acesso restrito**, que permite aos usuários se registrarem com o laboratório, desde que eles tenham o link de registro. 
2. Selecione **Adicionar usuários** na barra de ferramentas e, em seguida, selecione **Adicionar por endereços de email**. 

    ![Botão Adicionar usuários](../media/how-to-configure-student-usage/add-users-button.png)
1. Na página **Adicionar usuários** , insira os endereços de email dos usuários em linhas separadas ou em uma única linha separada por ponto e vírgula. 

    ![Adicionar endereços de email do usuário](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selecione **Guardar**. Você vê os endereços de email dos usuários e seus status (registrados ou não) na lista. 

    ![Lista de usuários](../media/how-to-configure-student-usage/users-list-new.png)

    Você verá os nomes dos usuários na lista depois que eles forem registrados no laboratório. 
    
## <a name="set-a-schedule-for-the-lab"></a>Definir um agendamento para o laboratório
Crie um evento agendado para o laboratório para que as VMs no laboratório sejam iniciadas/interrompidas automaticamente em horários específicos. A cota de usuário especificada anteriormente é o tempo adicional atribuído a cada usuário fora deste horário agendado. 

1. Alterne para a página **agendas** e selecione **Adicionar evento agendado** na barra de ferramentas. 

    ![Botão Adicionar agendamento na página agendas](../media/how-to-create-schedules/add-schedule-button.png)
2. Na página **Adicionar evento agendado** , execute as seguintes etapas:
    1. Confirme se **padrão** está selecionado como o **tipo de evento**.  
    2. Especifique a **data de início** da classe. 
    4. Especifique a **hora de início** em que você deseja que as VMs sejam iniciadas.
    5. Especifique a **hora de parada** em que as VMs devem ser desligadas. 
    6. Especifique o **fuso horário** para os horários de início e de término especificados. 
3. Na mesma página **Adicionar evento agendado** , selecione o agendamento atual na seção **repetir** .  

    ![Botão Adicionar agendamento na página agendas](../media/how-to-create-schedules/select-current-schedule.png)
5. Na caixa de diálogo **repetir** , execute as seguintes etapas:
    1. Confirme se **todas as semanas** estão definidas para o campo de **repetição** . 
    2. Selecione os dias nos quais você deseja que o agendamento entre em vigor. No exemplo a seguir, segunda-feira é selecionada. 
    3. Selecione uma **data de término** para a agenda.
    8. Selecione **Guardar**. 

        ![Definir agendamento de repetição](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Agora, na página **Adicionar evento agendado** , para **observações (opcional)** , insira qualquer descrição ou nota para o agendamento. 
4. Na página **Adicionar evento agendado** , selecione **salvar**. 

    ![Agenda semanal](../media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Navegue até a data de início no calendário para verificar se a agenda está definida.
    
    ![Agendar no calendário](../media/how-to-create-schedules/schedule-calendar.png)

    Para obter mais informações sobre como criar e gerenciar agendas para uma classe, consulte [criar e gerenciar agenda para laboratórios de sala de aula](how-to-create-schedules.md).

## <a name="send-invitation-emails-to-students"></a>Enviar emails de convite para alunos

1. Alterne para a exibição **usuários** se você ainda não estiver na página e selecione **convidar tudo** na barra de ferramentas. 

    ![Selecionar alunos](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Na página **Enviar convite por email** , insira uma mensagem opcional e, em seguida, selecione **Enviar**. O email inclui automaticamente o link de registro. Você pode obter esse link de registro selecionando **... (reticências)** na barra de ferramentas e no **link de registro**. 

    ![Enviar link de registro por email](../media/tutorial-setup-classroom-lab/send-email.png)
4. Você verá o status de **convite** na lista de **usuários** . O status deve ser alterado para **enviando** e **enviado em &lt;data&gt;** . 

    Para obter mais informações sobre como adicionar alunos a uma classe e gerenciar seu uso do laboratório, consulte [como configurar o uso do aluno](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou um laboratório de sala de aula e configurou o laboratório. Para saber como um aluno pode aceder a uma VM no laboratório através da ligação de registo, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Ligar a uma VM no laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)

