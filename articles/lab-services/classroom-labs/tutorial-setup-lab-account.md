---
title: Configurar uma conta de laboratório com o Azure Lab Services | Microsoft Docs
description: Aprenda a criar uma conta de laboratório com a Azure Lab Services, adicione um criador de laboratório e especifique as imagens do Marketplace a serem usadas por laboratórios na conta do laboratório.
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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: dba6a4c07691f3d7ec88d8b889e68d6ac7116f07
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134011"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Tutorial: Configurar uma conta de laboratório com o Azure Lab Services
No Azure Lab Services, a conta de laboratório serve como a conta central em que são geridos os laboratórios na organização. Na conta de laboratório, conceda permissões para outros utilizadores criarem laboratórios e definir políticas aplicáveis a todos os laboratórios na conta de laboratório. Neste tutorial, aprenda a criar uma conta de laboratório. 

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Criar uma conta de laboratório
> * Adicionar um utilizador à função de Criador do Laboratório

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-lab-account"></a>Criar uma conta de laboratório
Os passos seguintes mostram como utilizar o portal do Azure para criar uma conta de laboratório com o Azure Lab Services. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** no menu esquerdo. Selecione **DevOps** de **categorias**. Em seguida, selecione **Serviços de Laboratório**. Se selecionar estrela (`*`) ao lado dos Serviços de **Laboratório,** é adicionado à secção **FAVORITES** no menu esquerdo. A partir da próxima vez, selecione **Serviços** de Laboratório sob **OS FAVORITOS**.

    ![Todos os Serviços -> Serviços de Laboratório](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na página **lab Services,** selecione **Adicionar** na barra de ferramentas ou selecione Criar o botão de **conta de laboratório** na página. 

    ![Selecione Adicionar na página contas de laboratório](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. No separador Basics da página criar **uma conta de laboratório,** faça as **seguintes** ações: 
    1. Em **Nome da conta de laboratório**, introduza um nome. 
    2. Selecione a **Subscrição do Azure**, na qual pretende criar a conta de laboratório.
    3. Para **o grupo Recursos,** selecione um grupo de recursos existente ou selecione **Criar novo,** e insira um nome para o grupo de recursos.
    4. Para **localização**, selecione uma localização/região na qual pretende criar a conta de laboratório. 

        ![Conta de laboratório - página básica](../media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. Selecione **Rever + criar**.
    6. Reveja o resumo e selecione **Criar**. 

        ![Review + criar -> Criar](../media/tutorial-setup-lab-account/create-button.png)    
5. Quando a implementação estiver concluída, expanda **os próximos passos**e selecione **Ir para o recurso**. 

    ![Vá à página de conta de laboratório](../media/tutorial-setup-lab-account/go-to-lab-account.png)
6. Confirme que vê a página da **Conta** lab. 

    ![Página da conta de laboratório](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Adicionar um utilizador à função de Criador do Laboratório
Para configurar um laboratório de sala de aula numa conta de laboratório, o utilizador tem de ser membro da função **Criador de Laboratório** na conta de laboratório. Para dar permissão aos educadores para criarem laboratórios para as classes deles, adicione-os à função **Criador do Laboratório**:

> [!NOTE]
> A conta que utilizou para criar a conta de laboratório é adicionada automaticamente a esta função. Se está a planear usar a mesma conta de utilizador para criar um laboratório de sala de aula neste tutorial, ignore este passo. 

1. Na página **da Conta Lab,** selecione o controlo de **acesso (IAM)** , selecione **+ Adicione** na barra de ferramentas e, em seguida, selecione + Adicione a atribuição de **funções** na barra de ferramentas. 

    ![Controlo de Acesso -> Adicionar Botão de Atribuição de Funções](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na página de atribuição de **funções Add,** selecione **Lab Creator** para **Função**, selecione o utilizador que pretende adicionar à função De Criadores de Laboratório e selecione **Save**. 

    ![Adicionar criador de laboratório](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou uma conta de laboratório. Para aprender como criar um laboratório de sala de aula como professor, avançar para o próximo tutorial:

> [!div class="nextstepaction"]
> [Configurar um laboratório de sala de aula](tutorial-setup-classroom-lab.md)

