---
title: Otimizar os custos do Azure Databricks com uma compra prévia
description: Saiba como pode efetuar o pré-pagamento do Azure Databricks custos com capacidade de reserva para poupar dinheiro.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 99eb4de86aa227d558bec54d011a0b1548d27cf0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811261"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>Otimizar os custos do Azure Databricks com uma compra prévia

Pode salvar no seu Azure Databricks os custos de unidade (DBU) na compra prévia de Azure Databricks consolidar unidades (DBCU) para um ou três anos. Pode usar os DBCUs previamente adquiridas em qualquer altura durante a vigência de compra. Ao contrário de VMs, as unidades previamente adquiridas não expiram numa base horária e utilizá-los em qualquer altura durante o período de vigência da compra.

Qualquer utilização do Azure Databricks deducts automaticamente das DBUs previamente adquiridas. Não precisa de voltar a implementar ou atribuir um plano previamente adquirido a suas áreas de trabalho do Azure Databricks para a utilização DBU obter os descontos de compras prévia.

O desconto de compra prévia de aplica-se apenas à utilização de DBU. Outros custos, como computação, armazenamento e rede são cobrados em separado.

## <a name="determine-the-right-size-to-buy"></a>Determinar o tamanho certo para comprar

Pré-compra do Databricks se aplica a todos os escalões e cargas de trabalho do Databricks. Pode considerar a compra prévia de como um conjunto de unidades de consolidação de Databricks pré-pagos. Utilização é deduzida da agrupamento, independentemente da camada ou carga de trabalho. Utilização é deduzida na proporção seguinte:

| **Carga de trabalho** | **Rácio de aplicativo de DBU - escalão Standard** | **Rácio de aplicativo de DBU — escalão Premium** |
| --- | --- | --- |
| Análise de Dados | 0.4 | 0.55 |
| Engenharia de Dados | 0.15 | 0,30 |
| Engenharia de Dados Leve | 0.07 | 0.22 |

Por exemplo, quando uma quantidade da análise de dados – escalão Standard é consumido, as unidades de consolidação do Databricks previamente adquiridas é deduzidos por 0.4 unidades.

Antes de o comprar, calcule a quantidade total de DBU consumida para os escalões e diferentes cargas de trabalho. Utilize as taxas anteriores para normalizar a DBCU e, em seguida, execute uma projeção de utilização total, em seguida um ou três anos.

## <a name="purchase-databricks-commit-units"></a>Comprar unidades de consolidação do Databricks

Pode comprar planos de Databricks [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D). Para comprar capacidade de reserva, tem de ter a função de proprietário da subscrição, pelo menos, uma empresa.

- Atualmente, adquirir previamente só está disponível para clientes com Enterprise Agreement.
- Deve estar numa função de proprietário de pelo menos uma subscrição do Enterprise.
- Para subscrições Enterprise, **adicionar as instâncias reservadas** tem de estar ativada no [portal EA](https://ea.azure.com/). Em alternativa, se essa definição estiver desativada, tem de ser um administrador de EA da subscrição.

**Para comprar:**

1. Aceda ao [Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Selecione uma subscrição. Utilize o **subscrição** lista para selecionar a subscrição que é utilizada para pagar a capacidade de reserva. O método de pagamento da subscrição é cobrado os custos iniciais para a capacidade de reserva. Custos são deduzidos da saldo de alocação monetária a inscrição ou cobrados como utilização excedente.
1. Selecione um âmbito. Utilize o **âmbito** lista para selecionar um âmbito de subscrição:
    - **Único âmbito do grupo de recursos** – aplica o desconto de reserva para os recursos correspondentes na apenas o grupo de recursos selecionado.
    - **Único âmbito da subscrição** – aplica o desconto de reserva para os recursos correspondentes na subscrição selecionada.
    - **Partilhado âmbito** — se aplica o desconto de reserva a correspondência de recursos em subscrições elegíveis que estão no contexto de faturação. Para clientes com Enterprise Agreement, o contexto de faturação é a inscrição.
1. Selecione quantas unidades de consolidação do Azure Databricks que pretende comprar e concluir a compra.


![Exemplo que mostra a compra do Azure Databricks no portal do Azure](./media/billing-prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>Alterar âmbito e propriedade

Pode efetuar os seguintes tipos de alterações a uma reserva após a compra:

- Atualizar o âmbito da reserva
- Acesso baseado em funções

Não é possível dividir ou unir compra prévia de unidade de consolidação do Databricks. Para obter mais informações sobre a gestão de reservas, consulte [Gerir reservas após a compra](billing-manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Cancelamentos e trocas

Cancelar e o exchange não é suportada para planos de compras prévia do Databricks. Todas as compras podem ser anuladas.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre as reservas do Azure, veja os artigos seguintes:
  - [Quais são as reservas do Azure?](billing-save-compute-costs-reservations.md)
  - [Compreender como é aplicado um desconto DBCU de pré-compra do Azure Databricks](billing-reservation-discount-databricks.md)
  - [Compreender a utilização de reserva para inscrição da sua empresa](billing-understand-reserved-instance-usage-ea.md)
