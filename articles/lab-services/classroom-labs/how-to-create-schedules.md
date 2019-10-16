---
title: Criar agenda para laboratórios de sala de aula no Azure Lab Services | Microsoft Docs
description: Saiba como criar agendas para laboratórios de sala de aula no Azure Lab Services para que as VMs nos laboratórios sejam iniciadas e desligadas em um horário especificado.
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
ms.openlocfilehash: 4e3cf302437c3e4954ac977ac3f4ff6b2021a760
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330587"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Criar e gerenciar agendas para laboratórios de sala de aula no Azure Lab Services 
Os agendamentos permitem que você configure um laboratório de sala de aula de modo que as VMs no laboratório sejam iniciadas e desligadas automaticamente em um horário especificado. Você pode definir uma agenda única ou uma agenda recorrente. Os procedimentos a seguir fornecem etapas para criar e gerenciar agendas para um laboratório de sala de aula: 

> [!IMPORTANT]
> O tempo de execução agendado das VMs não conta a [cota alocada para um usuário](how-to-configure-student-usage.md#set-quotas-for-users). A cota é para o tempo fora das horas de agendamento que um aluno gasta nas VMs. 

## <a name="set-a-schedule-for-the-lab"></a>Definir um agendamento para o laboratório
Crie um evento agendado para o laboratório para que as VMs no laboratório sejam iniciadas/interrompidas automaticamente em horários específicos. A cota de usuário especificada anteriormente é o tempo adicional atribuído a cada usuário fora deste horário agendado. 

1. Alterne para a página **agendas** e selecione **Adicionar evento agendado** na barra de ferramentas. 

    ![Botão Adicionar agendamento na página agendas](../media/how-to-create-schedules/add-schedule-button.png)
2. Confirme se **padrão** está selecionado como o **tipo de evento**. Selecione **Iniciar somente** para especificar apenas a hora de início para as VMs. Você seleciona **parar somente** para especificar apenas a hora de parada para as VMs. 
7. Na seção **repetir** , selecione o agendamento atual. 

    ![Botão Adicionar agendamento na página agendas](../media/how-to-create-schedules/select-current-schedule.png)
5. Na caixa de diálogo **repetir** , execute as seguintes etapas:
    1. Confirme se **todas as semanas** estão definidas para o campo de **repetição** . 
    3. Especifique a **data de início**.
    4. Especifique a **hora de início** em que você deseja que as VMs sejam iniciadas.
    5. Especifique a **hora de parada** em que as VMs devem ser desligadas. 
    6. Especifique o **fuso horário** para os horários de início e de término especificados. 
    2. Selecione os dias nos quais você deseja que o agendamento entre em vigor. No exemplo a seguir, segunda-feira-quinta-feira está selecionado. 
    8. Selecione **Guardar**. 

        ![Definir agendamento de repetição](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Agora, na página **Adicionar evento agendado** , para **observações (opcional)** , insira qualquer descrição ou nota para o agendamento. 
4. Na página **Adicionar evento agendado** , selecione **salvar**. 

    ![Agenda semanal](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Exibir agendas no calendário
Você pode ver as datas e os horários agendados realçados no modo de exibição de calendário, conforme mostrado na imagem a seguir:

![Agendas no modo de exibição de calendário](../media/how-to-create-schedules/schedules-calendar.png)

Selecione o botão **hoje** no canto superior direito para alternar para a data atual no calendário. Selecione a seta para a **esquerda** para alternar para a semana anterior e a seta para a **direita** para alternar para a próxima semana no calendário. 

## <a name="edit-a-schedule"></a>Editar uma agenda
Ao selecionar uma agenda realçada no calendário, você verá botões para **Editar** ou **excluir** a agenda. 

![Editar página de agenda](../media/how-to-create-schedules/schedule-edit-button.png)

Na página **Editar evento agendado** , você pode atualizar a agenda e selecionar **salvar**. 

## <a name="delete-a-schedule"></a>Eliminar uma agenda

1. Para excluir uma agenda, selecione uma agenda realçada no calendário e selecione o botão de Lixeira (excluir):

    ![Botão excluir na barra de ferramentas](../media/how-to-create-schedules/schedule-delete-button.png)
2. Na caixa de diálogo **excluir evento agendado** , selecione **Sim** para confirmar a exclusão. 



## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como proprietário de um laboratório, criar e gerenciar laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário de um laboratório, configurar e controlar o uso de um laboratório](how-to-configure-student-usage.md)
- [Como usuário do laboratório, acesse laboratórios da sala de aula](how-to-use-classroom-lab.md)
