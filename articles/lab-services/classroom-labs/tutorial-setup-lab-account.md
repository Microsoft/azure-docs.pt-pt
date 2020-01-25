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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 62ccda96563a53ebc77337bb2f89cfddd4410a26
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719046"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Tutorial: Configurar uma conta de laboratório com o Azure Lab Services
No Azure Lab Services, a conta de laboratório serve como a conta central em que são geridos os laboratórios na organização. Na conta de laboratório, conceda permissões para outros utilizadores criarem laboratórios e definir políticas aplicáveis a todos os laboratórios na conta de laboratório. Neste tutorial, aprenda como criar uma conta de laboratório como administrador de laboratório. 

Neste tutorial, vai realizar as seguintes ações:

> [!div class="checklist"]
> * Criar uma conta de laboratório
> * Adicionar um utilizador à função de Criador do Laboratório
> * Especifique as imagens do Marketplace disponíveis para os proprietários de laboratório

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-lab-account"></a>Criar uma conta de laboratório
Os passos seguintes mostram como utilizar o portal do Azure para criar uma conta de laboratório com o Azure Lab Services. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu à esquerda. Selecione **serviços de laboratório** na seção **DEVOPS** . Se você selecionar asterisco (`*`) ao lado de **serviços de laboratório**, ele será adicionado à seção **favoritos** no menu à esquerda. Da próxima vez em diante, selecione **serviços de laboratório** em **favoritos**.

    ![Todos os serviços-> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na página de **serviços de laboratório** , selecione **Adicionar** na barra de ferramentas. 

    ![Selecione Adicionar na página contas do laboratório](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na página **conta do laboratório** , execute as seguintes ações: 
    1. Em **Nome da conta de laboratório**, introduza um nome. 
    2. Selecione a **Subscrição do Azure**, na qual pretende criar a conta de laboratório.
    3. Em **Grupo de recursos**: selecione **Criar novo** e introduza um nome para o grupo de recursos.
    4. Em **Localização**, selecione uma região/localização na qual pretende criar a conta de laboratório. 
    5. Selecione uma **Galeria de imagens compartilhada** existente ou crie uma. Você pode salvar a VM de modelo na Galeria de imagens compartilhadas para que ela seja reutilizada por outras pessoas. Para obter informações detalhadas sobre galerias de imagens compartilhadas, consulte [usar uma galeria de imagens compartilhadas no Azure Lab Services](how-to-use-shared-image-gallery.md). 
    6. Para **rede virtual de mesmo nível**, selecione uma rede virtual par (VNet) para a rede do laboratório. Os laboratórios criados nessa conta estão conectados à VNet selecionada e têm acesso aos recursos na VNet selecionada. 
    7. Especifique um **intervalo de endereços** para as VMs no laboratório. O intervalo de endereços deve estar na notação CIDR (roteamento entre domínios sem classificação) (exemplo: 10.20.0.0/23). As máquinas virtuais no laboratório serão criadas neste intervalo de endereços. Para obter mais informações, consulte [especificar um intervalo de endereços para VMs no laboratório](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab).
    8. Para o campo **permitir que o criador** do laboratório escolha o local do laboratório, especifique se deseja que os criadores do laboratório possam selecionar um local para o laboratório. Por padrão, a opção está desabilitada. Quando desabilitado, os criadores de laboratório não podem especificar um local para o laboratório que estão criando. Os laboratórios são criados na localização geográfica mais próxima da conta do laboratório. Quando ele está habilitado, um criador de laboratório pode selecionar um local no momento da criação de um laboratório. 
    9. Selecione **Criar**. 

        ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Selecione o **ícone de sino** na barra de ferramentas (**notificações**), confirme se a implantação foi bem-sucedida e, em seguida, selecione **ir para o recurso**. 

    Como alternativa, selecione **Atualizar** na página **contas do laboratório** e selecione a conta do laboratório que você criou. 

    ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Verá a página **conta de laboratório** seguinte:

    ![Página da conta de laboratório](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Adicionar um utilizador à função de Criador do Laboratório
Para configurar um laboratório de sala de aula numa conta de laboratório, o utilizador tem de ser membro da função **Criador de Laboratório** na conta de laboratório. A conta que utilizou para criar a conta de laboratório é adicionada automaticamente a esta função. Se estiver a planear utilizar a mesma conta de utilizador para criar um laboratório de sala de aula, pode ignorar este passo. Para utilizar outra conta de utilizador para criar um laboratório de sala de aula, siga os passos abaixo: 

Para dar permissão aos educadores para criarem laboratórios para as classes deles, adicione-os à função **Criador do Laboratório**:

1. Na página **conta do laboratório** , selecione **controle de acesso (iam)** , selecione **+ Adicionar** na barra de ferramentas e, em seguida, selecione **+ Adicionar atribuição de função** na barra de ferramentas. 

    ![Controle de acesso – botão > Adicionar atribuição de função](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na página **Adicionar atribuição de função** , selecione **criador de laboratório** para **função**, selecione o usuário que você deseja adicionar à função criadores de laboratório e selecione **salvar**. 

    ![Adicionar criador de laboratório](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Especificar imagens do Marketplace disponíveis para criadores de laboratório
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
Neste tutorial, criou uma conta de laboratório. Para saber mais sobre como criar um laboratório de sala de aula como um professor, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Configurar um laboratório de sala de aula](tutorial-setup-classroom-lab.md)

