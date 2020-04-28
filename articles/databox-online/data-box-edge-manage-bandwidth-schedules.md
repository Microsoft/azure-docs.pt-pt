---
title: Azure Data Box Edge gere horários de largura de banda / Microsoft Docs
description: Descreve como usar o portal Azure para gerir os horários de largura de banda no seu Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: f7b762d5502986c306de240519688aa639f58445
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60756894"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-edge"></a>Utilize o portal Azure para gerir os horários da largura de banda no seu Edge De Caixa de Dados Azure  

Este artigo descreve como gerir os utilizadores no seu Edge de Caixa de Dados Azure. As agendas de largura de banda permitem configurar a utilização de largura de banda de rede em várias agendas a horas diferentes. Estas agendas podem ser aplicadas a operações de carregamento e transferência do seu dispositivo para a cloud.

Pode adicionar, modificar ou eliminar os horários de largura de banda para o seu Data Box Edge através do portal Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Adicionar uma agenda
> * Modificar agenda
> * Eliminar uma agenda


## <a name="add-a-schedule"></a>Adicionar uma agenda

Faça os seguintes passos no portal Azure para adicionar um horário.

1. No portal Azure para o seu recurso Data Box Edge, vá à **largura de banda**.
2. No painel direito, selecione **+ Adicione o horário**.

    ![Selecione largura de banda](media/data-box-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. Em **Adicionar agenda**: 

   1. Forneça o **Dia de início**, o **Dia de fim**, a **Hora de início** e a **Hora de fim** da agenda.
   2. Verifique a opção **de todo** o dia se este horário deve funcionar durante todo o dia.
   3. **Taxa da largura de banda** é a largura de banda em Megabits por segundo (Mbps) utilizada pelo dispositivo em operações que envolvem a cloud (carregamentos e transferências). Forneça um número entre 20 e 1.000.000.007 para este campo.
   4. Marque a largura de banda como **Ilimitada** se não quiser limitar o carregamento e a transferência de dados.
   5. Selecione **Adicionar**.

      ![Adicionar horário](media/data-box-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. É criada uma agenda com os parâmetros especificados. Esta agenda é então apresentada na lista de agendas de largura de banda no portal.

    ![Lista atualizada de horários de largura de banda](media/data-box-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Editar agenda

Efetue os seguintes passos para editar uma agenda de largura de banda.

1. No portal Azure, vá ao seu recurso Data Box Edge e depois vá para **a largura de banda**. 
2. A partir da lista de horários da largura de banda, selecione e selecione um horário que pretende modificar.
    ![Selecione o horário da largura de banda](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. Efetue as alterações pretendidas e guarde as alterações.

    ![Modificar utilizador](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. Depois de modificar a agenda, a lista de agendas é atualizada para refletir a agenda modificada.

    ![Modificar utilizador](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Eliminar uma agenda

Faça os seguintes passos para eliminar uma programação de largura de banda associada ao seu dispositivo Data Box Edge.

1. No portal Azure, vá ao seu recurso Data Box Edge e depois vá para **a largura de banda**.  

2. Na lista de agendas de largura de banda, selecione a agenda que pretende eliminar. Na **programação Editar,** **selecione Eliminar**. Quando solicitado para confirmação, selecione **Sim**.

   ![Eliminar um utilizador](media/data-box-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. Depois de eliminar a agenda, a lista de agendas é atualizada.


## <a name="next-steps"></a>Passos seguintes

- Saiba como [gerir ações.](data-box-edge-manage-shares.md)
