---
title: Gerir horários de largura de banda no Azure Data Box Gateway gateway | Microsoft Docs
description: Descreve como utilizar o portal do Azure para gerir as agendas de largura de banda do Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 55676128f482c8fb4dbdeab0523e4556f3957af7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582561"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-gateway"></a>Utilizar o portal do Azure para gerir as agendas de largura de banda do Azure Data Box Gateway  

Este artigo descreve como utilizar o portal do Azure para gerir os utilizadores no Azure Data Box Gateway. As agendas de largura de banda permitem configurar a utilização de largura de banda de rede em várias agendas a horas diferentes. Estas agendas podem ser aplicadas a operações de carregamento e transferência do seu dispositivo para a cloud.

Pode adicionar, modificar ou eliminar os agendamentos de largura de banda do seu Azure Data Box Gateway através do portal do Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Adicionar uma agenda
> * Modificar agenda
> * Eliminar uma agenda

## <a name="add-a-schedule"></a>Adicionar uma agenda

Efetue os seguintes passos no portal do Azure para adicionar um utilizador.

1. No portal do Azure do recurso Data Box Gateway, aceda a **Largura de Banda**.
2. No painel direito, clique em **+ Adicionar agenda**.

    ![Clicar em adicionar utilizador](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-1.png)

3. Em **Adicionar agenda**: 

   1. Forneça o **dia de início,** **o fim do dia,** **a hora** de início e a hora de **fim** da programação. 
   2. Se esta opção for executada durante o dia todo, pode marcar a opção **Todo o dia**. 
   3. **A taxa de largura de** banda é a largura de banda em Megabits por segundo (Mbps) usada pelo seu dispositivo em operações que envolvam a nuvem (tanto uploads como downloads). Forneça um número entre 1 e 1000 para este campo. 
   4. Marque a largura de banda como **Ilimitada** se não quiser limitar o carregamento e a transferência de dados. 
   5. Clique em **Adicionar**.

      ![Clique em adicionar utilizador 2](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-2.png)

3. É criada uma agenda com os parâmetros especificados. Esta agenda é então apresentada na lista de agendas de largura de banda no portal.


## <a name="edit-schedule"></a>Editar agenda

Efetue os seguintes passos para editar uma agenda de largura de banda. 

1. No portal do Azure, aceda ao recurso Data Box Gateway e, em seguida, aceda a Largura de Banda. 
2. Na lista de agendas de largura de banda, selecione e clique na agenda que pretende modificar.

    ![Modificar utilizador](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-1.png)

3. Efetue as alterações pretendidas e guarde as alterações.

    ![Modificar o utilizador 2](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-2.png)

4. Depois de modificar a agenda, a lista de agendas é atualizada para refletir a agenda modificada.

    ![Modificar o utilizador 3](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Eliminar uma agenda

Efetue os seguintes passos para eliminar uma agenda de largura de banda associada ao dispositivo Data Box Gateway.

1. No portal do Azure, aceda ao recurso Data Box Gateway e, em seguida, aceda a **Largura de Banda**.  

2. Na lista de agendas de largura de banda, selecione a agenda que pretende eliminar. Clique com o botão direito do rato para apresentar o menu de contexto e clique em **Eliminar**. 

   ![Eliminar um utilizador](media/data-box-gateway-manage-bandwidth-schedules/delete-schedule-1.png)

3.  Depois de eliminar a agenda, a lista de agendas é atualizada.



## <a name="next-steps"></a>Passos seguintes

- Saiba como [Gerir a largura de banda](data-box-gateway-manage-bandwidth-schedules.md). 
