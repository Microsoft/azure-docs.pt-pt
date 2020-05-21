---
title: O que é Apache Spark em Azure Synapse Analytics?
description: Este artigo fornece uma introdução à Apache Spark no Azure Synapse Analytics e aos diferentes cenários em que pode utilizar a Spark.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 470cc8cc16c8bdb32bac32a7227690899abb9c51
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83644677"
---
# <a name="what-is-apache-spark-in-azure-synapse-analytics"></a>O que é Apache Spark em Azure Synapse Analytics

Apache Spark é um quadro de processamento paralelo que suporta o processamento na memória para aumentar o desempenho de aplicações analíticas de big data. Apache Spark in Azure Synapse Analytics é uma das implementações da Microsoft de Apache Spark na nuvem. O Azure Synapse facilita a criação e configuração de uma piscina Spark (pré-visualização) em Azure. As piscinas de faíscas em Azure Synapse são compatíveis com o Armazenamento Azure e o Armazenamento Azure Data Lake Generation 2. Para que possa utilizar piscinas Azure Spark para processar os seus dados armazenados em Azure.

![Spark: uma arquitetura unificada](./media/apache-spark-overview/spark-overview.png)

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="what-is-apache-spark"></a>O que é Apache Spark

Apache Spark fornece primitivos para a computação de cluster sin-memory. Os trabalhos do Spark podem carregar e colocar os dados em cache na memória e consultá-los repetidamente. A computação na memória é muito mais rápida do que aplicações baseadas em discos. A Spark também se integra com várias linguagens de programação para permitir manipular conjuntos de dados distribuídos como coleções locais. Não é necessário estruturar tudo como operações de mapa e redução.

![Mapa tradicionalReduzir vs. Faísca](./media/apache-spark-overview/map-reduce-vs-spark.png)

As piscinas de faíscas em Azure Synapse oferecem um serviço Spark totalmente gerido. Os benefícios de criar uma piscina spark em Synapse Analytics estão listados aqui.

| Funcionalidade | Descrição |
| --- | --- |
| Rapidez e eficiência |As ocorrências de faíscas começam em aproximadamente 2 minutos por menos de 60 nós e aproximadamente 5 minutos para mais de 60 nós. A instância encerra, por defeito, 5 minutos após o último trabalho executado, a menos que seja mantida viva por uma ligação de caderno. |
| Facilidade de criação |Pode criar uma nova piscina Spark em Azure Synapse em minutos usando o portal Azure, Azure PowerShell ou o Synapse Analytics .NET SDK. Veja [Começar com piscinas Spark em Synapse Analytics.](../quickstart-create-apache-spark-pool-studio.md) |
| Facilidade de utilização |A Synapse Analytics inclui um caderno personalizado derivado do [Nteract](https://nteract.io/). Pode utilizar estes blocos de notas para o processamento e a visualização de dados interativos.|
| APIs REST |Spark in Synapse Analytics inclui [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), um servidor de trabalho spark baseado em REST API para submeter e monitorizar remotamente trabalhos. |
| Suporte para Azure Data Lake Storage Geração 2| As piscinas de faíscas em Azure Synapse podem usar a Geração de Armazenamento do Lago De dados Azure 2, bem como o armazenamento BLOB. Para mais informações sobre o Armazenamento de Data Lake, consulte [a visão geral do armazenamento do Lago De Dados Azure](../../data-lake-store/data-lake-store-overview.md). |
| Integração com IDEs de terceiros | A Azure Synapse fornece um plugin IDE para o [IntelliJ IDEA da Jetbrains](https://www.jetbrains.com/idea/) que é útil para criar e submeter aplicações a uma piscina spark. |
| Bibliotecas Anaconda pré-carregadas |As piscinas de faíscas em Azure Synapse vêm com bibliotecas Anaconda pré-instaladas. O [Anaconda](https://docs.continuum.io/anaconda/) fornece cerca de 200 bibliotecas de Machine Learning, análise de dados, visualização, etc. |
| Escalabilidade | Apache Spark em piscinas Azure Synapse pode ter auto-escala ativada, de modo que as piscinas escalam para cima e para baixo conforme necessário. Além disso, as piscinas Spark podem ser encerradas sem perda de dados, uma vez que todos os dados são armazenados em Armazenamento Azure ou Armazenamento de Data Lake. |

As piscinas de faíscas em Azure Synapse incluem os seguintes componentes disponíveis nas piscinas por padrão.

- [Spark Core](https://spark.apache.org/docs/latest/). Inclui Spark Core, Spark SQL, GraphX e MLlib.
- [Anaconda](https://docs.continuum.io/anaconda/)
- [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Caderno Nteract](https://nteract.io/)

## <a name="spark-pool-architecture"></a>Arquitetura de piscina de faísca

É fácil compreender os componentes da Spark compreendendo como a Spark funciona no Synapse Analytics.

As aplicações de faísca funcionam como conjuntos independentes de processos numa piscina, coordenados pelo objeto SparkContext no seu programa principal (chamado programa de motorista).

O SparkContext pode ligar-se ao gestor de clusters, que aloca recursos através de aplicações. O gestor do cluster é [Apache Hadoop YARN.](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Uma vez conectado, a Spark adquire executores em nós na piscina, que são processos que executam computações e armazenam dados para a sua aplicação. Em seguida, envia o código da aplicação (definido pelos ficheiros JAR ou Python transmitidos para o SparkContext) aos executores. Por fim, o SparkContext envia tarefas para os executores executarem.

O SparkContext executa a função principal do utilizador e executa as várias operações paralelas nos nódosos. Em seguida, recolhe os resultados das operações. Os nódosos lêem e escrevem dados de e para o sistema de ficheiros. Os nódosos também cache dados transformados em memória como Conjuntos de Dados Distribuídos Resiliente (RDDs).

O SparkContext liga-se à piscina Spark e é responsável pela conversão de uma aplicação num gráfico direcionado (DAG). O gráfico consiste em tarefas individuais que são executadas dentro de um processo de executor nos nódosos. Cada aplicação tem os seus próprios processos de executor, que permanecem em funcionamento ao longo da duração de toda a aplicação e executam tarefas em múltiplos threads.

## <a name="apache-spark-in-synapse-analytics-use-cases"></a>Apache Spark em Synapse Analytics usa casos

As piscinas de faíscas no Synapse Analytics permitem os seguintes cenários-chave:

### <a name="data-engineeringdata-preparation"></a>Engenharia de Dados/Preparação de Dados

A Apache Spark inclui muitas funcionalidades linguísticas para apoiar a preparação e o processamento de grandes volumes de dados para que possa ser mais valioso e depois consumido por outros serviços dentro da Synapse Analytics. Isto é ativado através de várias línguas (C#, Scala, PySpark, Spark SQL) e bibliotecas fornecidas para processamento e conectividade.

### <a name="machine-learning"></a>Machine Learning

Apache Spark vem com [MLlib](https://spark.apache.org/mllib/), uma biblioteca de machine learning construída em cima de Spark que você pode usar a partir de uma piscina spark em Synapse Analytics. As piscinas de faíscas em Synapse Analytics também incluem a Anaconda, uma distribuição Python com uma variedade de pacotes para ciência de dados, incluindo machine learning. Quando combinado com suporte incorporado para cadernos, você tem um ambiente para criar aplicações de aprendizagem automática.

## <a name="where-do-i-start"></a>Por onde devo começar?

Use os seguintes artigos para saber mais sobre Apache Spark em Synapse Analytics:

- [Quickstart: Crie uma piscina de faíscas em Azure Synapse](../quickstart-create-apache-spark-pool-portal.md)
- [Quickstart: Crie um caderno Apache Spark](../quickstart-apache-spark-notebook.md)
- [Tutorial: Machine learning usando Apache Spark](./apache-spark-machine-learning-mllib-notebook.md)
- [Documentação oficial de Apache Spark](https://spark.apache.org/docs/latest/)

> [!NOTE]
> Parte da documentação oficial da Apache Spark baseia-se na utilização da consola de faíscas, esta não está disponível na Azure Synapse Spark, utilize o caderno ou as experiências intelliJ em vez disso.

## <a name="next-steps"></a>Próximos passos

Nesta visão geral, você obtém uma compreensão básica de Apache Spark em Azure Synapse Analytics. Avance para o próximo artigo para aprender a criar uma piscina de faíscas no Azure Synapse Analytics:

- [Crie uma piscina de faíscas em Azure Synapse](../quickstart-create-apache-spark-pool-portal.md)
