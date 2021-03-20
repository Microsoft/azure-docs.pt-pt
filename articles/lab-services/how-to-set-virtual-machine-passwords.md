---
title: Definir palavras-passe para VMs em Azure Lab Services | Microsoft Docs
description: Saiba como definir e redefinir palavras-passe para máquinas virtuais (VMs) em laboratórios da Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6ae577ee4c0c7e31760e0fb12afeaeac1ef8b7e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434231"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Configurar e gerir o conjunto de máquinas virtuais 
Este artigo mostra-lhe como fazer as seguintes tarefas:

- Aumentar o número de máquinas virtuais (VMs) no laboratório
- Inicie todos os VMs ou VMs selecionados 
- Reset VMs

## <a name="update-the-lab-capacity"></a>Atualizar a capacidade do laboratório
Para aumentar ou diminuir a capacidade do laboratório (número de máquinas virtuais em laboratório), faça os seguintes passos:

1. Na página **de piscina de máquina virtual,** selecione capacidade de **laboratório: &lt; &gt; máquinas numerais**.
2. Insira o novo **número de VMs** que deseja no laboratório. Este número deve ser superior ou igual ao número de utilizadores registados em laboratório. 
3. Em seguida, **selecione Save**. 

    ![Screenshot que mostra a janela "Capacidade de laboratório" com o botão "Maximum machines in lab" e "Save" selecionados.](./media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Se aumentar a capacidade, pode ver os VM ou VMs a serem criados. Se não vir o novo VM na lista, refresque a página. 

    ![VM sendo criado](./media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Iniciar VMs

### <a name="start-ot-stop-all-vms"></a>Inicie ot parar todos os VMs
1. Mude para a página **da piscina da máquina virtual.** 
2. **Selecione Iniciar tudo** a partir da barra de ferramentas. 

    ![Comece com todo o botão](./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Depois de todos os VMs terem começado, pode parar todos os VM selecionando o botão **Stop all** na barra de ferramentas. 

    ![Pare todos os botões](./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Iniciar VMs selecionados
Existem duas formas de iniciar VMs selecionados (um ou mais). A primeira forma é selecionar os VM ou VMs na lista e, em seguida, selecionar **Iniciar** na barra de ferramentas. 

A segunda forma é selecionar um ou mais VMs na lista e alternar o botão na coluna **do Estado.** 

![Iniciar VMs selecionados](./media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Da mesma forma, pode parar um ou mais VMs toggling o botão na coluna **State** ou selecionando **Parar** na barra de ferramentas. 

> [!NOTE]
> Quando um educador liga um VM estudantil, a quota para o aluno não é afetada. A quota para um utilizador especifica o número de horas de laboratório disponíveis para o utilizador fora do horário de aula programado. Para obter mais informações sobre quotas, consulte [Definição de quotas para utilizadores.](how-to-configure-student-usage.md?#set-quotas-for-users)

## <a name="reset-vms"></a>Reset VMs

Para reiniciar um ou mais VMs, selecione-os na lista e, em seguida, **selecione Reset** na barra de ferramentas. 

![Redefinir VMs selecionados](./media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

Na caixa de diálogo **(s) máquina virtual reset,** selecione **Reset**. 

![Caixa de diálogo VM reiniciada](./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)

## <a name="set-password-for-vms"></a>Definir palavra-passe para VMs
Um dono de laboratório (educador) pode definir/redefinir a palavra-passe para VMs no momento de criar o laboratório (assistente de criação de laboratório) ou depois de criar o laboratório na página **modelo.** 

### <a name="set-password-at-the-time-of-lab-creation"></a>Definir senha no momento da criação do laboratório
Um dono de laboratório (educador) pode definir uma palavra-passe para VMs no laboratório na página de **credenciais de máquina virtual** do assistente de criação de laboratório.

![Nova janela do laboratório](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Ao ativar/desativar a **mesma palavra-passe para todas as máquinas virtuais** nesta página, um educador pode optar por usar a mesma palavra-passe para todos os VMs em laboratório ou permitir que os alunos estabeleçam senhas para os seus VMs. Por predefinição, esta definição está ativada para todas as imagens do sistema operativo Windows e Linux, exceto ubuntu. Quando esta definição estiver desativada, os alunos serão solicitados a definir uma palavra-passe quando tentarem ligar-se ao VM pela primeira vez. 

### <a name="reset-password-later"></a>Redefinir a palavra-passe mais tarde

1. Na página **do modelo** do laboratório, selecione redefinir a **palavra-passe** na barra de ferramentas. 
1. Na caixa de diálogo de **palavras-passe Reset,** introduza uma palavra-passe e selecione **Redefinir a palavra-passe**.
    
    ![Definir caixa de diálogo de palavra-passe](./media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Ligar aos VMs do aluno
O criador do laboratório (educador) pode ligar-se a um aluno VM se estiverem reunidas as seguintes condições: 

- A **mesma palavra-passe de Utilização para todas as máquinas virtuais** foi selecionada ao criar o laboratório
- O VM está a funcionar. 

 Para ligar ao VM do aluno, passe o rato na VM na lista e selecione o botão do computador.  

![Ligue-se ao botão VM do aluno](./media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Quando o educador inicia o VM e se liga a ele, a quota de estudante não é afetada. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Lista de exportação de máquinas virtuais para um ficheiro CSV

1. Mude para o separador **piscina de máquina virtual.**
2. Selecione **...** (elipse) na barra de ferramentas e, em seguida, selecione **Export CSV**. 

    ![Lista de exportação de máquinas virtuais](./media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre outras opções de utilização do aluno que você (como proprietário de laboratório) pode configurar, consulte o seguinte artigo: [Configurar](how-to-configure-student-usage.md)o uso do aluno .

Para saber como os alunos podem redefinir palavras-passe para os seus VMs, consulte [set ou redefinir a palavra-passe para máquinas virtuais em laboratórios (estudantes)](how-to-set-virtual-machine-passwords-student.md).