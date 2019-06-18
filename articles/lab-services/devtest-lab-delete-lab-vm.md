---
title: Eliminar uma VM de laboratório ou num laboratório no Azure DevTest Labs | Documentos da Microsoft
description: Este artigo mostra-lhe como eliminar uma VM de laboratório ou num laboratório.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: spelluru
ms.openlocfilehash: 9634c70566aba21bdd28ee016c9fa94464ec9c1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127417"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Eliminar uma VM de laboratório ou num laboratório no Azure DevTest Labs
Este artigo mostra-lhe como eliminar uma VM de laboratório ou num laboratório.

## <a name="delete-a-lab"></a>Eliminar um laboratório
Quando elimina uma instância de DevTest Labs de um grupo de recursos, o serviço de DevTest Labs efetua as seguintes ações: 

- Todos os recursos que foram criados automaticamente no momento da criação do laboratório são automaticamente eliminados. O grupo de recursos não é eliminado. Se tivesse criado manualmente todos os recursos este grupo de recursos, o serviço não eliminá-los. 
- Todas as VMs nos grupos de laboratório e recursos associados a estas VMs são automaticamente eliminadas. Quando cria uma VM num laboratório, o serviço cria recursos (disco, interface de rede, endereço IP público, etc.) para a VM num grupo de recursos separado. No entanto, se criar manualmente todos os recursos adicionais nestes grupos de recursos, o serviço de DevTest Labs não elimina os recursos e o grupo de recursos. 

Para eliminar um laboratório, efetue as seguintes ações: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os recursos** no menu à esquerda, selecione **DevTest Labs** para o tipo de serviço e selecione o laboratório.

    ![Selecione o seu laboratório](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Sobre o **laboratório Dev/Test** página, clique em **eliminar** na barra de ferramentas. 

    ![Botão de eliminar](media/devtest-lab-delete-lab-vm/delete-button.png)
4. Sobre o **confirmação** página, introduza o **nome** do seu laboratório e selecione **eliminar**. 

    ![Confirmar](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. Para ver o estado da operação, selecione **notificações** ícone (campainha). 

    ![Notificações](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Eliminar uma VM num laboratório
Se eliminar uma VM num laboratório, alguns dos recursos (não todos) que foram criados no momento da criação do laboratório são eliminados. Não são eliminados os seguintes recursos: 

-   Cofre, no grupo de recursos principais de chaves
-   Disponibilidade definida, balanceador, endereço IP público no grupo de recursos VM de carga. Estes recursos são compartilhados por várias VMs num grupo de recursos. 

Máquina virtual, a interface de rede e disco associado à VM são eliminados. 

Para eliminar uma VM num laboratório, efetue as seguintes ações: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os recursos** no menu à esquerda, selecione **DevTest Labs** para o tipo de serviço e selecione o laboratório.

    ![Selecione o seu laboratório](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Selecione **... (reticências)**  para a VM na lista de VMs e selecione **eliminar**. 

    ![Eliminar VM no menu](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. Sobre o **confirmação** caixa de diálogo, selecione **Ok**. 
5. Para ver o estado da operação, selecione **notificações** ícone (campainha). 

Para eliminar uma VM a partir da **página de Máquina Virtual**, selecione **eliminar** da barra de ferramentas, conforme mostrado na imagem seguinte:

![Eliminar VM a partir da página VM](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>Passos Seguintes
Se pretender criar um laboratório, veja os artigos seguintes: 

- [Criar um laboratório](devtest-lab-create-lab.md)
- [Adicionar uma VM para o laboratório](devtest-lab-add-vm.md)