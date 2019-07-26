---
title: Definir configurações de uso em laboratórios de sala de aula de Azure Lab Services | Microsoft Docs
description: Saiba como configurar o número de usuários para o laboratório, obtê-los registrados com o laboratório, controlar o número de horas em que eles podem usar a VM e muito mais.
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 86f22864c416ad2a90bea09c02675d6eb3322308
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385609"
---
# <a name="configure-usage-settings-and-policies"></a>Definir configurações e políticas de uso
Este artigo descreve como adicionar usuários ao laboratório, obtê-los registrados com o laboratório, controlar o número de horas em que eles podem usar a VM e muito mais. 


## <a name="add-users-to-the-lab"></a>Adicionar usuários ao laboratório
Se você tiver o **acesso restrito** habilitado, adicione usuários (endereços de email) à lista.

1. Selecione **usuários** no menu à esquerda.
2. Selecione **Adicionar usuários** na barra de ferramentas. 

    ![Botão Adicionar usuários](../media/how-to-configure-student-usage/add-users-button.png)
1. Na página **Adicionar usuários** , insira os endereços de email dos usuários em linhas separadas ou em uma única linha separada por ponto e vírgula. 

    ![Adicionar endereços de email do usuário](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selecione **Guardar**. Você vê os endereços de email dos usuários e seus status (registrados ou não) na lista. 

    ![Lista de usuários](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="share-registration-link-with-students"></a>Compartilhar link de registro com alunos
Para enviar o link de registro para os alunos, use um dos métodos a seguir. O primeiro método mostra como enviar emails para alunos com o link de registro e uma mensagem opcional. O segundo método mostra como obter o link de registro que você pode compartilhar com outras pessoas como desejar. 

Se o **acesso restrito** estiver habilitado para o laboratório, somente os usuários na lista de usuários poderão usar o link de registro para se registrar no laboratório. Essa opção é habilitada por padrão. 

### <a name="send-email-to-users"></a>Enviar email aos usuários
Azure Lab Services permite que os professores enviem convites por email a todos ou a alunos selecionados sem precisar usar outro cliente de email. Os professores podem focalizar um aluno individual na lista para ver o ícone de email de cada aluno ou selecionar um ou mais alunos e usar **Enviar convite** na barra de ferramentas. Esse recurso envia um email com um link de registro e uma mensagem (se houver) adicionado pelo professor. Depois que o convite for enviado, o estado do convite será alterado para **convite enviado** para que os professores possam controlar quais alunos já receberam o link de registro e a data em que ele foi enviado.

1. Alterne para a exibição de **usuários** se você ainda não estiver na página. 
2. Selecione específico ou todos os usuários na lista. Para selecionar usuários específicos, marque as caixas de seleção na primeira coluna da lista. Para selecionar todos os usuários, marque a caixa de seleção na frente do título da primeira coluna (**nome**) ou marque todas as caixas de seleção para todos os usuários na lista. Você pode ver o status do **estado do convite** nesta lista.  Na imagem a seguir, o estado do convite para todos os alunos está definido como **convite não enviado**. 

    ![Selecionar alunos](../media/tutorial-setup-classroom-lab/select-students.png)
1. Selecione o **ícone de email (envelope)** em uma das linhas (ou) selecione **Enviar convite** na barra de ferramentas. Você também pode passar o mouse sobre um nome de aluno na lista para ver o ícone de email. 

    ![Enviar link de registro por email](../media/tutorial-setup-classroom-lab/send-email.png)
4. Na página **Enviar link de registro por email** , siga estas etapas: 
    1. Digite uma **mensagem opcional** que você deseja enviar aos alunos. O email inclui automaticamente o link de registro. 
    2. Na página **Enviar link de registro por email** , selecione **Enviar**. Você vê o status da alteração de convite para **Enviar convite** e, em seguida, para o **convite enviado**. 
        
        ![Convites enviados](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="get-registration-link"></a>Obter link de registro
1. Alterne para a exibição **usuários** selecionando **usuários** no menu à esquerda. 
2. Selecione o bloco **obter link de registro** .

    ![Ligação de registo do aluno](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Na caixa de diálogo **Registo de utilizador**, selecione o botão **Copiar**. A ligação é copiada para a área de transferência. Cole-a num editor de e-mail e envie um e-mail para o aluno. 

    ![Ligação de registo do aluno](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Na caixa de diálogo **Registo de utilizador**, selecione **Fechar**. 
4. Compartilhe o **link de registro** com um aluno para que o aluno possa se registrar para a classe. 

## <a name="view-users-registered-with-the-lab"></a>Ver utilizadores registados no laboratório

Selecione **usuários** no menu à esquerda para ver a lista de usuários registrados no laboratório. 

![Lista de usuários registrados com o laboratório](../media/how-to-configure-student-usage/users-list-new.png)

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



> [!IMPORTANT]
> Antes de enviar o link de registro para os alunos, os professores deverão definir o agendamento da classe se escolherem 0 horas de cota ou especificar as horas de cota para o laboratório.
>
> O [tempo de execução agendado das VMs](how-to-create-schedules.md) não conta a cota alocada para um usuário. A cota é para o tempo fora das horas de agendamento que um aluno gasta nas VMs. 

### <a name="add-users-by-uploading-a-csv-file"></a>Adicionar usuários carregando um arquivo CSV
Você também pode adicionar usuários carregando um arquivo CSV com endereços de email de usuários.

1. Crie um arquivo CSV com endereços de email de usuários em uma coluna.

    ![Cota por usuário](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Na página **usuários** do laboratório, selecione **carregar CSV** na barra de ferramentas.

    ![Botão carregar CSV](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Selecione o arquivo CSV com endereços de email do usuário. Ao selecionar **abrir** depois de selecionar o arquivo CSV, você verá a janela **Adicionar usuários** a seguir. A lista de endereços de email é preenchida com endereços de email do arquivo CSV. 

    ![Janela Adicionar usuários populada com endereços de email do arquivo CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. Selecione **salvar** na janela **Adicionar usuários** . 
5. Confirme que você vê os usuários na lista de usuários. 

    ![Lista de usuários adicionados](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>Gerenciar VMs do usuário
Depois que os alunos se registrarem com Azure Lab Services usando o link de registro que você forneceu a eles, você verá as VMs atribuídas aos alunos na guia **máquinas virtuais** . 

![Máquinas virtuais atribuídas a alunos](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Você pode executar as seguintes tarefas em uma VM de aluno: 

- Interrompa uma VM se a VM estiver em execução. 
- Inicie uma VM se a VM for interrompida. 
- Ligue à VM. 
- Exclua a VM. 
- Exiba o número de horas em que os usuários usaram a máquina virtual. 

## <a name="update-number-of-virtual-machines-in-lab"></a>Atualizar o número de máquinas virtuais no laboratório
Para atualizar o número de máquinas virtuais no laboratório, execute as seguintes etapas na página **máquinas virtuais** :

1. Selecione **máquinas virtuais** no menu à esquerda. 
2. Selecione a **capacidade do &lt;laboratório&gt; : número de máquina (s)** na barra de ferramentas. 
3. Insira o **número** de máquinas virtuais.
4. Selecione **Guardar**.

    ![Máquinas virtuais no laboratório](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como proprietário de um laboratório, criar e gerenciar laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário do laboratório, configurar e publicar modelos](how-to-create-manage-template.md)
- [Como usuário do laboratório, acesse laboratórios da sala de aula](how-to-use-classroom-lab.md)
