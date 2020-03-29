---
title: Configure as definições de utilização em laboratórios de sala de aula de Serviços de Laboratório Azure
description: Aprenda a configurar o número de alunos para um laboratório, registe-os no laboratório, controle o número de horas que podem usar o VM, e muito mais.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 2697bed77263bb5b8349898765851a9b87992279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159460"
---
# <a name="add-and-manage-lab-users"></a>Adicionar e gerir utilizadores de laboratório

Este artigo descreve como adicionar utilizadores de estudantes a um laboratório, registá-los no laboratório, controlar o número de horas adicionais que podem usar a máquina virtual (VM), e muito mais. 

## <a name="add-users-to-a-lab"></a>Adicione utilizadores a um laboratório

Nesta secção, você adiciona os alunos a um laboratório manualmente ou fazendo o upload de um ficheiro CSV. Faça o seguinte:

1. No painel esquerdo, selecione **Utilizadores**. 

    Por padrão, a opção de **acesso Restrito** é ativada e, a menos que estejam na lista de utilizadores, os alunos não podem inscrever-se no laboratório mesmo que tenham uma ligação de registo. Apenas os utilizadores listados podem registar-se no laboratório utilizando o link de registo que envia. Neste procedimento, adiciona os utilizadores à lista. Em alternativa, pode desligar o **acesso restrito,** o que permite aos alunos inscreverem-se no laboratório desde que tenham o link de inscrição. 

1. Na parte superior do painel dos **Utilizadores,** selecione **Adicionar utilizadores**e, em seguida, selecione Adicionar por endereço de **e-mail**. 

    ![O botão "Adicionar utilizadores"](../media/how-to-configure-student-usage/add-users-button.png)

1. No painel **de utilizadores add,** insira os endereços de e-mail dos alunos em linhas separadas ou numa única linha separada por pontos evícolas. 

    ![Adicionar endereços de e-mail dos utilizadores](../media/how-to-configure-student-usage/add-users-email-addresses.png)

1. Selecione **Guardar**. 

    A lista apresenta os endereços de e-mail e os estados dos utilizadores atuais, independentemente de estarem registados no laboratório ou não. 

    ![Lista de utilizadores](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > Depois de os alunos estarem registados no laboratório, a lista mostra os seus nomes. O nome que está mostrado na lista é construído usando os primeiros e últimos nomes dos alunos em Diretório Ativo Azure. 

### <a name="add-users-by-uploading-a-csv-file"></a>Adicione os utilizadores fazendo o upload de um ficheiro CSV

Também pode adicionar utilizadores fazendo o upload de um ficheiro CSV que contém os seus endereços de e-mail.

1. No Microsoft Excel, crie um ficheiro CSV que lista os endereços de e-mail dos alunos numa coluna.

    ![Lista de utilizadores num ficheiro CSV](../media/how-to-configure-student-usage/csv-file-with-users.png)

1. Na parte superior do painel **dos Utilizadores,** selecione **Adicionar utilizadores**e, em seguida, selecione **'Upload CSV**' .

    ![O botão "Upload CSV"](../media/how-to-configure-student-usage/upload-csv-button.png)

1. Selecione o ficheiro CSV que contém os endereços de e-mail dos alunos e, em seguida, selecione **Open**.

    A janela **Adicionar utilizadores** exibe a lista de endereços de e-mail do ficheiro CSV. 

    ![A janela "Adicionar utilizadores" com endereços de e-mail do ficheiro CSV](../media/how-to-configure-student-usage/add-users-window.png)

1. Selecione **Guardar**. 

1. No painel **dos Utilizadores,** veja a lista de alunos adicionados. 

    ![Lista de utilizadores adicionados no painel "Utilizadores"](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Enviar convites aos utilizadores

Para enviar uma ligação de registo a novos utilizadores, utilize um dos seguintes métodos. 

Se a opção de **acesso Restringir** estiver ativada para o laboratório, apenas os utilizadores listados podem utilizar a ligação de registo para se registarem no laboratório. Por predefinição, esta opção encontra-se ativada. 

### <a name="invite-all-users"></a>Convide todos os utilizadores

Este método mostra-lhe como enviar e-mail com um link de inscrição e uma mensagem opcional para todos os alunos listados.

1. No painel **utilizadores,** selecione **Convidar todos**. 

    ![O botão "Convidar todos"](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. No convite Enviar por janela de **e-mail,** introduza uma mensagem opcional e, em seguida, selecione **Enviar**. 

    O e-mail inclui automaticamente o link de registo. Para obter e guardar o link de registo separadamente, selecione a elipse (**...**) na parte superior do painel **dos Utilizadores** e, em seguida, selecione o link **de registo**. 

    ![A janela "Enviar link de registo por e-mail"](../media/tutorial-setup-classroom-lab/send-email.png)

    A coluna **Convite** da lista **de Utilizadores** apresenta o estado do convite para cada utilizador adicionado. O estado deve alterar-se para **enviar** e, em seguida, para **enviar na \<data>**. 

### <a name="invite-selected-users"></a>Convidar utilizadores selecionados

Este método mostra-lhe como convidar apenas certos alunos e obter um link de inscrição que pode partilhar com outras pessoas.

1. No painel **de Utilizadores,** selecione um aluno ou vários alunos na lista. 

1. Na fila para o aluno selecionado, selecione o ícone do **envelope** ou, na barra de ferramentas, selecione **Convidar**. 

    ![Convidar utilizadores selecionados](../media/how-to-configure-student-usage/invite-selected-users.png)

1. No convite Enviar por janela de **e-mail,** introduza uma **mensagem**opcional, e, em seguida, selecione **Enviar**. 

    ![Enviar e-mail para utilizadores selecionados](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    O painel **dos Utilizadores** apresenta o estado desta operação na coluna **Convite** da tabela. O e-mail de convite inclui o link de inscrição que os alunos podem usar para se inscreverem no laboratório.

## <a name="get-the-registration-link"></a>Obtenha o link de registo

Nesta secção, pode obter o link de registo do portal e enviá-lo utilizando a sua própria aplicação de e-mail. 

1. No painel **utilizadores,** selecione **link de registo**.

    ![Ligação de registo do aluno](../media/how-to-configure-student-usage/registration-link-button.png)

1. Na janela de registo do **Utilizador,** selecione **Copiar**, e, em seguida, selecione **Done**. 

    ![A janela "Registo do Utilizador"](../media/how-to-configure-student-usage/registration-link.png)

    A ligação é copiada para a área de transferência. 
    
1. No seu pedido de e-mail, colhe o link de inscrição e, em seguida, envie o e-mail a um aluno para que o aluno possa inscrever-se para a aula. 

## <a name="view-registered-users"></a>Ver utilizadores registados

1. Vá ao site da [Azure Lab Services.](https://labs.azure.com) 
1. Selecione **Iniciar sessão**e, em seguida, insira as suas credenciais. O Azure Lab Services suporta contas organizacionais e contas Microsoft.
1. Na página dos **meus laboratórios,** selecione o laboratório cujo uso quer rastrear. 
1. No painel esquerdo, selecione **Utilizadores,** ou selecione o azulejo **utilizadores.** 

    O painel **dos Utilizadores** apresenta uma lista de alunos que se registaram no seu laboratório.  

    ![Lista de utilizadores registados](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Definir quotas para utilizadores

Pode definir uma quota de hora para cada aluno fazendo o seguinte: 

1. No painel **utilizadores,** selecione **Quota por utilizador: \<número> hora na** barra de ferramentas. 
1. Na **quota por janela do utilizador,** especifique o número de horas que pretende dar a cada aluno fora da hora de aula programada e, em seguida, selecione **Guardar**.

    ![A janela "Quota por utilizador"](../media/how-to-configure-student-usage/quota-per-user.png)    

    Os valores alterados são agora apresentados na **Quota por utilizador: \<número de horas>** botão na barra de ferramentas e na lista de utilizadores, como mostrado aqui:

    ![Horas de quota por utilizador](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > O [tempo programado de vms](how-to-create-schedules.md) não conta com a quota atribuída a um aluno. A quota é para o tempo fora do horário programado que um aluno gasta em VMs. 

## <a name="set-additional-quotas-for-specific-users"></a>Definir quotas adicionais para utilizadores específicos

Pode especificar quotas para determinados alunos para além das quotas comuns que foram definidas para todos os utilizadores na secção anterior. Por exemplo, se você, como instrutor, definir a quota para todos os alunos para 10 horas e definir uma quota adicional de 5 horas para um aluno específico, esse aluno recebe 15 (10 + 5) horas de quota. Se mudar a quota comum mais tarde para, digamos, 15, o aluno recebe 20 (15 + 5) horas de quota. Lembre-se que esta quota global está fora do horário previsto. O tempo que um aluno passa num VM de laboratório durante o tempo programado não conta com esta quota. 

Para definir quotas adicionais, faça o seguinte:

1. No painel **utilizadores,** selecione um aluno da lista e, em seguida, selecione Ajustar a **quota** na barra de ferramentas. 

    ![O botão "Ajustar a quota"](../media/how-to-configure-student-usage/adjust-quota-button.png)

1. Na **quota Ajustar \<para o endereço de e-mail selecionado **para utilizadores ou utilizadores>, insira o número de horas de laboratório adicionais que pretende conceder ao aluno ou aluno selecionadoe e, em seguida, selecione **Apply**. 

    ![A "Quota de Ajuste..." janela](../media/how-to-configure-student-usage/additional-quota.png)

    A coluna **Utilização** apresenta a quota atualizada para os alunos selecionados. 

    ![Nova utilização para o utilizador](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Contas de estudantes

Para adicionar alunos a um laboratório de sala de aula, usa as suas contas de e-mail. Os alunos podem ter os seguintes tipos de contas de e-mail:

- Uma conta de e-mail estudantil fornecida pelo Diretório Ativo Azure da sua universidade para o Office 365. 
- Uma conta de e-mail de domínio Microsoft, como *outlook.com,* *hotmail.com,* *msn.com*ou *live.com.*
- Uma conta de e-mail não-Microsoft, como a fornecida pela Yahoo! ou Google. No entanto, este tipo de contas deve estar ligada a uma conta Microsoft.
- Uma conta do GitHub. Esta conta deve estar ligada a uma conta Microsoft.

### <a name="use-a-non-microsoft-email-account"></a>Utilize uma conta de e-mail não Microsoft
Os alunos podem usar contas de e-mail não microsoft para se registarem e iniciarem sessão num laboratório de sala de aula.  No entanto, o registo requer que criem primeiro uma conta Microsoft que esteja ligada ao seu endereço de e-mail não Microsoft.

Muitos estudantes podem já ter uma conta Microsoft ligada ao seu endereço de e-mail não Microsoft. Por exemplo, os estudantes já têm uma conta Microsoft se usaram o seu endereço de e-mail com outros produtos ou serviços da Microsoft, tais como Office, Skype, OneDrive ou Windows.  

Quando os alunos usam o link de inscrição para iniciar sessão numa sala de aula, são solicitados para o seu endereço de e-mail e senha. Os estudantes que tentarem iniciar sessão com uma conta não Microsoft que não esteja ligada a uma conta microsoft receberão a seguinte mensagem de erro: 

![Mensagem de erro no início do sessão](../media/how-to-configure-student-usage/cant-find-account.png)

Aqui está um link para os estudantes [se inscreverem para uma conta Microsoft](http://signup.live.com).  

> [!IMPORTANT]
> Quando os alunos se inscrevem num laboratório de sala de aula, não lhes é dada a opção de criar uma conta microsoft. Por esta razão, recomendamos que inclua http://signup.live.comeste link de inscrição, no e-mail de inscrição do laboratório em sala de aula que envia a estudantes que estão a usar contas não Microsoft.

### <a name="use-a-github-account"></a>Use uma conta GitHub
Os alunos também podem usar uma conta GitHub existente para se inscrever e iniciar sessão num laboratório de sala de aula. Se já tiverem uma conta Microsoft ligada à sua conta GitHub, os alunos podem iniciar sessão e fornecer a sua palavra-passe, como mostra a secção anterior. 

Se ainda não ligaram a sua conta GitHub a uma conta microsoft, podem fazer o seguinte:

1. Selecione o link de **opções de iniciar sessão,** como mostrado aqui:

    ![O link "Opções de insessão"](../media/how-to-configure-student-usage/signin-options.png)

1. Na janela **opções de iniciar** sessão, selecione **Iniciar sessão com o GitHub**.

    ![O link "Iniciar sessão com gitHub"](../media/how-to-configure-student-usage/signin-github.png)

    No momento, os estudantes criam então uma conta Microsoft que está ligada à sua conta GitHub. A ligação acontece automaticamente quando selecionam **Next**. São imediatamente assinados e ligados ao laboratório da sala de aula.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Exportar uma lista de utilizadores para um ficheiro CSV

1. Vá ao painel **dos Utilizadores.**
1. Na barra de ferramentas, selecione a elipse (**...**) e, em seguida, selecione **Export CSV**. 

    ![O botão "Export CSV"](../media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- Para administradores: [Criar e gerir contas](how-to-manage-lab-accounts.md) de laboratório
- Para os donos de [laboratório: Crie e gerencie laboratórios](how-to-manage-classroom-labs.md) e [instale e publique modelos](how-to-create-manage-template.md)
- Para utilizadores de laboratório: [Aceder a laboratórios de salas de aula](how-to-use-classroom-lab.md)
