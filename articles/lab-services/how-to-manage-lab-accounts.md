---
title: Gerir contas de laboratório em Azure Lab Services | Microsoft Docs
description: Saiba como criar uma conta de laboratório, ver todas as contas do laboratório ou apagar uma conta de laboratório numa subscrição do Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 65ff28401b73698e320735613b32c96bf3de58f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434095"
---
# <a name="create-and-manage-lab-accounts"></a>Criar e gerir contas de laboratório
Nos Serviços de Laboratório Azure, uma conta de laboratório é um recipiente para tipos de laboratório geridos, como laboratórios. Um administrador cria uma conta de laboratório com os Serviços Azure Lab e dá acesso aos donos de laboratórios que podem criar laboratórios na conta. Este artigo descreve como criar uma conta de laboratório, ver todas as contas do laboratório ou apagar uma conta de laboratório.

## <a name="create-a-lab-account"></a>Criar uma conta de laboratório
Os passos seguintes mostram como utilizar o portal do Azure para criar uma conta de laboratório com o Azure Lab Services. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** no menu esquerdo. Selecione **Contas de Laboratório** na secção **DevOps.** Se selecionar estrela `*` () ao lado de **Contas de Laboratório,** é adicionado à secção **FAVORITOS** no menu esquerdo. A partir da próxima vez, selecione **Contas de Laboratório** em **FAVORITOS**.

    ![Todos os serviços -> Contas de Laboratório](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na página **Contas de Laboratório,** selecione **Adicionar** na barra de ferramentas ou criar conta **de laboratório** na página. 

    ![Selecione Adicionar na página contas de laboratório](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. No **separador Básico da** página de **conta de laboratório,** faça as seguintes ações: 
    1. Em **Nome da conta de laboratório**, introduza um nome. 
    2. Selecione a **Subscrição do Azure**, na qual pretende criar a conta de laboratório.
    3. Em **Grupo de recursos**: selecione **Criar novo** e introduza um nome para o grupo de recursos.
    4. Em **Localização**, selecione uma região/localização na qual pretende criar a conta de laboratório.
    5. Para que o criador do laboratório escolha o campo **de localização do laboratório,** especifique se deseja que os criadores de laboratório possam selecionar um local para o laboratório. Por predefinição, a opção é desativada. Quando está desativado, os criadores de laboratório não podem especificar a localização do laboratório que estão a criar. Os laboratórios são criados na localização geográfica mais próxima da conta do laboratório. Quando está ativado, um criador de laboratório pode selecionar um local no momento de criar um laboratório. Para obter mais informações, consulte [Permitir que o criador do laboratório escolha a localização para o laboratório.](allow-lab-creator-pick-lab-location.md) 

        ![Criar conta de laboratório -> Básicos](./media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Selecione **Seguinte: Avançado** na parte inferior da página para navegar para o separador **Avançado** e, em seguida, fazer os seguintes passos: 
    1. Selecione uma galeria de **imagens partilhada** existente ou crie uma. Pode guardar o modelo VM na galeria de imagens partilhada para que seja reutilizado por outros. Para obter informações detalhadas sobre galerias de imagens partilhadas, consulte [uma galeria de imagens partilhada nos Serviços Azure Lab](how-to-use-shared-image-gallery.md).
    2. Especifique se pretende desligar automaticamente as máquinas virtuais do Windows quando os utilizadores se desligarem das **máquinas.** Especifique quanto tempo as máquinas virtuais devem esperar que o utilizador se reconecte antes de desligar automaticamente. 
    3. Para **a rede virtual Peer,** selecione uma rede virtual de pares (VNet) para a rede de laboratório. Os laboratórios criados nesta conta estão ligados ao VNet selecionado e têm acesso aos recursos no VNet selecionado. Para obter mais informações, consulte [a rede virtual do seu laboratório com uma rede virtual de pares.](how-to-connect-peer-virtual-network.md)    
    8. Especifique um intervalo de **endereços** para VMs no laboratório. O intervalo de endereços deve estar na notação de encaminhamento inter-domínio (CIDR) sem classe (exemplo: 10.20.0.0/23). Máquinas virtuais no laboratório serão criadas nesta gama de endereços. Para obter mais informações, consulte [Especificar uma gama de endereços para VMs no laboratório](how-to-connect-peer-virtual-network.md#specify-an-address-range-for-vms-in-the-lab-account)  

        > [!NOTE]
        > A propriedade **do intervalo de endereços** só se aplica se uma **rede virtual de pares** estiver ativada para o laboratório.

        ![Criar conta de laboratório -> Advanced](./media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Selecione **Seguinte: Tags** na parte inferior da página para mudar para o **separador Tags.** Adicione todas as etiquetas que queira associar à conta de laboratório. As etiquetas são pares de nome/valor que lhe permitem categorizar recursos e visualizar a faturação consolidada aplicando a mesma etiqueta a múltiplos recursos e grupos de recursos. Para mais informações, consulte [utilize etiquetas para organizar os seus recursos Azure.](../azure-resource-manager/management/tag-resources.md)

    ![Screenshot que mostra a página "Criar conta de laboratório" com o separador Tags realçado.](./media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Selecione **Review + criar** na parte inferior desta página para mudar para o **separador 'Rever +' criar.** 
4. Reveja as informações resumidas nesta página e selecione **Criar**. 

    ![Criar contas de laboratório -> Tags](./media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Aguarde até que a implementação esteja completa, expanda **os próximos passos**, e selecione **Ir para** o recurso como mostrado na seguinte imagem: 

    Também pode selecionar o ícone da **campainha** na barra de **ferramentas (Notificações),** confirmar se a implementação foi bem sucedida e, em seguida, selecionar **Ir para o recurso**. 

    Em alternativa, **selecione Refresh** na página Contas de **Laboratório** e selecione a conta de laboratório que criou. 

    ![Janela Criar uma conta de laboratório](./media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Verá a página **conta de laboratório** seguinte:

    ![Página da conta de laboratório](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Ver contas de laboratório
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os recursos** do menu. 
3. Selecione **Contas de Laboratório** para o **tipo**. 
    Também pode filtrar por subscrição, grupo de recursos, localizações e tags. 

    ![Todos os recursos -> Contas de Laboratório](./media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>Apagar uma conta de laboratório
Siga as instruções da secção anterior que exibe contas de laboratório numa lista. Utilize as seguintes instruções para eliminar uma conta de laboratório: 

1. Selecione a **conta de laboratório** que pretende eliminar. 
2. **Selecione Eliminar** da barra de ferramentas. 

    ![Botão de excluir de contas de laboratório ->](./media/how-to-manage-lab-accounts/delete-button.png)
1. Tipo **Sim** para confirmação.
1. Selecione **Eliminar**. 

    ![Apagar conta de laboratório - confirmação](./media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Também pode utilizar o módulo Az.LabServices PowerShell (pré-visualização) para gerir contas de laboratório. Para mais informações, consulte a página inicial do [Az.LabServices no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Passos seguintes
Consulte outros artigos na secção **"Como-a-Guias**  ->  **Criar e configurar contas de laboratório" (dono** de conta de laboratório) da tabela de conteúdos (TOC). 