---
title: Criar horário para laboratórios de sala de aula nos Serviços de Laboratório do Azure [ Microsoft Docs
description: Aprenda a criar horários para laboratórios de salas de aula em Serviços de Laboratório Azure para que os VMs nos laboratórios comecem e desliguem num momento determinado.
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
ms.openlocfilehash: 4887b4359451ca5ce85042b4de42d5376bf4a730
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80667772"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Criar e gerir horários para laboratórios de salas de aula em Serviços de Laboratório Azure 
Os horários permitem configurar um laboratório de sala de aula de modo a que os VMs no laboratório comecem e desliguem automaticamente num determinado momento. Pode definir um horário único ou um horário recorrente. Os seguintes procedimentos dão-lhe passos para criar e gerir horários para um laboratório de sala de aula: 

> [!IMPORTANT]
> O tempo de funcionamento programado dos VMs não conta com a [quota atribuída a um utilizador](how-to-configure-student-usage.md#set-quotas-for-users). A quota é para o tempo fora do horário horário que um aluno gasta em VMs. 

## <a name="set-a-schedule-for-the-lab"></a>Estabeleça um horário para o laboratório
Crie um evento programado para o laboratório para que os VMs no laboratório sejam automaticamente iniciados/parados em momentos específicos. A quota de utilizador especificada anteriormente é o tempo adicional atribuído a cada utilizador fora desta hora programada. 

> [!NOTE]
> Antes de começarmos, eis como os horários afetam as máquinas virtuais de laboratório: 
>- A máquina virtual do modelo não está incluída nos horários. 
>- Apenas as máquinas virtuais atribuídas são iniciadas. Isto significa que, se uma máquina não for reclamada por um utilizador final (aluno), a máquina não iniciará as horas programadas. 
>- Todas as máquinas virtuais (reclamadas por um utilizador ou não) são interrompidas com base no horário do laboratório. 

1. Mude para a página **'Agendas'** e selecione **Adicionar evento agendado** na barra de ferramentas. 

    ![Adicione o botão de agenda na página 'Agendas'](../media/how-to-create-schedules/add-schedule-button.png)
2. Confirme que a **Standard** é selecionada do **tipo Evento**. Selecione **Iniciar apenas** para especificar apenas a hora de início dos VMs. Selecione **Parar apenas** para especificar apenas o tempo de paragem para os VMs. 
7. Na secção **Repeat,** selecione o horário atual. 

    ![Adicione o botão de agenda na página 'Agendas'](../media/how-to-create-schedules/select-current-schedule.png)
5. Na caixa de diálogo **Repeat,** faça os seguintes passos:
    1. Confirme que **todas as semanas** estão definidas para o campo **Repeat.** 
    3. Especifique a data de **início**.
    4. Especifique a hora de **início** em que pretende que os VMs sejam iniciados.
    5. Especifique o tempo de **paragem** em que os VMs devem ser desligados. 
    6. Especifique o **fuso horário** para os tempos de início e paragem que especificou. 
    2. Selecione os dias em que pretende que o horário faça efeito. No exemplo seguinte, de segunda a quinta-feira é selecionado. 
    8. Selecione **Guardar**. 

        ![Definir agenda de repetição](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Agora, na página **de eventos agendado seleto,** para **Notas (opcional)**, insira qualquer descrição ou notas para a programação. 
4. Na página **de eventos agendada seleção,** selecione **Guardar**. 

    ![Horário semanal](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Ver horários no calendário
Pode ver as datas e horários programados destacados na vista do calendário, como mostra a seguinte imagem:

![Horários na vista do calendário](../media/how-to-create-schedules/schedules-calendar.png)

Selecione o botão **Today** no canto superior direito para mudar para a data atual do calendário. Selecione **seta esquerda** para mudar para a semana anterior e **seta direita** para mudar para a semana seguinte no calendário. 

## <a name="edit-a-schedule"></a>Editar uma programação
Quando selecionar um horário destacado no calendário, consulte botões para **editar** ou **apagar** o horário. 

![Editar página de horários](../media/how-to-create-schedules/schedule-edit-button.png)

Na página **de eventos agendada** para editar, pode atualizar a programação e selecionar **Guardar**. 

## <a name="delete-a-schedule"></a>Eliminar uma agenda

1. Para eliminar um horário, selecione um horário realçado no calendário e selecione o botão do ícone do lixo (excluir):

    ![Eliminar o botão na barra de ferramentas](../media/how-to-create-schedules/schedule-delete-button.png)
2. Na caixa de diálogo **de evento programada Delete,** selecione **Sim** para confirmar a eliminação. 



## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como administrador, crie e gere as contas de laboratório](how-to-manage-lab-accounts.md)
- [Como dono de laboratório, crie e gere a gestão de laboratórios](how-to-manage-classroom-labs.md)
- [Como dono de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como utilizador de laboratório, aceda a laboratórios de sala de aula](how-to-use-classroom-lab.md)
