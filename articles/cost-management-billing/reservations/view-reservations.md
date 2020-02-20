---
title: Ver reservas de recursos do Azure | Microsoft Docs
description: Saiba como pode ver reservas do Azure no portal do Azure.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 5c9d9074e4b8d0d9e36417daee4d58c1d9b28b64
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199250"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Ver reservas do Azure no portal do Azure

Dependendo do seu tipo de subscrição e permissões, existem algumas formas de ver reservas do Azure.

## <a name="view-purchased-reservations"></a>Ver reservas compradas

Por predefinição, ao comprar uma reserva, o utilizador e o administrador de conta podem ver a reserva. A função Proprietário é automaticamente atribuída a si e ao administrador de conta na encomenda da reserva e na própria reserva. Para permitir que outras pessoas vejam a reserva, tem de adicioná-las como um **Proprietário** ou **Leitor** na encomenda da reserva ou na própria reserva.

Para obter mais informações, veja [Adicionar ou alterar os utilizadores que podem gerir uma reserva](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

Para ver uma reserva como Proprietário ou Leitor:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Escreva **Reservas** na caixa de pesquisa.
    ![Captura de ecrã a mostrar a pesquisa no portal do Azure](./media/view-reservations/portal-reservation-search.png)  
3. A lista mostra todas as reservas onde possui a função Proprietário ou Leitor. Cada reserva mostra a última percentagem de utilização conhecida.  
    ![Exemplo a mostrar uma lista de reservas](./media/view-reservations/view-reservations.png)
4. Selecione uma reserva e veja a tendência de utilização dos últimos cinco dias.  
    ![Exemplo a mostrar a tendência de utilização da reserva](./media/view-reservations/reservation-utilization.png)
5. Também pode obter a percentagem de [utilização da reserva](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) através da API de utilização da Instância Reservada e do [pacote de conteúdos Microsoft Azure Consumption Insights do Power BI](/power-bi/service-connect-to-azure-consumption-insights).

Se tiver de alterar o âmbito de uma reserva, dividir uma reserva ou alterar quem pode gerir a mesma, veja [Gerir o Azure Reservations](manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Ver transações de reserva para inscrições Enterprise

 Se tiver uma inscrição Enterprise orientada por parceiros, veja as reservas ao aceder a **Relatórios** no EA Portal. Para outras inscrições Enterprise, pode ver as reservas no EA Portal e no portal do Azure. Tem de ser um administrador EA para ver as transações de reserva.

Para ver as transações de reserva no portal do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure **Cost Management + Faturação**.

    ![Captura de ecrã a mostrar a pesquisa no portal do Azure](./media/view-reservations/portal-cm-billing-search.png)

1. Selecione **Transações de reserva**.
1. Para filtrar os resultados, selecione **Período de tempo**, **Tipo** ou **Descrição**.
1. Selecione **Aplicar**.

    ![Captura de ecrã a mostrar os resultados de transações de reserva](./media/view-reservations/portal-billing-reservation-transaction-results.png)

Para obter os dados através de uma API, veja [Get Reserved Instance transaction charges for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) (Obter custos de transações de Instâncias Reservadas para clientes do Enterprise).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as reservas do Azure, veja os seguintes artigos:

- [O que são as reservas do Azure?](save-compute-costs-reservations.md)
- [Gerir reservas do Azure](manage-reserved-vm-instance.md)

Para comprar um plano de serviço:

- [Efetuar pré-pagamento da capacidade reservada do Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../../sql-database/sql-database-reserved-capacity.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

Para comprar um plano de software:

- [Efetuar pré-pagamento dos planos de software Red Hat nas Reservas do Azure](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Efetuar o pré-pagamento de planos de software SUSE das Reservas do Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)

Para compreender a utilização:

- [Compreender a utilização de reservas na sua subscrição Pay As You Go](understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)
- [Compreender a utilização de reservas nas subscrições do CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
