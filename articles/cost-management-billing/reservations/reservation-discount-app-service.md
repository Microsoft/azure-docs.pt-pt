---
title: Descontos de reserva do Serviço de Aplicações do Azure
description: Saiba como os descontos de reserva se aplicam a instâncias v3 do Azure App Service Premium e selos isolados.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: banders
ms.openlocfilehash: c599c64ce4b22bbf7bece77602b22fef6629d07c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369735"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-premium-v3-instances-and-isolated-stamps"></a>Como os descontos de reserva se aplicam a instâncias premium v3 do Azure App Service e carimbos isolados

Este artigo ajuda-o a entender como os descontos se aplicam a instâncias v3 do Azure App Service Premium e selos isolados.

## <a name="how-reservation-discounts-apply-to-premium-v3-instances"></a>Como os descontos de reserva se aplicam a instâncias Premium v3

Depois de comprar um Azure App Service Premium v3 Instância Reservada, o desconto de reserva é automaticamente aplicado a instâncias do Serviço de Aplicações que correspondem aos atributos e quantidade da reserva. Uma reserva cobre o custo das suas instâncias Premium v3. 

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>Como o desconto é aplicado ao Azure App Service 

Um desconto de reserva é *use-it-or-lose-it*. Portanto, se não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.
Quando encerra um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão perdidas.

### <a name="reservation-discount-for-premium-v3-instances"></a>Desconto de reserva para Casos Premium v3

O desconto de reserva Azure é aplicado para executar instâncias Premium v3 por hora. As reservas que adquiriu correspondem ao uso emitido pelas instâncias Premium v3 em execução para aplicar o desconto de reserva. Para os casos Premium v3 que não possam esgotar a hora completa, a reserva será preenchida de outras instâncias que não utilizem uma reserva, incluindo instâncias em execução simultânea. No final da hora, o pedido de reserva, por exemplo, está bloqueado. No caso de um caso não se realizar durante uma hora ou casos simultâneos dentro de uma hora não preencher a hora da reserva, a reserva é subutilizada por essa hora. O grafo a seguir ilustra a aplicação de uma reserva para a utilização de máquina virtual faturável. A ilustração baseia-se numa compra de reserva e duas instâncias de máquina virtual correspondentes.

![Imagem mostrando a aplicação de uma reserva para o uso de VM faturada](./media/reservation-discount-app-service/reserved-premium-v3-instance-application.png)

1.  Qualquer utilização acima da linha de reserva é cobrada à tarifa normal pay as you go. Não será faturado pela utilização abaixo da linha de reservas, pois já foi pago como parte da compra de reserva.
2.  Na hora 1, a instância 1 é executada por 0,75 horas e a instância 2 é executada por 0,5 horas. A utilização total para a hora 1 é de 1,25 horas. É cobrado tarifas pay as you go relativamente às 0,25 horas restantes.
3.  Para a hora 2 e a hora 3, ambas as instâncias foram executadas por 1 hora. Uma instância é coberta pela reserva e a outra é cobrada de acordo à tarifa pay as you go.
4.  Para a hora 4, a instância 1 é executada por 0,5 horas e a instância 2 é executada por 1 hora. A instância 1 é totalmente coberta pela reserva e 0,5 horas da instância 2 é coberta. É-lhe cobrada a tarifa pay as you go pela 0,5 hora restante.

Para compreender e ver a aplicação das Reservas do Azure em relatórios de utilização da faturação, veja [Compreender a utilização das reservas](understand-reserved-instance-usage-ea.md).

## <a name="how-reservation-discounts-apply-to-isolated-stamps"></a>Como os descontos de reserva se aplicam aos Selos Isolados

Depois de comprar capacidade reservada para o Imposto de Selo de Plano Isolado do Serviço de Aplicações do Azure, o desconto de reserva é aplicado automaticamente ao Imposto de Selo numa região. O desconto de reserva aplica-se à utilização emitida pelo medidor de Imposto de Selo de Plano Isolado. Funções de trabalho, front-ends adicionais e quaisquer outros recursos associados ao selo continuam a ser faturados à tarifa normal.

### <a name="reservation-discount-application"></a>Aplicação de desconto de reserva

O desconto do Imposto de Selo de Plano Isolado do Serviço de Aplicações aplica-se a selos isolados em execução de hora a hora. Se não tiver um selo implementado durante uma hora, a capacidade reservada será desperdiçada durante essa hora. Não será transferida.

Após a compra, é feita a correspondência da reserva que comprou com um selo isolado em execução numa região específica. Se encerrar esse selo, os descontos de reserva serão aplicados automaticamente a quaisquer outros selos em execução na região. Quando não existem selos, a reserva é aplicada ao próximo selo que é criado na região.

Quando os selos não são executados durante uma hora completa, a reserva aplica-se automaticamente a outros selos correspondentes na mesma região durante a mesma hora.

### <a name="choose-a-stamp-type---windows-or-linux"></a>Escolha um tipo de selo – Windows ou Linux

Por predefinição, um Selo de Plano Isolado vazio emite o medidor de selos do Windows. Por exemplo, quando não estão implementadas funções de trabalho. O selo continua a emitir o medidor quando são implementadas funções de trabalho do Windows. O medidor mudará para o medidor de selos do Linux se implementar uma função de trabalho do Linux. O selo emite o medidor do Windows quando estão implementadas funções de trabalho do Windows e do Linux.

Como resultado, o medidor de selos pode mudar entre o Windows e o Linux durante a vida útil do selo. Enquanto isso, as reservas são específicas do sistema operativo. Precisa de comprar uma reserva que suporte as funções de trabalho que planeia implementar no selo. Selos só para Windows e selos mistos utilizam a reserva do Windows. Selos apenas com funções de trabalho do Linux utilizam a reserva do Linux.

A única vez que deve comprar uma reserva de selo do Linux é quando planear ter _apenas_ funções de trabalho do Linux no selo.

### <a name="discount-examples"></a>Exemplos de desconto

Os seguintes exemplos mostram como o desconto de instância reservada do Imposto de Selo de Plano Isolado se aplica em função das implementações.

- **Exemplo 1**: compra uma instância de capacidade de Selo Reservado de Plano Isolado numa região sem selos de Plano Isolado do Serviço de Aplicações. Implementa um novo selo na região e paga tarifas reservadas para esse selo.
- **Exemplo 2**: compra uma instância de capacidade de Selo Reservado de Plano Isolado numa região que já tem um selo de Plano Isolado do Serviço de Aplicações. Começa a receber a tarifa reservada para esse selo implementado.
- **Exemplo 3**: compra uma instância de capacidade de Selo Reservado de Plano Isolado numa região com um selo de Plano Isolado do Serviço de Aplicações já implementado. Começa a receber a tarifa reservada no selo implementado. Mais tarde, elimina o selo e implementa um novo. Recebe a tarifa reserva para o novo selo. Os descontos não são transferidos para as durações sem selos implementados.
- **Exemplo 4**: compra uma instância de capacidade de Selo Reservado do Linux de Plano Isolado numa região e, em seguida, implementa um novo selo na região. Quando o selo é implementado inicialmente sem funções de trabalho, emite o medidor de selos do Windows. Não são recebidos descontos. Quando se implementa a primeira função de trabalho do Linux no selo, este emite o medidor de Selos do Linux e aplica-se o desconto de reserva. Se uma função de trabalho do Windows for implementada posteriormente no selo, o medidor de selos será revertido para o Windows. Deixa de receber um desconto para a reserva do Selo Reservado do Linux de Plano Isolado.

## <a name="next-steps"></a>Passos seguintes

- Para saber como gerir uma reserva, veja [Gerir Reservas do Azure](manage-reserved-vm-instance.md).
- Para saber mais sobre a pré-compra app Service Premium v3 e Selo Isolado reservado capacidade para economizar dinheiro, consulte [Prepay for Azure App Service com capacidade reservada.](prepay-app-service.md)
- Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:
  - [O que são as reservas do Azure?](save-compute-costs-reservations.md)
  - [Gerir Reservas no Azure](manage-reserved-vm-instance.md)
  - [Compreender a utilização de reservas para uma subscrição com tarifas pay as you go](understand-reserved-instance-usage.md)
  - [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)
