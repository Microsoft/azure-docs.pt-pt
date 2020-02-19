---
title: Gerir contas de laboratório nos Serviços de Laboratório Azure  Microsoft Docs
description: Aprenda a criar uma conta de laboratório, veja todas as contas do laboratório ou apague uma conta de laboratório numa subscrição do Azure.
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
ms.openlocfilehash: c82b5d02ab3928eb0472f2a047cdca2238bf0b63
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443404"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Gerir contas de laboratório nos Serviços de Laboratório Azure 
Nos Serviços de Laboratório Azure, uma conta de laboratório é um recipiente para tipos de laboratório geridos, como laboratórios de sala de aula. Um administrador cria uma conta de laboratório com os Serviços de Laboratório Azure e dá acesso aos donos de laboratório que podem criar laboratórios na conta. Este artigo descreve como criar uma conta de laboratório, ver todas as contas do laboratório ou apagar uma conta de laboratório.

## <a name="create-a-lab-account"></a>Criar uma conta de laboratório
Os passos seguintes mostram como utilizar o portal do Azure para criar uma conta de laboratório com o Azure Lab Services. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** no menu esquerdo. Selecione **Contas laboratoriais** na secção **DevOps.** Se selecionar estrela (`*`) ao lado das **Contas laboratoriais,** é adicionado à secção **FAVORITES** no menu esquerdo. A partir da próxima vez, selecione **Contas laboratoriais** em **FAVORITOS**.

    ![Todos os Serviços -> Contas laboratoriais](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na página **Lab Accounts,** selecione **Adicionar** na barra de ferramentas ou **criar conta** de laboratório na página. 

    ![Selecione Adicionar na página contas de laboratório](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. No separador Basics da página criar **uma conta de laboratório,** faça as **seguintes** ações: 
    1. Em **Nome da conta de laboratório**, introduza um nome. 
    2. Selecione a **Subscrição do Azure**, na qual pretende criar a conta de laboratório.
    3. Em **Grupo de recursos**, selecione **Criar novo** e introduza um nome para o grupo de recursos.
    4. Em **Localização**, selecione uma região/localização na qual pretende criar a conta de laboratório.
    5. Para que o criador do laboratório escolha o campo de localização do **laboratório,** especifique se quer que os criadores de laboratório sejam capazes de selecionar um local para o laboratório. Por predefinição, a opção é desativada. Quando é desativado, os criadores de laboratório não podem especificar a localização para o laboratório que estão a criar. Os laboratórios são criados no local geográfico mais próximo da conta de laboratório. Quando está ativado, um criador de laboratório pode selecionar um local no momento de criar um laboratório. Para mais informações, consulte Permitir que o criador do [laboratório escolha a localização para o laboratório.](allow-lab-creator-pick-lab-location.md) 

        ![Criar conta de laboratório -> Básicos](../media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Selecione **Seguinte: Avançado** na parte inferior da página para navegar para o separador Avançado e, **em** seguida, fazer os seguintes passos: 
    1. Selecione uma galeria de **imagens partilhadas** existente ou crie uma. Você pode guardar o modelo VM na galeria de imagens partilhadas para que seja reutilizado por outros. Para obter informações detalhadas sobre galerias de imagem partilhadas, consulte [Use uma galeria de imagens partilhada nos Serviços de Laboratório Azure.](how-to-use-shared-image-gallery.md)
    2. Especifique se pretende **desligar automaticamente as máquinas virtuais do Windows** quando os utilizadores se desligam das mesmas. Especifique quanto tempo as máquinas virtuais devem esperar que o utilizador se reconecte antes de desligar automaticamente. 
    3. Para **a rede virtual Peer,** selecione uma rede virtual de pares (VNet) para a rede de laboratório. Os laboratórios criados nesta conta estão ligados ao VNet selecionado e têm acesso aos recursos na VNet selecionada. Para mais informações, consulte [Connect a rede virtual do seu laboratório com uma rede virtual de pares](how-to-connect-peer-virtual-network.md).    
    8. Especifique um intervalo de **endereços** para VMs no laboratório. A gama de endereços deve constar da notação de encaminhamento inter-domínio sem classe (CIDR) (exemplo: 10.20.0.0/23). Máquinas virtuais no laboratório serão criadas nesta gama de endereços. Para mais informações, consulte Especificar um intervalo de [endereços para VMs no laboratório](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab)  

        ![Criar conta de laboratório -> Advanced](../media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Selecione **Seguinte: Etiquetas** na parte inferior da página para mudar para o separador **Tags.** Adicione as etiquetas que quiser associar à conta de laboratório. As etiquetas são pares de nome/valor que lhe permitem categorizar recursos e visualizar a faturação consolidada aplicando a mesma etiqueta a múltiplos recursos e grupos de recursos. para mais informações, consulte [Use tags para organizar os seus recursos Azure.](../../azure-resource-manager/management/tag-resources.md)

    ![Criar conta de laboratório -> Tags](../media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Selecione **Review + crie** na parte inferior desta página para mudar para o separador **Review + criar.** 
4. Reveja as informações sumárias desta página e selecione **Criar**. 

    ![Criar conta de laboratório -> Tags](../media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Aguarde até que a implementação esteja concluída, expanda **os próximos passos**e selecione **Ir a recursos** como mostrado na seguinte imagem: 

    Também pode selecionar o ícone da **campainha** na barra de**ferramentas (Notificações),** confirmar que a implementação foi bem sucedida e, em seguida, selecionar **Ir para o recurso**. 

    Em alternativa, selecione **Refresh** na página **Lab Accounts** e selecione a conta de laboratório que criou. 

    ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Verá a página **conta de laboratório** seguinte:

    ![Página da conta de laboratório](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Ver contas de laboratório
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **todos os recursos** do menu. 
3. Selecione **Contas laboratoriais** para o **tipo**. 
    Também pode filtrar por subscrição, grupo de recursos, localizações e etiquetas. 

    ![Todos os recursos -> Contas laboratoriais](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Ver e gerir laboratórios na conta de laboratório

1. Na página **da Conta lab,** selecione **Todos os laboratórios** no menu esquerdo.

    ![Laboratórios na conta](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Vê uma **lista de laboratórios** na conta com as seguintes informações: 
    1. Nome do laboratório.
    2. A data em que o laboratório foi criado. 
    3. Endereço de e-mail do utilizador que criou o laboratório. 
    4. Número máximo de utilizadores autorizados a entrar no laboratório. 
    5. Estado do laboratório. 
    6. Atribuições de funções. 

## <a name="delete-a-lab-in-the-lab-account"></a>Apagar um laboratório na conta do laboratório
Siga as instruções na secção anterior para ver uma lista dos laboratórios na conta do laboratório.

1. Selecione **... (elipse)** , e selecione **Delete**. 

    ![Apagar um laboratório - botão](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Selecione **Sim** na mensagem de aviso. 

    ![Confirmar a eliminação do laboratório](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Apagar uma conta de laboratório
Siga as instruções da secção anterior que apresenta as contas laboratoriais numa lista. Utilize as seguintes instruções para eliminar uma conta de laboratório: 

1. Selecione a conta de **laboratório** que pretende eliminar. 
2. **Selecione Apagar** a partir da barra de ferramentas. 

    ![Contas de Laboratório -> Apagar botão](../media/how-to-manage-lab-accounts/delete-button.png)
1. Escreva **Sim** para confirmação.
1. Selecione **Eliminar**. 

    ![Eliminar conta de laboratório - confirmação](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Também pode utilizar o módulo Az.LabServices PowerShell (pré-visualização) para gerir contas de laboratório. Para mais informações, consulte a página inicial da [Az.LabServices no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Permitir que o criador do laboratório escolha a localização do laboratório](allow-lab-creator-pick-lab-location.md)
- [Ligue a rede do seu laboratório com uma rede virtual de pares](how-to-connect-peer-virtual-network.md)
- [Anexar uma galeria de imagens partilhadas a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicione um utilizador como proprietário de laboratório](how-to-add-user-lab-owner.md)
- [Ver configurações de firewall para um laboratório](how-to-configure-firewall-settings.md)
- [Configure outras definições para um laboratório](how-to-configure-lab-accounts.md)