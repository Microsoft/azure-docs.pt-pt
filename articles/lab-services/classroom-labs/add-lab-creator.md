---
title: Adicione um utilizador como criador de laboratório nos Serviços de Laboratório Azure
description: Este artigo mostra como adicionar um utilizador ao papel de Criador de Laboratório para uma conta de laboratório nos Serviços de Laboratório Azure. Os criadores de laboratório podem criar laboratórios nesta conta de laboratório.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 0538747ec639b3fab1a7b38193796d80a7736170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444774"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Adicione criadores de laboratório a uma conta de laboratório nos Serviços de Laboratório Azure
Este artigo mostra-lhe como adicionar utilizadores como criadores de laboratório a uma conta de laboratório na Azure Lab Services. Estes usos podem então criar laboratórios de sala de aula na conta do laboratório. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Adicione a conta de utilizador da Microsoft ao papel de Criador de Laboratório
Para configurar um laboratório de sala de aula numa conta de laboratório, o utilizador tem de ser membro da função **Criador de Laboratório** na conta de laboratório. A conta que utilizou para criar a conta de laboratório é adicionada automaticamente a esta função. Se estiver a planear utilizar a mesma conta de utilizador para criar um laboratório de sala de aula, pode ignorar este passo. Para utilizar outra conta de utilizador para criar um laboratório de sala de aula, siga os passos abaixo: 

Para fornecer aos educadores a permissão para criar laboratórios para as suas aulas, adicione-os ao papel de Criador de **Laboratório:**

1. Na página **da Conta Lab,** selecione o controlo de **acesso (IAM)** e clique + Adicione a atribuição de **funções** na barra de ferramentas. 

    ![Controlo de Acesso -> adicionar botão de atribuição de funções](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na página de atribuição de **funções Add,** selecione **Lab Creator** para **Função**, selecione o utilizador que pretende adicionar à função De Criadores de Laboratório e selecione **Save**. 

    ![Adicionar criador de laboratório](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Se estiver a adicionar um utilizador de conta não Microsoft como criador de laboratório, consulte o utilizador da conta Add a non Microsoft como uma secção de criador de [laboratório.](#add-a-non-microsoft-account-user-as-a-lab-creator) 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Adicione um utilizador de conta não Microsoft como criador de laboratório
Para adicionar um utilizador como criador de laboratório, utiliza as suas contas de e-mail. Podem ser utilizados os seguintes tipos de contas de e-mail:

- Uma conta de e-mail fornecida pelo Office 365 Azure Ative Directory (AAD) da sua universidade. 
- Uma conta de e-mail `@msn.com`da `@live.com`Microsoft, como, `@outlook.com` `@hotmail.com`ou .
- Uma conta de e-mail não-Microsoft, como a fornecida pela Yahoo ou pela Google. No entanto, este tipo de contas deve estar ligada a uma conta Microsoft.
- Uma conta do GitHub. Esta conta deve estar ligada a uma conta Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Usando uma conta de e-mail não Microsoft
Criadores de laboratório/instrutores podem usar contas de e-mail não microsoft para se registar e iniciar sessão num laboratório de sala de aula.  No entanto, o sessão no portal lab Services requer que os instrutores criem primeiro uma conta Microsoft que esteja ligada ao seu endereço de e-mail não Microsoft.

Muitos instrutores podem já ter uma conta Microsoft ligada aos seus endereços de e-mail não Microsoft. Por exemplo, os instrutores já têm uma conta Microsoft se usaram o seu endereço de e-mail com outros produtos ou serviços da Microsoft, tais como Office, Skype, OneDrive ou Windows.  

Quando os instrutores assinam no portal dos Serviços de Laboratório, são solicitados para o seu endereço de e-mail e senha. Se o instrutor tentar iniciar sessão com uma conta não Microsoft que não tenha uma conta Microsoft ligada, o instrutor receberá a seguinte mensagem de erro: 

![Mensagem de erro](../media/how-to-configure-student-usage/cant-find-account.png)

Para se inscrever em uma conta Microsoft, os instrutores devem ir para [http://signup.live.com](http://signup.live.com).  


### <a name="using-a-github-account"></a>Usando uma conta GitHub
Os instrutores também podem usar uma conta GitHub existente para se registar e iniciar sessão num laboratório de sala de aula. Se o instrutor já tiver uma conta Microsoft ligada à sua conta GitHub, então pode iniciar sessão e fornecer a sua palavra-passe, como mostrado na secção anterior. Se ainda não ligaram a sua conta GitHub a uma conta Microsoft, devem selecionar **opções de Iniciar sessão:**

![Ligação de opções de insessão](../media/how-to-configure-student-usage/signin-options.png)

Na página de **opções de iniciar** sessão, selecione **Iniciar sessão com o GitHub**.

![Inscreva-se com link GitHub](../media/how-to-configure-student-usage/signin-github.png)

Finalmente, são solicitados a criar uma conta Microsoft que esteja ligada à sua conta GitHub. Acontece automaticamente quando o instrutor seleciona **Seguinte**.  O instrutor é imediatamente assinado e ligado ao laboratório da sala de aula.


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como dono de laboratório, crie e gere a gestão de laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário de laboratório, configura risa e publica modelos](how-to-create-manage-template.md)
- [Como dono de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como utilizador de laboratório, aceda a laboratórios de sala de aula](how-to-use-classroom-lab.md)
