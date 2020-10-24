---
title: Apache Spark conceitos centrais
description: Introdução ao Apache Spark em Azure Synapse Analytics e os diferentes conceitos.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: bb5c7e082dc4a35183190f5d2d6a4b305b907f4f
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92480484"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark in Azure Synapse Analytics Core Concepts

Apache Spark é uma estrutura de processamento paralela que suporta o processamento na memória para aumentar o desempenho de aplicações analíticas de big data. Apache Spark in Azure Synapse Analytics é uma das implementações da Microsoft de Apache Spark na nuvem. 

A Azure Synapse facilita a criação e configuração das capacidades de faísca em Azure. O Azure Synapse fornece uma implementação diferente destas capacidades de Faísca que são documentadas aqui.

## <a name="spark-pools-preview"></a>Piscinas de faíscas (pré-visualização)

Uma piscina Spark (pré-visualização) é criada no portal Azure. É a definição de uma piscina de faíscas que, quando instantânea, é usada para criar uma instância Spark que processa dados. Quando uma piscina Spark é criada, existe apenas como metadados, e nenhum recursos são consumidos, em execução ou cobrados. Uma piscina spark tem uma série de propriedades que controlam as características de uma ocorrência de faísca. Estas características incluem, mas não se limitam ao nome, tamanho, comportamento de escala, tempo de vida.

Como não há um dólar ou custo de recursos associado à criação de piscinas Spark, qualquer número pode ser criado com qualquer número de configurações diferentes. As permissões também podem ser aplicadas a piscinas Spark, permitindo que os utilizadores tenham apenas acesso a alguns e não a outros.

Uma das melhores práticas é criar piscinas de faíscas mais pequenas que possam ser usadas para o desenvolvimento e depurações e, em seguida, maiores para executar cargas de trabalho de produção.

Você pode ler como criar uma piscina spark e ver todas as suas propriedades aqui [Começar com piscinas Spark em Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md)

## <a name="spark-instances"></a>Ocorrências de faíscas

As ocorrências de faíscas são criadas quando se liga a uma piscina spark, cria uma sessão e gere um emprego. Como vários utilizadores podem ter acesso a uma única piscina Spark, é criado um novo exemplo de Spark para cada utilizador que se conecta. 

Quando se submete um segundo emprego, se houver capacidade na piscina, a instância faísca existente também tem capacidade. Então, a instância existente irá processar o trabalho. Caso contrário, se a capacidade estiver disponível ao nível da piscina, será criada uma nova instância Spark.

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
- Agora submete outro trabalho, J2, que usa 10 nós porque ainda há capacidade na piscina e o caso, J2, é processado pela SI1.

## <a name="quotas-and-resource-constraints-in-apache-spark-for-azure-synapse"></a>Quotas e restrições de recursos em Apache Spark para Azure Synapse

### <a name="workspace-level"></a>Nível de espaço de trabalho

Cada espaço de trabalho Azure Synapse vem com uma quota padrão de vCores que pode ser usado para Spark. A quota é dividida entre a quota de utilizador e a quota de fluxo de dados de modo que nenhum padrão de utilização utilize todos os vCores no espaço de trabalho. A quota é diferente dependendo do tipo da sua subscrição, mas é simétrica entre o utilizador e o fluxo de dados. No entanto, se solicitar mais vCores do que os restantes no espaço de trabalho, terá o seguinte erro:

```console
Failed to start session: [User] MAXIMUM_WORKSPACE_CAPACITY_EXCEEDED
Your Spark job requested 480 vcores.
However, the workspace only has xxx vcores available out of quota of yyy vcores.
Try reducing the numbers of vcores requested or increasing your vcore quota. Click here for more information - https://go.microsoft.com/fwlink/?linkid=213499
```

O link na mensagem aponta para este artigo.

O artigo que se segue descreve como solicitar um aumento da quota vCore do espaço de trabalho.

- Selecione "Azure Synapse Analytics" como o tipo de serviço.
- Na janela de detalhes da Quota, selecione Apache Spark (vCore) por espaço de trabalho

[Solicite um aumento de capacidade através do portal Azure](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests#request-a-standard-quota-increase-from-help--support)

### <a name="spark-pool-level"></a>Nível de piscina de faísca

Quando define uma piscina Spark está efetivamente a definir uma quota por utilizador para essa piscina, se executar vários cadernos ou empregos ou uma mistura do 2 é possível esgotar a quota da piscina. Se o fizer, então uma mensagem de erro como a seguinte será gerada

```console
Failed to start session: Your Spark job requested xx vcores.
However, the pool is consuming yy vcores out of available zz vcores.Try ending the running job(s) in the pool, reducing the numbers of vcores requested, increasing the pool maximum size or using another pool
```

Para resolver este problema, tem de reduzir o seu uso dos recursos da piscina antes de submeter um novo pedido de recursos executando um caderno ou um emprego.

## <a name="next-steps"></a>Passos seguintes

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentação de faíscas apache](https://spark.apache.org/docs/2.4.5/)
