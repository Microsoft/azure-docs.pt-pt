---
title: Como os descontos de reserva se aplicam ao Azure Synapse Analytics | Microsoft Docs
description: Saiba como os descontos de reserva se aplicam ao Azure Synapse Analytics para poupar dinheiro.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: banders
ms.openlocfilehash: 33a0a0a62814579dee10ae046338ff9f78d667cb
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82627080"
---
# <a name="how-reservation-discounts-apply-to-azure-synapse-analytics"></a>Como os descontos de reserva se aplicam ao Azure Synapse Analytics

Depois de comprar a capacidade reservada do Azure Synapse Analytics, o desconto de reserva é aplicado automaticamente às instâncias aprovisionadas que existem nessa região. O desconto de reserva aplica-se à utilização emitida pelo medidor de cDWU do Azure Synapse Analytics. O armazenamento e a rede são cobrados com tarifas pay as you go.

## <a name="reservation-discount-application"></a>Aplicação de desconto de reserva

O desconto da capacidade reservada do Azure Synapse Analytics é aplicado aos armazéns em execução por hora. Se não tiver um armazém implementado por uma hora, a capacidade reservada será desperdiçada para essa hora. Não será transferida.

Após a compra, a reserva que compra é correspondida à utilização emitida do Azure Synapse Analytics por armazéns em execução em qualquer ponto no tempo. Se encerrar alguns armazéns, os descontos de reserva serão aplicados automaticamente a qualquer outro armazém correspondente.

Para armazéns que não são executados por uma hora completa, a reserva é aplicada automaticamente a outras instâncias correspondentes nessa hora.

## <a name="discount-examples"></a>Exemplos de desconto

Os seguintes exemplos mostram como o desconto de capacidade reservada do Azure Synapse Analytics se aplica em função das implementações.

- **Exemplo 1**: adquire 5 unidades de capacidade reservada de 100 cDWU. Pode executar uma instância do Azure Synapse Analytics de DW1500c durante uma hora. Neste caso, a utilização é emitida para 15 unidades de 100 cDWU de utilização. O desconto de reserva aplica-se às 5 unidades que utilizou. É cobrado com tarifas pay as you go para as 10 unidades restantes de 100 cDWU de utilização que utilizou. Por outras palavras, é possível uma cobertura parcial para múltiplas reservas.

- **Exemplo 2**: adquire 5 unidades de capacidade reservada de 100 cDWU. Pode executar duas instâncias do Azure Synapse Analytics de DW100c durante uma hora. Neste caso, são emitidos dois eventos de utilização para 1 unidade de 100 cDWU de utilização. Os dois eventos de utilização recebem descontos de capacidade reservada. As 3 unidades restantes de capacidade reservada de 100 cDWU são desperdiçadas e não são transferidas para uso futuro. Por outras palavras, uma reserva individual pode ser associada a várias instâncias do Azure Synapse Analytics.

- **Exemplo 3**: adquire 1 unidade de capacidade reservada de 100 cDWU. Pode executar duas instâncias do Azure Synapse Analytics de DW100c. Cada uma é executada por 30 minutos. Neste caso, os dois eventos de utilização recebem descontos de capacidade reservada. Nenhuma utilização é cobrada com tarifas pay as you go.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

- Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

- [O que são as Reservas do Azure?](save-compute-costs-reservations.md)
- [Visualizar transações de reserva](view-reservations.md)
- [Obter transações de reserva e utilização por meio de API](reservation-apis.md)
- [Gerir reservas](manage-reserved-vm-instance.md)
