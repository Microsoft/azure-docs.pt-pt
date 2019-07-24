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
ms.openlocfilehash: 0c50a321cbeb0d07a5039038ff796df00463ac8a
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385662"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Tutorial: Configurar um laboratório de sala de aula 
Neste tutorial, vai configurar um laboratório de sala de aula com máquinas virtuais que serão utilizadas pelos alunos na sala de aula.  

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Criar um laboratório de sala de aula
> * Adicionar usuários ao laboratório
> * Enviar a ligação de registo para os alunos

## <a name="prerequisites"></a>Pré-requisitos
Para configurar um laboratório de sala de aula em uma conta de laboratório, você deve ser um membro de uma dessas funções na conta do laboratório: Proprietário, criador de laboratório ou colaborador. A conta usada para criar uma conta de laboratório é adicionada automaticamente à função proprietário.

Um proprietário de laboratório pode adicionar outros usuários à função de **criador de laboratório** . Por exemplo, um proprietário de laboratório adiciona professores à função de criador de laboratório. Em seguida, os professores criam laboratórios com VMs para suas classes. Os alunos usam o link de registro que eles recebem dos professores para se registrarem no laboratório. Depois de registrados, eles podem usar VMs nos laboratórios para fazer a classe funcionar e trabalhar em casa. Para obter etapas detalhadas para adicionar usuários à função de criador de laboratório, consulte [Adicionar um usuário à função criador de laboratório](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue para o [site do Azure Lab Services](https://labs.azure.com). Observe que o Internet Explorer 11 ainda não tem suporte. 
2. Selecione **Iniciar sessão** e introduza as suas credenciais. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
3. Na janela **Novo Laboratório**, realize as ações abaixo: 
    1. Especifique um **nome** para o seu laboratório. 
    2. Especifique o **número máximo de máquinas virtuais** no laboratório. Você pode aumentar ou descriar o número de VMs depois de criar o laboratório ou em um laboratório existente. Para obter mais informações, consulte [atualizar o número de VMs em um laboratório](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Selecione **Guardar**.

        ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na página **Selecionar especificações da máquina virtual**, execute os passos seguintes:
    1. Selecione um **tamanho** para máquinas virtuais (VM) criadas no laboratório. Atualmente, os tamanhos **pequeno**, **médio**, **médio (virtualização)** , **grande**e **GPU** são permitidos.
    3. Selecione a **imagem de VM** a ser utilizado para criar as VM no laboratório. Se você selecionar uma imagem do Linux, verá uma opção para habilitar a conexão de área de trabalho remota para ela. Para obter detalhes, consulte [habilitar conexão de área de trabalho remota para Linux](how-to-enable-remote-desktop-linux.md).
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
8. Na página **Configurar modelo** , execute as seguintes etapas: Essas etapas são **opcionais** para o tutorial.
    2. Ligue-se à VM de modelo através da seleção de **Ligar**. Se for uma VM de modelo do Linux, escolha se deseja se conectar usando SSH ou RDP (se o RDP estiver habilitado).
    1. Selecione **Redefinir senha** para redefinir a senha para a VM. 
    1. Instalar e configurar o software na sua VM de modelo. 
    1. **Parar** a VM.  
    1. Introduzir uma **descrição** do modelo
9. Selecione **Seguinte** na página do modelo. 
10. Na página **Publicar o modelo**, execute as ações seguintes. 
    1. Para publicar o modelo imediatamente, selecione **publicar**.  

        > [!WARNING]
        > Depois de publicar, não é possível anular a publicação. 
    2. Para publicar mais tarde, selecione **Guardar para utilização posterior**. Você pode publicar a VM de modelo após a conclusão do assistente. Para obter detalhes sobre como configurar e publicar após a conclusão do assistente, consulte [a seção publicar o modelo](how-to-create-manage-template.md#publish-the-template-vm) no artigo [como gerenciar laboratórios de sala de aula](how-to-manage-classroom-labs.md) .

        ![Publicar modelo](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Verá o **progresso da publicação** do modelo. Este processo pode demorar até uma hora. 

    ![Publicar modelo – progresso](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Verá a página seguinte, quando o modelo for publicado com êxito. Selecione **Done** (Concluído).

    ![Publicar modelo – êxito](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Verá o **dashboard** para o seu laboratório. 
    
    ![Dashboard de laboratório de sala de aula](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Alterne para a página **máquinas virtuais** selecionando máquinas virtuais no menu à esquerda ou selecionando máquinas virtuais bloco. Confirme que você vê as máquinas virtuais que estão em estado não **atribuído** . Estas VMs ainda não estão atribuídas a estudantes. Elas devem estar no estado **Parado**. Pode iniciar uma VM de estudante, ligar à VM, parar a VM e eliminar a VM nesta página. Pode iniciá-las nesta página ou permitir que os seus estudantes iniciem as VMs. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="add-users-to-the-lab"></a>Adicionar usuários ao laboratório

1. Selecione **usuários** no menu à esquerda. Por padrão, a opção de **acesso restrito** está habilitada. Quando essa configuração estiver ativada, um usuário não poderá se registrar no laboratório, mesmo se o usuário tiver o link de registro, a menos que o usuário esteja na lista de usuários. Somente os usuários na lista podem se registrar no laboratório usando o link de registro que você envia. Neste procedimento, você adiciona usuários à lista. Como alternativa, você pode desativar o **acesso restrito**, que permite aos usuários se registrarem com o laboratório, desde que eles tenham o link de registro. 
2. Selecione **Adicionar usuários** na barra de ferramentas. 

    ![Botão Adicionar usuários](../media/how-to-configure-student-usage/add-users-button.png)
1. Na página **Adicionar usuários** , insira os endereços de email dos usuários em linhas separadas ou em uma única linha separada por ponto e vírgula. 

    ![Adicionar endereços de email do usuário](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selecione **Guardar**. Você vê os endereços de email dos usuários e seus status (registrados ou não) na lista. 

    ![Lista de usuários](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-for-users"></a>Definir cotas para usuários
Você pode definir cotas por usuário usando as seguintes etapas: 

1. Selecione **usuários** no menu à esquerda se a página ainda não estiver ativa. 
2. Selecionar **cota por usuário: 10 horas** na barra de ferramentas. 
3. Na página **cota por usuário** , especifique o número de horas que você deseja dar a cada usuário (aluno): 
    1. **Número total de horas de laboratório por usuário**. Os usuários podem usar suas VMs para o número definido de horas (especificado para esse campo) **além do horário agendado**. Se você selecionar essa opção, insira o **número de horas** na caixa de texto. 

        ![Número de horas por usuário](../media/how-to-configure-student-usage/number-of-hours-per-user.png). 
    1. **0 horas (somente agenda)** . Os usuários podem usar suas VMs somente durante o horário agendado ou quando você, à medida que o proprietário do laboratório ativar as VMs para elas.

        ![Zero horas-somente hora agendada](../media/how-to-configure-student-usage/zero-hours.png)
    4. Selecione **Guardar**. 
5. Você verá os valores alterados na barra de ferramentas agora: **Cota por usuário: &lt;número de horas&gt;** . 

    ![Cota por usuário](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="set-a-schedule-for-the-lab"></a>Definir um agendamento para o laboratório
Se você tiver configurado a configuração de cota para **0 hora (agenda apenas)** , deverá definir um agendamento para o laboratório. Neste tutorial, você define a agenda para ser uma agenda semanal recorrente.

1. Alterne para a  página agendas e selecione **Adicionar agenda** na barra de ferramentas. 

    ![Botão Adicionar agendamento na página agendas](../media/how-to-create-schedules/add-schedule-button.png)
2. Na página **Adicionar agenda** , alterne para **semanalmente** na parte superior. 
3. Para **agendar dias (obrigatório)** , selecione os dias nos quais você deseja que o agendamento entre em vigor. No exemplo a seguir, segunda-feira é selecionada. 
4. Para o campo **de** , insira a **data de início da agenda** ou escolha uma data selecionando o botão **calendário** . Este campo é obrigatório. 
5. Em **data de término do agendamento**, insira ou selecione uma data de término na qual as VMs devem ser desligadas. 
6. Para **hora de início**, selecione a hora em que você deseja que as VMs sejam iniciadas. A hora de início será necessária se a hora de parada não estiver definida. Selecione **remover evento de início** se desejar especificar apenas a hora de parada. se a **hora de início** estiver desabilitada, selecione **Adicionar evento de início** ao lado da lista suspensa para habilitá-la. 
7. Em **hora de parada**, selecione a hora em que você deseja que as VMs sejam desligadas. A hora de parada será necessária se a hora de início não estiver definida. Selecione **remover evento de parada** se desejar especificar apenas a hora de início. se a **hora de parada** estiver desabilitada, selecione **Adicionar evento de parada** ao lado da lista suspensa para habilitá-la.
8. Para **fuso horário (obrigatório)** , selecione o fuso horário para os horários de início e de término especificados.  
9. Para **observações**, insira qualquer descrição ou nota para o agendamento. 
10. Selecione **Guardar**. 

    ![Agenda semanal](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="send-an-email-with-the-registration-link"></a>Enviar um email com o link de registro

1. Alterne para a exibição de **usuários** se você ainda não estiver na página. 
2. Selecione específico ou todos os usuários na lista. Para selecionar usuários específicos, marque as caixas de seleção na primeira coluna da lista. Para selecionar todos os usuários, marque a caixa de seleção na frente do título da primeira coluna (**nome**) ou marque todas as caixas de seleção para todos os usuários na lista. Você pode ver o status do **estado do convite** nesta lista.  Na imagem a seguir, o estado do convite para todos os alunos está definido como **convite não enviado**. 

    ![Selecionar alunos](../media/tutorial-setup-classroom-lab/select-students.png)
1. Selecione o **ícone de email (envelope)** em uma das linhas (ou) selecione **Enviar convite** na barra de ferramentas. Você também pode passar o mouse sobre um nome de aluno na lista para ver o ícone de email. 

    ![Enviar link de registro por email](../media/tutorial-setup-classroom-lab/send-email.png)
4. Na página **Enviar link de registro por email** , siga estas etapas: 
    1. Digite uma **mensagem opcional** que você deseja enviar aos alunos. O email inclui automaticamente o link de registro. 
    2. Na página **Enviar link de registro por email** , selecione **Enviar**. Você vê o status da alteração de convite para **Enviar convite** e, em seguida, para o **convite enviado**. 
        
        ![Convites enviados](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, criou um laboratório de sala de aula e configurou o laboratório. Para saber como um aluno pode aceder a uma VM no laboratório através da ligação de registo, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Ligar a uma VM no laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)

