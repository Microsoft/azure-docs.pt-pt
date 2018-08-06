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
ms.date: 07/17/2018
ms.author: spelluru
ms.openlocfilehash: b60c1e84eb5b62bfce0eb2ba96129deeee2fc3c3
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345313"
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
2. No menu principal do lado esquerdo, selecione **Criar um recurso**.
3. Procurar por **Serviços de Laboratório** no Azure Marketplace e selecione **Serviços de Laboratório** na lista pendente. 
4. Selecione **Serviços de Laboratório (Pré-visualização)** na lista filtrada de serviços. 
1. Na janela **Criar uma conta de laboratório**, selecione **Criar**.
2. Na janela **Conta de laboratório**, realize as ações seguintes: 
    1. Em **Nome da conta de laboratório**, introduza um nome. 
    2. Selecione a **Subscrição do Azure**, na qual pretende criar a conta de laboratório.
    3. Em **Grupo de recursos**: selecione **Criar novo** e introduza um nome para o grupo de recursos.
    4. Em **Localização**, selecione uma região/localização na qual pretende criar a conta de laboratório. 
    5. Selecione **Criar**. 

        ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Se não vir a página da conta de laboratório, selecione o botão **notificações** e, em seguida, clique no botão **Ir para recurso** nas notificações. 

    ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. Verá a página **conta de laboratório** seguinte:

    ![Página da conta de laboratório](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Adicionar um utilizador à função de Criador do Laboratório
Para configurar um laboratório de sala de aula numa conta de laboratório, o utilizador tem de ser membro da função **Criador de Laboratório** na conta de laboratório. A conta que utilizou para criar a conta de laboratório é adicionada automaticamente a esta função. Se estiver a planear utilizar a mesma conta de utilizador para criar um laboratório de sala de aula, pode ignorar este passo. Para utilizar outra conta de utilizador para criar um laboratório de sala de aula, siga os passos abaixo: 

Para dar permissão aos educadores para criarem laboratórios para as classes deles, adicione-os à função **Criador do Laboratório**:

1. Na página **Conta de Laboratório**, selecione **Controlo de acesso (IAM)** e clique em **+ Adicionar** na barra de ferramentas. 

    ![Página da conta de laboratório](../media/tutorial-setup-lab-account/access-control.png)
2. Na página **Adicionar permissões**, selecione **Criador do Laboratório** em **Função**, selecione o utilizador que quer adicionar à função de Criadores do Laboratório e selecione **Guardar**. 

    ![Adicionar utilizador à função de Criador do Laboratório](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)

## <a name="specify-marketplace-images-available-to-lab-owners"></a>Especifique as imagens do Marketplace disponíveis para os proprietários de laboratório
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

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou uma conta de laboratório. Para saber mais sobre como criar um laboratório de sala de aula como uma profissão, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Configurar um laboratório de sala de aula](tutorial-setup-classroom-lab.md)

