---
title: Configurar configurações de imagem do Azure Marketplace em Azure DevTest Labs
description: Configure quais as imagens do Azure Marketplace podem ser usadas ao criar um VM em Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5158ed33bf253db1dbe0eb3232bc43d27ce15e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86512440"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Configurar configurações de imagem do Azure Marketplace em Azure DevTest Labs
A DevTest Labs suporta a criação de VMs com base em imagens do Azure Marketplace, dependendo da forma como configuraste as imagens do Azure Marketplace para serem usadas no seu laboratório. Este artigo mostra-lhe como especificar quais, se houver, imagens do Azure Marketplace podem ser usadas ao criar VMs em laboratório. Garante que a sua equipa só tem acesso às imagens do Marketplace de que necessitam. 

## <a name="specify-allowed-images-for-creating-vms"></a>Especifique imagens permitidas para a criação de VMs
Seguindo estes passos para especificar quais as imagens do Azure Marketplace que são permitidas ao criar um VM. 

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Todos os Serviços** e, em seguida, selecione **DevTest Labs** da lista.
3. Da lista de laboratórios, selecione o seu laboratório. 
4. Na página inicial da página do laboratório, selecione **Configuração e políticas**.
5. Na página de **Configuração e Políticas** do laboratório em **Bases de Máquinas Virtuais,** selecione **imagens do Marketplace**.
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


## <a name="troubleshoot"></a>Resolução de problemas
Se não conseguir encontrar uma imagem específica para ativar o laboratório, siga estes passos: 

- Verifique se consegue ver a imagem enquanto cria um VM compute.
- A imagem pode não estar disponível no tipo de subscrição que utiliza. Consulte o administrador de subscrição sobre o tipo de subscrição (por exemplo: MSDN, grátis, pay-as-you-go, e assim por diante.). 
- O suporte para imagens da Gen 2 em DevTest Labs é limitado. Se ambas as versões Gen 1 e Gen 2 estiverem disponíveis para uma imagem, a DevTest Labs mostra apenas a versão Gen 1 da imagem ao criar um VM. A solução é criar uma imagem personalizada da Gen 2 fora do laboratório e usá-la para criar um VM. Se existe apenas a versão Gen 2 da imagem disponível, é suportada e mostrada na lista pela DevTest Labs. 
      


## <a name="next-steps"></a>Passos seguintes
Depois de configurar como as imagens do Azure Marketplace são permitidas ao criar um VM, o próximo passo é [adicionar um VM ao seu laboratório.](devtest-lab-add-vm.md)

