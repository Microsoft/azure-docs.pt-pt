---
title: Comprar uma reserva do Azure
description: Saiba quais são os aspetos importantes para comprar uma reserva do Azure.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: a70d434b06fd19071f3576f9333579e0bd2b85e7
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574979"
---
# <a name="buy-a-reservation"></a>Comprar uma reserva

O Azure Reservations ajuda-o a poupar ao subscrever planos de um ou três anos de vários recursos do Azure. Antes de assumir o compromisso de comprar uma reserva, não se esqueça de rever as secções seguintes para se preparar para a sua compra.

## <a name="who-can-buy-a-reservation"></a>Quem pode comprar uma reserva

Para comprar uma reserva, tem de ter a função de proprietário ou de comprador da reserva numa subscrição do Azure que seja do tipo Enterprise (MS-AZR-0017P ou MS-AZR-0148P) ou Pay As You Go (MS-AZR-0003P ou MS-AZR-0023P) ou Contrato de Cliente Microsoft. Os fornecedores de soluções cloud podem utilizar o portal do Azure ou o  [Centro de Parceiros](/partner-center/azure-reservations)  para comprar o Azure Reservations.

Os clientes do Contrato Enterprise (EA) podem limitar as compras a administradores do EA ao desativar a opção **Adicionar Instâncias Reservadas** no EA Portal. Para comprar uma reserva, os administradores do EA têm de ter acesso de proprietário ou de comprador da reserva em, pelo menos, uma subscrição do EA. A opção é útil para as empresas que pretendem uma equipa centralizada encarregue de comprar as reservas.

Os descontos de reserva só se aplicam a recursos associados a subscrições compradas através do Enterprise, do Fornecedor de Soluções Cloud (CSP), do Contrato de Cliente da Microsoft e de planos individuais com taxas pay as you go.

## <a name="scope-reservations"></a>Definir o âmbito de reservas

Pode definir o âmbito de uma reserva para uma subscrição ou para um grupo de recursos. Definir o âmbito de uma reserva permite selecionar onde as poupanças na reserva serão aplicadas. Ao definir o âmbito da reserva para um grupo de recursos, os descontos de reserva aplicam-se apenas ao grupo de recursos e não à totalidade da subscrição.

### <a name="reservation-scoping-options"></a>Opções de definição do âmbito da reserva

Tem três opções para definir o âmbito de uma reserva, consoante as suas necessidades:

- **Âmbito de grupo de recursos único** — aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionado.
- **Âmbito de subscrição individual** — aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.
- **Âmbito partilhado** – aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que estejam no contexto de faturação.
    - Para clientes do Contrato Enterprise, o contexto de faturação é a inscrição. O âmbito partilhado da reserva incluiria vários inquilinos do Active Directory numa inscrição.
    - Para os clientes do Contrato de Cliente Microsoft, o âmbito da faturação é o perfil de faturação.
    - Para subscrições individuais com taxas pay as you go, o âmbito da faturação reside em todas as subscrições elegíveis criadas pelo administrador de conta.

Enquanto aplica descontos de reserva sobre a sua utilização, o Azure processa a reserva pela seguinte ordem:

1. Reservas cujo âmbito está definido para um grupo de recursos
2. Reservas de âmbito único
3. Reservas de âmbito partilhado

Pode sempre atualizar o âmbito após comprar uma reserva. Para tal, aceda à reserva, clique em **Configuração** e redefina o âmbito da reserva. A redefinição do âmbito de uma reserva não constitui uma transação comercial. O seu termo de reserva não será alterado. Para obter mais informações sobre a atualização do âmbito, veja [Atualizar o âmbito depois de comprar uma reserva](manage-reserved-vm-instance.md#change-the-reservation-scope).

![Exemplo a mostrar uma alteração do âmbito de reserva](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="discounted-subscription-and-offer-types"></a>Subscrição com desconto e tipos de oferta

Os descontos da reserva aplicam-se às seguintes subscrições e tipos de oferta elegíveis.

- Contrato Enterprise (números de oferta: MS-AZR-0017P ou MS-AZR-0148P)
- Subscrições do Contrato de Cliente da Microsoft.
- Planos individuais com preços pay as you go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P)
- Subscrições CSP

Os recursos executados numa subscrição com outros tipos de oferta não recebem o desconto de reserva.

## <a name="purchase-reservations"></a>Reservas de compra

Pode comprar reservas no portal, nas APIs, no PowerShell e na CLI do Azure. Leia os seguintes artigos que se aplicam a si quando estiver pronto para fazer uma compra de reserva:

- [Serviço de Aplicações](prepay-app-service.md)
- [Cache do Azure para Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [BD do Cosmos](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](/azure/data-explorer/pricing-reserved-capacity)
- [Armazenamento de Discos](../../virtual-machines/disks-reserved-capacity.md)
- [Dedicated Host](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Planos de software](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Armazenamento](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Base de Dados SQL](../../azure-sql/database/reserved-capacity-overview.md)
- [Base de Dados do Azure para PostgreSQL](../../postgresql/concept-reserved-pricing.md)
- [Base de Dados do Azure para MySQL](../../mysql/concept-reserved-pricing.md)
- [Azure Database for MariaDB](../../mariadb/concept-reserved-pricing.md)
- [Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md)
- [Máquinas virtuais](../../virtual-machines/prepay-reserved-vm-instances.md)

## <a name="buy-reservations-with-monthly-payments"></a>Comprar reservas com pagamentos mensais

Pode pagar as reservas através de pagamentos mensais. Ao contrário de uma compra antecipada em que paga o montante total, a opção de pagamento mensal divide o custo total da reserva em partes iguais para cada mês do período. O custo total de reservas pagas em adiantado ou mensalmente é o mesmo e não pagará qualquer taxa adicional ao optar por pagamentos mensais.

Se a reserva for comprada através do Contrato de Cliente Microsoft (MCA), o montante de pagamento mensal pode variar consoante a taxa de câmbio da moeda local.

Os pagamentos mensais não estão disponíveis para: Databricks, reservas de SUSE Linux, Planos do Red Hat e Licenças do Azure Red Hat OpenShift.

### <a name="view-payments-made"></a>Ver os pagamentos efetuados

Pode ver os pagamentos que foram efetuados através de APIs, dados de utilização e análise de custos. Para as reservas pagas mensalmente, o valor de frequência é apresentado como **periódico** nos dados de utilização e na API de Custos de Reservas. Para as reservas pagas antecipadamente, o valor é apresentado como **único**.

Análise de custos que mostra as compras mensais na vista predefinida. Para ver todas as compras, aplique o filtro **compra** em **Tipo de custo** e **recorrente** em **Frequência**. Para ver apenas as reservas, aplique um filtro para **Reserva**.

![Exemplo que mostra os custos da compra de reservas na análise de custos](./media/prepare-buy-reservation/cost-analysis.png)

### <a name="exchange-and-refunds"></a>Troca e reembolsos

Tal como outras reservas, pode pedir o reembolso ou trocar reservas compradas com faturação mensal. 

Ao trocar uma reserva paga mensalmente, o custo de duração total da nova compra deve ser superior ao dos pagamentos restantes que são cancelados para a reserva devolvida. Não existem quaisquer outros limites ou taxas para trocas. Pode trocar uma reserva paga antecipadamente para comprar uma nova reserva que é faturada mensalmente. No entanto, o valor de duração da nova reserva deve ser superior ao valor proporcional da reserva a ser devolvida.

Se cancelar uma reserva que seja paga mensalmente, os pagamentos futuros cancelados acumulam-se até ao limite de reembolso de 50 000 USD.

Para obter mais informações sobre trocas e reembolsos, veja [Trocas e reembolsos personalizados das Reservas do Azure](exchange-and-refund-azure-reservations.md).

## <a name="reservation-notifications"></a>Notificações de reserva

Dependendo da forma como paga a sua subscrição do Azure, são enviadas por e-mail notificações de reserva aos seguintes utilizadores na sua organização. As notificações são enviadas a propósito de vários eventos, incluindo: 

- Comprar
- Expiração da reserva futura
- Validade
- Renovação
- Cancelamento
- Alteração do âmbito

Para clientes com subscrições EA:

- As notificações são enviadas apenas para os contactos de notificação do EA.
- Os utilizadores adicionados a uma reserva através de uma permissão RBAC do Azure (IAM) não recebem notificações por e-mail.

Para os clientes de subscrições individuais:

- O comprador recebe uma notificação de compra.
- No momento da compra, o proprietário da conta de faturação da subscrição recebe uma notificação de compra.
- O proprietário da conta recebe todos os restantes tipos de notificações.

## <a name="next-steps"></a>Passos seguintes

- [Gerir reservas para recursos do Azure](manage-reserved-vm-instance.md)