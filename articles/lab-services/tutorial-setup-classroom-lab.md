---
title: Configurar um laboratório de sala de aula com o Azure Lab Services | Microsoft Docs
description: Neste tutorial, você usa a Azure Lab Services para criar um laboratório de sala de aula com máquinas virtuais que são usadas por alunos da sua turma.
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: 3abbf5221382b46dbf4e73f9f4dc3b639bc5ecbd
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602519"
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

    Para obter mais informações sobre a criação e gestão de horários para uma aula, consulte [Criar e gerir a programação para laboratórios.](how-to-create-schedules.md)


## <a name="add-users-to-the-lab"></a>Adicionar utilizadores ao laboratório

Quando adiciona os utilizadores, por padrão, a opção **de acesso Restrict** é ligada e, a menos que estejam na lista de utilizadores, os alunos não podem inscrever-se no laboratório mesmo que tenham um link de registo. Apenas os utilizadores listados podem registar-se no laboratório utilizando o link de registo que envia. Pode desligar **o acesso Restrict,** que permite aos alunos inscreverem-se no laboratório desde que tenham o link de registo. 

### <a name="add-users-from-an-azure-ad-group"></a>Adicionar utilizadores de um grupo AD Azure

Pode sincronizar uma lista de utilizadores de laboratório com um grupo existente do Azure Ative Directory (Azure AD) para que não tenha de adicionar ou eliminar manualmente os utilizadores. 

Um grupo Azure AD pode ser criado dentro do Azure Ative Directory da sua organização para gerir o acesso a recursos organizacionais e aplicações baseadas na nuvem. Para saber mais, consulte [os grupos AD Azure.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups) Se a sua organização utilizar os serviços Microsoft Office 365 ou Azure, a sua organização já terá administradores que gerem o seu Azure Ative Directory. 

> [!IMPORTANT]
> Certifique-se de que a lista de utilizadores está vazia. Se houver utilizadores existentes dentro de um laboratório que adicionou manualmente ou através da importação de um ficheiro CSV, a opção de sincronizar o laboratório a um grupo existente não aparecerá. 

1. No painel esquerdo, **selecione Utilizadores**. 
1. Clique **em Sync a partir do grupo**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-sync-group.png" alt-text="Adicione os utilizadores sincronizando a partir de um grupo AD Azure":::
    
1. Será solicitado a escolher um grupo AD Azure existente para sincronizar o seu laboratório. 
    
    Se não vir um grupo AD Azure na lista, pode ser devido às seguintes razões:

    -   Se você é um utilizador convidado para um Azure Ative Directory (normalmente se você está fora da organização que detém o AD Azure), e você não é capaz de procurar grupos dentro do Azure AD. Neste caso, não poderá adicionar um grupo AD Azure ao laboratório neste caso. 
    -   Os grupos AD AZure criados através de Equipas não aparecem nesta lista. Pode adicionar a app Azure Lab Services dentro de Equipas para criar e gerir laboratórios diretamente a partir do mesmo. Consulte mais informações sobre [a gestão da lista de utilizadores de um laboratório dentro de Equipas](how-to-manage-user-lists-within-teams.md). 
1. Uma vez que escolheu o grupo AD Azure para sincronizar o seu laboratório para, clique em **Adicionar**.
1. Assim que um laboratório estiver sincronizado, irá puxar todos dentro do grupo AD Azure para o laboratório como utilizadores, e verá a lista de utilizadores atualizada. Só as pessoas deste grupo AD Azure terão acesso ao seu laboratório. A lista de utilizadores será atualizada a cada 24 horas para corresponder à mais recente adesão do grupo AZURE AD. Também pode clicar no botão Sync no separador Utilizadores para sincronizar manualmente as alterações mais recentes do grupo AD Azure.
1. Convide os utilizadores para o seu laboratório clicando no botão **Convidar Todos,** que enviará um e-mail a todos os utilizadores com o link de registo para o laboratório. 

### <a name="add-users-manually-from-emails-or-csv-file"></a>Adicione os utilizadores manualmente a partir de e-mails ou ficheiros CSV

Nesta secção, adicione os alunos manualmente (por endereço de e-mail ou carregando um ficheiro CSV). 

#### <a name="add-users-by-email-address"></a>Adicionar utilizadores por endereço de e-mail

1. No painel esquerdo, **selecione Utilizadores**. 
1. Clique **em Adicionar os utilizadores manualmente**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-manually.png" alt-text="Adicione os utilizadores manualmente":::
1. **Selecione Adicionar por endereço de e-mail** (predefinição), insira os endereços de e-mail dos alunos em linhas separadas ou numa única linha separada por pontos de ligação. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-email-addresses.png" alt-text="Adicionar endereços de e-mail dos utilizadores":::
1. Selecione **Guardar**. 

    A lista apresenta os endereços de e-mail e os status dos utilizadores atuais, quer estejam registados no laboratório ou não. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="Lista de utilizadores":::

    > [!NOTE]
    > Depois de os alunos estarem registados no laboratório, a lista mostra os seus nomes. O nome que é mostrado na lista é construído usando os primeiros e últimos nomes dos alunos no Diretório Ativo Azure. 

#### <a name="add-users-by-uploading-a-csv-file"></a>Adicione os utilizadores carregando um ficheiro CSV

Também pode adicionar utilizadores carregando um ficheiro CSV que contém os seus endereços de e-mail. 

Um ficheiro de texto CSV é utilizado para armazenar dados tabulares separados por vírgula (CSV) (números e texto). Em vez de armazenar informação em campos de colunas (como em folhas de cálculo), um ficheiro CSV armazena informações separadas por vírgulas. Cada linha num ficheiro CSV terá o mesmo número de "campos" separados por vírgulas. Pode utilizar o Excel para criar e editar facilmente ficheiros CSV.

1. No Microsoft Excel, crie um ficheiro CSV que lista os endereços de e-mail dos alunos numa coluna.

    :::image type="content" source="./media/how-to-configure-student-usage/csv-file-with-users.png" alt-text="Lista de utilizadores num ficheiro CSV":::
1. Na parte superior do painel de **Utilizadores,** **selecione Adicionar utilizadores** e, em seguida, selecione **Upload CSV**.
1. Selecione o ficheiro CSV que contém os endereços de e-mail dos alunos e, em seguida, selecione **Open**.

    A janela **'Adicionar' apresenta** a lista de endereços de e-mail a partir do ficheiro CSV. 
1. Selecione **Guardar**. 
1. No painel **de Utilizadores,** veja a lista de alunos adicionados. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="Lista de utilizadores adicionados no painel de Utilizadores"::: 

## <a name="send-invitation-emails-to-users"></a>Enviar e-mails de convite para utilizadores

1. Mude para a visualização **do Utilizadores** se ainda não estiver na página e selecione **Convidar todos** na barra de ferramentas. 

    ![Selecione alunos](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. Na página de **envio por e-mail,** introduza uma mensagem opcional e, em seguida, selecione **Enviar**. O e-mail inclui automaticamente o link de registo. Pode obter este link de registo selecionando **... (elipse)** na barra de ferramentas e **no link de registo.** 

    ![Enviar link de registo por e-mail](./media/tutorial-setup-classroom-lab/send-email.png)
4. Vê o estado do **convite** na lista **de Utilizadores.** O estado deve ser alterado para **Enviar** e, em seguida, para **Enviar na &lt; &gt; data**. 

Para obter mais informações sobre a adição de alunos a uma aula e gerir o seu uso do laboratório, consulte [Como configurar o uso dos alunos.](how-to-configure-student-usage.md)

## <a name="next-steps"></a>Próximos passos
Neste tutorial, criaste um laboratório para a tua aula em Azure. Para saber como um aluno pode aceder a uma VM no laboratório através da ligação de registo, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Ligar a uma VM no laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)

