---
title: Apache Spark conceitos centrais
description: Este artigo fornece uma introdução ao Apache Spark in Azure Synapse Analytics e aos diferentes conceitos.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 806f4dff49e9650dba073721109e7d54a18ecbbe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87052338"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark in Azure Synapse Analytics Core Concepts

Apache Spark é uma estrutura de processamento paralela que suporta o processamento na memória para aumentar o desempenho de aplicações analíticas de big data. Apache Spark in Azure Synapse Analytics é uma das implementações da Microsoft de Apache Spark na nuvem. 

A Azure Synapse facilita a criação e configuração das capacidades de faísca em Azure. O Azure Synapse fornece uma implementação diferente destas capacidades de Faísca que são documentadas aqui.

## <a name="spark-pools-preview"></a>Piscinas de faíscas (pré-visualização)

Uma piscina Spark (pré-visualização) é criada no portal Azure. É a definição de uma piscina spark que, quando instantânea, é usada para criar uma instância Spark que processa dados. Quando uma piscina Spark é criada, existe apenas como metadados; nenhum recursos é consumido, correndo ou cobrado. Uma piscina spark tem uma série de propriedades que controlam as características de uma instância Spark; estas características incluem, mas não se limitam ao nome, tamanho, comportamento de escala, tempo para viver.

Como não há um dólar ou custo de recurso associado à criação de pools Spark, qualquer número pode ser criado com qualquer número de configurações diferentes. As permissões também podem ser aplicadas a piscinas Spark, permitindo que os utilizadores tenham apenas acesso a alguns e não a outros.

Uma das melhores práticas é criar piscinas de faíscas mais pequenas que possam ser usadas para o desenvolvimento e depurações e, em seguida, maiores para executar cargas de trabalho de produção.

Você pode ler como criar uma piscina spark e ver todas as suas propriedades aqui [Começar com piscinas Spark em Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md)

## <a name="spark-instances"></a>Ocorrências de faíscas

As ocorrências de faíscas são criadas quando se liga a uma piscina spark, cria uma sessão e gere um emprego. Como vários utilizadores podem ter acesso a uma única piscina Spark, é criado um novo exemplo de Spark para cada utilizador que se conecta. 

Quando se submete um segundo emprego, então se houver capacidade na piscina, a instância spark existente também tem capacidade, então a instância existente irá processar o trabalho; se não e houver capacidade ao nível da piscina, então será criado um novo exemplo de Faísca.

## <a name="examples"></a>Exemplos

### <a name="example-1"></a>Exemplo 1

- Você cria uma piscina spark chamada SP1; tem um tamanho fixo de cluster de 20 nós.
- Você submete um trabalho de caderno, J1 que usa 10 nós, uma instância Spark, SI1 é criado para processar o trabalho.
- Agora submete outro trabalho, J2, que usa 10 nós porque ainda há capacidade na piscina e o caso, o J2, é processado pela SI1.
- Se j2 tivesse pedido 11 nós, não teria havido capacidade em SP1 ou SI1. Neste caso, se J2 vier de um caderno, então o trabalho será rejeitado; se J2 vier de um lote, então será feito em fila.

### <a name="example-2"></a>Exemplo 2

- Você cria uma piscina spark chamada SP2; tem uma escala automática ativada de 10 a 20 nosdes
- Você submete um trabalho de caderno, J1 que usa 10 nós, uma instância Spark, SI1, é criado para processar o trabalho.
- Agora submete outro trabalho, J2, que usa 10 nós, porque ainda há capacidade na piscina, o caso automóvel cresce para 20 nós e processos J2.

### <a name="example-3"></a>Exemplo 3

- Você cria uma piscina spark chamada SP1; tem um tamanho fixo de cluster de 20 nós.
- Você submete um trabalho de caderno, J1 que usa 10 nós, uma instância Spark, SI1 é criado para processar o trabalho.
- Outro utilizador, u2, submete um Job, J3, que usa 10 nós, uma nova instância Spark, SI2, é criada para processar o trabalho.
- Agora submete outro trabalho, J2, que usa 10 nós porque ainda há capacidade na piscina e a instância, J2, é processada pela SI1.

## <a name="next-steps"></a>Passos seguintes

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentação de faíscas apache](https://spark.apache.org/docs/2.4.4/)
