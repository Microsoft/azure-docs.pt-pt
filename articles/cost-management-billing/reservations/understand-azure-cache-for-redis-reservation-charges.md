---
title: Compreender como se aplica o desconto de reserva à Cache do Azure para Redis | Microsoft Docs
description: Saiba como o desconto de reserva é aplicado às instâncias da Cache do Azure para Redis.
author: yegu-ms
manager: maiye
ms.service: cache
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: yegu
ms.openlocfilehash: 7b57bfbd09cd2b528b5a879acdb41b4716289aa6
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372009"
---
# <a name="how-the-reservation-discount-is-applied-to-azure-cache-for-redis"></a>Como é aplicado o desconto de reserva à Cache do Azure para Redis

Depois de comprar capacidade reservada da Cache do Azure para Redis, o desconto de reserva é aplicado automaticamente às instâncias de caches que correspondam aos atributos e à quantidade da reserva. As reservas só abrangem os custos de computação da Cache do Azure para Redis. É-lhe cobrada uma taxa normal de armazenamento e rede. A capacidade reservada só está disponível para caches de [escalão premium](/azure/azure-cache-for-redis/quickstart-create-redis).

## <a name="how-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

Um desconto de reserva funciona numa base * **use-it-or-lose-it** (utilizar ou perder). Portanto, se não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.

Quando encerra um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão perdidas.

## <a name="discount-applied-to-azure-cache-for-redis"></a>Desconto aplicado à Cache do Azure para Redis

O desconto de reserva da Cache do Azure para Redis é aplicado às caches de hora em hora. A reserva que comprar é correspondida à utilização de computação emitida pelas caches em execução. No caso das caches que não são executadas durante horas completas, a reserva é automaticamente aplicada às outras caches que correspondam aos atributos da reserva. O desconto pode ser aplicado a caches que estejam a ser executadas ao mesmo tempo. Se não tiver caches que sejam executadas durante toda a hora que correspondam aos atributos da reserva, não irá tirar partido completo das vantagens do desconto de reserva para essa hora.

Os exemplos seguintes mostram como o desconto da capacidade reservada da Cache do Azure para Redis se aplica em função do número de caches que comprou de e quando as mesmas estão em execução.

_ **Exemplo 1** : Compra capacidade reservada da Cache do Azure para Redis para uma cache de 6 GB. Se estiver a executar uma cache de 13 GB que corresponda aos restantes atributos da reserva, é-lhe cobrado o preço pay as you go por 7 GB da utilização de computação da Cache do Azure para Redis e obtém o desconto de reserva para uma hora da utilização de computação de cache de 6 GB.

Nos outros exemplos, pressuponha que a capacidade reservada da Cache do Azure para Redis que comprou é para uma cache de 26 GB e que o resto dos atributos de reserva correspondem à cache em execução.

* **Exemplo 2** : Executa duas caches de 13 GB durante uma hora. O desconto de reserva de 26 GB é aplicado à utilização de computação de ambas as caches.

* **Exemplo 3** : Executa apenas uma cache de 26 GB das 13:00 às 13:30. Executa outra cache de 26 GB das 13:30 às 14:00. Ambas são abrangidas pelo desconto de reserva.

* **Exemplo 4** : Executa apenas uma cache de 26 GB das 13:00 às 13:45. Executa outra cache de 26 GB das 13:30 às 14:00. É-lhe cobrado o preço pay-as-you-go pela sobreposição de 15 minutos. O desconto de reserva aplica-se à utilização de computação durante o resto do tempo.

Para compreender e ver a aplicação das Reservas do Azure em relatórios de utilização da faturação, veja [Compreender a utilização das reservas do Azure](./understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos
Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).