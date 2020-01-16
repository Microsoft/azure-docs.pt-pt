---
title: Compreender como se aplica o desconto de reserva ao Azure Cosmos DB | Microsoft Docs
description: Saiba como se aplica o desconto de reserva ao débito aprovisionado (RU/s) no Azure Cosmos DB.
services: cosmos-db
author: bandersmsft
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: banders
ms.reviewer: sngun
ms.openlocfilehash: cb20c88577bba614c70fdf5a266d482d06aeca57
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995562"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Compreender como se aplica o desconto de reserva ao Azure Cosmos DB

Depois de comprar uma capacidade reservada do Azure Cosmos DB, o desconto de reserva é automaticamente aplicado aos recursos do Azure Cosmos DB que correspondam aos atributos e à quantidade da reserva. Uma reserva inclui o débito aprovisionado dos recursos do Azure Cosmos DB. Não inclui software, redes, armazenamento ou custos de contentores predefinidos.

## <a name="how-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

Um desconto de reserva é "*use-it-or-lose-it*" (utilizar ou perder). Portanto, se não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.

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
|Azure Cosmos DB – 100 RU/s/Hora – Europa do Norte|  EU Norte       |    1     |
|Azure Cosmos DB – 100 RU/s/Hora – Sul da Coreia do Sul|    Sul KR do Sul     |     1    |
|Azure Cosmos DB – 100 RU/s/Hora – Europa Ocidental|    EU Oeste     |      1   |
|Azure Cosmos DB – 100 RU/s/Hora – Coreia do Sul Central|   KR do Sul Central    |       1  |
|Azure Cosmos DB – 100 RU/s/Hora – Sul do Reino Unido|   Sul do Reino Unido      |     1    |
|Azure Cosmos DB – 100 RU/s/Hora – Oeste do Reino Unido|   Oeste do Reino Unido      |    1     |
|Azure Cosmos DB – 100 RU/s/Hora – Norte do Reino Unido |   Norte do Reino Unido    |     1    |
|Azure Cosmos DB – 100 RU/s/Hora – Sul do Reino Unido 2|   Sul do Reino Unido 2      |     1    |
|Azure Cosmos DB – 100 RU/s/Hora – EUA Leste 2|  Este dos EUA 2     |     1    |
|Azure Cosmos DB – 100 RU/s/Hora – EUA Centro-Norte|   Centro Norte dos EUA      |     1    |
|Azure Cosmos DB – 100 RU/s/Hora – EUA Oeste|   Oeste dos EUA      |     1    |
|Azure Cosmos DB – 100 RU/s/Hora – EUA Central| Centro dos EUA        |     1    |
|Azure Cosmos DB – 100 RU/s/Hora – EUA Oeste 2|   E.U.A. Oeste 2      |      1   |
|Azure Cosmos DB – 100 RU/s/Hora – EUA Centro-Oeste|   E.U.A. Centro-Oeste      |       1  |
|Azure Cosmos DB – 100 RU/s/Hora – EUA Leste|   Este dos EUA      |  1       |
|Azure Cosmos DB – 100 RU/s/Hora – Norte da África do Sul|     Norte da África do Sul    |   1      |
|Azure Cosmos DB – 100 RU/s/Hora – Oeste da África do Sul |    Oeste da África do Sul      |    1     |
|Azure Cosmos DB – 100 RU/s/Hora – Sul da Índia|    IN Sul     |    1,0375    |
|Azure Cosmos DB – 100 RU/s/Hora – Leste do Canadá|   Leste do Canadá      |    1.1      |
|Azure Cosmos DB – 100 RU/s/Hora – Leste do Japão|   Este JA      |    1,125     |
|Azure Cosmos DB – 100 RU/s/Hora – Oeste do Japão|     Oeste JA    |   1,125       |
|Azure Cosmos DB – 100 RU/s/Hora – Oeste da Índia|     Índia Ocidental    |    1,1375     |
|Azure Cosmos DB – 100 RU/s/Hora – Índia Central|    IN Central     |  1,1375       |
|Azure Cosmos DB – 100 RU/s/Hora – Leste da Austrália|     AU Leste    |   1,15       |
|Azure Cosmos DB – 100 RU/s/Hora – Canadá Central|  Canadá Central       |   1.2       |
|Azure Cosmos DB – 100 RU/s/Hora – França Central|   FR Central      |    1,25      |
|Azure Cosmos DB – 100 RU/s/Hora – Sul BR|  Sul BR       |   1.5      |
|Azure Cosmos DB – 100 RU/s/Hora – Austrália Central|   AU Central      |   1.5      |
|Azure Cosmos DB – 100 RU/s/Hora – Austrália Central 2| AU Central 2        |    1.5     |
|Azure Cosmos DB – 100 RU/s/Hora – Sul de França|    FR Sul     |    1,625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Cenários que demonstram como se aplica o desconto de reserva

Considere os seguintes requisitos para uma reserva:

* Taxa de transferência necessária: 50.000 RU/s  
* Regiões usadas: 2

Neste caso, o total dos custos a pedido seria de 500 unidades do medidor de 100 RU/s nestas duas zonas. O consumo total de RU/s é de 100 000 por hora.

**Cenário 1**

Por exemplo, suponhamos que precisa de implementações do Azure Cosmos DB nas regiões EUA Centro-Norte e EUA Oeste. Cada região tem um consumo de débito de 50 000 RU/s. Uma compra de reserva de 100 000 RU/s iria equilibrar por completo os custos a pedido.

O desconto que uma reserva abrange é calculado da seguinte forma: consumo de débito * proporção_desconto_reserva_para_essa_região. Para as regiões EUA Centro-Norte e EUA Oeste, a proporção do desconto de reserva é 1. Portanto, o total de RU/s com desconto é de 100 000. Esse valor é calculado como: 50.000 * 1 + 50.000 * 1 = 100.000 RU/s. Não tem de pagar quaisquer custos adicionais às tarifas normais pay as you go.

|Descrição do medidor | Região |Consumo de débito (RU/s) |Desconto de reserva aplicado às RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/Hora – EUA Centro-Norte  |   Centro Norte dos EUA  | 50 000  | 50 000  |
|Azure Cosmos DB – 100 RU/s/Hora – EUA Oeste  |  Oeste dos EUA   |  50 000  |  50 000 |

**Cenário 2**

Por exemplo, suponhamos que precisa de implementações do Azure Cosmos DB nas regiões Austrália Central 2 e Sul de França. Cada região tem um consumo de débito de 50 000 RU/s. Uma compra de reserva de 100 000 RU/s seria aplicável da seguinte forma (supondo que a utilização da Austrália Central 2 tenha sido descontada primeiro):

|Descrição do medidor | Região |Consumo de débito (RU/s) |Desconto de reserva aplicado às RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/Hora – Austrália Central 2  |  AU Central 2   |  50 000  |  50 000   |
|Azure Cosmos DB – 100 RU/s/Hora – Sul de França  |  FR Sul   |  50 000 |  15 384  |

Uma utilização de 50 000 unidades na região Austrália Central 2 corresponde a 75 000 RU/s de utilização faturável (ou utilização normalizada). O valor é calculado da seguinte forma: consumo de débito * proporção_desconto_reserva_para_essa_região. O cálculo é igual a 75 000 RU/s de utilização faturável ou normalizada. Esse valor é calculado como: 50.000 * 1,5 = 75.000 RU/s.

As 100 000 RU/s da compra de reserva cobririam as 75 000 RU/s na Austrália Central 2. Deixaria 25 000 RU/s para a região do Sul de França. Das 25 000 RU/s restantes, aplica-se um desconto de reserva de 15 384 RU/s à região do Sul de França. O valor de desconto é calculado como: 25.000/1,625 = 15.384 RU/s. Os restantes 34 616 RU/s na região do Sul de França são cobrados com as tarifas normais pay as you go.

O sistema de faturação do Azure atribuirá o benefício de faturação de reserva à primeira instância que é processada e que corresponde às definições da reserva. Por exemplo, neste caso é a Austrália Central 2.

Para compreender e ver a aplicação das reservas do Azure em relatórios de utilização da faturação, veja [Compreender a utilização das reservas do Azure](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as reservas do Azure, veja os seguintes artigos:

* [O que são as reservas do Azure](save-compute-costs-reservations.md)  
* [Efetuar pré-pagamento de recursos do Azure Cosmos DB com capacidade reservada do Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../../sql-database/sql-database-reserved-capacity.md)  
* [Gerir reservas do Azure](manage-reserved-vm-instance.md)  
* [Compreender a utilização de reservas na sua subscrição Pay As You Go](understand-reserved-instance-usage.md)  
* [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)
* [Compreender a utilização de reservas nas subscrições do CSP](https://docs.microsoft.com/partner-center/azure-reservations)
