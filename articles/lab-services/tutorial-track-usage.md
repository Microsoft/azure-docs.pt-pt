---
title: Acompanhar a utilização de um laboratório no Azure Lab Services | Microsoft Docs
description: Neste tutorial, enquanto criador/proprietário do laboratório, vai acompanhar a utilização do mesmo.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: a9a9b49b568decc621be1969a8578d61ac7e4861
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445037"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Tutorial: Acompanhar a utilização de um laboratório no Azure Lab Services
Este tutorial mostra-lhe como é que os criadores/proprietários de laboratórios podem acompanhar a utilização dos laboratórios.

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Ver utilizadores registados no laboratório
> * Ver a utilização das VMs no laboratório
> * Gerir VMs de alunos 


## <a name="view-registered-users"></a>Ver utilizadores registados

1. Navegue para o [site do Azure Lab Services](https://labs.azure.com). 
2. Selecione **Iniciar sessão** e introduza as suas credenciais. O Azure Lab Services suporta contas organizacionais e contas Microsoft.
3. Na página **Os meus laboratórios**, selecione o laboratório cuja utilização quer acompanhar. 
4. Selecione **Utilizadores** no menu esquerdo ou azulejo **do Utilizadores.** Verá os alunos que se registaram no laboratório.  

    ![Utilizadores registados](./media/tutorial-track-usage/registered-users.png)

    Para obter mais informações sobre a adição e gestão de utilizadores para o laboratório, consulte [Adicionar e gerir os utilizadores de laboratório.](how-to-configure-student-usage.md)

## <a name="view-the-usage-of-vms"></a>Ver o uso de VMs

1. Selecione **Máquinas virtuais**, no menu do lado esquerdo. 
2. Confirme que vê o estado das VMs e o número de horas que estiveram em execução. O tempo que um dono de laboratório passa num VM estudantil não conta com o tempo de utilização mostrado na última coluna. 

    ![Utilização de VM](./media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Gerir VMs de alunos 
Nesta página, pode iniciar, parar ou redefinir os VMs dos alunos utilizando controlos na coluna **do Estado** ou na barra de ferramentas.

![Ações VM](./media/tutorial-track-usage/vm-controls.png)

Para obter mais informações sobre a gestão de conjuntos de máquinas virtuais para o laboratório, consulte [Configurar e gerir o pool de máquinas virtuais.](how-to-set-virtual-machine-passwords.md)

> [!NOTE]
> Quando um educador liga um VM estudantil, a quota para o aluno não é afetada. A quota para um utilizador especifica o número de horas de laboratório disponíveis para o utilizador fora do horário de aula programado. Para obter mais informações sobre quotas, consulte [Definição de quotas para utilizadores.](how-to-configure-student-usage.md?#set-quotas-for-users)

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre os laboratórios de sala de aula, consulte artigos em [guias de como fazer.](how-to-manage-lab-accounts.md)
