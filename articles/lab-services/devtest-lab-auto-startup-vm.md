---
title: Configure as definições de arranque automático para um VM em Azure DevTest Labs [ Microsoft Docs
description: Aprenda a configurar as definições de arranque automático para VMs em laboratório. Esta definição permite que os VMs no laboratório sejam automaticamente iniciados num horário.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: 95f810ba16f358c5aabc35e26294cdb3f8c3cca0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807917"
---
# <a name="auto-startup-lab-virtual-machines"></a>Máquinas virtuais de laboratório de startups automáticas  
O Azure DevTest Labs permite configurar as máquinas virtuais no seu laboratório para serem automaticamente iniciadas e encerradas com base num horário. Para obter informações sobre a configuração das definições de encerramento automático, consulte Gerir as políticas de [encerramento automático para um laboratório em Laboratórios Azure DevTest](devtest-lab-auto-shutdown.md). 

Ao contrário do encerramento automático, onde todos os VMs são incluídos quando a apólice é ativada, a política de arranque automático requer que um utilizador de laboratório selecione explicitamente um VM e opte por este horário. Assim, não vai facilmente encontrar a situação em que vMs indesejados são acidentalmente iniciados automaticamente e causam gastos inesperados.

Este artigo mostra-lhe como configurar a política de arranque automático para um laboratório.

## <a name="configure-autostart-settings-for-a-lab"></a>Configure as definições de arranque automático para um laboratório 
1. Navegue para a página inicial para o seu laboratório. 
2. Selecione **Configuração e políticas** no menu esquerdo. 

    ![Menu de configuração e políticas](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. Na página **de Configuração e Políticas,** faça os seguintes passos:
    
    1. Selecione **on** for **Permitir que as máquinas virtuais estejam programadas para o arranque automático** para ativar a função de arranque automático para este laboratório. 
    2. Selecione uma hora de início (por exemplo: 8:00:00) para o campo de início do **Horário.** 
    3. Selecione um **fuso horário** a utilizar. 
    4. Selecione **dias da semana** em que os VMs precisam de ser iniciados automaticamente. 
    5. Em seguida, selecione **Guardar** na barra de ferramentas para guardar as definições. 

        ![Configurações de arranque automático](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Esta política não aplica automaticamente o arranque automático a quaisquer máquinas virtuais no laboratório. Para **optar por** máquinas virtuais individuais, vá à página da máquina virtual e ative o arranque **automático** desse VM.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Ativar o arranque automático para um VM no laboratório
O procedimento seguinte fornece-lhe passos para optar por um VM na política de arranque automático do laboratório. 

1. Na página inicial do seu laboratório, selecione o **VM** na lista das **minhas máquinas virtuais.** 

    ![Menu de configuração e políticas](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. Na página da **máquina Virtual,** selecione **Autostart** no menu esquerdo ou na lista de **Horários.** 

    ![Selecione menu de arranque automático](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. Na página **Autostart,** selecione **On** for the **Permitir que esta máquina virtual seja programada para** a opção de início automática.

    ![Ativar arranque automático para o VM](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Em seguida, selecione **Guardar** na barra de ferramentas para salvar a regulação. 


## <a name="next-steps"></a>Passos seguintes
Para aprender sobre a política de configuração de encerramento automático para um laboratório, consulte [Gerir políticas de encerramento automático para um laboratório em Azure DevTest Labs](devtest-lab-auto-shutdown.md)
