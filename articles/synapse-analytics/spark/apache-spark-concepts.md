---
title: Apache Spark in Azure Synapse Analytics - Core Concepts
description: Este artigo fornece uma introdução à Apache Spark no Azure Synapse Analytics e aos diferentes conceitos.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 6276d198e547efec3d2e3cb88816da5e2b593aae
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83644689"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark em Conceitos Centrais de Analytics Azure Synapse

Apache Spark é um quadro de processamento paralelo que suporta o processamento na memória para aumentar o desempenho de aplicações analíticas de big data. Apache Spark in Azure Synapse Analytics é uma das implementações da Microsoft de Apache Spark na nuvem. 

O Azure Synapse facilita a criação e configuração das capacidades de Spark em Azure. A Azure Synapse fornece uma implementação diferente destas capacidades spark que estão documentadas aqui.

## <a name="spark-pools-preview"></a>Piscinas de faíscas (pré-visualização)

Uma piscina Spark (pré-visualização) é criada no portal Azure. É a definição de um conjunto de Faíscas que, quando instantâneo, é usado para criar uma instância de Faísca que processa dados. Quando uma piscina spark é criada, existe apenas como metadados; nenhum recursos é consumido, correndo ou cobrado. Uma piscina spark tem uma série de propriedades que controlam as características de um caso Spark; estas características incluem, mas não se limitam ao nome, tamanho, comportamento de escala, tempo para viver.

Como não há um dólar ou custo de recursos associado à criação de piscinas Spark, qualquer número pode ser criado com qualquer número de configurações diferentes. As permissões também podem ser aplicadas a piscinas Spark permitindo que os utilizadores apenas tenham acesso a alguns e não a outros.

A melhor prática é criar piscinas de faíscas mais pequenas que possam ser usadas para desenvolvimento e depuração e, em seguida, maiores para executar cargas de trabalho de produção.

Você pode ler como criar uma piscina de faíscas e ver todas as suas propriedades aqui [Começar com piscinas Spark em Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md)

## <a name="spark-instances"></a>Casos de faíscas

As instâncias de faísca são criadas quando se conecta a uma piscina spark, cria uma sessão e executa um trabalho. Como vários utilizadores podem ter acesso a uma única piscina Spark, é criada uma nova instância Spark para cada utilizador que se conecta. 

Quando apresentar um segundo emprego, então se houver capacidade na piscina, a instância spark existente também tem capacidade, então a instância existente irá processar o trabalho; se não e houver capacidade ao nível da piscina, então será criada uma nova instância spark.

## <a name="examples"></a>Exemplos

### <a name="example-1"></a>Exemplo 1

- Você cria uma piscina de faíscas chamada SP1; tem um cluster fixo de 20 nós.
- Você submete um trabalho de caderno, J1 que usa 10 nós, uma instância spark, SI1 é criado para processar o trabalho.
- Agora submete-se a outro trabalho, J2, que usa 10 nós porque ainda há capacidade na piscina e a instância, o J2, é processada pelo SI1.
- Se j2 tivesse pedido 11 nós, não teria havido capacidade em SP1 ou SI1. Neste caso, se J2 vier de um caderno, então o trabalho será rejeitado; Se J2 vier de um lote, então será feito na fila.

### <a name="example-2"></a>Exemplo 2

- Você cria uma piscina de faísca sP2; tem uma escala automática habilitada de 10 a 20 nódosos
- Você submete um trabalho de caderno, J1 que usa 10 nós, uma instância spark, SI1, é criada para processar o trabalho.
- Agora submete-se a outro trabalho, J2, que usa 10 nós, porque ainda há capacidade na piscina a instância auto cresce para 20 nós e processa J2.

### <a name="example-3"></a>Exemplo 3

- Você cria uma piscina de faíscas chamada SP1; tem um cluster fixo de 20 nós.
- Você submete um trabalho de caderno, J1 que usa 10 nós, uma instância spark, SI1 é criado para processar o trabalho.
- Outro utilizador, u2, submete um Job, J3, que usa 10 nós, uma nova instância spark, SI2, é criada para processar o trabalho.
- Agora submete-se a outro trabalho, J2, que usa 10 nós porque ainda há capacidade na piscina e a instância, J2, é processada pela SI1.

## <a name="next-steps"></a>Próximos passos

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentação de Faísca apache](https://spark.apache.org/docs/2.4.4/)
