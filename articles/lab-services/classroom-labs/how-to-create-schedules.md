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
ms.date: 01/30/2019
ms.author: spelluru
ms.openlocfilehash: f607ba68563aa92797f45cf77db0575ae6802fee
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385598"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Criar e gerenciar agendas para laboratórios de sala de aula no Azure Lab Services 
Os agendamentos permitem que você configure um laboratório de sala de aula de modo que as VMs no laboratório sejam iniciadas e desligadas automaticamente em um horário especificado. Você pode definir uma agenda única ou uma agenda recorrente. Os procedimentos a seguir fornecem etapas para criar e gerenciar agendas para um laboratório de sala de aula: 

> [!IMPORTANT]
> O tempo de execução agendado das VMs não conta a [cota alocada para um usuário](how-to-configure-student-usage.md#set-quotas-for-users). A cota é para o tempo fora das horas de agendamento que um aluno gasta nas VMs. 

## <a name="add-a-schedule-once"></a>Adicionar uma agenda (uma vez)

1. Alterne para a  página agendas e selecione **Adicionar agenda** na barra de ferramentas. 

    ![Botão Adicionar agendamento na página agendas](../media/how-to-create-schedules/add-schedule-button.png)
2. Na página **Adicionar agenda** , confirme se a opção **uma vez** está selecionada na parte superior. Se não estiver, selecione **uma vez**. 
3. Em **data do agendamento (obrigatório)** , insira a data ou selecione o ícone de calendário para selecionar uma data. 
4. Para **hora de início**, selecione a hora em que deseja que as VMs sejam iniciadas. A hora de início será necessária se a hora de parada não estiver definida. Selecione **remover evento de início** se desejar especificar apenas a hora de parada. se a **hora de início** estiver desabilitada, selecione **Adicionar evento de início** ao lado da lista suspensa para habilitá-la. 
5. Em **hora de parada**, selecione a hora em que deseja que as VMs sejam desligadas. A hora de parada será necessária se a hora de início não estiver definida. Selecione **remover evento de parada** se desejar especificar apenas a hora de início. se a **hora de parada** estiver desabilitada, selecione **Adicionar evento de parada** ao lado da lista suspensa para habilitá-la.
6. Para **fuso horário (obrigatório)** , selecione o fuso horário para os horários de início e de término especificados. 
7. Para **observações**, insira qualquer descrição ou nota para o agendamento. 
8. Selecione **Guardar**. 

    ![Agenda de OneTime](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>Adicionar um agendamento recorrente (semanal)

1. Alterne para a  página agendas e selecione **Adicionar agenda** na barra de ferramentas. 

    ![Botão Adicionar agendamento na página agendas](../media/how-to-create-schedules/add-schedule-button.png)
2. Na página **Adicionar agenda** , alterne para **semanalmente** na parte superior. 
3. Para **agendar dias (obrigatório)** , selecione os dias nos quais você deseja que o agendamento entre em vigor. No exemplo a seguir, segunda-feira é selecionada. 
4. Para o campo **de** , insira a **data de início da agenda** ou escolha uma data selecionando o botão **calendário** . Este campo é obrigatório. 
5. Em **data de término do agendamento**, insira ou selecione uma data de término na qual as VMs devem ser desligadas. 
6. Para **hora de início**, selecione a hora em que você deseja que as VMs sejam iniciadas. A hora de início será necessária se a hora de parada não estiver definida. Selecione **remover evento de início** se desejar especificar apenas a hora de parada. se a **hora de início** estiver desabilitada, selecione **Adicionar evento de início** ao lado da lista suspensa para habilitá-la. 
7. Em **hora de parada**, selecione a hora em que você deseja que as VMs sejam desligadas. A hora de parada será necessária se a hora de início não estiver definida. Selecione **remover evento de parada** se desejar especificar apenas a hora de início. se a **hora de parada** estiver desabilitada, selecione **Adicionar evento de parada** ao lado da lista suspensa para habilitá-la.
8. Para **fuso horário (obrigatório)** , selecione o fuso horário para os horários de início e de término especificados.  
9. Para **observações**, insira qualquer descrição ou nota para o agendamento. 
10. Selecione **Guardar**. 

    ![Agenda semanal](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Exibir agendas no calendário
Você pode ver as datas e os horários agendados realçados no modo de exibição de calendário, conforme mostrado na imagem a seguir:

![Agendas no modo de exibição de calendário](../media/how-to-create-schedules/schedules-in-calendar.png)

Selecione o botão **hoje** no canto superior direito para alternar para a data atual no calendário. Selecione a seta para a **esquerda** para alternar para a semana anterior e a seta para a **direita** para alternar para a próxima semana no calendário. 

## <a name="edit-a-schedule"></a>Editar uma agenda
Ao clicar duas vezes em uma agenda realçada no calendário ou selecionar o botão de **lápis** na barra de ferramentas, você verá a página **Editar agenda** . A atualização das configurações nesta página é igual às configurações de atualização na página **Adicionar agenda** , conforme descrito na seção [Adicionar um agendamento recorrente](#add-a-recurring-schedule-weekly) . 

![Editar página de agenda](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>Eliminar uma agenda

1. Para excluir uma agenda, selecione o botão Lixeira (excluir) na barra de ferramentas, conforme mostrado na imagem a seguir:

    ![Botão excluir na barra de ferramentas](../media/how-to-create-schedules/delete-schedule-button.png)

    Você pode usar o botão excluir para qualquer uma das datas e horários agendados no calendário e selecionar **excluir**. 
2. Na página **excluir agendas** , selecione **Sim**.

    ![Excluir confirmação de agendas](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como proprietário de um laboratório, criar e gerenciar laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário de um laboratório, configurar e controlar o uso de um laboratório](how-to-configure-student-usage.md)
- [Como usuário do laboratório, acesse laboratórios da sala de aula](how-to-use-classroom-lab.md)
