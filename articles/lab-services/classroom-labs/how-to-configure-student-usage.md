---
title: Configurar definições de utilização nos laboratórios de sala de aula do Azure Lab Services | Documentos da Microsoft
description: Saiba como configurar o número de utilizadores para o laboratório, obtê-los registado com o laboratório, controlar o número de horas que podem utilizar a VM e muito mais.
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
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: eadbaacd4bb860cd09997775b8d77cbcf0ff17f2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181627"
---
# <a name="configure-usage-settings-and-policies"></a>Configurar as políticas e definições de utilização
Este artigo descreve como adicionar utilizadores ao laboratório, obtê-los registado com o laboratório, controlar o número de horas que podem utilizar a VM e muito mais. 


## <a name="add-users-to-the-lab"></a>Adicionar utilizadores ao laboratório
Se tiver o **restringir o acesso** ativado, adicione utilizadores (endereços de e-mail) à lista.

1. Selecione **utilizadores** no menu da esquerda.
2. Selecione **adicionar utilizadores** na barra de ferramentas. 

    ![Adicionar botão de utilizadores](../media/how-to-configure-student-usage/add-users-button.png)
1. Sobre o **adicionar utilizadores** página, introduza os endereços de e-mail dos utilizadores em linhas separadas ou numa única linha, separados por ponto e vírgula. 

    ![Endereços de e-mail do utilizador](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selecione **Guardar**. Ver os endereços de e-mail dos utilizadores e os respetivos Estados (registados ou não) na lista. 

    ![Lista de utilizadores](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="share-registration-link-with-students"></a>Ligação de registo da partilha com estudantes
Para enviar a ligação de registo para estudantes, utilize um dos seguintes métodos. O primeiro método mostra-lhe como enviar e-mails para estudantes com a ligação de registo e uma mensagem opcional. O segundo método mostra como obter o link de registo que pode partilhar com outras pessoas como pretender. 

Se o **restringir o acesso** está ativada para o laboratório, apenas os utilizadores da lista de utilizadores podem utilizar a ligação de registo se registrar para o laboratório. Esta opção está ativada por predefinição. 

### <a name="send-email-to-users"></a>Enviar e-mail para utilizadores
1. Mude para o **utilizadores** ver se ainda não está na página. 
2. Na lista, selecione específicos ou todos os utilizadores. Para selecionar utilizadores específicos, marque as caixas de seleção na primeira coluna da lista. Para selecionar todos os utilizadores, selecione a caixa de verificação na frente o título da primeira coluna (**nome**) ou selecione todas as caixas de verificação para todos os utilizadores na lista.
3. Selecione **Enviar convite** na barra de ferramentas. Também pode passar o mouse sobre um nome para estudantes na lista e enviar o ícone de e-mail. 

    ![Enviar ligação de registo por e-mail](../media/tutorial-setup-classroom-lab/send-email.png)
4. Sobre o **ligação de registo de enviar por e-mail** página, siga estes passos: 
    1. Tipo de uma **mensagem opcional** que pretende enviar para os alunos. O e-mail inclui automaticamente a ligação de registo. 
    2. Sobre o **ligação de registo de enviar por e-mail** página, selecione **enviar**. 

## <a name="get-registration-link"></a>Obter ligação de registo
1. Mude para o **usuários** vista ao selecionar **utilizadores** no menu da esquerda. 
2. Selecione **obter a ligação de registo** mosaico.

    ![Ligação de registo do aluno](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Na caixa de diálogo **Registo de utilizador**, selecione o botão **Copiar**. A ligação é copiada para a área de transferência. Cole-a num editor de e-mail e envie um e-mail para o aluno. 

    ![Ligação de registo do aluno](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Na caixa de diálogo **Registo de utilizador**, selecione **Fechar**. 
4. Partilha a **ligação de registo** com um aluno, para que os estudantes podem registar para a classe. 

## <a name="view-users-registered-with-the-lab"></a>Ver utilizadores registados no laboratório

Selecione **utilizadores** no menu da esquerda para ver a lista de utilizadores registados com o laboratório. 

![Lista de utilizadores registados com o laboratório](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-per-user"></a>Definir quotas por utilizador
Pode definir quotas de por utilizador, utilizando os seguintes passos: 

1. Selecione **utilizadores** no menu da esquerda.
2. Selecione **Quota por utilizador: ilimitado** na barra de ferramentas. 
3. Sobre o **Quota por utilizador** página, selecione uma das seguintes opções: 
    1. **Nenhuma**. Os utilizadores podem utilizar as suas máquinas virtuais apenas durante a hora agendada ou quando o proprietário de laboratório ativa máquinas virtuais para eles.
    2. **Sem limite (predefinição)**. Os utilizadores podem utilizar as suas máquinas virtuais sem quaisquer restrições de tempo.
    3. **Especifique o número de horas por utilizador**. Os utilizadores podem utilizar as suas máquinas virtuais para o número de conjunto de horas (especificadas abaixo), além da data agendada. Se selecionar esta opção, introduza o **número de horas** na caixa de texto. 

        ![Número de horas por utilizador](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. Selecione **Guardar**. 
5. Veja os valores alterados na barra de ferramentas agora: **Quota de por utilizador: &lt;número de horas&gt;**. 

    ![Quota de por utilizador](../media/how-to-configure-student-usage/quota-per-user.png)

> [!IMPORTANT]
> O [agendada com tempo de VMs](how-to-create-schedules.md) não contam para a quota atribuída a um utilizador. A quota é para o tempo fora do horário de agenda que passa a um aluno em VMs. 

### <a name="add-users-by-uploading-a-csv-file"></a>Adicionar utilizadores ao carregar um ficheiro CSV
Também pode adicionar utilizadores ao carregar um ficheiro CSV com endereços de e-mail dos utilizadores.

1. Crie um ficheiro CSV com endereços de e-mail dos utilizadores numa coluna.

    ![Quota de por utilizador](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Sobre o **os utilizadores** página o laboratório, selecione **carregar CSV** na barra de ferramentas.

    ![Carregar o botão CSV](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Selecione o ficheiro CSV com endereços de e-mail do utilizador. Quando seleciona **aberto** depois de selecionar o ficheiro CSV, consulte o seguinte **adicionar utilizadores** janela. A lista de endereços de e-mail é preenchida com endereços de e-mail a partir do ficheiro CSV. 

    ![Adicionar a janela de utilizadores preenchida com os endereços de e-mail de um ficheiro CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. Selecione **salvar** no **adicionar utilizadores** janela. 
5. Confirme que vê os utilizadores da lista de utilizadores. 

    ![Lista de utilizadores adicionados](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>Gerir VMs do utilizador
Assim que os estudantes registar com o Azure Lab Services usando o registro link fornecido aos mesmos, verá as VMs atribuídas aos estudantes na **máquinas virtuais** separador. 

![Máquinas de virtuais atribuídas para estudantes](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Pode efetuar as seguintes tarefas numa VM de estudante: 

- Pare uma VM se a VM está em execução. 
- Inicie uma VM se a VM é parada. 
- Ligue à VM. 
- Elimine a VM. 
- Ver o número de horas que os utilizadores utilizaram a máquina virtual. 

## <a name="update-number-of-virtual-machines-in-lab"></a>Atualizar o número de máquinas virtuais num laboratório
Para atualizar o número de máquinas virtuais no laboratório, siga os passos seguintes **máquinas virtuais** página:

1. Selecione **máquinas virtuais** no menu da esquerda. 
2. Selecione **capacidade de laboratório: &lt;número&gt; máquina (s)** na barra de ferramentas. 
3. Introduza o **número** de máquinas virtuais.
4. Selecione **Guardar**.

    ![Máquinas virtuais no laboratório](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- [Como administrador, criar e gerir contas de laboratório](how-to-manage-lab-accounts.md)
- [Como proprietário de um laboratório, criar e gerir laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário de um laboratório, configurar e publicar modelos](how-to-create-manage-template.md)
- [Como um utilizador de laboratório, acessar os laboratórios de sala de aula](how-to-use-classroom-lab.md)
