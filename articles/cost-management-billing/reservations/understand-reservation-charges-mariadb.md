---
title: Compreender um desconto de reserva - Azure Database for MariaDB
description: Saiba como um desconto de reserva é aplicado ao Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 2197f3ec9907aa9f1afde6272dd2e8a807465476
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240397"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mariadb"></a>Como um desconto de reserva é aplicado ao Azure Database for MariaDB

Depois de comprar uma capacidade reservada do Azure Database for MariaDB, o desconto de reserva é automaticamente aplicado aos servidores MariaDB que correspondam aos atributos e à quantidade da reserva. Uma reserva abrange apenas os custos de computação do Azure Database for MariaDB. É-lhe cobrada uma taxa normal de armazenamento e rede.

## <a name="how-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

Um desconto de reserva funciona numa base * **use-it-or-lose-it** (utilizar ou perder). Portanto, se não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.

Quando encerra um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão perdidas.

## <a name="discount-applied-to-azure-database-for-mariadb"></a>Desconto aplicado ao Azure Database for MariaDB

O desconto da capacidade reservada do Azure Database for MariaDB é aplicado à execução dos servidores MariaDB por hora. É feita a correspondência entre a reserva que comprar e a utilização de computação emitida pela execução dos servidores do Azure Database for MariaDB. No caso dos servidores MariaDB que não são executados durante uma hora completa, a reserva é automaticamente aplicada a outro Azure Database for MariaDB que corresponda aos atributos da reserva. O desconto pode ser aplicado a servidores do Azure Database for MariaDB que estejam a ser executados em simultâneo. Se não tiver um servidor MariaDB que seja executado durante toda a hora que corresponda aos atributos da reserva, não receberá o benefício completo do desconto de reserva para essa hora.

Os exemplos seguintes mostram como é aplicado o desconto da capacidade reservada do Azure Database for MariaDB em função do número de núcleos que comprou e quando os mesmos estão em execução.

_ **Exemplo 1** : compra uma capacidade reservada do Azure Database for MariaDB para 8 vCores. Se estiver a executar um servidor do Azure Database for MariaDB de 16 vCores que corresponda ao resto dos atributos da reserva, ser-lhe-á cobrado o preço pay as you go para 8 vCores da utilização de computação do servidor MariaDB e receberá o desconto de reserva para uma hora de utilização de computação do servidor MariaDB de 8 vCores.

Para o resto destes exemplos, vamos assumir que a capacidade reservada do Azure Database for MariaDB que comprou é para um Azure Database for MariaDB de 16 vCores e que o resto dos atributos da reserva correspondem aos servidores MariaDB em execução.

* **Exemplo 2** : Executa dois servidores do Azure Database for MariaDB com 8 vCores, cada um durante uma hora. O desconto de reserva de 16 vCores é aplicado à utilização de computação de ambos os servidores do Azure Database for MariaDB de 8 vCores.

* **Exemplo 3** : executa um servidor do Azure Database for MariaDB de 16 vCores das 13:00 às 13:30. Executa outro servidor do Azure Database for MariaDB de 16 vCores das 13:30 às 14:00. Ambas são abrangidas pelo desconto de reserva.

* **Exemplo 4** : executa um servidor do Azure Database for MariaDB de 16 vCores das 13:00 às 13:45. Executa outro servidor do Azure Database for MariaDB de 16 vCores das 13:30 às 14:00. É-lhe cobrado o preço pay-as-you-go pela sobreposição de 15 minutos. O desconto de reserva aplica-se à utilização de computação durante o resto do tempo.

Para compreender e ver a aplicação das Reservas do Azure em relatórios de utilização da faturação, veja [Compreender a utilização das reservas do Azure](./understand-reserved-instance-usage-ea.md).

## <a name="next-steps"></a>Passos seguintes

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).