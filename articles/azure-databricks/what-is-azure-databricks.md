---
title: O que é o Azure Databricks?
description: Saiba mais sobre Azure Databricks e como ele traz o Spark no databricks para o Azure. O Azure Databricks é uma plataforma de análise baseada no Apache Spark e otimizada para a plataforma de serviços cloud Microsoft Azure.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: overview
ms.date: 05/08/2019
ms.author: mamccrea
ms.custom: mvc
ms.openlocfilehash: 149b83b9aacf23cde06a2b35aed06c164afe9647
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243335"
---
# <a name="what-is-azure-databricks"></a>O que é o Azure Databricks?

O Azure Databricks é uma plataforma de análise baseada no Apache Spark e otimizada para a plataforma de serviços cloud Microsoft Azure. Concebida com os fundadores do Apache Spark, o Databricks está integrado com o Azure para prestar configurações com um clique, fluxos de trabalho fluídos e uma área de trabalho interativa que permite a colaboração entre cientistas de dados, engenheiros de dados e analistas empresariais.

![O que é o Azure Databricks?](./media/what-is-azure-databricks/azure-databricks-overview.png "O que é o Azure Databricks?")

Azure Databricks é um serviço de análise rápido, fácil e colaborativo com base em Apache Spark. Para um pipeline Big Data, os dados (brutos ou estruturados) são incluídos no Azure por meio de Azure Data Factory em lotes ou transmitidos quase em tempo real usando Kafka, Hub de eventos ou Hub IoT. Esses dados estão em um data Lake para armazenamento persistente de longo prazo, no armazenamento de BLOBs do Azure ou Azure Data Lake Storage. Como parte do seu fluxo de trabalho de análise, use Azure Databricks para ler dados de várias fontes de dados, como o [armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md), [Azure data Lake Storage](../data-lake-store/index.md), [Azure Cosmos DB](../cosmos-db/index.yml)ou [Azure SQL data warehouse](../sql-data-warehouse/index.yml) e transformá-lo em um processo avançado informações que usam o Spark.

![Pipeline do databricks](./media/what-is-azure-databricks/databricks-pipeline.png)

## <a name="apache-spark-based-analytics-platform"></a>Plataforma de análise baseada no Apache Spark

O Azure Databricks inclui as capacidades e tecnologias completas de cluster do Apache Spark open source. O Spark no Azure Databricks inclui os seguintes componentes:

![Apache Spark no Azure Databricks](./media/what-is-azure-databricks/apache-spark-ecosystem-databricks.png "Apache Spark no Azure Databricks")

* **Spark SQL e DataFrames**: o Spark SQL é o módulo do Spark para trabalhar com dados estruturados. Um DataFrame é uma coleção distribuída de dados organizados em colunas com nome. É conceptualmente equivalente a uma tabela numa base de dados relacional ou a um pacote de dados em R/Python.

* **Transmissão em fluxo**: análise e processamento de dados em tempo real para aplicações interativas e analíticas. Pode ser integrado com HDFS, Flume e Kafka.

* **MLib**: biblioteca de Machine Learning constituída por utilitários e algoritmos de aprendizagem comuns, incluindo classificação, regressão, clustering, filtragem em colaboração, redução de dimensionalidade, bem como primitivos de otimização subjacentes.

* **GraphX**: gráficos e computação de gráficos para um âmbito alargado de casos de utilização, desde a análise cognitiva até à exploração de dados.

* **Spark Core API**: inclui suporte para R, SQL, Python, Scala e Java.

## <a name="apache-spark-in-azure-databricks"></a>Apache Spark no Azure Databricks

O Azure Databricks baseia-se nas capacidades do Spark ao fornecer uma plataforma cloud de gestão zero, que inclui:

- Clusters do Spark totalmente geridos
- Uma área de trabalho interativa para exploração e visualização
- Uma plataforma para alimentar as suas aplicações baseadas no Spark favoritas

### <a name="fully-managed-apache-spark-clusters-in-the-cloud"></a>Clusters do Apache Spark totalmente geridos na cloud

O Azure Databricks possui um ambiente de produção seguro e fiável na cloud, gerido e suportado por especialistas em Spark. Pode:

* Criar clusters em segundos.
* Dimensionar automaticamente os clusters de forma dinâmica na vertical e horizontal, incluindo clusters sem servidor, e partilhá-los entre equipas. 
* Utilizar clusters através de programação, utilizando as APIs REST. 
* Utilizar as capacidades de integração de dados segura baseadas no Spark, que lhe permitem uniformizar os dados sem centralização. 
* Obter acesso instantânea às funcionalidades mais recentes do Apache Spark com cada versão.

### <a name="databricks-runtime"></a>Runtime do Databricks
O Runtime do Databricks baseia-se no Apache Spark e foi nativamente concebido para a cloud do Azure. 

Com a opção **Sem servidor**, o Azure Databricks elimina totalmente a complexidade da infraestrutura e a necessidade de conhecimentos especializados para preparar e configurar a sua infraestrutura de dados. A opção Sem servidor ajuda os cientistas de dados a iterar rapidamente como uma equipa.

Para os engenheiros de dados que se preocupam com o desempenho das tarefas de produção, o Azure Databricks fornece um motor Spark que é mais rápido e eficaz através de várias otimizações na camada de E/S e na camada de processamento (E/S do Databricks).

### <a name="workspace-for-collaboration"></a>Área de trabalho para colaboração

Através de um ambiente de colaboração e integrado, o Azure Databricks simplifica o processo de exploração de dados, prototipagem e execução de aplicações condicionadas por dados no Spark.

* Determine como utilizar os dados com exploração de dados fácil.
* Documente o seu progresso em blocos de notas em R, Python, Scala ou SQL.
* Visualize dados em apenas alguns cliques e utilize ferramentas familiares como o Matplotlib, ggplot ou d3.
* Utilize dashboards interativos para criar relatórios dinâmicos.
* Utilize o Spark e interaja com os dados em simultâneo.

## <a name="enterprise-security"></a>Segurança empresarial

O Azure Databricks fornece a segurança do Azure de nível empresarial, incluindo a integração do Azure Active Directory, controlos baseados em funções e SLAs que protegem os seus dados e a sua empresa.

* A integração com o Azure Active Directory permite-lhe executar soluções completas baseadas no Azure com o Azure Databricks.
* O acesso baseado em funções do Azure Databricks ativa permissões de utilizador detalhadas para blocos de notas, clusters, tarefas e dados.
* SLAs de nível empresarial. 

## <a name="integration-with-azure-services"></a>Integração nos serviços do Azure

O Azure Databricks integra-se profundamente com as bases de dados e arquivos do Azure: SQL Data Warehouse, Cosmos DB, Data Lake Store e Armazenamento de Blobs. 

## <a name="integration-with-power-bi"></a>Integração com o Power BI
Através da forte integração com o Power BI, o Azure Databricks permite-lhe detetar e partilhar as suas informações importantes de forma rápida e fácil. Também pode utilizar outras ferramentas de BI, com o Tableau Software através de pontos finais de cluster JDBC/ODBC.

## <a name="next-steps"></a>Passos seguintes

* [Início rápido: Executar uma tarefa do Spark no Azure Databricks](quickstart-create-databricks-workspace-portal.md)
* [Trabalhar com clusters do Spark](https://docs.azuredatabricks.net/user-guide/clusters/index.html)
* [Trabalhar com blocos de notas](https://docs.azuredatabricks.net/user-guide/notebooks/index.html)
* [Criar tarefas do Spark](https://docs.azuredatabricks.net/user-guide/jobs.html)

 









