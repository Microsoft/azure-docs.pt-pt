---
title: Definir senhas para VMs nos Serviços de Laboratório Azure  Microsoft Docs
description: Aprenda a definir e redefinir senhas para máquinas virtuais (VMs) em laboratórios de sala de aula da Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 0af2b5f7d756060d463d2b4f3ef71bdd135710ee
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77591528"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Configurar e gerir o conjunto de máquinas virtuais 
Este artigo mostra-lhe como fazer as seguintes tarefas:

- Aumentar o número de máquinas virtuais (VMs) no laboratório
- Iniciar todos os VMs ou VMs selecionados 
- Repor VMs

## <a name="update-the-lab-capacity"></a>Atualizar a capacidade do laboratório
Para aumentar ou diminuir a capacidade do laboratório (número de máquinas virtuais num laboratório), faça os seguintes passos:

1. Na página de piscina de **máquinas Virtuais,** selecione **capacidade do Laboratório: número &lt;&gt; máquinas.**
2. Insira o novo **número de VMs** que quer no laboratório. Este número deve ser maior ou igual ao número de utilizadores registados no laboratório. 
3. Em seguida, selecione **Guardar**. 

    ![Inicie todo o botão](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Se aumentar a capacidade, pode ver os VM ou VMs a serem criados. Se não vir o novo VM na lista, refresque a página. 

    ![VM sendo criado](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Iniciar VMs

### <a name="start-ot-stop-all-vms"></a>Inicie a paragem de todos os VMs
1. Mude para a página de piscina de **máquinas virtuais.** 
2. Selecione **Iniciar tudo** a partir da barra de ferramentas. 

    ![Inicie todo o botão](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Depois de todos os VMs estarem iniciados, pode parar todos os VMs selecionando o botão **Stop all** na barra de ferramentas. 

    ![Pare todos os botões](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Iniciar VMs selecionados
Existem duas formas de começar os VMs selecionados (uma ou mais). A primeira maneira é selecionar os VM ou VMs na lista e, em seguida, selecionar **Iniciar** na barra de ferramentas. 

A segunda forma é selecionar um ou mais VMs na lista e alternar o botão na coluna **do Estado.** 

![Iniciar VMs selecionados](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Da mesma forma, pode parar um ou mais VMs tocando o botão na coluna **do Estado** ou selecionando **Parar** na barra de ferramentas. 

> [!NOTE]
> Quando um educador liga um Estudante VM, a quota para o aluno não é afetada. A quota para um utilizador especifica o número de horas de laboratório disponíveis para o utilizador fora do horário de aula programado. Para obter mais informações sobre quotas, consulte [As quotas definidas para os utilizadores.](how-to-configure-student-usage.md?#set-quotas-for-users)

## <a name="reset-vms"></a>Repor VMs
Para redefinir um ou mais VMs, selecione-os na lista e, em seguida, selecione **Reset** na barra de ferramentas. 

![Redefinir VMs selecionados](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

Na caixa de diálogo da **máquina virtual reset,** selecione **Reset**. 

![Redefinir caixa de diálogo VM](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>Definir palavra-passe para VMs
Um dono de laboratório (professor) pode definir/redefinir a palavra-passe para VMs no momento da criação do laboratório (assistente de criação de laboratório) ou depois de criar o laboratório na página **Modelo.** 

### <a name="set-password-at-the-time-of-lab-creation"></a>Definir senha no momento da criação de laboratório
Um dono de laboratório (professor) pode definir uma senha para VMs no laboratório na página de **credenciais** de máquina virtual do assistente de criação de laboratório.

![Nova janela de laboratório](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Ao ativar/desativar a **mesma palavra-passe para todas as máquinas virtuais** nesta página, um professor pode optar por utilizar a mesma palavra-passe para todos os VMs do laboratório ou permitir que os alunos desloquem palavras-passe para os seus VMs. Por predefinição, esta definição está ativada para todas as imagens do sistema operativo Windows e Linux, exceto ubuntu. Quando esta definição for desativada, os alunos serão solicitados a definir uma palavra-passe quando tentarem ligar-se ao VM pela primeira vez. 

### <a name="reset-password-later"></a>Redefinir a palavra-passe mais tarde

1. Na página **'Modelo'** do laboratório, selecione **reset palavra-passe** na barra de ferramentas. 
1. Na caixa de diálogo de **palavra-passe Reset,** introduza uma palavra-passe e selecione **a palavra-passe Reset**.
    
    ![Definir caixa de diálogo de palavra-passe](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Ligar-se a VMs estudantis
O criador do laboratório (instrutor/professor) pode ligar-se a um VM do aluno se forem satisfeitas as seguintes condições: 

- A **mesma palavra-passe use para todas as máquinas virtuais** foi selecionada ao criar o laboratório
- O VM está a funcionar. 

 Para ligar ao VM do aluno, passe o rato no VM da lista e selecione o botão do computador.  

![Ligue-se ao botão VM do aluno](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Quando o professor inicia o VM e se liga a ele, a quota do aluno não é afetada. 

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre outras opções de utilização do aluno que você (como proprietário de laboratório) pode configurar, consulte o seguinte artigo: [Configure](how-to-configure-student-usage.md)o uso do aluno .

Para saber como os alunos podem redefinir senhas para os seus VMs, consulte [Definir ou redefinir palavra-passe para máquinas virtuais em laboratórios de sala de aula (alunos)](how-to-set-virtual-machine-passwords-student.md).