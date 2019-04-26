---
title: Ver as reservas para recursos do Azure | Documentos da Microsoft
description: Saiba como ver as reservas do Azure no portal do Azure.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 872837c774368820527b12778b1a7dd4ddc5c7af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60369229"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Ver as reservas do Azure no portal do Azure

Dependendo do tipo de subscrição e permissões, existem duas formas para ver as reservas para o Azure.

## <a name="view-purchased-reservations"></a>Ver as reservas adquiridas

Por predefinição, quando adquire uma reserva, e o administrador de conta podem ver a reserva. O administrador de conta e obtém automaticamente a função de proprietário na encomenda de reserva e reserva. Para permitir que outras pessoas ver a reserva, tem de adicioná-los como um **proprietário** ou **leitor** na encomenda de reserva ou reserva.

Para obter mais informações, consulte [adicionar ou alterar os utilizadores que podem gerir uma reserva](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

Para ver uma reserva como proprietário ou leitor,

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Pesquisar nos **reservas**.
    ![Captura de ecrã que mostra a pesquisa de portal do Azure](./media/billing-view-reservation/portal-reservation-search.png)  
3. A lista mostra todas as reservas em que tiver a função de proprietário ou leitor. Cada reserva mostra a percentagem de utilização de conhecidos último.  
    ![Exemplo que mostra uma lista de reservas](./media/billing-view-reservation/view-reservations.png)
4. Selecionar uma reserva e ver a tendência de utilização dos últimos cinco dias.  
    ![Tendência de utilização de reserva do exemplo que mostra](./media/billing-view-reservation/reservation-utilization.png)
5. Também pode obter o [utilização de reserva](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) usando a API de utilização de instância reservada e com o [pacote de conteúdos do Microsoft Azure consumo Insights Power BI](/power-bi/service-connect-to-azure-consumption-insights).

Se precisar de alterar o âmbito de uma reserva, dividir uma reserva ou alteração que pode gerir uma reserva, consulte [Gerir reservas de Azure](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Ver transações de reserva para inscrições de empresas

 Se tiver uma inscrição de Enterprise parceiro por instrutor, ver as reservas ao aceder **relatórios** no portal do EA. Para outras inscrições de empresas, pode ver as reservas no portal do EA e no portal do Azure. Tem de ser um administrador EA para exibir transações de reserva.

Para exibir transações de reserva no portal do Azure,

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Faça uma pesquisa em **Gestão de Custos + Faturação**.

    ![Captura de ecrã que mostra a pesquisa de portal do Azure](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Selecione **transações de reserva**.
1. Para filtrar os resultados, selecione **Timespan**, **tipo**, ou **Descrição**.
1. Selecione **Aplicar**.

    ![Captura de ecrã que mostra os resultados de transações de reserva](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

Para obter os dados através de uma API, consulte [tarifas de transação de obter a instância reservada para os clientes empresariais](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as reservas do Azure, veja os artigos seguintes:

- [Quais são as reservas para o Azure?](billing-save-compute-costs-reservations.md)
- [Gerir reservas para o Azure](billing-manage-reserved-vm-instance.md)

Compre um plano do serviço:

- [Efetue o pré-pagamento do Cosmos DB reservado capacidade](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../sql-database/sql-database-reserved-capacity.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)

Compre um plano de software:

- [Efetue o pré-pagamento do planos de software do Red Hat do Azure reservas](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Efetuar o pré-pagamento de planos de software SUSE das Reservas do Azure](../virtual-machines/linux/prepay-suse-software-charges.md)

Compreenda a utilização:

- [Compreender a utilização de reserva para a sua subscrição pay as you go](billing-understand-reserved-instance-usage.md)
- [Compreender a utilização de reserva para inscrição da sua empresa](billing-understand-reserved-instance-usage-ea.md)
- [Compreender a utilização de reserva para subscrições de CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
