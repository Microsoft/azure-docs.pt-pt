---
title: Resolução de problemas Azure transfers de reserva entre inquilinos
description: Este artigo ajuda os proprietários de reservas a transferir uma ordem de reserva de um inquilino do Azure Ative Directory (diretório) para outro.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.openlocfilehash: 79473d57cc7504e7e6ef4ef68ba0cee74203f62b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055838"
---
# <a name="troubleshoot-reservation-transfers-between-tenants"></a>Transferências de reserva de resolução de problemas entre inquilinos

Este artigo ajuda os proprietários de reservas a transferir uma ordem de reserva de um inquilino do Azure Ative Directory (diretório) para outro. Quando você muda o diretório de uma encomenda de reserva, ele remove qualquer acesso Azure RBAC à ordem de reserva e reservas dependentes. Só tu terás acesso depois da mudança. Mudar o diretório não muda a propriedade da faturação para a ordem de reserva. O diretório é alterado para a ordem de reserva dos pais e reservas dependentes.

Uma troca de reservas e cancelamento não é necessária para transferir entre inquilinos.

Depois de transferir uma reserva para outro inquilino, também poderá querer adicionar mais proprietários à reserva. Para mais informações, consulte [Quem pode gerir uma reserva por padrão.](view-reservations.md#who-can-manage-a-reservation-by-default)

Ao transferir uma encomenda de reserva, todas as reservas ao abrigo da encomenda são transferidas com ela.

## <a name="transfer-a-reservation"></a>Transferir uma reserva

Use os seguintes passos para transferir uma encomenda de reserva e é reserva dependente para outro inquilino.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Se não é administrador de faturação, mas é proprietário de reservas, navegue para **Reservas** e, em seguida, salte para o passo 6.
1. Navegue para **Cost Management + Faturação**.
    - Se você é um administrador da EA, no menu esquerdo, selecione **os âmbitos de faturação** e, em seguida, na lista de âmbitos de faturação, selecione um.
    - Se for dono de um perfil de faturação do Microsoft Customer Agreement, no menu esquerdo, selecione **perfis de Faturação**. Na lista de perfis de faturação, selecione um.
1. No menu esquerdo, selecione **Transações de Reserva.** A lista de transações de reserva é mostrada.
1. Um banner no topo da página lê *agora Os administradores de faturação podem gerir reservas. Clique aqui para gerir reservas.* Selecione o banner.
1. É apresentada a lista completa de reservas para a sua inscrição ou perfil de faturação da EA.
1. Selecione a reserva que pretende transferir.
1. Nos detalhes da reserva, selecione o ID do pedido de reserva.
1. Na ordem de reserva, selecione **Alterar diretório**.
1. No painel de diretório Change, selecione o diretório AD Azure para o quais pretende transferir a reserva e, em seguida, **selecione Confirmar**.

## <a name="next-steps"></a>Próximos passos

- Para obter mais informações sobre as reservas, veja [O que são as Reservas do Azure?](save-compute-costs-reservations.md).