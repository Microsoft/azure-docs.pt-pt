---
title: Anexe ou desprete uma galeria de imagens partilhada no Azure Lab Services | Microsoft Docs
description: Este artigo descreve como anexar uma galeria de imagens partilhada a um laboratório de sala de aula em Azure Lab Services.
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: 99b944ad85e311cd66b0a1cec4e585de37f83500
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786406"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Anexar ou destacar uma galeria de imagens partilhada nos Serviços do Laboratório Azure
Este artigo mostra-lhe como anexar ou separar uma galeria de imagens partilhada numa conta de laboratório. 

> [!NOTE]
> Quando [guarda uma imagem de modelo de um laboratório](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) em Azure Lab Services para uma galeria de imagens partilhada, a imagem é enviada para a galeria como uma imagem especializada. [As imagens especializadas](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) mantêm informações específicas da máquina e perfis de utilizador. Você ainda pode enviar diretamente uma imagem generalizada para a galeria fora dos Serviços Azure Lab. 
>
> Um criador de laboratório pode criar um modelo VM baseado em imagens generalizadas e especializadas nos Serviços de Laboratório Azure. 

## <a name="scenarios"></a>Cenários
Aqui estão os dois cenários apoiados por esta característica: 

- Um administrador de contas de laboratório anexa uma galeria de imagens partilhada à conta do laboratório, e envia uma imagem para a galeria de imagens partilhada fora do contexto de um laboratório. Depois, os criadores de laboratório podem usar essa imagem da galeria de imagens partilhada para criar laboratórios. 
- Uma conta de laboratório anexa uma galeria de imagens partilhada à conta do laboratório. Um criador de laboratório (instrutor) guarda a imagem personalizada do seu laboratório para a galeria de imagens partilhada. Em seguida, outros criadores de laboratório podem selecionar esta imagem a partir da galeria de imagens partilhada para criar um modelo para os seus laboratórios. 

    Quando uma imagem é guardada numa galeria de imagens partilhada, a Azure Lab Services replica a imagem guardada para outras regiões disponíveis na mesma [geografia.](https://azure.microsoft.com/global-infrastructure/geographies/) Garante que a imagem está disponível para laboratórios criados noutras regiões na mesma geografia. Guardar imagens para uma galeria de imagens partilhadas incorre num custo adicional, que inclui o custo de todas as imagens replicadas. Este custo é separado do custo de utilização dos Serviços Azure Lab. Para obter mais informações sobre os preços da Galeria de Imagens Partilhadas, consulte [a Galeria de Imagens Partilhadas – Billing](../virtual-machines/shared-image-galleries.md#billing).

> [!IMPORTANT]
> Durante a utilização de uma Galeria de Imagens Partilhadas, a Azure Lab Services suporta apenas imagens com menos de 128 GB de ESPAÇO DISCO OS. Imagens com mais de 128 GB de espaço em disco ou vários discos não serão mostradas na lista de imagens de máquinas virtuais durante a criação de laboratório.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurar no momento da criação de conta de laboratório
Quando estiver a criar uma conta de laboratório, pode anexar uma galeria de imagens partilhada à conta do laboratório. Pode selecionar uma galeria de imagens partilhada existente a partir da lista de drop-down ou criar uma nova. Para criar e anexar uma galeria de imagens partilhada à conta do laboratório, selecione **Criar novo,** insira um nome para a galeria e introduza **OK**. 

![Configurar galeria de imagens partilhada no momento da criação de conta de laboratório](./media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configurar após a criação da conta de laboratório
Após a criação da conta de laboratório, pode fazer as seguintes tarefas:

- Criar e anexar uma galeria de imagens partilhada
- Anexar uma galeria de imagens partilhada na conta do laboratório
- Desprender uma galeria de imagens partilhada da conta do laboratório

## <a name="create-and-attach-a-shared-image-gallery"></a>Criar e anexar uma galeria de imagens partilhada
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** no menu esquerdo. Selecione **Serviços de Laboratório** na secção **DEVOPS.** Se selecionar estrela `*` () ao lado dos **Serviços de Laboratório,** é adicionado à secção **FAVORITOS** no menu esquerdo. A partir da próxima vez, selecione **Serviços de Laboratório** em **FAVORITOS**.

    ![Todos os Serviços -> Serviços de Laboratório](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecione a sua conta de laboratório para ver a página **da Conta de Laboratório.** 
4. Selecione **galeria de imagens partilhada** no menu esquerdo e selecione + **Criar** na barra de ferramentas.  

    ![Criar botão de galeria de imagens partilhada](./media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Na janela da **galeria de imagens partilhada Create,** insira um **nome** para a galeria e insira **OK**. 

    ![Criar janela de galeria de imagens partilhada](./media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    A Azure Lab Services cria a galeria de imagens partilhada e anexou-a à conta do laboratório. Todos os laboratórios criados nesta conta de laboratório têm acesso à galeria de imagens partilhada anexada. 

    ![Galeria de imagens anexa](./media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    No painel inferior, vê-se imagens na galeria de imagens partilhada. Nesta nova galeria, não há imagens. Quando faz o upload das imagens para a galeria, vê-as nesta página.     

    Todas as imagens na galeria de imagens partilhada anexada são ativadas por padrão. Pode ativar ou desativar imagens selecionadas selecionando-as na lista e utilizando o botão **Desativar imagens selecionadas** ou **desativar imagens selecionadas.**

## <a name="attach-an-existing-shared-image-gallery"></a>Anexar uma galeria de imagens partilhada existente
O procedimento a seguir mostra como anexar uma galeria de imagens partilhada existente a uma conta de laboratório. 

1. Na página **'Contas lab',** selecione **a galeria de imagens partilhadas** no menu esquerdo e selecione **Fixe** na barra de ferramentas. 

    ![Galeria de imagens partilhada - Adicionar botão](./media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Na **página 'Anexar' uma** página existente na Galeria de Imagens Partilhadas, selecione a sua galeria de imagens partilhada e selecione **OK**.

    ![Selecione uma galeria existente](./media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Vê o seguinte ecrã: 

    ![Minha galeria na lista](./media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Neste exemplo, ainda não há imagens na galeria de imagens partilhada.

    A identidade do Azure Lab Services é adicionada como contribuinte para a galeria de imagens partilhada que está anexada ao laboratório. Permite aos educadores/administradores de TI guardar imagens de máquinas virtuais para a galeria de imagens partilhada. Todos os laboratórios criados nesta conta de laboratório têm acesso à galeria de imagens partilhada anexada. 

    Todas as imagens na galeria de imagens partilhada anexada são ativadas por padrão. Pode ativar ou desativar imagens selecionadas selecionando-as na lista e utilizando o botão **Desativar imagens selecionadas** ou **desativar imagens selecionadas.** 

## <a name="detach-a-shared-image-gallery"></a>Desprender uma galeria de imagens partilhada
Apenas uma galeria de imagens partilhada pode ser anexada a um laboratório. Se quiser anexar outra galeria de imagens partilhada, retire a atual antes de anexar a nova. Para separar uma galeria de imagens partilhada do seu laboratório, selecione **Detach** na barra de ferramentas e confirme a operação de retirada. 

![Retire a galeria de imagens partilhada da conta do laboratório](./media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Próximos passos
Para aprender como guardar uma imagem de laboratório na galeria de imagens partilhada ou usar uma imagem da galeria de imagens partilhada para criar um VM, veja [Como usar a galeria de imagens partilhada.](how-to-use-shared-image-gallery.md)

Para obter mais informações sobre galerias de imagens partilhadas em geral, consulte [a galeria de imagens partilhada.](../virtual-machines/shared-image-galleries.md)