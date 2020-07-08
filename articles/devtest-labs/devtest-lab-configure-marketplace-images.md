---
title: Configurar configurações de imagem do Azure Marketplace em Azure DevTest Labs
description: Configure quais as imagens do Azure Marketplace podem ser usadas ao criar um VM em Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 9fdb4e3a888e876f91b8af2e4854a9c101eea45c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482722"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Configurar configurações de imagem do Azure Marketplace em Azure DevTest Labs
A DevTest Labs suporta a criação de VMs com base em imagens do Azure Marketplace, dependendo da forma como configuraste as imagens do Azure Marketplace para serem usadas no seu laboratório. Este artigo mostra-lhe como especificar quais, se houver, imagens do Azure Marketplace podem ser usadas ao criar VMs em laboratório. Isto garante que a sua equipa só tem acesso às imagens do Marketplace de que necessitam. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Selecione quais as imagens do Azure Marketplace que são permitidas ao criar um VM
1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Da lista de laboratórios, selecione o laboratório desejado. 
4. Na lâmina do laboratório, selecione **Configuração e políticas**.
5. Na **configuração e nas políticas** do laboratório, em **bases de máquinas virtuais,** selecione **imagens do Marketplace**.
6. Especifique se deseja que todas as imagens qualificadas do Azure Marketplace estejam disponíveis para utilização como base de um novo VM. Se selecionar **Sim,** então todas as imagens do Azure Marketplace que satisfaçam todos os seguintes critérios são permitidas no laboratório:
   
   * A imagem cria um único VM, **e**
   * A imagem usa O Gestor de Recursos Azure para a prestação de VMs, **e**
   * A imagem não requer a compra de um plano de licenciamento extra
     
     Se não pretender que não sejam permitidas imagens ou se pretende especificar quais as imagens que podem ser utilizadas, selecione **Nº**.
     
     ![Opção para permitir que todas as imagens do Marketplace sejam usadas como imagens base para VMs](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Se selecionar **Não** para o passo anterior, as **imagens Permitidas/Selecione todas as** caixas de verificação. 
   Pode utilizar esta opção juntamente com a caixa de pesquisa para selecionar ou desmarcar rapidamente todos os itens apresentados na lista.
   * Selecione as imagens do Azure Marketplace que pretende permitir a criação de VM individualmente, verificando a caixa de verificação correspondente de cada imagem.
   * Selecione nada da lista se não quiser permitir que quaisquer imagens do Azure Marketplace sejam usadas no laboratório.
   
     ![Pode especificar quais as imagens do Azure Marketplace que podem ser usadas como imagens base para VMs](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximos passos
Depois de configurar como as imagens do Azure Marketplace são permitidas ao criar um VM, o próximo passo é [adicionar um VM ao seu laboratório.](devtest-lab-add-vm.md)

