---
title: Gerir contas de laboratório no Azure Lab Services | Documentos da Microsoft
description: Saiba como criar uma conta de laboratório, ver todas as contas de laboratório ou eliminar uma conta de laboratório numa subscrição do Azure.
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
ms.date: 02/07/2018
ms.author: spelluru
ms.openlocfilehash: f1194d8385d1e7ddcb906d0c8c3a2b56648e2547
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120827"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Gerir contas de laboratório no Azure Lab Services 
No Azure Lab Services, uma conta de laboratório é um contentor para tipos de laboratório gerido, como laboratórios de sala de aula. Um administrador configura uma conta de laboratório com o Azure Lab Services e fornece acesso a proprietários de laboratório que podem criar laboratórios na conta. Este artigo descreve como criar uma conta de laboratório, ver todas as contas de laboratório ou eliminar uma conta de laboratório.

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
    5. Para **elemento da rede virtual**, selecione um elemento da rede virtual (VNet) para a rede de laboratório. Laboratórios criados nesta conta estão ligados à VNet selecionada e tem acesso aos recursos da VNet selecionada. 
    7. Para o **criador do laboratório de permitir a escolher a localização de laboratório** campo, especifique se pretende que os criadores de laboratório para poder selecionar uma localização para o laboratório. Por predefinição, a opção está desativada. Quando estiver desativada, os criadores de laboratório não é possível especificar uma localização para o laboratório estiver a criar. Os laboratórios são criados na localização geográfica mais próxima à conta de laboratório. Quando estiver ativada, um criador de laboratório pode selecionar uma localização no momento da criação de um laboratório.      
    8. Selecione **Criar**. 

        ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Selecione o **ícone de sino** na barra de ferramentas (**notificações**), confirme que a implementação foi concluída com êxito e, em seguida, selecione **Ir para recurso**. 

    Em alternativa, selecione **atualizar** sobre o **contas de laboratório** página e selecione a conta de laboratório que criou. 

    ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Verá a página **conta de laboratório** seguinte:

    ![Página da conta de laboratório](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Adicionar um utilizador à função de Criador do Laboratório
Para configurar um laboratório de sala de aula numa conta de laboratório, o utilizador tem de ser membro da função **Criador de Laboratório** na conta de laboratório. A conta que utilizou para criar a conta de laboratório é adicionada automaticamente a esta função. Se estiver a planear utilizar a mesma conta de utilizador para criar um laboratório de sala de aula, pode ignorar este passo. Para utilizar outra conta de utilizador para criar um laboratório de sala de aula, siga os passos abaixo: 

Para dar permissão aos educadores para criarem laboratórios para as classes deles, adicione-os à função **Criador do Laboratório**:

1. Sobre o **conta de laboratório** página, selecione **controlo de acesso (IAM)** e clique em **+ adicionar atribuição de função** na barra de ferramentas. 

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

## <a name="configure-the-lab-account"></a>Configurar a conta de laboratório
1. Sobre o **conta de laboratório** página, selecione **configuração de laboratórios** no menu da esquerda.

    ![Página de configuração de laboratórios](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Para **elemento da rede virtual**, selecione **ativado** ou **desativado**. O valor predefinido é **desativado**. Para ativar a rede virtual ponto a ponto, siga os passos abaixo: 
    1. Selecione **ativada**.
    2. Selecione o **VNet** na lista pendente. 
    3. Selecione **Guardar** na barra de ferramentas. 
    
        Laboratórios criados nesta conta estão ligados à rede virtual selecionada. Podem aceder aos recursos na rede virtual selecionada. 
3. Para o **criador do laboratório de permitir a escolher a localização de laboratório**, selecione **ativado** se pretender que o criador de laboratório para poder selecionar uma localização para o laboratório. Se estiver desativada, os laboratórios são criados automaticamente na mesma localização em que a conta de laboratório existe. 

## <a name="view-lab-accounts"></a>Contas de laboratório do Vista
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os recursos** no menu. 
3. Selecione **contas de laboratório** para o **tipo**. 
    Também pode filtrar por subscrição, grupo de recursos, localizações e as etiquetas. 

    ![Todos os recursos -> contas de laboratório](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Ver e gerir laboratórios na conta de laboratório

1. Sobre o **conta de laboratório** página, selecione **laboratórios** no menu da esquerda.

    ![Laboratórios na conta](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Verá um **lista de laboratórios** na conta com as seguintes informações: 
    1. Nome do laboratório.
    2. A data em que foi criado o laboratório. 
    3. Endereço de e-mail do utilizador que criou o laboratório. 
    4. Número máximo de utilizadores com permissão para o laboratório. 
    5. Estado do laboratório. 



## <a name="delete-a-lab-in-the-lab-account"></a>Eliminar um laboratório na conta de laboratório
Siga as instruções na secção anterior para ver uma lista dos laboratórios na conta de laboratório.

1. Selecione **... (reticências)** e selecione **eliminar**. 

    ![Eliminar um laboratório – botão](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Selecione **Sim** na mensagem de aviso. 

    ![Confirmar a eliminação de laboratório](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Eliminar uma conta de laboratório
Siga as instruções da secção anterior, que apresenta as contas de laboratório numa lista. Utilize as instruções seguintes para eliminar uma conta de laboratório: 

1. Selecione o **conta de laboratório** que pretende eliminar. 
2. Selecione **eliminar** da barra de ferramentas. 

    ![Contas de laboratório -> botão Delete](../media/how-to-manage-lab-accounts/delete-button.png)
1. Tipo **Sim** confirmação.
1. Selecione **Eliminar**. 

    ![Eliminar conta de laboratório - confirmação](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)



## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- [Como proprietário de um laboratório, criar e gerir laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário de um laboratório, configurar e publicar modelos](how-to-create-manage-template.md)
- [Como proprietário de um laboratório, configurar e controlar a utilização de um laboratório](how-to-configure-student-usage.md)
- [Como um utilizador de laboratório, acessar os laboratórios de sala de aula](how-to-use-classroom-lab.md)