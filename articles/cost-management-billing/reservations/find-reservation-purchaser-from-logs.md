---
title: Encontre um comprador de reservas nos registos do Azure Monitor
description: Este artigo ajuda a encontrar um comprador de reservas com informações dos registos do Azure Monitor.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: 965e90eed0690d57b6ad3cf3a1543b1329c0fe74
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773377"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Encontre um comprador de reservas a partir de registos Azure

Este artigo ajuda a encontrar um comprador de reservas com informações dos seus registos de diretório. Os registos de diretório do Azure Monitor mostram os IDs de e-mail dos utilizadores que efetuam compras de reservas.

## <a name="find-the-purchaser"></a>Encontre o comprador

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para **monitorizar** a atividade  >  **do registo**  >  **de atividades.**  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="Screenshot mostrando navegação para log de atividade - Atividade." lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. Selecione **Atividade de Diretório**. Se vir uma mensagem indicando *que precisa de permissão para visualizar registos de nível de diretório,* selecione o [link](../../role-based-access-control/elevate-access-global-admin.md) para aprender a obter permissões.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="Screenshot mostrando Atividade do Diretório sem permissão para visualizar o registo." lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. Assim que tiver permissão, filtre **o Fornecedor de Recursos do Inquilino** com a **Microsoft.Capacity**. Você deve ver todos os eventos relacionados com a reserva para o tempo selecionado. Se necessário, altere o tempo.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="Screenshot mostrando o utilizador que comprou a reserva." lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    Se necessário, poderá ser necessário **editar colunas** para selecionar **o Evento iniciado por**.
   O utilizador que efetuou a compra da reserva é mostrado no **Evento iniciado por**.

## <a name="next-steps"></a>Passos seguintes

- Se necessário, os administradores de faturação podem [apropriar-se de uma reserva.](view-reservations.md#how-billing-administrators-can-view-or-manage-reservations)
