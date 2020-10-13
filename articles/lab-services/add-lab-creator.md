---
title: Adicione um utilizador como criador de laboratório nos Serviços Azure Lab
description: Este artigo mostra como adicionar um utilizador ao papel de Criador de Laboratório para uma conta de laboratório nos Serviços do Laboratório Azure. Os criadores do laboratório podem criar laboratórios dentro desta conta de laboratório.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5fdeb33dab60a2fb9f1680295c355e2c7c0e775a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399487"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Adicione criadores de laboratório a uma conta de laboratório nos Serviços do Laboratório Azure
Este artigo mostra-lhe como adicionar utilizadores como criadores de laboratório a uma conta de laboratório nos Serviços Azure Lab. Estas utilizações podem então criar laboratórios de sala de aula na conta do laboratório. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Adicione a conta de utilizador da Microsoft à função De Criador de Laboratório
Para configurar um laboratório de sala de aula numa conta de laboratório, o utilizador tem de ser membro da função **Criador de Laboratório** na conta de laboratório. A conta que utilizou para criar a conta de laboratório é adicionada automaticamente a esta função. Se estiver a planear utilizar a mesma conta de utilizador para criar um laboratório de sala de aula, pode ignorar este passo. Para utilizar outra conta de utilizador para criar um laboratório de sala de aula, siga os passos abaixo: 

Para fornecer aos educadores a permissão para criar laboratórios para as suas aulas, adicione-os ao papel **de Criador de Laboratório:**

1. Na página **Conta Lab,** selecione **Access control (IAM)** e clique **em + Adicionar a atribuição de funções** na barra de ferramentas. 

    ![Controlo de acesso -> adicionar botão de atribuição de funções](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na página de **atribuição de funções Adicionar,** selecione **O Criador de Laboratório** para **Função**, selecione o utilizador que pretende adicionar à função Criadores de Laboratório e selecione **Save**. 

    ![Adicionar criador de laboratório](./media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Se estiver a adicionar um utilizador de conta não Microsoft como criador de laboratório, consulte o utilizador de conta Add não Microsoft como uma secção [de criador de laboratório.](#add-a-non-microsoft-account-user-as-a-lab-creator) 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Adicione um utilizador de conta não-Microsoft como criador de laboratório
Para adicionar um utilizador como criador de laboratório, utiliza as suas contas de e-mail. Podem ser utilizados os seguintes tipos de contas de e-mail:

- Uma conta de e-mail fornecida pelo Azure Ative Directory (AAD) da sua universidade.
- Uma conta de e-mail da Microsoft, `@outlook.com` `@hotmail.com` como, `@msn.com` por exemplo, ou `@live.com` .
- Uma conta de e-mail não-Microsoft, como uma fornecida pela Yahoo ou google. No entanto, este tipo de contas deve estar ligado a uma conta microsoft.
- Uma conta do GitHub. Esta conta deve estar ligada a uma conta microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Usando uma conta de e-mail não-Microsoft
Os criadores/instrutores de laboratório podem usar contas de e-mail não-Microsoft para se registarem e iniciarem singam-se num laboratório de sala de aula.  No entanto, o início de sessão no portal Lab Services requer que os instrutores criem primeiro uma conta Microsoft que esteja ligada ao seu endereço de e-mail não-Microsoft.

Muitos instrutores podem já ter uma conta Microsoft ligada aos seus endereços de e-mail não Microsoft. Por exemplo, os instrutores já têm uma conta Microsoft se tiverem usado o seu endereço de e-mail com outros produtos ou serviços da Microsoft, tais como Office, Skype, OneDrive ou Windows.  

Quando os instrutores insinam-se no portal lab Services, são solicitados para o seu endereço de e-mail e senha. Se o instrutor tentar iniciar scontabilidade com uma conta não Microsoft que não tenha uma conta Microsoft ligada, o instrutor receberá a seguinte mensagem de erro: 

![Mensagem de erro](./media/how-to-configure-student-usage/cant-find-account.png)

Para se inscreverem numa conta da Microsoft, os instrutores devem ir a [http://signup.live.com](http://signup.live.com) .  


### <a name="using-a-github-account"></a>Usando uma conta GitHub
Os instrutores também podem usar uma conta GitHub existente para se registarem e iniciarem singuamento num laboratório de sala de aula. Se o instrutor já tiver uma conta Microsoft ligada à sua conta GitHub, então pode iniciar sômposição e fornecer a sua palavra-passe como mostrado na secção anterior. Se ainda não ligaram a sua conta GitHub a uma conta Microsoft, devem selecionar **opções de inscrição:**

![Link de opções de inscrição](./media/how-to-configure-student-usage/signin-options.png)

Na página **de opções de iniciar sção,** selecione **Iniciar sôs com o GitHub**.

![Inscreva-se com link GitHub](./media/how-to-configure-student-usage/signin-github.png)

Finalmente, são solicitados a criar uma conta Microsoft que esteja ligada à sua conta GitHub. Acontece automaticamente quando o instrutor seleciona **Next**.  O instrutor é imediatamente assinado e ligado ao laboratório da sala de aula.


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como dono de laboratório, crie e gere laboratórios](how-to-manage-classroom-labs.md)
- [Como dono de laboratório, crie e publique modelos](how-to-create-manage-template.md)
- [Como dono de laboratório, configurar e controlar o uso de um laboratório](how-to-configure-student-usage.md)
- [Como utilizador de laboratório, aceda aos laboratórios de sala de aula](how-to-use-classroom-lab.md)
