---
title: Ver transações de compra e reembolso de Reservas do Azure
description: Saiba como ver transações de compra e reembolso de Reservas do Azure.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: b986aa2bfce203be85adbcde8e2966c167bf7ca1
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151775"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>Ver transações de compra e reembolso de reservas

Existem algumas formas diferentes de ver transações de compra e reembolso de reservas. Pode utilizar o portal do Azure, o Power BI e APIs REST.

## <a name="view-reservation-transactions-in-the-azure-portal"></a>Ver transações de reserva no portal do Azure

O administrador de faturação de uma inscrição Enterprise ou de um Contrato de Cliente Microsoft pode ver transações de reserva em Cost Management e Faturação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure **Cost Management + Faturação**.
1. Selecione **Transações de reserva**.
1. Para filtrar os resultados, selecione **Período de tempo**, **Tipo** ou **Descrição**.
1. Selecione **Aplicar**.

[![Captura de ecrã que mostra transações de reserva no portal do Azure](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>Ver transações de reserva no Power BI

O administrador de faturação de uma inscrição Enterprise ou de um Contrato de Cliente Microsoft pode ver transações de reserva com a aplicação Power BI do Cost Management.

1. Obtenha a [Aplicação Power BI do Cost Management](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
1. Navegue até ao relatório Compras de RI.

[![Exemplo que mostra transações de reserva](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

Para obter mais informações, veja [Aplicação Power BI do Azure Cost Management para Contratos Enterprise](../costs/analyze-cost-data-azure-cost-management-power-bi-template-app.md).

## <a name="use-apis-to-get-reservation-transactions"></a>Utilizar APIs para obter transações de reserva

Os utilizadores do Contrato Enterprise (EA) e do Contrato de Cliente Microsoft podem obter dados de transações de reserva através da [API Transações de Reserva - Lista](/rest/api/consumption/reservationtransactions/list).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

- Para saber como gerir uma reserva, veja [Gerir Reservas do Azure](manage-reserved-vm-instance.md).
- Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:
  - [O que são as reservas do Azure?](save-compute-costs-reservations.md)
  - [Gerir Reservas no Azure](manage-reserved-vm-instance.md)