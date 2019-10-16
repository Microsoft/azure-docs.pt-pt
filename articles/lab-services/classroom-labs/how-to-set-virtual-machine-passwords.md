---
title: Definir senhas para VMs no Azure Lab Services | Microsoft Docs
description: Saiba como definir e redefinir senhas para VMs (máquinas virtuais) em laboratórios de sala de aula do Azure Lab Services.
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 40cdd0adf7bf100e1dbca64dbba68db3bc59a4fe
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331505"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Configurar e gerenciar o pool de máquinas virtuais 
Este artigo mostra como realizar as seguintes tarefas:

- Aumentar o número de máquinas virtuais (VMs) no laboratório
- Iniciar todas as VMs ou VMs selecionadas 
- Redefinir VMs

## <a name="update-the-lab-capacity"></a>Atualizar a capacidade do laboratório
Para aumentar ou diminuir a capacidade do laboratório (número de máquinas virtuais em um laboratório), execute as seguintes etapas:

1. Na página **pool de máquinas virtuais** , selecione **capacidade do laboratório: &lt;Number @ no__t-3 máquinas**.
2. Insira o novo **número de VMs** que você deseja no laboratório. Esse número deve ser maior ou igual ao número de usuários registrados no laboratório. 
3. Em seguida, selecione **Guardar**. 

    ![Botão iniciar tudo](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Se você aumentou a capacidade, poderá ver a VM ou as VMs que estão sendo criadas. 

    ![VM sendo criada](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Iniciar VMs

### <a name="start-ot-stop-all-vms"></a>Começar a fazer parar todas as VMs
1. Alterne para a página do **pool de máquinas virtuais** . 
2. Selecione **Iniciar tudo** na barra de ferramentas. 

    ![Botão iniciar tudo](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Depois que todas as VMs forem iniciadas, você poderá interromper todas as VMs selecionando o botão **parar tudo** na barra de ferramentas. 

    ![Botão parar tudo](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Iniciar VMs selecionadas
Há duas maneiras de iniciar as VMs selecionadas (uma ou mais). A primeira maneira é selecionar a VM ou as VMs na lista e, em seguida, selecionar **Iniciar** na barra de ferramentas. A segunda maneira é selecionar a VM ou as VMs na lista, selecionar lista suspensa na coluna **estado** em uma das linhas e, em seguida, selecionar **Iniciar**. 

![Iniciar VMs selecionadas](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Da mesma forma, você pode interromper uma ou mais VMs usando a lista suspensa na coluna **estado** ou **parar** na barra de ferramentas. 

## <a name="reset-vms"></a>Redefinir VMs
Para redefinir uma ou mais VMs, selecione-as na lista e, em seguida, selecione **Redefinir** na barra de ferramentas. 

![Redefinir as VMs selecionadas](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

Na caixa de diálogo **Redefinir máquina (s) virtual (is)** , selecione **Redefinir**. 

![Caixa de diálogo redefinir VM](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>Definir senha para VMs
Um professor (proprietário do laboratório) pode definir/redefinir a senha para VMs no momento da criação do laboratório (Assistente de criação de laboratório) ou depois de criar o laboratório na página de **modelo** . 

### <a name="set-password-at-the-time-of-lab-creation"></a>Definir a senha no momento da criação do laboratório
Um professor (proprietário do laboratório) pode definir uma senha para VMs no laboratório na página de **credenciais da máquina virtual** do assistente de criação de laboratório.

![Nova janela de laboratório](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Ao habilitar/desabilitar a opção **usar a mesma senha para todas as máquinas virtuais** nesta página, um professor pode optar por usar a mesma senha para todas as VMs no laboratório ou permitir que os alunos definam senhas para suas VMs. Por padrão, essa configuração é habilitada para todas as imagens do sistema operacional Windows e Linux, exceto o Ubuntu. Quando essa configuração estiver desabilitada, os alunos serão solicitados a definir uma senha quando tentarem se conectar à VM pela primeira vez. 

### <a name="reset-password-later"></a>Redefinir senha mais tarde

1. Na página **modelo** do laboratório, selecione **Redefinir senha** na barra de ferramentas. 

    ![Menu Redefinir senha na home page](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. Na caixa de diálogo **Redefinir senha** , insira uma senha e selecione **Redefinir senha**.
    
    ![Caixa de diálogo Definir senha](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as outras opções de uso do aluno que você (como proprietário do laboratório) podem configurar, consulte o seguinte artigo: [Configurar o uso do aluno](how-to-configure-student-usage.md).

Para saber mais sobre como os alunos podem redefinir senhas para suas VMs, consulte [definir ou Redefinir senha para máquinas virtuais em laboratórios de sala de aula (estudantes)](how-to-set-virtual-machine-passwords-student.md).