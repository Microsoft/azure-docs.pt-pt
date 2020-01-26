---
title: Eliminar um laboratório ou VM num laboratório em Laboratórios Azure DevTest
description: Este artigo mostra-lhe como apagar um laboratório ou apagar um VM num laboratório utilizando o portal Azure (Azure DevTest Labs).
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 28fb464714f464a4c0a8f5eaf304dcdd5d603c90
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759725"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Eliminar um laboratório ou VM num laboratório em Laboratórios Azure DevTest
Este artigo mostra-lhe como apagar um laboratório ou VM num laboratório.

## <a name="delete-a-lab"></a>Apagar um laboratório
Ao eliminar uma instância de DevTest Labs de um grupo de recursos, o serviço DevTest Labs realiza as seguintes ações: 

- Todos os recursos que foram automaticamente criados no momento da criação do laboratório são automaticamente eliminados. O próprio grupo de recursos não é suprimido. Se criou manualmente quaisquer recursos deste grupo de recursos, o serviço não os apaga. 
- Todos os VMs do laboratório e grupos de recursos associados a estes VMs são automaticamente eliminados. Quando se cria um VM num laboratório, o serviço cria recursos (disco, interface de rede, endereço IP público, etc.) para o VM num grupo de recursos separado. No entanto, se criar manualmente quaisquer recursos adicionais nestes grupos de recursos, o serviço DevTest Labs não elimina esses recursos e o grupo de recursos. 

Para eliminar um laboratório, faça as seguintes ações: 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os recursos** do menu à esquerda, selecione **DevTest Labs** para o tipo de serviço e selecione o laboratório.

    ![Selecione o seu laboratório](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Na página **DevTest Lab,** clique em **Apagar** na barra de ferramentas. 

    ![Botão de eliminar](media/devtest-lab-delete-lab-vm/delete-button.png)
4. Na página **confirmação,** introduza o **nome** do seu laboratório e selecione **Eliminar**. 

    ![Confirmar](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. Para ver o estado da operação, selecione ícone **notificações** (Sino). 

    ![Notificações](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Apagar um VM em um laboratório
Se eu apagar um VM num laboratório, alguns dos recursos (nem todos) que foram criados no momento da criação do laboratório são eliminados. Os seguintes recursos não são eliminados: 

-   Cofre chave no grupo principal de recursos
-   Conjunto de disponibilidade, balanceador de carga, endereço IP público no grupo de recursos VM. Estes recursos são partilhados por vários VMs num grupo de recursos. 

A máquina virtual, a interface de rede e o disco associado saem. 

Para eliminar um VM num laboratório, faça as seguintes ações: 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os recursos** do menu à esquerda, selecione **DevTest Labs** para o tipo de serviço e selecione o laboratório.

    ![Selecione o seu laboratório](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Selecione **... (elipse)** para o VM na lista de VMs, e selecione **Eliminar**. 

    ![Eliminar VM no menu](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. Na caixa de diálogo de **confirmação,** selecione **Ok**. 
5. Para ver o estado da operação, selecione ícone **notificações** (Sino). 

Para eliminar um VM da **página Máquina Virtual,** selecione **Eliminar** da barra de ferramentas como mostrado na seguinte imagem:

![Eliminar VM da página VM](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>Passos seguintes
Se quiser criar um laboratório, consulte os seguintes artigos: 

- [Criar um laboratório](devtest-lab-create-lab.md)
- [Adicione um VM ao laboratório](devtest-lab-add-vm.md)