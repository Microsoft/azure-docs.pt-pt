---
title: Como se aplicam os descontos de reserva aos Selos de Plano Isolado do Serviço de Aplicações do Azure
description: Saiba como se aplicam os descontos de reserva aos Selos de Plano Isolado do Serviço de Aplicações do Azure.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 759f83001353957f23b917440b68719b54690bea
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718806"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Como se aplicam os descontos de reserva aos Selos de Plano Isolado do Serviço de Aplicações do Azure

Depois de comprar capacidade reservada para o Imposto de Selo de Plano Isolado do Serviço de Aplicações do Azure, o desconto de reserva é aplicado automaticamente ao Imposto de Selo numa região. O desconto de reserva aplica-se à utilização emitida pelo medidor de Imposto de Selo de Plano Isolado. Funções de trabalho, front-ends adicionais e quaisquer outros recursos associados ao selo continuam a ser faturados à tarifa normal.

## <a name="reservation-discount-application"></a>Aplicação de desconto de reserva

O desconto do Imposto de Selo de Plano Isolado do Serviço de Aplicações aplica-se a selos isolados em execução de hora a hora. Se não tiver um selo implementado durante uma hora, a capacidade reservada será desperdiçada durante essa hora. Não será transferida.

Após a compra, é feita a correspondência da reserva que comprou com um selo isolado em execução numa região específica. Se encerrar esse selo, os descontos de reserva serão aplicados automaticamente a quaisquer outros selos em execução na região. Quando não existem selos, a reserva é aplicada ao próximo selo que é criado na região.

Quando os selos não são executados durante uma hora completa, a reserva aplica-se automaticamente a outros selos correspondentes na mesma região durante a mesma hora.

## <a name="choose-a-stamp-type---windows-or-linux"></a>Escolha um tipo de selo – Windows ou Linux

Por predefinição, um Selo de Plano Isolado vazio emite o medidor de selos do Windows. Por exemplo, quando não estão implementadas funções de trabalho. O selo continua a emitir o medidor quando são implementadas funções de trabalho do Windows. O medidor mudará para o medidor de selos do Linux se implementar uma função de trabalho do Linux. O selo emite o medidor do Windows quando estão implementadas funções de trabalho do Windows e do Linux.

Como resultado, o medidor de selos pode mudar entre o Windows e o Linux durante a vida útil do selo. Enquanto isso, as reservas são específicas do sistema operativo. Precisa de comprar uma reserva que suporte as funções de trabalho que planeia implementar no selo. Selos só para Windows e selos mistos utilizam a reserva do Windows. Selos apenas com funções de trabalho do Linux utilizam a reserva do Linux.

A única vez que deve comprar uma reserva de selo do Linux é quando planear ter _apenas_ funções de trabalho do Linux no selo.

## <a name="discount-examples"></a>Exemplos de desconto

Os seguintes exemplos mostram como o desconto de instância reservada do Imposto de Selo de Plano Isolado se aplica em função das implementações.

- **Exemplo 1**: compra uma instância de capacidade de Selo Reservado de Plano Isolado numa região sem selos de Plano Isolado do Serviço de Aplicações. Implementa um novo selo na região e paga tarifas reservadas para esse selo.
- **Exemplo 2**: compra uma instância de capacidade de Selo Reservado de Plano Isolado numa região que já tem um selo de Plano Isolado do Serviço de Aplicações. Começa a receber a tarifa reservada para esse selo implementado.
- **Exemplo 3**: compra uma instância de capacidade de Selo Reservado de Plano Isolado numa região com um selo de Plano Isolado do Serviço de Aplicações já implementado. Começa a receber a tarifa reservada no selo implementado. Mais tarde, elimina o selo e implementa um novo. Recebe a tarifa reserva para o novo selo. Os descontos não são transferidos para as durações sem selos implementados.
- **Exemplo 4**: compra uma instância de capacidade de Selo Reservado do Linux de Plano Isolado numa região e, em seguida, implementa um novo selo na região. Quando o selo é implementado inicialmente sem funções de trabalho, emite o medidor de selos do Windows. Não são recebidos descontos. Quando se implementa a primeira função de trabalho do Linux no selo, este emite o medidor de Selos do Linux e aplica-se o desconto de reserva. Se uma função de trabalho do Windows for implementada posteriormente no selo, o medidor de selos será revertido para o Windows. Deixa de receber um desconto para a reserva do Selo Reservado do Linux de Plano Isolado.

## <a name="next-steps"></a>Passos seguintes

- Para saber como gerir uma reserva, veja [Gerir Reservas do Azure](billing-manage-reserved-vm-instance.md).
- Para saber mais sobre a compra antecipada de capacidade reservada de Selos de Plano Isolado do Serviço de Aplicações para economizar dinheiro, veja [Pagamento antecipado do Imposto do Selo de Plano Isolado do Serviço de Aplicações do Azure com capacidade reservada](billing-prepay-app-service-isolated-stamp.md).
- Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:
  - [O que são as reservas do Azure?](billing-save-compute-costs-reservations.md)
  - [Gerir Reservas no Azure](billing-manage-reserved-vm-instance.md)
  - [Compreender a utilização de reservas para uma subscrição com tarifas pay as you go](billing-understand-reserved-instance-usage.md)
  - [Compreender a utilização de reservas na inscrição Enterprise](billing-understand-reserved-instance-usage-ea.md)
