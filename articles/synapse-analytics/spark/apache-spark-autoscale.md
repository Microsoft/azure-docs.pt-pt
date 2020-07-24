---
title: Escala automática casos de Faísca Apache
description: Utilize a função Azure Synapse Autoscale para escalar automaticamente as instâncias de faíscas apaches
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 03/31/2020
ms.openlocfilehash: 7c6278d24e52159424c3e346b2382c93f4ab8160
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075798"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>Escala automática Piscinas Apache Spark Azure Synapse

A funcionalidade De Escala Automática da piscina Apache Spark for Azure Synapse Analytics escala automaticamente o número de nós num caso de cluster para cima e para baixo. Durante a criação de uma nova piscina Apache Spark para Azure Synapse Analytics, um número mínimo e máximo de nós pode ser definido quando a Autoscale é selecionada. Em seguida, a autoescala os requisitos de recursos da carga e dimensiona o número de nós para cima ou para baixo. Não há nenhum custo adicional para esta funcionalidade.

## <a name="metrics-monitoring"></a>Monitorização de métricas

A autoescala continuamente a instância Spark e recolhe as seguintes métricas:

|Métrica|Descrição|
|---|---|
|CPU total pendente|O número total de núcleos necessários para iniciar a execução de todos os nós pendentes.|
|Memória Total Pendente|A memória total (em MB) necessária para iniciar a execução de todos os nós pendentes.|
|CpU total gratuito|A soma de todos os núcleos não reutilizados nos nós ativos.|
|Memória Total Livre|A soma da memória nãousada (em MB) nos nós ativos.|
|Memória usada por nó|A carga num nó. Um nó no qual são utilizados 10 GB de memória, é considerado sob mais carga do que um trabalhador com 2 GB de memória usada.|

As métricas acima são verificadas a cada 30 segundos. A autoescala toma decisões de escala e de escala com base nestas métricas.

## <a name="load-based-scale-conditions"></a>Condições de escala à base de carga

Quando forem detetadas as seguintes condições, a Autoscale emitirá um pedido de escala:

|Aumento vertical|Escala para baixo|
|---|---|
|O CPU total pendente é superior ao CPU total gratuito por mais de 1 minuto.|O CPU total pendente é inferior ao total de CPU gratuito por mais de 2 minutos.|
|A memória total pendente é maior do que a memória total gratuita por mais de 1 minuto.|A memória total pendente é inferior à memória total gratuita por mais de 2 minutos.|

Para a escala, o serviço Azure Synapse Autoscale calcula quantos novos nós são necessários para satisfazer os requisitos atuais de CPU e memória, e em seguida, emite um pedido de escala para adicionar o número necessário de nós.

Para uma escala para baixo, com base no número de executores, mestres de aplicações por nó e os atuais requisitos de CPU e memória, a Autoscale emite um pedido para remover um determinado número de nós. O serviço também deteta quais os nós que são candidatos à remoção com base na execução de empregos em curso. A operação de escala para baixo primeiro desativa os nós e, em seguida, remove-os do cluster.

## <a name="get-started"></a>Introdução

### <a name="create-a-spark-pool-with-autoscaling"></a>Criar uma piscina spark com autoscaling

Para ativar a funcionalidade Autoscale, complete os seguintes passos como parte do processo normal de criação de piscinas:

1. No **separador Basics,** selecione a caixa **de verificação de escala automática Enable.**
1. Introduza os valores pretendidos para as seguintes propriedades:  

    * **Número** min de nós.
    * **Número máximo** de nós.

O número inicial de nós será o mínimo. Este valor define o tamanho inicial da instância quando é criado. O número mínimo de nós não pode ser inferior a três.

## <a name="best-practices"></a>Melhores práticas

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Considere a latência de operações de escala para cima ou para baixo

Pode levar 1 a 5 minutos para uma operação de escalonamento ser concluída.

### <a name="preparation-for-scaling-down"></a>Preparação para escalonamento

Durante o processo de escalonamento, a Autoscale colocará os nós em estado de desmantelamento para que nenhum novo executor possa lançar nesse nó.

Os trabalhos de corrida continuarão a funcionar e a terminar. Os postos de trabalho pendentes aguardarão para ser agendados normalmente, com menos nós disponíveis.

## <a name="next-steps"></a>Passos seguintes

Quickstart para criar uma nova piscina Spark [Criar uma piscina spark](../quickstart-create-apache-spark-pool-portal.md)
