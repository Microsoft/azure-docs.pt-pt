---
title: Configurar uma conta de laboratório com o Azure Lab Services | Microsoft Docs
description: Neste tutorial, vai configurar uma conta de laboratório com o Azure Lab Services.
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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 7cf0788bdd013eb6a42ee5aa3f5c054a557cbc77
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651843"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Tutorial: Configurar uma conta de laboratório com o Azure Lab Services
No Azure Lab Services, a conta de laboratório serve como a conta central em que são geridos os laboratórios na organização. Na conta de laboratório, conceda permissões para outros utilizadores criarem laboratórios e definir políticas aplicáveis a todos os laboratórios na conta de laboratório. Neste tutorial, aprenda como criar uma conta de laboratório como administrador de laboratório. 

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Criar uma conta de laboratório
> * Adicionar um utilizador à função de Criador do Laboratório
> * Especifique as imagens do Marketplace disponíveis para os proprietários de laboratório

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-lab-account"></a>Criar uma conta de laboratório
Os passos seguintes mostram como utilizar o portal do Azure para criar uma conta de laboratório com o Azure Lab Services. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu da esquerda. Selecione **contas de laboratório** no **DEVOPS** secção. Se selecionar asterisco (`*`) junto a **contas de laboratório**, é adicionado para o **Favoritos** secção no menu da esquerda. A partir da próxima vez em diante, pode selecionar **contas de laboratório** sob **Favoritos**.

    ![Todos os serviços -> contas de laboratório](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Sobre o **contas de laboratório** página, selecione **Add** na barra de ferramentas. 

    ![Selecione adicionar na página de contas de laboratório](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Sobre o **conta de laboratório** página, efetue as seguintes ações: 
    1. Em **Nome da conta de laboratório**, introduza um nome. 
    2. Selecione a **Subscrição do Azure**, na qual pretende criar a conta de laboratório.
    3. Em **Grupo de recursos**: selecione **Criar novo** e introduza um nome para o grupo de recursos.
    4. Em **Localização**, selecione uma região/localização na qual pretende criar a conta de laboratório. 
    5. Para o **criador do laboratório de permitir a escolher a localização de laboratório** campo, especifique se pretende que os criadores de laboratório para poder selecionar uma localização para o laboratório. Por predefinição, a opção está desativada. Quando estiver desativada, os criadores de laboratório não é possível especificar uma localização para o laboratório estiver a criar. Os laboratórios são criados na localização geográfica mais próxima à conta de laboratório. Quando estiver ativada, um criador de laboratório pode selecionar uma localização no momento da criação de um laboratório. 
    6. Selecione **Criar**. 

        ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Selecione o **ícone de sino** na barra de ferramentas (**notificações**), confirme que a implementação foi concluída com êxito e, em seguida, selecione **Ir para recurso**. 

    Em alternativa, selecione **atualizar** sobre o **contas de laboratório** página e selecione a conta de laboratório que criou. 

    ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Verá a página **conta de laboratório** seguinte:

    ![Página da conta de laboratório](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Adicionar um utilizador à função de Criador do Laboratório
Para configurar um laboratório de sala de aula numa conta de laboratório, o utilizador tem de ser membro da função **Criador de Laboratório** na conta de laboratório. A conta que utilizou para criar a conta de laboratório é adicionada automaticamente a esta função. Se estiver a planear utilizar a mesma conta de utilizador para criar um laboratório de sala de aula, pode ignorar este passo. Para utilizar outra conta de utilizador para criar um laboratório de sala de aula, siga os passos abaixo: 

Para dar permissão aos educadores para criarem laboratórios para as classes deles, adicione-os à função **Criador do Laboratório**:

1. Sobre o **conta de laboratório** página, selecione **controlo de acesso (IAM)**, selecione **+ adicionar** na barra de ferramentas e, em seguida, selecione **+ adicionar atribuição de função** no barra de ferramentas. 

    ![Controlo de acesso -> botão Adicionar atribuição de função](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Sobre o **adicionar atribuição de função** página, selecione **criador do laboratório** para **função**, selecione o utilizador que pretende adicionar à função de criadores de laboratório e selecione **guardar**. 

    ![Adicionar o criador do laboratório](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Especifique as imagens do Marketplace disponíveis para os criadores de laboratório
Enquanto proprietário de uma conta de laboratório, pode especificar as imagens do Marketplace que os criadores de laboratórios podem utilizar para criar laboratórios nessa conta. 

1. Selecione **Imagens do Marketplace** no menu à esquerda. Por predefinição, verá a lista completa de imagens (ativadas e desativadas). Pode filtrar a lista para ver apenas as imagens ativadas/desativadas ao selecionar a opção **Apenas ativado**/**Apenas desativado** na lista pendente na parte superior. 
    
    ![Página de imagens do Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    As imagens do Marketplace que aparecem na lista são as que satisfazem as seguintes condições:
        
    - Cria uma VM individual.
    - Utiliza o Azure Resource Manager para aprovisionar VMs
    - Não requer a compra de planos de licenciamento extra
2. Para **desativar** uma imagem do Marketplace que tenha sido ativada, efetue uma das seguintes ações: 
    1. Selecione **... (reticências)** na última coluna e selecione **Desativar imagem**. 

        ![Desativar uma imagem](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Selecione uma ou mais imagens a partir da lista ao selecionar as caixas de verificação antes dos nomes de imagem na lista e selecione **Desativar imagens selecionadas**. 

        ![Desativar várias imagens](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. De forma semelhante, para **ativar** uma imagem do Marketplace, efetue uma das seguintes ações: 
    1. Selecione **... (reticências)** na última coluna e selecione **Ativar imagem**. 
    2. Selecione uma ou mais imagens a partir da lista ao selecionar as caixas de verificação antes dos nomes de imagem na lista e selecione **Ativar imagens selecionadas**. 

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, criou uma conta de laboratório. Para saber mais sobre como criar um laboratório de sala de aula como uma profissão, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Configurar um laboratório de sala de aula](tutorial-setup-classroom-lab.md)

