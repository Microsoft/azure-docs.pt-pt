---
title: Como os descontos de reserva se aplicam ao Azure SQL Data Warehouse | Documentos da Microsoft
description: Saiba como os descontos de reserva se aplicam ao Azure SQL Data Warehouse para ajudar a poupar dinheiro.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 10e19377d31489cd19465fe6171ffb530bd58c28
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014727"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Como os descontos de reserva se aplicam ao Azure SQL Data Warehouse

Depois de comprar capacidade reservada do Azure SQL Data Warehouse, o desconto de reserva é automaticamente aplicado às armazéns de dados que existem nessa região. O desconto de reserva se aplica a utilização emitida pelo medidor de cDWU do SQL Data Warehouse. Redes e armazenamento são cobrados às tarifas pay as you go.

## <a name="reservation-discount-application"></a>Aplicação de desconto de reserva

O desconto de capacidade de reserva do SQL Data Warehouse é aplicado à execução armazéns numa base horária. Se não tiver um armazém implementado durante uma hora, em seguida, a capacidade de reserva é desperdiçada durante essa hora. Ele não passa.

Após a compra, a reserva que adquirir é correspondida a utilização de SQL Data Warehouse emitida ao executar armazéns em qualquer ponto no tempo. Se encerrar alguns armazéns, em seguida, os descontos de reserva aplicam automaticamente a outros armazéns de correspondentes.

Para armazéns de que não são executados durante uma hora completa, a reserva é aplicada automaticamente às outras instâncias correspondentes nessa hora.

## <a name="discount-examples"></a>Exemplos de desconto

Os exemplos seguintes mostram como aplica o desconto de capacidade de reserva do SQL Data Warehouse, consoante as implementações.

- **Exemplo 1**: O utilizador compra 5 unidades de 100 cDWU reservado capacidade. Executar uma instância de armazém de dados do SQL DW1500c durante uma hora. Neste caso, a utilização é emitida para 15 unidades de 100 cDWU utilização. O desconto de reserva se aplica as 5 unidades de que utilizou. É cobrado com base em tarifas pay as you go para as restantes 10 unidades de 100 utilização cDWU que utilizou.

- **Exemplo 2**: O utilizador compra 5 unidades de 100 cDWU reservado capacidade. Execute duas instâncias de DW100c SQL Data Warehouse para uma hora. Neste caso, dois eventos de utilização são emitidos para 1 unidade de 100 cDWU utilização. Ambos os eventos de utilização obtém descontos de capacidade de reserva. As restantes 3 unidades de 100 cDWU reservado capacidade são desperdiçadas e não vão ser transferidos para utilização futura.

- **Exemplo 3**: Comprar 1 unidade de capacidade de cDWU reservado 100. Execute duas instâncias de DW100c SQL Data Warehouse. Cada uma seja executada durante 30 minutos. Neste caso, os dois eventos de utilização obtém descontos de capacidade de reserva. Nenhuma utilização é cobrada com base em tarifas pay as you go.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

- Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as reservas do Azure, veja os artigos seguintes:

- [Quais são as reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Ver transações de reserva](billing-view-reservations.md)
- [Obtenha as transações de reserva e de utilização através da API](billing-reservation-apis.md)
- [Gerir reservas](billing-manage-reserved-vm-instance.md)
