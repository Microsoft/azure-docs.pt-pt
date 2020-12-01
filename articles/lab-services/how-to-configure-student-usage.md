---
title: Configurar configurações de utilização em laboratórios de Serviços de Laboratório Azure
description: Aprenda a configurar o número de alunos para um laboratório, registei-os no laboratório, controle o número de horas que podem usar o VM, e muito mais.
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: e768c74d338cf21eb56660fe3790fc1f0f3ec80d
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434554"
---
# <a name="add-and-manage-lab-users"></a>Adicionar e gerir utilizadores de laboratório

Este artigo descreve como adicionar utilizadores estudantes a um laboratório, registá-los no laboratório, controlar o número de horas adicionais que podem usar a máquina virtual (VM), e muito mais. 

## <a name="add-users-to-a-lab"></a>Adicionar utilizadores a um laboratório

Nesta secção, adicione os alunos a um laboratório manualmente ou carregando um ficheiro CSV. Faça o seguinte:

1. No painel esquerdo, **selecione Utilizadores**. 

    Por padrão, a opção **de acesso Restrict** é ligada e, a menos que estejam na lista de utilizadores, os alunos não podem inscrever-se no laboratório mesmo que tenham um link de registo. Apenas os utilizadores listados podem registar-se no laboratório utilizando o link de registo que envia. Neste procedimento, adicione utilizadores à lista. Em alternativa, pode desligar **o acesso Restrict,** que permite aos alunos inscreverem-se no laboratório desde que tenham o link de registo. 

1. No topo do painel de **Utilizadores,** **selecione Adicionar utilizadores** e, em seguida, selecione **Adicionar por endereço de e-mail**. 

    ![O botão "Adicionar utilizadores"](./media/how-to-configure-student-usage/add-users-button.png)

1. No painel **de utilizadores Add,** insira os endereços de e-mail dos alunos em linhas separadas ou numa única linha separada por pontos de ligação. 

    ![Adicionar endereços de e-mail dos utilizadores](./media/how-to-configure-student-usage/add-users-email-addresses.png)

1. Selecione **Guardar**. 

    A lista apresenta os endereços de e-mail e os status dos utilizadores atuais, quer estejam registados no laboratório ou não. 

    ![Lista de utilizadores](./media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > Depois de os alunos estarem registados no laboratório, a lista mostra os seus nomes. O nome que é mostrado na lista é construído usando os primeiros e últimos nomes dos alunos no Diretório Ativo Azure. 

### <a name="add-users-by-uploading-a-csv-file"></a>Adicione os utilizadores carregando um ficheiro CSV

Também pode adicionar utilizadores carregando um ficheiro CSV que contém os seus endereços de e-mail. 

Um ficheiro de texto CSV é utilizado para armazenar dados tabulares separados por vírgula (CSV) (números e texto). Em vez de armazenar informação em campos de colunas (como em folhas de cálculo), um ficheiro CSV armazena informações separadas por vírgulas. Cada linha num ficheiro CSV terá o mesmo número de "campos" separados por vírgulas. Pode utilizar o Excel para criar e editar facilmente ficheiros CSV.

1. No Microsoft Excel, crie um ficheiro CSV que lista os endereços de e-mail dos alunos numa coluna.

    ![Lista de utilizadores num ficheiro CSV](./media/how-to-configure-student-usage/csv-file-with-users.png)

1. Na parte superior do painel de **Utilizadores,** **selecione Adicionar utilizadores** e, em seguida, selecione **Upload CSV**.

    ![O botão "Upload CSV"](./media/how-to-configure-student-usage/upload-csv-button.png)

1. Selecione o ficheiro CSV que contém os endereços de e-mail dos alunos e, em seguida, selecione **Open**.

    A janela **'Adicionar' apresenta** a lista de endereços de e-mail a partir do ficheiro CSV. 

    ![A janela "Adicionar utilizadores" com endereços de e-mail do ficheiro CSV](./media/how-to-configure-student-usage/add-users-window.png)

1. Selecione **Guardar**. 

1. No painel **de Utilizadores,** veja a lista de alunos adicionados. 

    ![Lista de utilizadores adicionados no painel "Utilizadores"](./media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Enviar convites aos utilizadores

Para enviar um link de registo para novos utilizadores, utilize um dos seguintes métodos. 

Se a opção **de acesso Restrict** estiver ativada para o laboratório, apenas os utilizadores listados podem utilizar o link de registo para se registarem no laboratório. Por predefinição, esta opção encontra-se ativada. 

### <a name="invite-all-users"></a>Convidar todos os utilizadores

Este método mostra-lhe como enviar e-mail com um link de registo e uma mensagem opcional para todos os alunos listados.

1. No painel **de Utilizadores,** selecione **Convidar todos**. 

    ![O botão "Convidar todos"](./media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Na janela **de envio por e-mail,** introduza uma mensagem opcional e, em seguida, selecione **Enviar**. 

    O e-mail inclui automaticamente o link de registo. Para obter e guardar o link de registo separadamente, selecione a elipse **(...**) no topo do painel de **Utilizadores** e, em seguida, selecione **o link de registo**. 

    ![A janela "Enviar link de registo por e-mail"](./media/tutorial-setup-classroom-lab/send-email.png)

    A coluna **Convite** da lista **de Utilizadores** apresenta o estado do convite para cada utilizador adicionado. O estado deve alterar-se para **Enviar** e, em seguida, para **Enviar em \<date>**. 

### <a name="invite-selected-users"></a>Convidar utilizadores selecionados

Este método mostra-lhe como convidar apenas certos alunos e obter um link de inscrição que você pode compartilhar com outras pessoas.

1. No painel **de Utilizadores,** selecione um aluno ou vários alunos da lista. 

1. Na linha para o aluno que selecionou, selecione o ícone do **envelope** ou, na barra de ferramentas, selecione **Convidar**. 

    ![Convidar utilizadores selecionados](./media/how-to-configure-student-usage/invite-selected-users.png)

1. Na janela **de envio por e-mail,** introduza uma **mensagem** opcional e, em seguida, selecione **Enviar**. 

    ![Enviar e-mail para utilizadores selecionados](./media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    O **painel de Utilizadores** apresenta o estado desta operação na coluna **Convite** da tabela. O e-mail de convite inclui o link de inscrição que os alunos podem usar para se inscreverem no laboratório.

## <a name="get-the-registration-link"></a>Obtenha o link de registo

Nesta secção, pode obter o link de registo do portal e enviá-lo utilizando a sua própria aplicação de e-mail. 

1. No painel **de Utilizadores,** selecione **o link de registo.**

    ![Ligação de registo do aluno](./media/how-to-configure-student-usage/registration-link-button.png)

1. Na janela **de registo do Utilizador,** selecione **Copy** e, em seguida, selecione 'Fazer' ( 'Copiar' e, em seguida, selecione **'Fazer'.** 

    ![A janela de registo do utilizador](./media/how-to-configure-student-usage/registration-link.png)

    A ligação é copiada para a área de transferência. 
    
1. No seu pedido de e-mail, cole o link de inscrição e, em seguida, envie o e-mail para um aluno para que o aluno possa inscrever-se para a aula. 

## <a name="view-registered-users"></a>Ver utilizadores registados

1. Vá ao site da [Azure Lab Services.](https://labs.azure.com) 
1. Selecione **Iniciar sômin** e, em seguida, insira as suas credenciais. O Azure Lab Services suporta contas organizacionais e contas Microsoft.
1. Na página **dos meus laboratórios,** selecione o laboratório cujo uso quer rastrear. 
1. No painel esquerdo, selecione **Utilizadores,** ou selecione o azulejo **do Utilizadores.** 

    O painel **de utilizadores** apresenta uma lista de alunos que se registaram no seu laboratório.  

    ![Lista de utilizadores registados](./media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Definir quotas para utilizadores

Pode definir uma quota de hora para cada aluno fazendo o seguinte: 

1. No painel **de Utilizadores,** selecione **Quota por utilizador: \<number> hora(s)** na barra de ferramentas. 
1. Na **quota por** janela do utilizador, especifique o número de horas que pretende dar a cada aluno fora do horário de aula programado e, em seguida, selecione **Save**.

    ![A janela "Quota por utilizador"](./media/how-to-configure-student-usage/quota-per-user.png)    

    Os valores alterados são agora apresentados na **Quota por utilizador: \<number of hours>** botão na barra de ferramentas e na lista de utilizadores, como mostrado aqui:

    ![Horário de quota por utilizador](./media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > O [tempo de funcionamento programado dos VM não](how-to-create-schedules.md) conta com a quota atribuída a um aluno. A quota é para o tempo fora das horas programadas que um aluno gasta em VMs. 

## <a name="set-additional-quotas-for-specific-users"></a>Definir quotas adicionais para utilizadores específicos

Pode especificar quotas para determinados estudantes para além das quotas comuns fixadas para todos os utilizadores na secção anterior. Por exemplo, se você, como instrutor, definir a quota para todos os alunos para 10 horas e definir uma quota adicional de 5 horas para um aluno específico, esse aluno recebe 15 (10 + 5) horas de quota. Se alterar a quota comum mais tarde para, digamos, 15, o aluno recebe 20 (15 + 5) horas de quota. Lembre-se que esta quota global está fora do horário previsto. O tempo que um aluno passa num laboratório VM durante o horário programado não conta contra esta quota. 

Para fixar quotas adicionais, faça o seguinte:

1. No painel **de Utilizadores,** selecione um aluno da lista e, em seguida, selecione Ajustar a **quota** na barra de ferramentas. 

    ![O botão "Ajustar quota"](./media/how-to-configure-student-usage/adjust-quota-button.png)

1. Na **quota ajustar \<selected user or users email address> para**, insira o número de horas de laboratório adicionais que pretende conceder ao aluno ou estudantes selecionados e, em seguida, selecione **Aplicar**. 

    ![A "Quota de Ajuste..." janela](./media/how-to-configure-student-usage/additional-quota.png)

    A coluna **Utilização** apresenta a quota atualizada para os alunos selecionados. 

    ![Nova utilização para o utilizador](./media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Contas de estudantes

Para adicionar alunos a um laboratório de sala de aula, você usa as suas contas de e-mail. Os alunos podem ter os seguintes tipos de contas de e-mail:

- Uma conta de e-mail estudantil fornecida pelo azure ative directory da sua universidade.
- Uma conta de e-mail de domínio microsoft, como *outlook.com,* *hotmail.com,* *msn.com* ou *live.com*.
- Uma conta de e-mail não-Microsoft, como a fornecida pela Yahoo! ou Google. No entanto, este tipo de contas deve estar ligado a uma conta microsoft.
- Uma conta do GitHub. Esta conta deve estar ligada a uma conta microsoft.

### <a name="use-a-non-microsoft-email-account"></a>Utilize uma conta de e-mail não Microsoft

Os estudantes podem usar contas de e-mail não-Microsoft para se registarem e iniciarem singurção num laboratório de sala de aula.  No entanto, o registo requer que criem primeiro uma conta Microsoft que esteja ligada ao seu endereço de e-mail não Microsoft.

Muitos estudantes podem já ter uma conta Microsoft que está ligada ao seu endereço de e-mail não-Microsoft. Por exemplo, os estudantes já têm uma conta Microsoft se utilizaram o seu endereço de e-mail com outros produtos ou serviços da Microsoft, tais como Office, Skype, OneDrive ou Windows.  

Quando os alunos usam o link de inscrição para iniciar singing em uma sala de aula, eles são solicitados para o seu endereço de e-mail e senha. Os estudantes que tentarem entrar com uma conta não-Microsoft que não esteja ligada a uma conta da Microsoft receberão a seguinte mensagem de erro: 

![Mensagem de erro no início de s inscrever](./media/how-to-configure-student-usage/cant-find-account.png)

Aqui está um link para os estudantes [se inscreverem para uma conta microsoft](http://signup.live.com).  

> [!IMPORTANT]
> Quando os alunos se inscrevem num laboratório de sala de aula, não lhes é dada a opção de criar uma conta microsoft. Por esta razão, recomendamos que inclua este link de http://signup.live.com inscrição, no e-mail de registo do laboratório de sala de aula que envia para estudantes que estão usando contas não Microsoft.

### <a name="use-a-github-account"></a>Use uma conta GitHub

Os alunos também podem usar uma conta GitHub existente para se registarem e se inscreverem num laboratório de sala de aula. Se já tiverem uma conta Microsoft ligada à sua conta GitHub, os estudantes podem iniciar scontabilidade e fornecer a sua palavra-passe como mostrado na secção anterior. 

Se ainda não ligaram a sua conta GitHub a uma conta da Microsoft, podem fazer o seguinte:

1. Selecione o link **de opções de inscrição,** como mostrado aqui:

    ![O link "Opções de Inscrição"](./media/how-to-configure-student-usage/signin-options.png)

1. Na janela **de opções de inscrição,** selecione **Iniciar sôs com o GitHub**.

    ![O link "Iniciar com GitHub"](./media/how-to-configure-student-usage/signin-github.png)

    A pedido, os estudantes criam então uma conta Microsoft que está ligada à sua conta GitHub. A ligação acontece automaticamente quando selecionam **Next**. Depois, são imediatamente assinados e ligados ao laboratório da sala de aula.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Exportar uma lista de utilizadores para um ficheiro CSV

1. Vá ao painel **de utilizadores.**
1. Na barra de ferramentas, selecione a elipse **(...**) e, em seguida, selecione **Export CSV**. 

    ![O botão "Exportação CSV"](./media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos:

- Para administradores: [Criar e gerir contas de laboratório](how-to-manage-lab-accounts.md)
- Para os proprietários de laboratórios: [Criar e gerir laboratórios](how-to-manage-classroom-labs.md) e [configurar e publicar modelos](how-to-create-manage-template.md)
- Para utilizadores de laboratório: [Laboratórios de acesso](how-to-use-classroom-lab.md)
