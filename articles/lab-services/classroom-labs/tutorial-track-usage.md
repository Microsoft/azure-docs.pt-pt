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
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: e2831191905da1b9e0ad55131be9eaa7aa13950e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60777635"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Tutorial: Controlar a utilização de um laboratório no serviço de laboratório do Azure
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
4. Selecione **usuários** no menu da esquerda ou **utilizadores** mosaico. Verá os alunos que se registaram no laboratório. Selecione **Registration link** (Ligação de registo), copie a ligação e envie-a para alunos novos que ainda não se tenham registado no mesmo. 

    ![Utilizadores registados](../media/tutorial-track-usage/registered-users.png)

## <a name="view-the-usage-of-vms-in-the-lab"></a>Ver a utilização das VMs no laboratório 

1. Selecione **Máquinas virtuais**, no menu do lado esquerdo. 
2. Confirme que vê o estado das VMs e o número de horas que estiveram em execução. O tempo que despende de proprietário de um laboratório no estudante VM não contabiliza em relação ao tempo de utilização, mostrado na última coluna. 

    ![Utilização de VM](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Gerir VMs de alunos 
Conforme passa o mouse sobre uma linha na lista de máquina virtual, verá controles para realizar as seguintes tarefas (como mostrado na imagem na secção anterior): 

- Ligar a uma VM
- Iniciar uma VM
- Parar uma VM
- Eliminar uma VM


![Controlos de VMs](../media/tutorial-track-usage/vm-controls.png)

Também pode utilizar os botões da barra de ferramentas para iniciar, parar ou eliminar uma VM. 



## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre laboratórios de sala de aula, veja artigos sob [guias de procedimentos](how-to-manage-lab-accounts.md).
