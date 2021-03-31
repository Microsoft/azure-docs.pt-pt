---
title: Configurar configurar configurações de arranque automático para um VM em Azure DevTest Labs | Microsoft Docs
description: Aprenda a configurar configurações de arranque automático para VMs em laboratório. Esta definição permite que os VMs no laboratório sejam automaticamente iniciados num horário.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 83e7b0836273a59eaaf66471bd0cb42d63ccf1c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91328486"
---
# <a name="auto-startup-lab-virtual-machines"></a>Máquinas virtuais de laboratório de startups automáticas  
A Azure DevTest Labs permite configurar máquinas virtuais no seu laboratório para serem automaticamente iniciadas e encerradas com base num horário. Para obter informações sobre configurar as definições de autoshutdown, consulte [as políticas de autoshutdown para um laboratório em Azure DevTest Labs](devtest-lab-auto-shutdown.md). 

Ao contrário do autoshutdown, onde todos os VMs são incluídos quando a política é ligada, a política de arranque automático requer que um utilizador de laboratório selecione explicitamente um VM e opte por este horário. Dessa forma, não encontrará facilmente a situação em que os VMs indesejados são acidentalmente iniciados e causam gastos inesperados.

Este artigo mostra-lhe como configurar a política de arranque automático para um laboratório.

## <a name="configure-autostart-settings-for-a-lab"></a>Configurar configurar configurações de arranque automático para um laboratório 
1. Navegue para a página inicial para o seu laboratório. 
2. Selecione **Configuração e políticas** no menu à esquerda. 

    ![Screenshot que mostra o menu "Configuração e políticas" no DevTest Lab.](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. Na página **de Configuração e políticas,** faça os seguintes passos:
    
    1. Selecione **On** for **Allow machines virtuais a ser programado para iniciar automaticamente** para ativar a função de arranque automático para este laboratório. 
    2. Selecione uma hora de início (por exemplo: 8:00:00 AM) para o campo **de partida agendamento.** 
    3. Selecione um **fuso horário** a utilizar. 
    4. Selecione **os dias da semana** em que os VMs precisam de ser iniciados automaticamente. 
    5. Em seguida, **selecione Guarde** na barra de ferramentas para guardar as definições. 

        ![Definições de arranque automático](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Esta política não aplica automaticamente o arranque automático de máquinas virtuais em laboratório. Para **optar por** máquinas virtuais individuais, vá à página da máquina virtual e ative o arranque **automático** para esse VM.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Ativar automaticamente um VM no laboratório
O procedimento a seguir proporciona-lhe passos para optar por um VM na política de arranque automático do laboratório. 

1. Na página inicial do seu laboratório, selecione o **VM** na lista **de máquinas virtuais.** 

    ![Menu de configuração e políticas](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. Na página **da máquina Virtual,** selecione **Iniciar automaticamente** no menu esquerdo ou na lista **Agendas.** 

    ![Selecione menu de arranque automático](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. Na página **De arranque automático,** selecione **On** for the Allow this virtual machine **to scheduled for automatic start** option.

    ![Ativar automaticamente para o VM](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Em seguida, **selecione Guarde** na barra de ferramentas para guardar a definição. 


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a política de configuração de autoshutdown para um laboratório, consulte [Gerir políticas de autoshutdown para um laboratório em Azure DevTest Labs](devtest-lab-auto-shutdown.md)
