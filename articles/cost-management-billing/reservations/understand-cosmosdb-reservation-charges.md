---
title: Compreender o desconto de reserva no Azure Cosmos DB
description: Saiba como se aplica o desconto de reserva ao débito aprovisionado (RU/s) no Azure Cosmos DB.
author: bandersmsft
ms.author: banders
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: ca4c1810912771f56661ca5b682b6991735f526e
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150822"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Compreender como se aplica o desconto de reserva ao Azure Cosmos DB

Depois de comprar uma capacidade reservada do Azure Cosmos DB, o desconto de reserva é automaticamente aplicado aos recursos do Azure Cosmos DB que correspondam aos atributos e à quantidade da reserva. Uma reserva inclui o débito aprovisionado dos recursos do Azure Cosmos DB. Não inclui software, redes, armazenamento ou custos de contentores predefinidos.

## <a name="how-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

Um desconto de reserva é “*use-it-or-lose-it*” (utilizar ou perder). Portanto, se não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.

Quando encerra um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão *perdidas*.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Desconto de reserva aplicado a contas do Azure Cosmos DB

O desconto de reserva é aplicado ao [débito aprovisionado](../../cosmos-db/request-units.md) em termos de unidades de pedido por segundo (RU/s) de hora a hora. Para os recursos do Azure Cosmos DB que não são executados durante uma hora completa, o desconto de reserva é automaticamente aplicado aos outros recursos do Azure Cosmos DB que correspondam aos atributos da reserva. O desconto pode ser aplicado a recursos do Azure Cosmos DB que estejam a ser executados em simultâneo. Se não tiver recursos do Cosmos DB que sejam executados durante um hora completa que correspondam aos atributos da reserva, não irá tirar partido completo do desconto de reserva para essa hora.

Os descontos são escalonados. As reservas com unidades de pedidos mais elevadas fornecem descontos maiores.

A compra de reserva aplicará os descontos a todas as regiões na proporção equivalente aos preços regionais a pedido. Para obter mais informações sobre as proporções de desconto de reserva em cada região, veja a seção [Desconto de reserva por região](#reservation-discount-per-region) deste artigo.

## <a name="reservation-discount-per-region"></a>Desconto de reserva por região
O desconto de reserva aplica-se aos custos de débito do Azure Cosmos DB por hora. É aplicado no âmbito de uma subscrição individual ou de uma inscrição/conta. O desconto de reserva aplica-se à utilização de medidores em regiões diferentes nas seguintes proporções:

|Descrição do medidor  |Região |Proporção  |
|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/Hora – Sudeste da Ásia-Pacífico  |  Sudeste da Ásia-Pacífico    |   1      |
|Azure Cosmos DB – 100 RU/s/Hora – Leste da Ásia-Pacífico |   Leste da Ásia-Pacífico   |    1     |
|Azure Cosmos DB – 100 RU/s/Hora – EU Norte|  EU Norte       |    1     |
|Azure Cosmos DB – 100 RU/s/Hora – Sul KR do Sul|    Sul KR do Sul     |     1    |
|Azure Cosmos DB – 100 RU/s/Hora – EU Oeste|    EU Oeste     |      1   |
|Azure Cosmos DB – 100 RU/s/Hora – KR do Sul Central|   KR do Sul Central    |       1  |
|Azure Cosmos DB – 100 RU/s/Hora – Sul do Reino Unido|   Sul do Reino Unido      |     1    |
|Azure Cosmos DB – 100 RU/s/Hora – Oeste do Reino Unido|   Oeste do Reino Unido      |    1     |
|Azure Cosmos DB – 100 RU/s/Hora – Norte do Reino Unido |   Norte do Reino Unido    |     1    |
|Azure Cosmos DB – 100 RU/s/Hora – Sul do Reino Unido 2|   Sul do Reino Unido 2      |     1    |
|Azure Cosmos DB – 100 RU/s/Hora – E.U.A.  Leste 2|  E.U.A. Leste 2     |     1    |
|Azure Cosmos DB – 100 RU/s/Hora – E.U.A. Centro-Norte|   E.U.A. Centro-Norte      |     1    |
|Azure Cosmos DB – 100 RU/s/Hora – E.U.A. Oeste|   E.U.A. Oeste      |     1    |
|Azure Cosmos DB – 100 RU/s/Hora – E.U.A. Central| E.U.A. Central        |     1    |
|Azure Cosmos DB – 100 RU/s/Hora – E.U.A. Oeste 2|   E.U.A. Oeste 2      |      1   |
|Azure Cosmos DB – 100 RU/s/Hora – E.U.A. Centro-Oeste|   E.U.A. Centro-Oeste      |       1  |
|Azure Cosmos DB – 100 RU/s/Hora – E.U.A. Leste|   E.U.A Leste      |  1       |
|Azure Cosmos DB – 100 RU/s/Hora – Norte da África do Sul|     Norte da África do Sul    |   1      |
|Azure Cosmos DB – 100 RU/s/Hora – Oeste da África do Sul |    Oeste da África do Sul      |    1     |
|Azure Cosmos DB – 100 RU/s/Hora – IN Sul|    IN Sul     |    1,0375    |
|Azure Cosmos DB – 100 RU/s/Hora – CA Leste|   CA Leste      |    1.1      |
|Azure Cosmos DB – 100 RU/s/Hora – JA Leste|   JA Leste      |    1,125     |
|Azure Cosmos DB – 100 RU/s/Hora – JA Oeste|     JA Oeste    |   1,125       |
|Azure Cosmos DB – 100 RU/s/Hora – IN Oeste|     IN Oeste    |    1,1375     |
|Azure Cosmos DB – 100 RU/s/Hora – IN Central|    IN Central     |  1,1375       |
|Azure Cosmos DB – 100 RU/s/Hora – AU Leste|     AU Leste    |   1,15       |
|Azure Cosmos DB – 100 RU/s/Hora – CA Central|  CA Central       |   1.2       |
|Azure Cosmos DB – 100 RU/s/Hora – FR Central|   FR Central      |    1,25      |
|Azure Cosmos DB – 100 RU/s/Hora – Sul BR|  Sul BR       |   1.5      |
|Azure Cosmos DB – 100 RU/s/Hora – AU Central|   AU Central      |   1.5      |
|Azure Cosmos DB – 100 RU/s/Hora – AU Central 2| AU Central 2        |    1.5     |
|Azure Cosmos DB – 100 RU/s/Hora – FR Sul|    FR Sul     |    1,625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Cenários que demonstram como se aplica o desconto de reserva

Considere os seguintes requisitos para uma reserva:

* Débito necessário: 50 000 RU/s  
* Regiões utilizadas: 2

Neste caso, o total dos custos a pedido seria de 500 unidades do medidor de 100 RU/s nestas duas zonas. O consumo total de RU/s é de 100 000 por hora.

**Cenário 1**

Por exemplo, suponhamos que precisa de implementações do Azure Cosmos DB nas regiões E.U.A. Centro-Norte e E.U.A. Oeste. Cada região tem um consumo de débito de 50 000 RU/s. Uma compra de reserva de 100 000 RU/s iria equilibrar por completo os custos a pedido.

O desconto que uma reserva abrange é calculado da seguinte forma: consumo de débito * proporção_desconto_reserva_para_essa_região. Para as regiões E.U.A. Centro-Norte e E.U.A. Oeste, a proporção do desconto de reserva é 1. Portanto, o total de RU/s com desconto é de 100 000. Este valor é calculado da seguinte forma: 50 000 x 1 + 50 000 x 1 = 100 000 RU/s. Não tem de pagar quaisquer custos adicionais às tarifas normais pay as you go.

|Descrição do medidor | Região |Consumo de débito (RU/s) |Desconto de reserva aplicado às RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/Hora – E.U.A. Centro-Norte  |   E.U.A. Centro-Norte  | 50 000  | 50 000  |
|Azure Cosmos DB – 100 RU/s/Hora – E.U.A. Oeste  |  E.U.A. Oeste   |  50 000  |  50 000 |

**Cenário 2**

Por exemplo, suponhamos que precisa de implementações do Azure Cosmos DB nas regiões AU Central 2 e Sul de França. Cada região tem um consumo de débito de 50 000 RU/s. Uma compra de reserva de 100 000 RU/s seria aplicável da seguinte forma (supondo que a utilização da AU Central 2 tenha sido descontada primeiro):

|Descrição do medidor | Região |Consumo de débito (RU/s) |Desconto de reserva aplicado às RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/Hora – AU Central 2  |  AU Central 2   |  50 000  |  50 000   |
|Azure Cosmos DB – 100 RU/s/Hora – FR Sul  |  FR Sul   |  50 000 |  15 384  |

Uma utilização de 50 000 unidades na região AU Central 2 corresponde a 75 000 RU/s de utilização faturável (ou utilização normalizada). O valor é calculado da seguinte forma: consumo de débito * proporção_desconto_reserva_para_essa_região. O cálculo é igual a 75 000 RU/s de utilização faturável ou normalizada. Este valor é calculado da seguinte forma: 50 000 x 1,5 = 75 000 RU/s.

As 100 000 RU/s da compra de reserva cobririam as 75 000 RU/s na AU Central 2. Deixaria 25 000 RU/s para a região do FR Sul. Das 25 000 RU/s restantes, aplica-se um desconto de reserva de 15 384 RU/s à região do FR Sul. Este valor de desconto é calculado da seguinte forma: 25 000 / 1,625 = 15 384 RU/s. Os restantes 34 616 RU/s na região do FR Sul são cobrados com as tarifas normais pay as you go.

O sistema de faturação do Azure atribuirá o benefício de faturação de reserva à primeira instância que é processada e que corresponde às definições da reserva. Por exemplo, neste caso é a AU Central 2.

Para compreender e ver a aplicação das reservas do Azure em relatórios de utilização da faturação, veja [Compreender a utilização das reservas do Azure](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as reservas do Azure, veja os seguintes artigos:

* [O que são as reservas do Azure](save-compute-costs-reservations.md)  
* [Efetuar pré-pagamento de recursos do Azure Cosmos DB com capacidade reservada do Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../../azure-sql/database/reserved-capacity-overview.md)  
* [Gerir reservas do Azure](manage-reserved-vm-instance.md)  
* [Compreender a utilização de reservas na sua subscrição Pay As You Go](understand-reserved-instance-usage.md)  
* [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)
* [Compreender a utilização de reservas nas subscrições do CSP](/partner-center/azure-reservations)