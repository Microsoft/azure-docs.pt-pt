---
title: Escala automaticamente os casos de Faísca Synapse Azure
description: Utilize a função De Escala Automática Azure Synapse para escalar automaticamente as instâncias de faísca synapse
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: be7ac79b9429d2fc72c3b6bc2b6d92666b089dfb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591969"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>Escala automaticamente piscinas de Faíscas Apache Synapse Anapse Anapse

A funcionalidade De Escala Automática da Piscina De Synapse Azure Synapse aescala automaticamente o número de nós numa instância de cluster para cima e para baixo. Durante a criação de uma nova Apache Spark para a piscina Azure Synapse Analytics, um número mínimo e máximo de nós pode ser definido quando a Escala Automática é selecionada. A escala automática monitoriza então os requisitos de recursos da carga e escala o número de nós para cima ou para baixo. Não há nenhuma taxa adicional para esta funcionalidade.

## <a name="metrics-monitoring"></a>Monitorização de métricas

A escala automática monitoriza continuamente a instância Spark e recolhe as seguintes métricas:

|Metric|Descrição|
|---|---|
|CpU total pendente|O número total de núcleos necessários para iniciar a execução de todos os nós pendentes.|
|Total de Memória Pendente|A memória total (em MB) necessária para iniciar a execução de todos os nós pendentes.|
|CpU total grátis|A soma de todos os núcleos não utilizados nos nós ativos.|
|Memória Total Livre|A soma da memória não utilizada (em MB) nos nós ativos.|
|Memória Usada por Nó|A carga num nó. Um nó no qual é utilizado 10 GB de memória, é considerado sob mais carga do que um trabalhador com 2 GB de memória usada.|

As métricas acima são verificadas a cada 30 segundos. A escala automática toma decisões de escala e escala com base nestas métricas.

## <a name="load-based-scale-conditions"></a>Condições de escala baseadas em carga

Quando forem detetadas as seguintes condições, a Escala Automática emitirá um pedido de escala:

|Aumento vertical|Redução da escala|
|---|---|
|O CPU total pendente é superior ao total de CPU gratuito por mais de 1 minuto.|O CPU total pendente é inferior ao total de CPU gratuito por mais de 2 minutos.|
|A memória total pendente é maior do que a memória total gratuita por mais de 1 minuto.|A memória total pendente é inferior a memória gratuita total por mais de 2 minutos.|

Para a escala, o serviço Azure Synapse Autoscale calcula quantos novos nós são necessários para satisfazer os atuais requisitos de CPU e memória, e emite um pedido de escala para adicionar o número necessário de nós.

Para a redução da escala, com base no número de executores, mestres de aplicação por nó e os atuais requisitos de CPU e memória, a Autoscale emite um pedido para remover um certo número de nós. O serviço também deteta quais os nódosos candidatos à remoção com base na execução atual do emprego. A operação de redução da escala primeiro desativa os nós e, em seguida, retira-os do cluster.

## <a name="get-started"></a>Introdução

### <a name="create-a-spark-pool-with-autoscaling"></a>Crie uma piscina de faíscas com autoscalcificação

Para ativar a função De escala Automática, complete os seguintes passos como parte do processo normal de criação da piscina:

1. No separador **Basics,** selecione a caixa de verificação **enable autoscale.**
1. Introduza os valores desejados para as seguintes propriedades:  

    * **Número min** de nós.
    * **Número máximo** de nós.

O número inicial de nós será o mínimo. Este valor define o tamanho inicial da instância quando é criado. O número mínimo de nós não pode ser inferior a três.

## <a name="best-practices"></a>Melhores práticas

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Considere a latência da escala para cima ou para baixo operações

Pode levar 1 a 5 minutos para que uma operação de escalação esteja concluída.

### <a name="preparation-for-scaling-down"></a>Preparação para a escala

Durante o processo de escala, a Escala Automática colocará os nós em estado de desmantelamento para que nenhum novo executor possa lançar nesse nó.

Os trabalhos de corrida continuarão a funcionar e a terminar. Os postos de trabalho pendentes esperam ser programados normalmente, com menos nós disponíveis.

## <a name="next-steps"></a>Passos seguintes

Quickstart para criar uma nova piscina Spark [Criar uma piscina de faíscas](..\quickstart-create-apache-spark-pool.md)
