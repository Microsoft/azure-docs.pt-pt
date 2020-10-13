---
title: Azure Stack Edge Pro GPU gere horários de largura de banda Microsoft Docs
description: Descreve como usar o portal Azure para gerir os horários de largura de banda no gpu do Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: faf78e9f7efb83c5f28538e18ed081378d120848
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90890662"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-stack-edge-pro-gpu"></a>Utilize o portal Azure para gerir os horários de largura de banda no seu GPU Azure Stack Edge Pro 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Este artigo descreve como gerir os horários de largura de banda no seu Azure Stack Edge Pro. As agendas de largura de banda permitem configurar a utilização de largura de banda de rede em várias agendas a horas diferentes. Estas agendas podem ser aplicadas a operações de carregamento e transferência do seu dispositivo para a cloud.

Pode adicionar, modificar ou eliminar os horários de largura de banda do seu Azure Stack Edge Pro através do portal Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Adicionar uma agenda
> * Modificar agenda
> * Eliminar uma agenda


## <a name="add-a-schedule"></a>Adicionar uma agenda

Faça os seguintes passos no portal Azure para adicionar um horário.

1. No portal Azure para o seu recurso Azure Stack Edge, vá para **a Largura de Banda**.
2. No painel direito, selecione **+ Programação de adicionar**.

    ![Selecione largura de banda](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-1.png)

3. Em **Adicionar agenda**: 

   1. Forneça o **dia de início,** **o fim do dia,** **a hora**de início e a hora de **fim** da programação.
   2. Verifique a opção **Todos os dias** se este horário deve funcionar durante todo o dia.
   3. **A taxa de largura de** banda é a largura de banda em Megabits por segundo (Mbps) usada pelo seu dispositivo em operações que envolvam a nuvem (tanto uploads como downloads). Forneça um número entre 20 e 2.147.483.647 para este campo.
   4. Marque a largura de banda como **Ilimitada** se não quiser limitar o carregamento e a transferência de dados.
   5. Selecione **Adicionar**.

      ![Adicionar horário](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-2.png)

3. É criada uma agenda com os parâmetros especificados. Esta agenda é então apresentada na lista de agendas de largura de banda no portal.

    ![Lista atualizada de horários de largura de banda](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Editar agenda

Efetue os seguintes passos para editar uma agenda de largura de banda.

1. No portal Azure, vá ao seu recurso Azure Stack Edge e, em seguida, vá para **a Largura de Banda**. 
2. Na lista de horários de largura de banda, selecione um horário que pretende modificar.
    ![Selecione horário de largura de banda](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-1.png)

3. Efetue as alterações pretendidas e guarde as alterações.

    ![Modificar utilizador](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-2.png)

4. Depois de modificar a agenda, a lista de agendas é atualizada para refletir a agenda modificada.

    ![Modificar utilizador](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Eliminar uma agenda

Faça os seguintes passos para eliminar um calendário de largura de banda associado ao seu dispositivo Azure Stack Edge Pro.

1. No portal Azure, vá ao seu recurso Azure Stack Edge e, em seguida, vá para **a Largura de Banda**.  

2. Na lista de agendas de largura de banda, selecione a agenda que pretende eliminar. Na **programação editar,** selecione **Delete**. Quando solicitado para confirmação, selecione **Sim**.

   ![Eliminar um utilizador](media/azure-stack-edge-j-series-manage-bandwidth-schedules/delete-schedule-2.png)

3. Depois de eliminar a agenda, a lista de agendas é atualizada.


## <a name="next-steps"></a>Passos seguintes

- Saiba como [gerir as ações.](azure-stack-edge-j-series-manage-shares.md)
