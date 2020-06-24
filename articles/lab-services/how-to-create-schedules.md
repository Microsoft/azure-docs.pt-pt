---
title: Criar horários para laboratórios de sala de aula nos Serviços Azure Lab Microsoft Docs
description: Aprenda a criar horários para laboratórios de sala de aula em Azure Lab Services para que os VMs nos laboratórios comecem e fechem numa hora especificada.
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
ms.openlocfilehash: 68d608471b347895a170cc6cf83dfb4180c0a488
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84895928"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Criar e gerir horários para laboratórios de sala de aula em Azure Lab Services 
Os horários permitem-lhe configurar um laboratório de sala de aula de tal forma que os VMs no laboratório comecem e desliguem automaticamente numa hora especificada. Pode definir um horário único ou um horário recorrente. Os seguintes procedimentos dão-lhe passos para criar e gerir horários para um laboratório de sala de aula: 

> [!IMPORTANT]
> O tempo de funcionamento programado dos VM não conta com a [quota atribuída a um utilizador.](how-to-configure-student-usage.md#set-quotas-for-users) A quota é para o tempo fora do horário horário que um aluno gasta em VMs. 

## <a name="set-a-schedule-for-the-lab"></a>Estabeleça um horário para o laboratório
Crie um evento programado para o laboratório para que os VMs no laboratório sejam automaticamente iniciados/parados em momentos específicos. A quota de utilizador especificada anteriormente é o tempo adicional atribuído a cada utilizador fora desta hora programada. 

> [!NOTE]
> Antes de começarmos, eis como os horários afetam as máquinas virtuais de laboratório: 
>- A máquina virtual do modelo não está incluída nos horários. 
>- Apenas as máquinas virtuais atribuídas são iniciadas. Isto significa que, se uma máquina não for reclamada por um utilizador final (estudante), a máquina não arrancará nas horas programadas. 
>- Todas as máquinas virtuais (reclamadas por um utilizador ou não) são paradas com base no horário do laboratório. 

1. Mude para a página **Agendas** e **selecione Adicionar evento agendado** na barra de ferramentas. 

    ![Adicione o botão de agenda na página Agendas](./media/how-to-create-schedules/add-schedule-button.png)
2. Confirme que **a Standard** está selecionada do **tipo Evento**. Selecione **Iniciar apenas** para especificar apenas a hora de início para os VMs. Selecione **Parar apenas** para especificar apenas o tempo de paragem para os VMs. 
7. Na secção **Repetir,** selecione o horário atual. 

    ![Adicione o botão de agenda na página Agendas](./media/how-to-create-schedules/select-current-schedule.png)
5. Na caixa de diálogo **Repeat,** faça os seguintes passos:
    1. Confirme que **todas as semanas** estão definidas para o campo **Repetição.** 
    3. Especificar a **data de início.**
    4. Especifique a **hora de início** em que deseja que os VMs sejam iniciados.
    5. Especificar o **tempo de paragem** em que os VM devem ser desligados. 
    6. Especifique o **fuso horário** para os tempos de início e de paragem especificados. 
    2. Selecione os dias em que deseja que o horário entre em vigor. No exemplo seguinte, é selecionado de segunda a quinta-feira. 
    8. Selecione **Guardar**. 

        ![Definir horário de repetição](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Agora, na página de **eventos agendada add,** para **Notas (opcional)**, insira qualquer descrição ou notas para o horário. 
4. Na página de **eventos agendada para adicionar,** selecione **Save**. 

    ![Horário semanal](./media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Ver horários no calendário
Pode ver as datas e horários programados destacados na vista do calendário como mostrado na seguinte imagem:

![Horários na vista do calendário](./media/how-to-create-schedules/schedules-calendar.png)

Selecione o botão **Today** no canto superior direito para mudar para a data atual no calendário. Selecione **a seta esquerda** para mudar para a semana anterior e **seta direita** para mudar para a semana seguinte no calendário. 

## <a name="edit-a-schedule"></a>Editar uma programação
Quando seleciona uma programação realçada no calendário, vê botões para **editar** ou **eliminar** o horário. 

![Editar página de agenda](./media/how-to-create-schedules/schedule-edit-button.png)

Na página de **eventos agendada para Editar,** pode atualizar o horário e selecionar **Guardar**. 

## <a name="delete-a-schedule"></a>Eliminar uma agenda

1. Para eliminar um horário, selecione uma programação realçada no calendário e selecione o botão ícone do lixo (apagar):

    ![Eliminar botão na barra de ferramentas](./media/how-to-create-schedules/schedule-delete-button.png)
2. Na caixa de diálogo **de evento agendada para eliminar,** selecione **Sim** para confirmar a eliminação. 



## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como administrador, crie e gere contas de laboratório](how-to-manage-lab-accounts.md)
- [Como dono de laboratório, crie e gere laboratórios](how-to-manage-classroom-labs.md)
- [Como dono de laboratório, configurar e controlar o uso de um laboratório](how-to-configure-student-usage.md)
- [Como utilizador de laboratório, aceda aos laboratórios de sala de aula](how-to-use-classroom-lab.md)
