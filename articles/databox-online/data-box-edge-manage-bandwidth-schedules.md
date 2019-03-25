---
title: Edge de caixa de dados do Azure gerir agendas de largura de banda | Documentos da Microsoft
description: Descreve como utilizar o portal do Azure para gerir os agendamentos de largura de banda no seu limite de caixa de dados do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: f88003e38a34eb3396b83158ff71e4739080cd9d
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401397"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-edge"></a>Utilizar o portal do Azure para gerir os agendamentos de largura de banda no seu limite de caixa de dados do Azure  

Este artigo descreve como gerir os utilizadores no seu limite de caixa de dados do Azure. As agendas de largura de banda permitem configurar a utilização de largura de banda de rede em várias agendas a horas diferentes. Estas agendas podem ser aplicadas a operações de carregamento e transferência do seu dispositivo para a cloud.

Pode adicionar, modificar ou eliminar os agendamentos de largura de banda para dados de caixa de extremidade através do portal do Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Adicionar uma agenda
> * Modificar agenda
> * Eliminar uma agenda


## <a name="add-a-schedule"></a>Adicionar uma agenda

Execute os seguintes passos no portal do Azure para adicionar uma agenda.

1. No portal do Azure para o seu recurso de borda de caixa de dados, aceda a **largura de banda**.
2. No painel direito, selecione **+ adicionar agenda**.

    ![Selecione a largura de banda](media/data-box-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. Em **Adicionar agenda**: 

   1. Forneça o **Dia de início**, o **Dia de fim**, a **Hora de início** e a **Hora de fim** da agenda.
   2. Verifique os **todo o dia** opção se esta agenda deve executar todo o dia.
   3. **Taxa da largura de banda** é a largura de banda em Megabits por segundo (Mbps) utilizada pelo dispositivo em operações que envolvem a cloud (carregamentos e transferências). Forneça um número entre 20 e 1,000,000,007 para este campo.
   4. Marque a largura de banda como **Ilimitada** se não quiser limitar o carregamento e a transferência de dados.
   5. Selecione **Adicionar**.

      ![Adicionar agendamento](media/data-box-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. É criada uma agenda com os parâmetros especificados. Esta agenda é então apresentada na lista de agendas de largura de banda no portal.

    ![Lista atualizada de agendas de largura de banda](media/data-box-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Editar agenda

Efetue os seguintes passos para editar uma agenda de largura de banda.

1. No portal do Azure, aceda ao seu recurso de borda de caixa de dados e, em seguida, aceda a **largura de banda**. 
2. Na lista de agendamentos de largura de banda, selecione e selecione uma agenda que pretende modificar.
    ![Selecione o agendamento de largura de banda](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. Efetue as alterações pretendidas e guarde as alterações.

    ![Modificar utilizador](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. Depois de modificar a agenda, a lista de agendas é atualizada para refletir a agenda modificada.

    ![Modificar utilizador](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Eliminar uma agenda

Siga os passos abaixo para eliminar um agendamento de largura de banda associado com o seu dispositivo Edge de caixa de dados.

1. No portal do Azure, aceda ao seu recurso de borda de caixa de dados e, em seguida, aceda a **largura de banda**.  

2. Na lista de agendas de largura de banda, selecione a agenda que pretende eliminar. Na **editar agendamento**, selecione **eliminar**. Quando lhe for pedida confirmação, selecione **Sim**.

   ![Eliminar um utilizador](media/data-box-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. Depois de eliminar a agenda, a lista de agendas é atualizada.


## <a name="next-steps"></a>Passos Seguintes

- Saiba como [gerir partilhas](data-box-edge-manage-shares.md).
