---
title: O que é Apache Spark
description: Este artigo fornece uma introdução ao Apache Spark in Azure Synapse Analytics e os diferentes cenários em que você pode usar Spark.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b31fe5daaa0882dc0927c1340902b20df56eb6b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96450445"
---
# <a name="apache-spark-in-azure-synapse-analytics"></a>Apache Spark no Azure Synapse Analytics

Apache Spark é uma estrutura de processamento paralela que suporta o processamento na memória para aumentar o desempenho de aplicações analíticas de big data. Apache Spark in Azure Synapse Analytics é uma das implementações da Microsoft de Apache Spark na nuvem. A Azure Synapse facilita a criação e configuração de uma piscina Apache Spark sem servidor em Azure. As piscinas de faíscas em Azure Synapse são compatíveis com o armazenamento Azure e o armazenamento Azure Data Lake Generation 2. Assim, pode utilizar piscinas Spark para processar os seus dados armazenados em Azure.

![Spark: uma arquitetura unificada](./media/apache-spark-overview/spark-overview.png)

## <a name="what-is-apache-spark"></a>O que é Apache Spark

Apache Spark fornece primitivos para a computação de cluster in-memory. Um trabalho do Spark pode carregar e colocar em cache dados na memória e consultá-los repetidamente. A computação dentro da memória é muito mais rápida do que as aplicações baseadas em disco. Spark também se integra com várias linguagens de programação para permitir manipular conjuntos de dados distribuídos como coleções locais. Não é necessário estruturar tudo como operações de mapa e redução.

![MapReduce tradicional vs. Faísca](./media/apache-spark-overview/map-reduce-vs-spark.png)

As piscinas spark em Azure Synapse oferecem um serviço Spark totalmente gerido. Os benefícios de criar uma piscina spark em Azure Synapse Analytics estão listados aqui.

| Funcionalidade | Descrição |
| --- | --- |
| Rapidez e eficiência |As ocorrências de faíscas começam em aproximadamente 2 minutos por menos de 60 nós e aproximadamente 5 minutos para mais de 60 nós. O caso encerra, por defeito, 5 minutos após o último trabalho executado, a menos que seja mantido vivo por uma ligação de caderno. |
| Facilidade de criação |Pode criar uma nova piscina Spark em Azure Synapse em minutos utilizando o portal Azure PowerShell ou o Synapse Analytics .NET SDK. Ver [Começar com piscinas Spark em Azure Synapse Analytics](../quickstart-create-apache-spark-pool-studio.md). |
| Facilidade de utilização |A Synapse Analytics inclui um caderno personalizado derivado do [Nteract.](https://nteract.io/) Pode utilizar estes blocos de notas para o processamento e a visualização de dados interativos.|
| APIs REST |Spark in Azure Synapse Analytics inclui [Apache Livy,](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)um servidor de trabalho spark baseado em REST para submeter e monitorizar remotamente empregos. |
| Suporte para Azure Data Lake Storage Generation 2| As piscinas spark em Azure Synapse podem usar Azure Data Lake Storage Generation 2, bem como armazenamento BLOB. Para obter mais informações sobre o armazenamento do Data Lake, consulte [a visão geral do armazenamento do Lago de Dados Azure.](../../data-lake-store/data-lake-store-overview.md) |
| Integração com IDEs de terceiros | O Azure Synapse fornece um plugin IDE para [o IntelliJ IDEA do JetBrains](https://www.jetbrains.com/idea/) que é útil para criar e submeter candidaturas a uma piscina spark. |
| Bibliotecas Anaconda pré-carregadas |As piscinas de faíscas em Azure Synapse vêm com bibliotecas Anaconda pré-instaladas. O [Anaconda](https://docs.continuum.io/anaconda/) fornece cerca de 200 bibliotecas de Machine Learning, análise de dados, visualização, etc. |
| Escalabilidade | As piscinas Apache Spark in Azure Synapse podem ter a Escala Automática ativada, de modo que as piscinas escalam adicionando ou removendo os nós conforme necessário. Além disso, os conjuntos do Spark podem ser encerrados sem perda de dados, uma vez que todos os dados estão armazenados no Armazenamento do Azure ou no Data Lake Storage. |

As piscinas de faíscas em Azure Synapse incluem os seguintes componentes que estão disponíveis nas piscinas por padrão.

- [Spark Core](https://spark.apache.org/docs/2.4.5/). Inclui o Apache Spark Core, o Spark SQL, o GraphX e o MLlib.
- [Anaconda](https://docs.continuum.io/anaconda/)
- [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Caderno Nteract](https://nteract.io/)

## <a name="spark-pool-architecture"></a>Arquitetura da piscina de faíscas

É fácil entender os componentes da Spark compreendendo como a Spark funciona no Azure Synapse Analytics.

As aplicações spark funcionam como conjuntos independentes de processos numa piscina, coordenados pelo objeto SparkContext no seu programa principal (chamado programa de condutor).

O SparkContext pode ligar-se ao gestor do cluster, que aloca recursos através de aplicações. O gestor de clusters é [Apache Hadoop YARN.](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Uma vez conectado, a Spark adquire executores em nós na piscina, que são processos que executam computações e armazenam dados para a sua aplicação. Em seguida, envia o código da aplicação (definido pelos ficheiros JAR ou Python transmitidos para o SparkContext) aos executores. Por fim, o SparkContext envia tarefas para os executores executarem.

O SparkContext executa a função principal do utilizador e executa as várias operações paralelas nos nós. Em seguida, recolhe os resultados das operações. Os nós lêem e escrevem dados de e para o sistema de ficheiros. Os nóns também cache transformaram dados na memória como Conjuntos de Dados Distribuídos Resilientes (RDDs).

O SparkContext liga-se à piscina Spark e é responsável pela conversão de uma aplicação num gráfico acíclico direcionado (DAG). O gráfico consiste em tarefas individuais que são executadas dentro de um processo de executor nos nós. Cada aplicação tem os seus próprios processos de executor, que permanecem em funcionamento ao longo da duração de toda a aplicação e executam tarefas em múltiplos threads.

## <a name="apache-spark-in-azure-synapse-analytics-use-cases"></a>Apache Spark in Azure Synapse Analytics usa casos

As piscinas de faíscas em Azure Synapse Analytics permitem os seguintes cenários-chave:

### <a name="data-engineeringdata-preparation"></a>Engenharia de Dados/Preparação de Dados

O Apache Spark inclui muitas funcionalidades linguísticas para apoiar a preparação e o processamento de grandes volumes de dados para que possa ser tornado mais valioso e depois consumido por outros serviços dentro do Azure Synapse Analytics. Isto é ativado através de vários idiomas (C#, Scala, PySpark, Spark SQL) e forneceu bibliotecas para processamento e conectividade.

### <a name="machine-learning"></a>Machine Learning

Apache Spark vem com [MLlib,](https://spark.apache.org/mllib/)uma biblioteca de machine learning construída em cima de Spark que você pode usar a partir de uma piscina de faíscas em Azure Synapse Analytics. As piscinas de faíscas em Azure Synapse Analytics também incluem Anaconda, uma distribuição Python com uma variedade de pacotes para a ciência de dados, incluindo machine learning. Quando combinado com o suporte incorporado para blocos de notas, tem um ambiente para criar aplicações de machine learning.

## <a name="where-do-i-start"></a>Por onde começo

Use os seguintes artigos para saber mais sobre Apache Spark em Azure Synapse Analytics:

- [Quickstart: Crie uma piscina de faíscas em Azure Synapse](../quickstart-create-apache-spark-pool-portal.md)
- [Quickstart: Criar um caderno Apache Spark](../quickstart-apache-spark-notebook.md)
- [Tutorial: Aprendizagem automática usando Apache Spark](./apache-spark-machine-learning-mllib-notebook.md)
- [Documentação oficial da Faísca Apache Spark](https://spark.apache.org/docs/2.4.5/)

> [!NOTE]
> Parte da documentação oficial do Apache Spark baseia-se na utilização da consola de faíscas, esta não está disponível no Azure Synapse Spark, usa o caderno ou experiências IntelliJ em vez disso

## <a name="next-steps"></a>Passos seguintes

Nesta visão geral, você obtém uma compreensão básica de Apache Spark em Azure Synapse Analytics. Avance para o próximo artigo para aprender a criar uma piscina spark em Azure Synapse Analytics:

- [Criar uma piscina de faíscas em Azure Synapse](../quickstart-create-apache-spark-pool-portal.md)
