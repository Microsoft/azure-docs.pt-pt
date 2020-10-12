---
title: Apague um laboratório ou VM em um laboratório em Azure DevTest Labs
description: Este artigo mostra-lhe como apagar um laboratório ou apagar um VM num laboratório utilizando o portal Azure DevTest Labs).
ms.topic: article
ms.date: 01/24/2020
ms.openlocfilehash: 495fb98f3da41a47d316dd64554ba616ede0af47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481226"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Apague um laboratório ou VM em um laboratório em Azure DevTest Labs
Este artigo mostra como apagar um laboratório ou VM num laboratório.

## <a name="delete-a-lab"></a>Apagar um laboratório
Quando elimina uma instância da DevTest Labs de um grupo de recursos, o serviço DevTest Labs executa as seguintes ações: 

- Todos os recursos que foram automaticamente criados no momento da criação do laboratório são automaticamente eliminados. O próprio grupo de recursos não é eliminado. Se criou manualmente quaisquer recursos deste grupo de recursos, o serviço não os apaga. 
- Todos os VMs do laboratório e grupos de recursos associados a estes VMs são automaticamente eliminados. Quando cria um VM em laboratório, o serviço cria recursos (disco, interface de rede, endereço IP público, etc.) para o VM num grupo de recursos separado. No entanto, se criar manualmente quaisquer recursos adicionais nestes grupos de recursos, o serviço DevTest Labs não elimina esses recursos e o grupo de recursos. 

Para eliminar um laboratório, faça as seguintes ações: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os recursos** do menu à esquerda, selecione **DevTest Labs** para o tipo de serviço e selecione o laboratório.

    ![Selecione o seu laboratório](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Na página **DevTest Lab,** clique em **Eliminar** na barra de ferramentas. 

    ![Eliminar botão](media/devtest-lab-delete-lab-vm/delete-button.png)
4. Na página **confirmação,** insira o **nome** do seu laboratório e selecione **Delete**. 

    ![Confirmar](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. Para ver o estado da operação, selecione o ícone **notificações** (Bell). 

    ![Notificações](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Apagar um VM em um laboratório
Se eu apagar um VM num laboratório, alguns dos recursos (nem todos) que foram criados no momento da criação do laboratório são eliminados. Os seguintes recursos não são suprimidos: 

-   Cofre chave no grupo de recursos principal
-   Conjunto de disponibilidade, balanceador de carga, endereço IP público no grupo de recursos VM. Estes recursos são partilhados por vários VMs num grupo de recursos. 

A máquina virtual, a interface de rede e o disco associado ao VM são eliminados. 

Para eliminar um VM em laboratório, faça as seguintes ações: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os recursos** do menu à esquerda, selecione **DevTest Labs** para o tipo de serviço e selecione o laboratório.

    ![Selecione o seu laboratório](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Selecione **... (elipse)** para o VM na lista de VMs, e selecione **Delete**. 

    ![Eliminar VM no menu](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. Na caixa de diálogo de **confirmação,** selecione **Ok**. 
5. Para ver o estado da operação, selecione o ícone **notificações** (Bell). 

Para eliminar um VM da **página 'Máquina Virtual',** selecione **Eliminar** a barra de ferramentas como mostrado na seguinte imagem:

![Eliminar VM da página VM](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>Passos seguintes
Se quiser criar um laboratório, consulte os seguintes artigos: 

- [Criar um laboratório](devtest-lab-create-lab.md)
- [Adicionar uma VM ao laboratório](devtest-lab-add-vm.md)