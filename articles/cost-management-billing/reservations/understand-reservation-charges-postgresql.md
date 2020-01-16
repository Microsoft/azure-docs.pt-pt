---
title: Como um desconto de reserva é aplicado ao Servidor único da Base de Dados do Azure para PostgreSQL
description: Como um desconto de reserva é aplicado ao Servidor único da Base de Dados do Azure para PostgreSQL
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.openlocfilehash: 2f1843690861a463c634f523e82facffc72b6743
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995510"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-postgresql-single-server"></a>Como um desconto de reserva é aplicado ao Servidor único da Base de Dados do Azure para PostgreSQL

Depois de comprar uma capacidade reservada de Servidor único da Base de Dados do Azure para PostgreSQL, o desconto de reserva é automaticamente aplicado aos Servidores únicos da Base de Dados do Azure para PostgreSQL que correspondem aos atributos e à quantidade da reserva. Uma reserva abrange apenas os custos de computação do Servidor único da Base de Dados do Azure para PostgreSQL. É-lhe cobrada uma taxa normal de armazenamento e rede. 

## <a name="how-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

Um desconto de reserva funciona numa base ***use-it-or-lose-it*** (utilizar ou perder). Portanto, se não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.</br>

Quando encerra um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão perdidas.

## <a name="discount-applied-to-azure-database-for-postgresql-single-server"></a>Desconto aplicado ao Servidor único da Base de Dados do Azure para PostgreSQL

O desconto de capacidade reservada do Servidor único da Base de Dados do Azure para PostgreSQL é aplicado à execução do Servidor único PostgreSQL e cobrado à hora. É feita a correspondência entre a reserva que comprar e a utilização de computação emitida pela execução do Servidor único da Base de Dados do Azure para PostgreSQL. No caso dos Servidores únicos PostgreSQL que não são executados durante uma hora completa, a reserva é automaticamente aplicada a outro Servidor único da Base de Dados do Azure para PostgreSQL que corresponda aos atributos da reserva. O desconto pode ser aplicado aos Servidores únicos da Base de Dados do Azure para PostgreSQL que estejam a ser executados em simultâneo. Se não tiver um Servidor único PostgreSQL que seja executado durante toda a hora que corresponda aos atributos da reserva, não receberá o benefício completo do desconto de reserva para essa hora.

Os exemplos seguintes mostram como o desconto da capacidade reservada do Servidor único da Base de Dados do Azure para PostgreSQL se aplica em função do número de núcleos que comprou e quando os mesmos estão em execução.

* **Exemplo 1**: você compra um banco de dados do Azure para PostgreSQL capacidade reservada de servidor único para 8 vCore. Se estiver a executar um Servidor único da Base de Dados do Azure para PostgreSQL de 16 vCores que corresponda ao resto dos atributos da reserva, ser-lhe-á cobrado o preço pay as you go para 8 vCores da utilização de computação do Servidor único PostgreSQL e receberá o desconto de reserva para uma hora de utilização de computação do Servidor único PostgreSQL de 8 vCores.</br>

Para o resto destes exemplos, assuma que a capacidade reservada do Servidor único da Base de Dados do Azure para PostgreSQL que comprou é para um Servidor único da Base de Dados do Azure para PostgreSQL de 16 vCores e que o resto dos atributos da reserva correspondem aos Servidores únicos PostgreSQL em execução.

* **Exemplo 2**: você executa dois bancos de dados do Azure para PostgreSQL servidores únicos com 8 vCore cada por hora. O desconto de reserva de 16 vCores é aplicado à utilização de computação de ambos os Servidores únicos da Base de Dados do Azure para PostgreSQL de 8 vCores.

* **Exemplo 3**: você executa o 1 16 VCORE do banco de dados do Azure para PostgreSQL servidor único de 1 pm a 1:30 PM. Executa o outro Servidor único da Base de Dados do Azure para PostgreSQL de 16 vCores das 13:30 às 14:00. Ambas são abrangidas pelo desconto de reserva.

* **Exemplo 4**: você executa o 1 16 VCORE do banco de dados do Azure para PostgreSQL servidor único de 1 pm a 1:45 PM. Executa o outro Servidor único da Base de Dados do Azure para PostgreSQL de 16 vCores das 13:30 às 14:00. É-lhe cobrado o preço pay-as-you-go pela sobreposição de 15 minutos. O desconto de reserva aplica-se à utilização de computação durante o resto do tempo.

Para compreender e ver a aplicação das Reservas do Azure em relatórios de utilização da faturação, veja [Compreender a utilização das reservas do Azure](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos
Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
