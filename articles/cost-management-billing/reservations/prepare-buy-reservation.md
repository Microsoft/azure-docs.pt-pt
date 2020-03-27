---
title: Preparar-se para comprar uma reserva do Azure
description: Saiba quais são os aspetos importantes antes de comprar uma reserva do Azure.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 1f5ca2d43356eab98cffe8414c00d97e5744739a
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235657"
---
# <a name="prepare-to-buy-a-reservation"></a>Preparar-se para comprar uma reserva

O Azure Reservations ajuda-o a poupar ao subscrever planos de um ou três anos de vários recursos do Azure. Antes de assumir o compromisso de comprar uma reserva, não se esqueça de rever as secções seguintes para se preparar para a sua compra.

## <a name="who-can-buy-a-reservation"></a>Quem pode comprar uma reserva

Para comprar um plano, tem de ter uma função de proprietário da subscrição numa subscrição Enterprise (MS-AZR-0017P ou MS-AZR-0148P), Pay As You Go (MS-AZR-0003P ou MS-AZR-0023P) ou Contrato de Cliente da Microsoft. Os fornecedores de soluções cloud podem utilizar o portal do Azure ou o  [Centro de Parceiros](/partner-center/azure-reservations)  para comprar o Azure Reservations.

Os clientes do Contrato Enterprise (EA) podem limitar as compras a administradores do EA ao desativar a opção **Adicionar Instâncias Reservadas** no EA Portal. Os administradores do EA têm de ser o proprietário de, pelo menos, uma subscrição do EA para poderem comprar uma reserva. A opção mencionada é útil para as empresas que pretendem uma equipa centralizada encarregue de comprar reservas para diferentes centros de custos. Após a compra, as equipas centralizadas podem adicionar proprietários de centros de custos às reservas. Posteriormente, os proprietários podem definir o âmbito da reserva para as respetivas subscrições. A equipa centralizada não precisa de ter acesso de proprietário da subscrição nos locais onde a compra da reserva já foi concluída.

Os descontos de reserva só se aplicam a recursos associados a subscrições compradas através do Enterprise, do Fornecedor de Soluções Cloud (CSP), do Contrato de Cliente da Microsoft e de planos individuais com taxas pay as you go.

## <a name="scope-reservations"></a>Definir o âmbito de reservas

Pode definir o âmbito de uma reserva para uma subscrição ou para um grupo de recursos. Definir o âmbito de uma reserva permite selecionar onde as poupanças na reserva serão aplicadas. Ao definir o âmbito da reserva para um grupo de recursos, os descontos de reserva aplicam-se apenas ao grupo de recursos e não à totalidade da subscrição.

### <a name="reservation-scoping-options"></a>Opções de definição do âmbito da reserva

Tem três opções para definir o âmbito de uma reserva, consoante as suas necessidades:

- **Âmbito de grupo de recursos único** — aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionado.
- **Âmbito de subscrição individual** — aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.
- **Âmbito partilhado** — aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que estejam no contexto de faturação. Para clientes do Contrato Enterprise, o contexto de faturação é a inscrição. Para clientes do Contrato de Cliente da Microsoft, o âmbito de faturação é o perfil de faturação. Para subscrições individuais com taxas pay as you go, o âmbito da faturação reside em todas as subscrições elegíveis criadas pelo administrador de conta.

Enquanto aplica descontos de reserva sobre a sua utilização, o Azure processa a reserva pela seguinte ordem:

1. Reservas cujo âmbito está definido para um grupo de recursos
2. Reservas de âmbito único
3. Reservas de âmbito partilhado

Um grupo de recursos único pode obter descontos de reserva provenientes de múltiplas reservas, dependendo de como define o âmbito das suas reservas.

Pode sempre atualizar o âmbito após comprar uma reserva. Para tal, aceda à reserva, clique em **Configuração** e redefina o âmbito da reserva. A redefinição do âmbito de uma reserva não constitui uma transação comercial. O seu termo de reserva não será alterado. Para obter mais informações sobre a atualização do âmbito, veja [Atualizar o âmbito depois de comprar uma reserva](manage-reserved-vm-instance.md#change-the-reservation-scope).

![Exemplo a mostrar uma alteração do âmbito de reserva](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="purchase-reservations"></a>Reservas de compra

Pode comprar reservas no portal, nas APIs, no PowerShell e na CLI do Azure. Leia os seguintes artigos que se aplicam a si quando estiver pronto para fazer uma compra de reserva:

- [Serviço de Aplicações](prepay-app-service-isolated-stamp.md)
- [Cache do Azure para Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [BD do Cosmos](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](../../data-explorer/pricing-reserved-capacity.md)
- [Armazenamento de Discos](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Dedicated Host](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Planos de software](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Armazenamento](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Base de Dados SQL](../../sql-database/sql-database-reserved-capacity.md)
- [SQL Data Warehouse](prepay-sql-data-warehouse-charges.md)
- [Máquinas virtuais](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="next-steps"></a>Passos seguintes

- [Gerir reservas para recursos do Azure](manage-reserved-vm-instance.md)
