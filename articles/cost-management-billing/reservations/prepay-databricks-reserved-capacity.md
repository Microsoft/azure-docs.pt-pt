---
title: Otimizar custos do Azure Databricks com uma pré-compra
description: Saiba como pode efetuar um pré-pagamento por encargos do Azure Databricks com capacidade de reserva para poupar dinheiro.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 390a8b421a7b34391bde689e4b968fa98cdbaf76
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599159"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>Otimizar custos do Azure Databricks com uma pré-compra

Pode economizar nos seus custos de unidades do Azure Databricks (DBU) quando efetua uma pré-compra de unidades de consolidação do Azure Databricks (DBCU) por um ou três anos. Pode usar as DBCUs pré-compradas em qualquer altura durante o termo de compra. Ao contrário das VMs, as unidades pré-compradas não expiram por hora e são usadas em qualquer altura durante o termo da compra.

Qualquer utilização de Azure Databricks é deduzida das DBUs pré-compradas automaticamente. Não precisa de reimplementar ou atribuir um plano pré-comprado às suas áreas de trabalho do Azure Databricks para a utilização da DBU para obter os descontos de pré-compra.

O desconto de pré-compra aplica-se apenas à utilização da DBU. Outros custos, como computação, armazenamento e rede, são cobrados em separado.

## <a name="determine-the-right-size-to-buy"></a>Determinar o tamanho certo a comprar

A pré-compra de Databricks aplica-se a todas as cargas de trabalho e escalões de Databricks. Pode considerar a pré-compra como um conjunto de unidades de consolidação de Databricks pré-pagos. A utilização é deduzida do conjunto, independentemente da carga de trabalho ou do escalão. A utilização é deduzida na seguinte proporção:

| **Carga de trabalho** | **Taxa de aplicações de DBU — escalão Standard** | **Taxa de aplicações de DBU — escalão Premium** |
| --- | --- | --- |
| Análise de Dados | 0.4 | 0.55 |
| Engenharia de Dados | 0.15 | 0.30 |
| Engenharia de Dados Leve | 0.07 | 0.22 |

Por exemplo, quando uma quantidade de Análise de Dados – Escalão Standard é consumida, as unidades de consolidação de Databricks pré-compradas são deduzidas por 0,4 unidades.

Antes de comprar, calcule a quantidade total de DBUs consumidas para diferentes cargas de trabalho e escalões. Use as proporções precedentes para normalizar para DBCU e, em seguida, execute uma projeção da utilização total sobre o próximo ano ou a três anos.

## <a name="purchase-databricks-commit-units"></a>Comprar unidades de consolidação do Databricks

Pode comprar planos do Databricks no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D). Para comprar capacidade de reserva, deve ter a função de proprietário em pelo menos uma subscrição Enterprise.

- Tem de ter a função de Proprietário em pelo menos um Contrato Enterprise (números da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um Contrato de Cliente Microsoft ou ter uma subscrição individual com tarifas pay as you go (números da oferta: MS-AZR-0003P ou MS-AZR-0023P).
- Para subscrições EA, a opção Adicionar Instâncias Reservadas deve ser ativada no EA Portal. Em alternativa, se essa definição estiver desativada, terá de ser o Administrador EA da subscrição.
- Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com/). Em alternativa, se essa definição estiver desativada, tem ser um Administrador EA da subscrição.

**Para Comprar:**

1. Aceda ao [Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Selecione uma subscrição. Use a lista **Subscrição** para selecionar a subscrição que serve para pagar a capacidade de reserva. Os custos iniciais são cobrados de acordo com o método de pagamento indicado na capacidade de reserva. Os custos são deduzidos do saldo do Pré-pagamento do Azure (que antes se chamava alocação monetária) da inscrição, se disponível, ou cobrados como utilização excedida.
1. Selecione um âmbito. Use a lista **Âmbito** para selecionar um âmbito de subscrição:
    - **Âmbito de grupo de recursos único** – aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionado.
    - **Âmbito de subscrição individual** – aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.
    - **Âmbito partilhado** – aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que estejam no contexto de faturação. Para clientes do Contrato Enterprise, o contexto de faturação é a inscrição.
1. Selecione quantas unidades de consolidação de Azure Databricks quer comprar e conclua a compra.


![Exemplo que mostra a compra de Azure Databricks no portal do Azure](./media/prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>Alterar âmbito e propriedade

Pode fazer os seguintes tipos de alterações numa reserva após a compra:

- Atualizar âmbito da reserva
- Controlo de acesso baseado em funções do Azure (RBAC do Azure)

Não pode dividir ou unir a pré-compra de unidades de consolidação de Databricks. Para obter mais informações sobre como gerir reservas, veja [Gerir reservas após a compra](manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Cancelamentos e trocas

O cancelamento e a troca não são suportados para os planos de pré-compra de Databricks. Todas as compras são definitivas.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:
  - [O que são as reservas do Azure?](save-compute-costs-reservations.md)
  - [Compreenda como é aplicado um desconto de DBCU de pré-compra de Azure Databricks](reservation-discount-databricks.md)
  - [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)
