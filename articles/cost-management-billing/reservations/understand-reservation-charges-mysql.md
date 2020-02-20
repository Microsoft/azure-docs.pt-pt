---
title: Como um desconto de reserva é aplicado à Base de Dados do Azure para MySQL
description: Como um desconto de reserva é aplicado à Base de Dados do Azure para MySQL
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/13/2020
ms.openlocfilehash: ed067e4bbde737cb08afe7c9429854e54e2ef3d9
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199318"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mysql"></a>Como um desconto de reserva é aplicado à Base de Dados do Azure para MySQL

Depois de comprar uma capacidade reservada da Base de Dados do Azure para MySQL, o desconto de reserva é automaticamente aplicado aos servidores MySQL que correspondam aos atributos e à quantidade da reserva. Uma reserva abrange apenas os custos de computação da sua Base de Dados do Azure para MySQL. É-lhe cobrada uma taxa normal de armazenamento e rede.

## <a name="how-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

Um desconto de reserva funciona numa base ***use-it-or-lose-it*** (utilizar ou perder). Portanto, se não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.</br>

Quando encerra um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não forem encontrados recursos correspondentes no âmbito especificado, as horas reservadas perdem-se.

## <a name="discount-applied-to-azure-database-for-mysql"></a>Desconto aplicado à Base de Dados do Azure para MySQL

O desconto da capacidade reservada da Base de Dados do Azure para MySQL é aplicado à execução dos seus servidores MySQL de hora a hora. A reserva que comprar é correspondida à utilização de computação emitida pelos servidores da Base de Dados do Azure para MySQL em execução. No caso dos servidores MySQL que não são executados durante uma hora completa, a reserva é automaticamente aplicada a outra Base de Dados do Azure para MySQL que corresponda aos atributos da reserva. O desconto pode ser aplicado a servidores da Base de Dados do Azure para MySQL que estejam a ser executados em simultâneo. Se não tiver um servidor MySQL que seja executado durante toda a hora que corresponda aos atributos da reserva, não receberá o benefício completo do desconto de reserva para essa hora.

Os exemplos seguintes mostram como o desconto da capacidade reservada da Base de Dados do Azure para MySQL se aplica em função do número de núcleos que comprou e quando os mesmos estão em execução.

* **Exemplo 1**: Compra uma capacidade reservada da Base de Dados do Azure para MySQL para 8 vCore. Se estiver a executar um servidor da Base de Dados do Azure para MySQL de 16 vCore que corresponda ao resto dos atributos da reserva, é-lhe cobrado o preço pay as you go para 8 vCore da utilização de computação do seu servidor MySQL e recebe o desconto de reserva para uma hora de utilização de computação do servidor MySQL de 8 vCore.</br>

Para o resto destes exemplos, assuma que a capacidade reservada da Base de Dados do Azure para MySQL que comprou é para uma Base de Dados do Azure para MySQL de 16 vCore e que o resto dos atributos da reserva correspondem aos servidores MySQL em execução.

* **Exemplo 2**: Executa dois servidores da Base de Dados do Azure para MySQL com 8 vCore cada durante uma hora. O desconto de reserva de 16 vCore é aplicado à utilização de computação para ambos os servidores da Base de Dados do Azure para MySQL de 8 vCore.

* **Exemplo 3**: Executa um servidor da Base de Dados do Azure para MySQL de 16 vCore das 13:00 às 13:30. Executa outro servidor da Base de Dados do Azure para MySQL de 16 vCore das 13:30 às 14:00. Ambas são abrangidas pelo desconto de reserva.

* **Exemplo 4**: Executa um servidor da Base de Dados do Azure para MySQL de 16 vCore das 13:00 às 13:45. Executa outro servidor da Base de Dados do Azure para MySQL de 16 vCore das 13:30 às 14:00. É-lhe cobrado o preço pay-as-you-go pela sobreposição de 15 minutos. O desconto de reserva aplica-se à utilização de computação durante o resto do tempo.

Para compreender e ver a aplicação das Reservas do Azure em relatórios de utilização da faturação, veja [Compreender a utilização das reservas do Azure](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos
Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
