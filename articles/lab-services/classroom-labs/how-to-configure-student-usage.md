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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 691907d1c221283f99ba59f0937cfbaf673f427a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72324488"
---
# <a name="add-and-manage-lab-users"></a>Adicionar e gerenciar usuários de laboratório
Este artigo descreve como adicionar usuários ao laboratório, obtê-los registrados com o laboratório, controlar o número de horas em que eles podem usar a VM e muito mais. 


## <a name="add-users-to-the-lab"></a>Adicionar usuários ao laboratório

1. Selecione **usuários** no menu à esquerda. Por padrão, a opção de **acesso restrito** está habilitada. Quando essa configuração estiver ativada, um usuário não poderá se registrar no laboratório, mesmo se o usuário tiver o link de registro, a menos que o usuário esteja na lista de usuários. Somente os usuários na lista podem se registrar no laboratório usando o link de registro que você envia. Neste procedimento, você adiciona usuários à lista. Como alternativa, você pode desativar o **acesso restrito**, que permite aos usuários se registrarem com o laboratório, desde que eles tenham o link de registro. 
2. Selecione **Adicionar usuários** na barra de ferramentas e, em seguida, selecione **Adicionar por endereços de email**. 

    ![Botão Adicionar usuários](../media/how-to-configure-student-usage/add-users-button.png)
1. Na página **Adicionar usuários** , insira os endereços de email dos usuários em linhas separadas ou em uma única linha separada por ponto e vírgula. 

    ![Adicionar endereços de email do usuário](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selecione **Guardar**. Você vê os endereços de email dos usuários e seus status (registrados ou não) na lista. 

    ![Lista de usuários](../media/how-to-configure-student-usage/users-list-new.png)

### <a name="add-users-by-uploading-a-csv-file"></a>Adicionar usuários carregando um arquivo CSV
Você também pode adicionar usuários carregando um arquivo CSV com endereços de email de usuários.

1. Crie um arquivo CSV com endereços de email de usuários em uma coluna.

    ![Arquivo CSV com usuários](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Na página **usuários** do laboratório, selecione **Adicionar usuários** na barra de ferramentas e, em seguida, selecione **carregar CSV**.

    ![Botão carregar CSV](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Selecione o arquivo CSV com endereços de email do usuário. Ao selecionar **abrir** depois de selecionar o arquivo CSV, você verá a janela **Adicionar usuários** a seguir. A lista de endereços de email é preenchida com endereços de email do arquivo CSV. 

    ![Janela Adicionar usuários populada com endereços de email do arquivo CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. Selecione **salvar** na janela **Adicionar usuários** . 
5. Confirme que você vê os usuários na lista de usuários. 

    ![Lista de usuários adicionados](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Enviar convites para usuários
Para enviar o link de registro para os alunos, use um dos métodos a seguir. O primeiro método mostra como enviar emails para alunos com o link de registro e uma mensagem opcional. O segundo método mostra como obter o link de registro que você pode compartilhar com outras pessoas como desejar. 

Se o **acesso restrito** estiver habilitado para o laboratório, somente os usuários na lista de usuários poderão usar o link de registro para se registrar no laboratório. Essa opção é habilitada por padrão. 

### <a name="invite-all-users"></a>Convidar todos os usuários

1. Alterne para a página **usuários** do laboratório. 
2. Selecione **convidar todos** na barra de ferramentas. 
3. Insira uma **mensagem** para os usuários. Esta é uma etapa opcional.
4. Em seguida, selecione **Enviar**.

    ![Convidar todos os usuários](../media/how-to-configure-student-usage/invite-all.png)

    Você verá o status dessa operação na coluna **convite** da lista de **usuários** . O email de convite incluirá o link de registro que os usuários podem usar para se registrar no laboratório. 

### <a name="invite-selected-users"></a>Convidar usuários selecionados

1. Selecione um usuário ou vários usuários na lista. 
2. Em seguida, selecione o ícone de **envelope** que você vê na linha selecionada (ou) selecione **convidar** na barra de ferramentas. 

    ![Convidar usuários selecionados](../media/how-to-configure-student-usage/invite-selected-users.png)
3. Na janela **Enviar convite por email** , insira uma **mensagem**opcional e, em seguida, selecione **Enviar**. 

    ![Enviar email para os usuários selecionados](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Você verá o status dessa operação na coluna **convite** da lista de **usuários** . O email de convite incluirá o link de registro que os usuários podem usar para se registrar no laboratório.

1. Alterne para a exibição de **usuários** se você ainda não estiver na página. 

## <a name="get-registration-link"></a>Obter link de registro
Você também pode obter o link de registro no portal e enviá-lo usando seu próprio aplicativo cliente de email. 

1. Alterne para a exibição **usuários** selecionando **usuários** no menu à esquerda. 
2. Selecionar **... (reticências)** na barra de ferramentas e selecione **link de registro**.

    ![Ligação de registo do aluno](../media/how-to-configure-student-usage/registration-link-button.png)
1. Na caixa de diálogo **registro de usuário** , selecione o botão **copiar** . A ligação é copiada para a área de transferência. Cole-a num editor de e-mail e envie um e-mail para o aluno. 

    ![Ligação de registo do aluno](../media/how-to-configure-student-usage/registration-link.png)
2. Na caixa de diálogo **registro de usuário** , selecione **concluído**. 
4. Envie o **link de registro** para um aluno para que o aluno possa se registrar para a classe. 

## <a name="view-users-registered-with-the-lab"></a>Ver utilizadores registados no laboratório

Selecione **usuários** no menu à esquerda para ver a lista de usuários registrados no laboratório. 

![Lista de usuários registrados com o laboratório](../media/how-to-configure-student-usage/students-registered.png)

## <a name="set-quotas-for-users"></a>Definir cotas para usuários
Você pode definir cotas por usuário usando as seguintes etapas: 

1. Selecione **usuários** no menu à esquerda se a página ainda não estiver ativa. 
2. Selecione **a cota por usuário: &lt;number @ no__t-2 horas** na barra de ferramentas. 
3. Na página **cota por usuário** , especifique o número de horas que você deseja dar a cada usuário (aluno) fora da hora da classe agendada e, em seguida, selecione **salvar**.

    ![Cota por usuário](../media/how-to-configure-student-usage/quota-per-user.png)    
5. Você verá os valores alterados na barra de ferramentas agora: **cota por usuário: &lt;number de horas @ no__t-2**. 

    ![Cota por usuário-após](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > O [tempo de execução agendado das VMs](how-to-create-schedules.md) não conta a cota alocada para um usuário. A cota é para o tempo fora das horas de agendamento que um aluno gasta nas VMs. 

## <a name="set-additional-quota-for-a-specific-user"></a>Definir cota adicional para um usuário específico
Você pode definir uma cota separada para um usuário. Para fazer isso, siga estas etapas:

1. Selecione um usuário (aluno) na lista de usuários na página **usuários** .
2. Em seguida, selecione **ajustar cota** na barra de ferramentas. 

    ![Botão ajustar cota](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Insira o número de **horas adicionais** para o usuário ou usuários selecionados e, em seguida, selecione **aplicar**. 

    ![Cota adicional para um usuário](../media/how-to-configure-student-usage/additional-quota.png)
4. Você verá o uso atualizado para o usuário na coluna **uso** . 

    ![Novo uso para o usuário](../media/how-to-configure-student-usage/new-usage-hours.png)


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como proprietário de um laboratório, criar e gerenciar laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário do laboratório, configurar e publicar modelos](how-to-create-manage-template.md)
- [Como usuário do laboratório, acesse laboratórios da sala de aula](how-to-use-classroom-lab.md)
