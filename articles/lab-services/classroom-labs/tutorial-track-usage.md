---
title: Acompanhar a utilização de um laboratório no Azure Lab Services | Microsoft Docs
description: Neste tutorial, enquanto criador/proprietário do laboratório, vai acompanhar a utilização do mesmo.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/18/2019
ms.author: spelluru
ms.openlocfilehash: 842392ab425628a1c82a39e25a65066064747211
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675779"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Tutorial: Acompanhar a utilização de um laboratório no Azure Lab Services
Este tutorial mostra-lhe como é que os criadores/proprietários de laboratórios podem acompanhar a utilização dos laboratórios.

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Ver utilizadores registados no laboratório
> * Ver a utilização das VMs no laboratório
> * Gerir VMs de alunos 


## <a name="view-users-registered-with-the-lab"></a>Ver utilizadores registados no laboratório

1. Navegue para o [site do Azure Lab Services](https://labs.azure.com). 
2. Selecione **Iniciar sessão** e introduza as suas credenciais. O Azure Lab Services suporta contas organizacionais e contas Microsoft.
3. Na página **Os meus laboratórios**, selecione o laboratório cuja utilização quer acompanhar. 
4. Selecione **usuários** no bloco do menu ou **usuários** à esquerda. Verá os alunos que se registaram no laboratório. Selecione **Registration link** (Ligação de registo), copie a ligação e envie-a para alunos novos que ainda não se tenham registado no mesmo. 

    ![Utilizadores registados](../media/tutorial-track-usage/registered-users.png)

## <a name="view-the-usage-of-vms-in-the-lab"></a>Ver a utilização das VMs no laboratório 

1. Selecione **Máquinas virtuais**, no menu do lado esquerdo. 
2. Confirme que vê o estado das VMs e o número de horas que estiveram em execução. O tempo que um proprietário de laboratório gasta em uma VM de aluno não conta com o tempo de uso mostrado na última coluna. 

    ![Utilização de VM](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Gerir VMs de alunos 
Nessa página, você pode iniciar, parar ou redefinir VMs de estudante usando uma das listas suspensas na coluna **status** ou nos botões na barra de ferramentas. 

![Controlos de VMs](../media/tutorial-track-usage/vm-controls.png)

Você também pode usar os botões da barra de ferramentas para iniciar, parar ou excluir uma VM. 


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre os laboratórios de sala de aula, consulte os artigos em [guias de instruções](how-to-manage-lab-accounts.md).
