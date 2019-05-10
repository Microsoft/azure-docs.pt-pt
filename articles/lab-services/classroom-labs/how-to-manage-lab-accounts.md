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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 6f283ce007e96547e01a01a3753ddcb60574bfc3
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412810"
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
    5. Selecione um existente **Galeria de imagem partilhada** ou criar uma. Pode guardar o modelo de VM na Galeria de imagens partilhado para que ele seja reutilizado por outros utilizadores. Para obter informações detalhadas sobre as galerias de imagem partilhada, consulte [utilizar uma galeria de imagem partilhada do Azure Lab Services](how-to-use-shared-image-gallery.md).
    6. Para **elemento da rede virtual**, selecione um elemento da rede virtual (VNet) para a rede de laboratório. Laboratórios criados nesta conta estão ligados à VNet selecionada e tem acesso aos recursos da VNet selecionada. 
    7. Especifique um **intervalo de endereços** para VMs do laboratório. O intervalo de endereços deve estar na notação de classless entre domínios de encaminhamento (CIDR) (exemplo: 10.20.0.0/23). Máquinas virtuais no laboratório serão criadas neste intervalo de endereço. Para obter mais informações, consulte [especificar um intervalo de endereços para VMs do laboratório](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab).    
    8. Para o **criador do laboratório de permitir a escolher a localização de laboratório** campo, especifique se pretende que os criadores de laboratório para poder selecionar uma localização para o laboratório. Por predefinição, a opção está desativada. Quando estiver desativada, os criadores de laboratório não é possível especificar uma localização para o laboratório estiver a criar. Os laboratórios são criados na localização geográfica mais próxima à conta de laboratório. Quando estiver ativada, um criador de laboratório pode selecionar uma localização no momento da criação de um laboratório.      
    9. Selecione **Criar**. 

        ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Selecione o **ícone de sino** na barra de ferramentas (**notificações**), confirme que a implementação foi concluída com êxito e, em seguida, selecione **Ir para recurso**. 

    Em alternativa, selecione **atualizar** sobre o **contas de laboratório** página e selecione a conta de laboratório que criou. 

    ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Verá a página **conta de laboratório** seguinte:

    ![Página da conta de laboratório](../media/tutorial-setup-lab-account/lab-account-page.png)

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
Veja o seguinte artigo: [Como configurar contas de laboratório](how-to-configure-lab-accounts.md).