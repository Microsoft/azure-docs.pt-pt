---
title: Compreender como o desconto de reserva é aplicado ao Azure Cosmos DB | Documentos da Microsoft
description: Saiba como o desconto de reserva é aplicado a débito aprovisionado (RU/s) no Azure Cosmos DB.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: banders
ms.reviewer: sngun
ms.openlocfilehash: f6549710f90c8d59ed443ab9ae1a302a2d8278d5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57899524"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Compreender como o desconto de reserva é aplicado ao Azure Cosmos DB

Depois de comprar uma capacidade de reservada do Azure Cosmos DB, o desconto de reserva é automaticamente aplicado aos recursos do Azure Cosmos DB que correspondem a atributos e a quantidade da reserva. Uma reserva abrange o débito aprovisionado para recursos do Azure Cosmos DB. Ela não cobre o software, rede, armazenamento ou predefinidas cobranças de contentor.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Desconto de reserva aplicado a contas do Azure Cosmos DB

É aplicado um desconto de reserva para [débito aprovisionado](../cosmos-db/request-units.md) em termos de unidades de pedido por segundo (RU/s) com base numa hora em hora. Para obter recursos do Azure Cosmos DB que não são executados a hora completa, o desconto de reserva é aplicado automaticamente a outros recursos do Cosmos DB que correspondem os atributos de reserva. O desconto pode aplicar aos recursos do Azure Cosmos DB que estão em execução em simultâneo. Se não tiver recursos do Cosmos DB que executam durante a hora completa e que correspondam aos atributos de reserva, não obtém todos os benefícios do desconto de reserva para essa hora.

Os descontos são dispostos em camadas. As reservas com unidades de pedido superior oferecem descontos superiores. 

A compra de reserva aplicará descontos para todas as regiões na proporção equivalente para os preços regionais de sob demanda. Para taxas de desconto de reserva em cada região, consulte a [desconto de reserva por região](#reservation-discount-per-region) seção deste artigo.

## <a name="reservation-discount-per-region"></a>Desconto de reserva por região
O desconto de reserva é aplicado aos custos de débito do Azure Cosmos DB com base numa hora em hora. Esta é aplicada no âmbito da conta/inscritos ou única subscrição. O desconto de reserva aplica-se para medir a utilização em diferentes regiões nas taxas seguintes:

|Descrição de medidor  |Região |Proporção  |
|---------|---------|---------|
|Sudeste da Ásia Pacífico - 100 RU/s/hora - Azure Cosmos DB  |  Sudeste da Ásia-Pacífico    |   1      |
|Leste da Ásia Pacífico - 100 RU/s/hora - Azure Cosmos DB |   Leste da Ásia-Pacífico   |    1     |
|Azure Cosmos DB - 100 RU/s/hora - EU norte|  EU Norte       |    1     |
|Sul da Coreia do Azure Cosmos DB - 100 RU/s/hora-|    Sul KR do Sul     |     1    |
|Azure Cosmos DB - 100 RU/s/hora - EU oeste|    EU Oeste     |      1   |
|Azure Cosmos DB - 100 RU/s/hora - Coreia Central|   KR do Sul Central    |       1  |
|Azure Cosmos DB - 100 RU/s/hora - Unido Sul|   Reino Unido Sul      |     1    |
|Azure Cosmos DB - 100 RU/s/hora - Unido oeste|   Reino Unido Oeste      |    1     |
|Azure Cosmos DB - 100 RU/s/hora - UK norte |   Norte do Reino Unido    |     1    |
|Azure Cosmos DB - 100 RU/s/hora - Unido Sul 2|   Sul do Reino Unido 2      |     1    |
|Azure Cosmos DB - 100 RU/s/hora - E.U. a leste 2|  Este dos EUA 2     |     1    |
|Azure Cosmos DB - 100 RU/s/hora - Centro Norte dos EUA|   Centro Norte dos EUA      |     1    |
|Azure Cosmos DB - 100 RU/s/hora - E.U.A. oeste|   E.U.A. Oeste      |     1    |
|Azure Cosmos DB - 100 RU/s/hora - E.U.A. Central| Centro dos EUA        |     1    |
|Azure Cosmos DB - 100 RU/s/hora - E.U.A. oeste 2|   E.U.A. Oeste 2      |      1   |
|Azure Cosmos DB - 100 RU/s/hora - e.u.a. centro-oeste|   E.U.A. Centro-Oeste      |       1  |
|Azure Cosmos DB - 100 RU/s/hora - E.U. a leste|   E.U.A Leste      |  1       |
|Norte da África do Azure Cosmos DB - 100 RU/s/hora-|     Norte da África do    |   1      |
|Azure Cosmos DB - 100 RU/s/Hour - SA West |    África do Sul Ocidental      |    1     |
|Azure Cosmos DB - 100 RU/s/hora - sul da Índia|    IN Sul     |    1.0375    |
|Azure Cosmos DB - 100 RU/s/hora - AC leste|   Leste do Canadá      |    1.1      |
|Azure Cosmos DB - 100 RU/s/hora - JA leste|   Este JA      |    1.125     |
|Azure Cosmos DB - 100 RU/s/Hour - JA West|     Oeste JA    |   1.125       |
|Azure Cosmos DB - 100 RU/s/hora - Índia Ocidental|     Índia Ocidental    |    1.1375     |
|Azure Cosmos DB - 100 RU/s/hora - Índia Central|    IN Central     |  1.1375       |
|Azure Cosmos DB - 100 RU/s/hora - AU leste|     AU Leste    |   1.15       |
|Azure Cosmos DB - 100 RU/s/hora - Canadá Central|  Canadá Central       |   1.2       |
|Azure Cosmos DB - 100 RU/s/hora - França Central|   FR Central      |    1.25      |
|Sul do Brasil do Azure Cosmos DB - 100 RU/s/hora-|  Sul BR       |   1.5      |
|Azure Cosmos DB - 100 RU/s/hora - Austrália Central|   AU Central      |   1.5      |
|Azure Cosmos DB - 100 RU/s/hora - Austrália Central 2| AU Central 2        |    1.5     |
|Sul de França - 100 RU/s/hora - Azure Cosmos DB|    FR Sul     |    1.625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Cenários que demonstram como o desconto de reserva é aplicado

Considere os seguintes requisitos para uma reserva:

* Taxa de transferência necessária: 50 000 RU/s  
* Regiões utilizados: 2 

Neste caso, o total das cobranças de sob demanda destinam-se 500 quantidade de medidor de 100 RU/s nestas duas regiões. O consumo de RU/s total é 100.000 a cada hora. 

**Cenário 1**

Por exemplo, suponha que precisa de implementações do Azure Cosmos DB nas regiões e.u.a. Centro-Norte e E.u.a. oeste. Cada região tem um consumo de débito de 50 000 RU/s. Uma compra de reserva de 100 000 RU/s seria completamente equilibrar os custos com a pedido.

O desconto que abrange uma reserva é computado como: o consumo de débito * reservation_discount_ratio_for_that_region. Para as regiões e.u.a. Centro-Norte e E.u.a. oeste, a taxa de desconto de reserva é 1. Sendo assim, o total RU/s com desconto são 100.000. Este valor é computado como: 50 000 * 1 + 50 000 * 1 = 100 000 RU/s. Não tem de pagar quaisquer custos adicionais às tarifas pay as you go normais. 

|Descrição de medidor | Região |Consumo de débito (RU/s) |Desconto de reserva aplicado para RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 RU/s/hora - Centro Norte dos EUA  |   Centro Norte dos EUA  | 50,000  | 50,000  |
|Azure Cosmos DB - 100 RU/s/hora - E.U.A. oeste  |  E.U.A. Oeste   |  50,000  |  50,000 |

**Cenário 2**

Por exemplo, suponha que precisa de implementações do Azure Cosmos DB nas regiões AU Central 2 e sul de França. Cada região tem um consumo de débito de 50 000 RU/s. Uma compra de reserva de 100 000 RU/s seria aplicável da seguinte forma (supondo que a utilização de AU Central 2 foi com desconto em primeiro lugar):

|Descrição de medidor | Região |Consumo de débito (RU/s) |Desconto de reserva aplicado para RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 RU/s/hora - Austrália Central 2  |  AU Central 2   |  50,000  |  50,000   |
|Sul de França - 100 RU/s/hora - Azure Cosmos DB  |  FR Sul   |  50,000 |  15,384  |

Uma utilização de 50 000 unidades na região Central 2 de AU corresponde a 75.000 RU/s de utilização faturável (ou utilização normalizada). Este valor é computado como: o consumo de débito * reservation_discount_ratio_for_that_region. A computação é igual a 75.000 RU/s de utilização cobrar ou normalizada. Este valor é computado como: 50 000 * 1.5 = 75,000 RU/s.

100 000 RU/s de compra de reserva seria de deslocamento 75.000 RU/s em 2 de Central de AU. Ele deixa 25 000 RU/s para a região Sul de França. De que os restantes 25 000 RU/s, é aplicado um desconto de reserva de 15,384 RU/s para a região Sul de França. O valor do desconto é computado como: 25.000 / 1.625 = 15,384 RU/s. O restante 34,616 RU/s na região Sul de França são cobradas às tarifas pay as you go normais. 

O sistema de faturação do Azure atribuirá a principal benefício para a primeira instância que está a ser processado e que corresponde à configuração de reserva de faturação de reserva. Por exemplo, é AU Central 2 em vez disso.

Para compreender e ver a aplicação das reservas do Azure no relatórios de utilização de faturação, veja [utilização de reserva de compreender o Azure](../billing/billing-understand-reserved-instance-usage-ea.md).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as reservas do Azure, veja os artigos seguintes:

* [Quais são as reservas do Azure?](../billing/billing-save-compute-costs-reservations.md)  
* [Efetue o pré-pagamento do recursos do Azure Cosmos DB com capacidade de reservada do Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../sql-database/sql-database-reserved-capacity.md)  
* [Gerir reservas do Azure](../billing/billing-manage-reserved-vm-instance.md)  
* [Compreender a utilização de reserva para a sua subscrição pay as you go](../billing/billing-understand-reserved-instance-usage.md)  
* [Compreender a utilização de reserva para inscrição da sua empresa](../billing/billing-understand-reserved-instance-usage-ea.md)  
* [Compreender a utilização de reserva para subscrições de CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

