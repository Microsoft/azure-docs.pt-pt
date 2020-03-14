---
title: Configure as definições de utilização em laboratórios de sala de aula de Serviços de Laboratório Azure
description: Saiba configurar o número de utilizadores para o laboratório, registá-los no laboratório, controlar o número de horas que podem usar o VM, e muito mais.
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
ms.openlocfilehash: daa302e9be026157b464a12998f3faa52526aba6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270943"
---
# <a name="add-and-manage-lab-users"></a>Adicionar e gerir utilizadores de laboratório
Este artigo descreve como adicionar utilizadores ao laboratório, registrá-los no laboratório, controlar o número de horas que podem usar o VM, e muito mais. 


## <a name="add-users-to-the-lab"></a>Adicione utilizadores ao laboratório

1. Selecione **Utilizadores** no menu esquerdo. Por predefinição, a opção de **acesso Restrict** está ativada. Quando esta definição está aberta, um utilizador não pode registar-se no laboratório mesmo que o utilizador tenha o link de registo, a menos que o utilizador esteja na lista de utilizadores. Apenas os utilizadores da lista podem registar-se no laboratório utilizando o link de registo que envia. Neste procedimento, adiciona os utilizadores à lista. Em alternativa, pode desligar o **acesso restrito,** o que permite aos utilizadores registarem-se no laboratório desde que tenham a ligação de registo. 
2. Selecione **Adicionar utilizadores** na barra de ferramentas e, em seguida, selecione **Adicionar por endereços de e-mail**. 

    ![Adicione o botão dos utilizadores](../media/how-to-configure-student-usage/add-users-button.png)
1. Na página **adicionar utilizadores,** introduza endereços de e-mail dos utilizadores em linhas separadas ou numa única linha separada por pontos evíocos. 

    ![Adicionar endereços de e-mail do utilizador](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selecione **Guardar**. Veja os endereços de e-mail dos utilizadores e os seus status (registados ou não) na lista. 

    ![Lista de utilizadores](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > Verá os nomes dos utilizadores na lista depois de estarem registados no laboratório. O nome mostrado na lista é construído utilizando os primeiros e últimos nomes do utilizador no Diretório Ativo Azure. 

### <a name="add-users-by-uploading-a-csv-file"></a>Adicione os utilizadores fazendo o upload de um ficheiro CSV
Também pode adicionar utilizadores fazendo o upload de um ficheiro CSV com endereços de e-mail dos utilizadores.

1. Crie um ficheiro CSV com endereços de e-mail dos utilizadores numa coluna.

    ![Ficheiro CSV com utilizadores](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Na página **utilizadores** do laboratório, selecione **Adicionar utilizadores** na barra de ferramentas e, em seguida, selecione **Upload CSV**.

    ![Carregar botão CSV](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Selecione o ficheiro CSV com endereços de e-mail do utilizador. Quando selecionar **O Open** depois de selecionar o ficheiro CSV, consulte a seguinte janela de adicionar **utilizadores.** A lista de endereços de e-mail está preenchida com endereços de e-mail do ficheiro CSV. 

    ![Adicione a janela dos utilizadores povoada com endereços de e-mail do ficheiro CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. Selecione **Guardar** na janela **adicionar utilizadores.** 
5. Confirme que vê utilizadores na lista de utilizadores. 

    ![Lista de utilizadores adicionados](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Enviar convites aos utilizadores
Para enviar o link de inscrição para os alunos, utilize um dos seguintes métodos. O primeiro método mostra como enviar e-mails aos alunos com o link de inscrição e uma mensagem opcional. O segundo método mostra-lhe como obter o link de registo que pode partilhar com os outros da forma que quiser. 

Se o **acesso Restrito** estiver ativado para o laboratório, apenas os utilizadores na lista de utilizadores podem utilizar o link de registo para se registarem no laboratório. Esta opção é ativada por defeito. 

### <a name="invite-all-users"></a>Convide todos os utilizadores
1. Mude para a vista **utilizadores** se ainda não estiver na página e selecione **Convidar tudo** na barra de ferramentas. 

    ![Selecione alunos](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. No convite Enviar por página de **e-mail,** introduza uma mensagem opcional e, em seguida, selecione **Enviar**. O e-mail inclui automaticamente o link de registo. Pode obter este link de registo **selecionando... (elipse)** na barra de ferramentas e **no link de registo.** 

    ![Enviar link de registo por e-mail](../media/tutorial-setup-classroom-lab/send-email.png)
4. Vê o estado do **convite** na lista de **Utilizadores.** O estado deve alterar-se para **Envio** e, em seguida, para **Enviado em \<data>** 

    Para obter mais informações sobre a adição de alunos a uma aula e a gestão do seu uso do laboratório, consulte [como configurar o uso](how-to-configure-student-usage.md)do aluno.

### <a name="invite-selected-users"></a>Convidar utilizadores selecionados

1. Selecione um utilizador ou vários utilizadores na lista. 
2. Em seguida, selecione o ícone do **envelope** que vê na linha selecionada (ou) selecione **Convidar** na barra de ferramentas. 

    ![Convidar utilizadores selecionados](../media/how-to-configure-student-usage/invite-selected-users.png)
3. No convite Enviar por janela de **e-mail,** introduza uma **mensagem**opcional, e, em seguida, selecione **Enviar**. 

    ![Enviar e-mail para utilizadores selecionados](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Vê o estado desta operação na coluna **Convite** da lista **de Utilizadores.** O e-mail de convite inclui o link de registo que os utilizadores podem usar para se registarem no laboratório.

1. Mude para a vista **dos Utilizadores** se ainda não estiver na página. 

## <a name="get-registration-link"></a>Obter link de registo
Também pode obter o link de registo do portal e enviá-lo usando a sua própria aplicação de cliente de e-mail. 

1. Mude para a vista **Utilizadores** selecionando **utilizadores** no menu esquerdo. 
2. Selecione **... (elipse)** na barra de ferramentas e, em seguida, selecione o **link de registo**.

    ![Ligação de registo do aluno](../media/how-to-configure-student-usage/registration-link-button.png)
1. Na caixa de diálogo de **registo do Utilizador,** selecione o botão **Copiar.** A ligação é copiada para a área de transferência. Cole-a num editor de e-mail e envie um e-mail para o aluno. 

    ![Ligação de registo do aluno](../media/how-to-configure-student-usage/registration-link.png)
2. Na caixa de diálogo de **registo do Utilizador,** selecione **Done**. 
4. Envie o link de **inscrição** a um aluno para que o aluno possa inscrever-se para a aula. 

## <a name="view-registered-users"></a>Ver utilizadores registados

1. Navegue para o [site do Azure Lab Services](https://labs.azure.com). 
2. Selecione **Iniciar sessão** e introduza as suas credenciais. O Azure Lab Services suporta contas organizacionais e contas Microsoft.
3. Na página **Os meus laboratórios**, selecione o laboratório cuja utilização quer acompanhar. 
4. Selecione **Utilizadores** no menu esquerdo ou na telha **dos Utilizadores.** Verá os alunos que se registaram no laboratório.  

    ![Utilizadores registados](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Definir quotas para utilizadores
Pode definir quotas por utilizador utilizando os seguintes passos: 

1. Selecione **Utilizadores** no menu esquerdo se a página ainda não estiver ativa. 
2. Selecione **Quota por utilizador: \<número> horas** na barra de ferramentas. 
3. Na página **quota por utilizador,** especifique o número de horas que pretende dar a cada utilizador (aluno) fora da hora de aula programada e, em seguida, selecione **Guardar**.

    ![Quota por utilizador](../media/how-to-configure-student-usage/quota-per-user.png)    
5. Vê agora os valores alterados na barra de ferramentas: **Quota por utilizador: \<número de horas>** e também na lista de utilizadores.

    ![Quota por utilizador — depois](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > O [tempo de funcionamento programado dos VMs](how-to-create-schedules.md) não conta com a quota atribuída a um utilizador. A quota é para o tempo fora do horário horário que um aluno gasta em VMs. 

## <a name="set-additional-quota-for-a-specific-user"></a>Definir quota adicional para um utilizador específico
Pode especificar quota adicional para um utilizador. Esta quota é para além do conjunto de quotas comuns para todos os utilizadores da secção anterior. Por exemplo, se (como instrutor) definir a quota para todos os utilizadores para 10 horas e definir quota adicional de 5 horas para um utilizador específico, que os utilizadores recebem 15 (10 + 5) horas de quota. Se alterar a quota comum mais tarde para, digamos, 15, então o utilizador recebe 20 (15 + 5) horas de quota. Lembre-se que esta quota global está fora do horário programado. O tempo que o aluno passa num VM de laboratório durante o horário não conta com esta quota. 

Para isso, siga estes passos:

1. Selecione um utilizador (estudante) na lista de utilizadores na página **utilizadores.**
2. Em seguida, selecione Ajustar a **quota** a partir da barra de ferramentas. 

    ![Ajuste o botão de quota](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Insira o número de **horas adicionais** para o utilizador ou utilizadores selecionados e, em seguida, selecione **Aplicar**. 

    ![Quota adicional para um utilizador](../media/how-to-configure-student-usage/additional-quota.png)
4. Consulte a utilização atualizada para o utilizador na coluna **Utilização.** 

    ![Nova utilização para o utilizador](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Contas de estudantes
Para adicionar alunos a um laboratório de sala de aula, usa as suas contas de e-mail. Podem ser utilizados os seguintes tipos de contas de e-mail:

- Uma conta de e-mail estudantil fornecida pelo Escritório 365 Azure Ative Diretório da sua universidade. 
- Uma conta de e-mail da Microsoft, como `@outlook.com`, `@hotmail.com`, `@msn.com`ou `@live.com`.
- Uma conta de e-mail não-Microsoft, como a fornecida pela Yahoo ou pela Google. No entanto, este tipo de contas deve estar ligada a uma conta Microsoft.
- Uma conta do GitHub. Esta conta deve estar ligada a uma conta Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Usando uma conta de e-mail não Microsoft
Os alunos podem usar contas de e-mail não microsoft para se registarem e iniciarem sessão num laboratório de sala de aula.  No entanto, o registo requer que os estudantes criem primeiro uma conta Microsoft que esteja ligada ao seu endereço de e-mail não Microsoft.

Muitos estudantes podem já ter uma conta Microsoft ligada aos seus endereços de e-mail não Microsoft. Por exemplo, os estudantes já têm uma conta Microsoft se usaram o seu endereço de e-mail com outros produtos ou serviços da Microsoft, tais como Office, Skype, OneDrive ou Windows.  

Quando um aluno clica no URL de inscrição para iniciar sessão numa sala de aula, é solicitado para o seu endereço de e-mail e senha. Se o aluno tentar iniciar sessão com uma conta não Microsoft que não tenha uma conta Microsoft ligada, o aluno receberá a seguinte mensagem de erro: 

![Mensagem de erro](../media/how-to-configure-student-usage/cant-find-account.png)

Para se inscreverem numa conta da Microsoft, os alunos devem ir para [http://signup.live.com](http://signup.live.com).  

> [!IMPORTANT]
> Quando os alunos se inscrevem num laboratório de sala de aula, não lhes é dada a opção de criar uma conta Microsoft. Por isso, recomendamos que inclua este link de inscrição no e-mail de inscrição do laboratório de sala de aula que envia a estudantes que estão a usar contas não Microsoft.

### <a name="using-a-github-account"></a>Usando uma conta GitHub
Os alunos também podem usar uma conta GitHub existente para se inscrever e iniciar sessão num laboratório de sala de aula. Se o aluno já tiver uma conta Microsoft ligada à sua conta GitHub, então pode iniciar sessão e fornecer a sua palavra-passe, como mostrado na secção anterior. Se ainda não ligaram a sua conta GitHub a uma conta Microsoft, devem selecionar **opções de Iniciar sessão:**

![Ligação de opções de insessão](../media/how-to-configure-student-usage/signin-options.png)

Na página de **opções de iniciar** sessão, selecione **Iniciar sessão com o GitHub**.

![Inscreva-se com link GitHub](../media/how-to-configure-student-usage/signin-github.png)

Finalmente, são solicitados a criar uma conta Microsoft que esteja ligada à sua conta GitHub. Acontece automaticamente quando o aluno seleciona **Next**.  O aluno é imediatamente assinado e ligado ao laboratório da sala de aula.

## <a name="export-list-of-users-to-a-csv-file"></a>Lista de exportação de utilizadores para um ficheiro CSV

1. Mude para o separador **Utilizadores.**
2. Selecione **...** (elipsis) na barra de ferramentas e, em seguida, selecione **Export CSV**. 

    ![Lista de exportação de utilizadores](../media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- [Como administrador, crie e gere as contas de laboratório](how-to-manage-lab-accounts.md)
- [Como dono de laboratório, crie e gere a gestão de laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário de laboratório, configura risa e publica modelos](how-to-create-manage-template.md)
- [Como utilizador de laboratório, aceda a laboratórios de sala de aula](how-to-use-classroom-lab.md)
