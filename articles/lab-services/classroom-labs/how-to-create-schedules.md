---
title: Criar o agendamento para laboratórios de sala de aula no Azure Lab Services | Documentos da Microsoft
description: Saiba como criar agendas para laboratórios de sala de aula no Azure Lab Services para que as VMs nos laboratórios arrancar e encerre durante um período de tempo especificado.
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
ms.openlocfilehash: 34bc8263053cd4a701c16ee1832cf1b27340a345
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60696043"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Criar e gerenciar cronogramas para laboratórios de sala de aula no Azure Lab Services 
Agendas permitem-lhe configurar um laboratório de sala de aula, de modo a que as VMs no laboratório iniciarem automaticamente e encerre durante um período de tempo especificado. Pode definir um agendamento periódico ou numa agenda periódica. Os procedimentos seguintes dão-lhe os passos para criar e gerir agendas para um laboratório de sala de aula: 

> [!IMPORTANT]
> O tempo de execução agendado das VMs não contam para o [quota atribuída a um utilizador](how-to-configure-student-usage.md#set-quotas-per-user). A quota é para o tempo fora do horário de agenda que passa a um aluno em VMs. 

## <a name="add-a-schedule-once"></a>Adicionar um agendamento (uma vez)

1. Mude para o **agendas** página e selecione **adicionar agenda** na barra de ferramentas. 

    ![Adicionar botão de agenda na página de agendas](../media/how-to-create-schedules/add-schedule-button.png)
2. Sobre o **adicionar agenda** página, confirme se **uma vez** opção está selecionada na parte superior. Se não estiver, selecione **uma vez**. 
3. Para **agendar data (obrigatória)** , introduza a data ou selecione o ícone de calendário para selecionar uma data. 
4. Para **hora de início**, selecione a hora em que pretende que as VMs para ser iniciado. A hora de início é necessária se a hora de paragem não está definida. Selecione **remover Iniciar evento** se pretender especificar a hora de paragem. Se o **hora de início** é desativada, selecione **evento de início de adicionar** junto à lista pendente para ativá-la. 
5. Para **hora de paragem**, selecione a hora em que pretende que as VMs sejam encerradas. A hora de paragem é necessária se a hora de início não está definida. Selecione **evento stop do Remove** se pretender especificar a hora de início. Se o **hora de paragem** é desativada, selecione **evento stop do Add** junto à lista pendente para ativá-la.
6. Para **fuso de horário (obrigatório)** , selecione o fuso horário para o início e parar de vezes especificado. 
7. Para **notas**, introduza qualquer descrição ou notas para a agenda. 
8. Selecione **Guardar**. 

    ![Onetime agenda](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>Adicionar uma agenda periódica (semanal)

1. Mude para o **agendas** página e selecione **adicionar agenda** na barra de ferramentas. 

    ![Adicionar botão de agenda na página de agendas](../media/how-to-create-schedules/add-schedule-button.png)
2. Sobre o **adicionar agenda** página, mude para **semanal** na parte superior. 
3. Para **agendar dias (obrigatório)** , selecione os dias em que pretende que a agenda para entrar em vigor. No exemplo a seguir, de segunda a sexta-feira está selecionada. 
4. Para o **partir** , insira o **agendar a data de início** ou escolher uma data, selecionando o **calendário** botão. Este campo é obrigatório. 
5. Para **data de fim da agenda**, introduza ou selecione uma data de término no qual as VMs devem ser encerradas. 
6. Para **hora de início**, selecione a hora em que pretende que as VMs para ser iniciado. A hora de início é necessária se a hora de paragem não está definida. Selecione **remover Iniciar evento** se pretender especificar a hora de paragem. Se o **hora de início** é desativada, selecione **evento de início de adicionar** junto à lista pendente para ativá-la. 
7. Para **hora de paragem**, selecione a hora em que pretende que as VMs sejam encerradas. A hora de paragem é necessária se a hora de início não está definida. Selecione **evento stop do Remove** se pretender especificar a hora de início. Se o **hora de paragem** é desativada, selecione **evento stop do Add** junto à lista pendente para ativá-la.
8. Para **fuso de horário (obrigatório)** , selecione o fuso horário para o início e parar de vezes especificado.  
9. Para **notas**, introduza qualquer descrição ou notas para a agenda. 
10. Selecione **Guardar**. 

    ![Agendamento semanal](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Ver agendas num calendário
Pode ver as datas agendadas e as horas realçadas na vista de calendário, conforme mostrado na imagem seguinte:

![Agendas na vista de calendário](../media/how-to-create-schedules/schedules-in-calendar.png)

Selecione o **hoje mesmo** botão no canto superior direito para mudar para a data atual no calendário. Selecione **seta para a esquerda** para mudar para a semana anterior e **seta para a direita** para mudar para a próxima semana no calendário. 

## <a name="edit-a-schedule"></a>Editar uma agenda
Quando clicar duas vezes com base numa agenda realçada no calendário ou selecione o **lápis** botão na barra de ferramentas, verá o **editar agendamento** página. A atualizar as definições nesta página é a mesma que a atualizar as definições no **adicionar agenda** página, conforme descrito no [adicionar uma agenda periódica](#add-a-recurring-schedule-weekly) secção. 

![Editar página de agenda](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>Eliminar uma agenda

1. Para eliminar uma agenda, selecione o caixote do lixo pode (eliminar) na barra de ferramentas, conforme mostrado na imagem seguinte:

    ![Eliminar o botão na barra de ferramentas](../media/how-to-create-schedules/delete-schedule-button.png)

    Pode utilizar o botão Eliminar para qualquer um dos agendada datas e horas no calendário e selecione **eliminar**. 
2. Sobre o **eliminar agendas** página, selecione **Sim**.

    ![Confirmação de agendas de eliminação](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- [Como administrador, criar e gerir contas de laboratório](how-to-manage-lab-accounts.md)
- [Como proprietário de um laboratório, criar e gerir laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário de um laboratório, configurar e controlar a utilização de um laboratório](how-to-configure-student-usage.md)
- [Como um utilizador de laboratório, acessar os laboratórios de sala de aula](how-to-use-classroom-lab.md)
