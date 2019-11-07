---
title: Gerenciar contas de laboratório no Azure Lab Services | Microsoft Docs
description: Saiba como criar uma conta de laboratório, exibir todas as contas de laboratório ou excluir uma conta de laboratório em uma assinatura do Azure.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: ee64780bca13bf497793dc90ad22d3eaf91949a6
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606335"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Gerenciar contas de laboratório no Azure Lab Services 
No Azure Lab Services, uma conta de laboratório é um contêiner para tipos de laboratório gerenciados, como laboratórios de sala de aula. Um administrador configura uma conta de laboratório com Azure Lab Services e fornece acesso a proprietários de laboratório que podem criar laboratórios na conta. Este artigo descreve como criar uma conta de laboratório, exibir todas as contas de laboratório ou excluir uma conta de laboratório.

## <a name="create-a-lab-account"></a>Criar uma conta de laboratório
Os passos seguintes mostram como utilizar o portal do Azure para criar uma conta de laboratório com o Azure Lab Services. 

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu à esquerda. Selecione **contas de laboratório** na seção **DevOps** . Se você selecionar Star (`*`) ao lado de **contas de laboratório**, ela será adicionada à seção **favoritos** no menu à esquerda. Da próxima vez em diante, você seleciona contas de **laboratório** em **favoritos**.

    ![Todos os serviços-> contas de laboratório](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na página **contas do laboratório** , selecione **Adicionar** na barra de ferramentas. 

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

## <a name="view-lab-accounts"></a>Exibir contas de laboratório
1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os recursos** no menu. 
3. Selecione **contas de laboratório** para o **tipo**. 
    Você também pode filtrar por assinatura, grupo de recursos, locais e marcas. 

    ![Todos os recursos – > contas de laboratório](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Exibir e gerenciar laboratórios na conta do laboratório

1. Na página **conta do laboratório** , selecione **laboratórios** no menu à esquerda.

    ![Laboratórios na conta](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Você verá uma **lista de laboratórios** na conta com as seguintes informações: 
    1. Nome do laboratório.
    2. A data em que o laboratório foi criado. 
    3. Endereço de email do usuário que criou o laboratório. 
    4. Número máximo de usuários permitidos no laboratório. 
    5. Status do laboratório. 

## <a name="delete-a-lab-in-the-lab-account"></a>Excluir um laboratório na conta do laboratório
Siga as instruções na seção anterior para ver uma lista dos laboratórios na conta do laboratório.

1. Selecionar **... (reticências)** e selecione **excluir**. 

    ![Excluir um botão de laboratório](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Selecione **Sim** na mensagem de aviso. 

    ![Confirmar a exclusão do laboratório](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Excluir uma conta de laboratório
Siga as instruções da seção anterior que exibe as contas de laboratório em uma lista. Use as instruções a seguir para excluir uma conta de laboratório: 

1. Selecione a **conta de laboratório** que você deseja excluir. 
2. Selecione **excluir** na barra de ferramentas. 

    ![Contas de laboratório – botão > excluir](../media/how-to-manage-lab-accounts/delete-button.png)
1. Digite **Sim** para confirmação.
1. Selecione **Eliminar**. 

    ![Excluir conta de laboratório-confirmação](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Você também pode usar o módulo do PowerShell AZ. LabServices (versão prévia) para gerenciar contas de laboratório. Para obter mais informações, consulte [AZ. LabServices Home Page no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Passos seguintes
Consulte o seguinte artigo: [como configurar contas de laboratório](how-to-configure-lab-accounts.md).