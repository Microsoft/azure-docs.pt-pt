---
title: O que é o Azure Synapse Analytics?
description: Uma visão geral da Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/28/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 5d2765787a4eb417f4aa86396e07a25f8157b8ce
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223062"
---
# <a name="what-is-azure-synapse-analytics"></a>O que é o Azure Synapse Analytics?

A análise empresarial deve funcionar em grande escala em qualquer tipo de dados, sejam brutos, refinados ou altamente curados. Isto normalmente requer que as empresas cosam tecnologias de armazenamento de big data e dados em complexos oleodutos de dados que funcionam em dados em lojas relacionais e lagos de dados. Este tipo de soluções são difíceis de construir, manter e garantir. Os seus atrasos de complexidade em fornecer as empresas de insights precisam.

**O Azure Synapse** é um serviço de análise integrado que acelera o tempo para conhecer armazéns de dados e grandes sistemas de dados. A Azure Synapse reúne o melhor das tecnologias **SQL** utilizadas no armazenamento de dados empresariais, tecnologias **Spark** utilizadas para big data, **Pipelines** para integração de dados e ETL/ELT, e integração profunda com outros serviços Azure, como **Power BI,** **CosmosDB,** e **AzureML.**

## <a name="key-features--benefits"></a>Principais funcionalidades & benefícios

### <a name="industry-leading-sql"></a>SQL líder da indústria

* **O Synapse SQL** é um sistema de consulta distribuído que permite às empresas implementar cenários de armazenamento de dados e virtualização de dados utilizando experiências padrão e familiares de T-SQL. Também expande as capacidades do SQL para abordar cenários de streaming e machine learning.

* O Synapse SQL oferece modelos de recursos **sem servidor** e **dedicados,** oferecendo opções de consumo e faturação para adequar as suas necessidades. Para obter um desempenho e custo previsíveis, crie conjuntos de SQL dedicados para reservar a capacidade de processamento para dados armazenados em tabelas SQL. Para cargas de trabalho não planeadas ou rebentadas, utilize o ponto final SQL sempre disponível e sem servidor.
* Use capacidades de **streaming** incorporadas para obter dados de fontes de dados em nuvem em tabelas SQL
* Integre a IA com o SQL utilizando modelos **de machine learning** para obter dados utilizando a [função T-SQL PREDICT](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)

### <a name="industry-standard-apache-spark"></a>Faísca Apache padrão da indústria

**Apache Spark for Azure Synapse** integra profundamente e sem problemas o Apache Spark -- o mais popular motor de dados de código aberto usado para a preparação de dados, engenharia de dados, ETL e machine learning.

* Modelos ML com algoritmos SparkML e integração AzureML para Apache Spark 2.4 com suporte incorporado para Linux Foundation Delta Lake.
* Modelo de recursos simplificado que o liberta de ter que se preocupar com a gestão de clusters.
* Arranque rápido da Spark e autoscalagem agressiva.
* Suporte incorporado para .NET for Spark permitindo-lhe reutilizar a sua experiência C# e o código .NET existente dentro de uma aplicação Spark.

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>Interop de SQL e Apache Spark no seu Lago de Dados

A Azure Synapse remove as barreiras tecnológicas tradicionais entre a utilização de SQL e Spark juntos. Pode misturar e combinar perfeitamente com base nas suas necessidades e conhecimentos.

* Um sistema de metadados partilhado compatível com hiv permite que as tabelas definidas em ficheiros no lago de dados sejam perfeitamente consumidas por Spark ou Hive.
* SQL e Spark podem explorar e analisar diretamente os ficheiros Parquet, CSV, TSV e JSON armazenados no lago de dados.
* Carga e descarga rápida de carga escalável para dados que vão entre bases de dados SQL e Spark

### <a name="built-in-data-integration-via-pipelines"></a>Integração de dados incorporados através de oleodutos

A Azure Synapse vem integrado com o mesmo motor de integração de dados e experiências como a Azure Data Factory, permitindo-lhe criar oleodutos ETL ricos em escala sem sair da Azure Synapse Analytics.

* Ingerir dados de mais de 90 fontes de dados
* Code-Free ETL com atividades de fluxo de dados
* Orquestrar Cadernos, Trabalhos de Faísca, Procedimentos Armazenados, Scripts SQL e muito mais

### <a name="unified-management-monitoring-and-security"></a>Gestão, monitorização e segurança unificadas

O Azure Synapse fornece uma única forma de as empresas gerirem os recursos analíticos, monitorizarem o uso e a atividade e imporem a segurança.

* Atribuir aos utilizadores a Função para simplificar o acesso aos recursos de análise
* Controlo de acesso de grãos finos sobre dados e código
* Um único dashboard para monitorizar recursos, uso e utilizadores em SQL e Spark

### <a name="synapse-studio"></a>Synapse Studio

**O Synapse Studio** é a experiência nativa da web que une tudo para os engenheiros de dados, permitindo-lhes num local fazer todas as tarefas necessárias para construir uma solução completa.

* Construir uma solução de análise de ponta a ponta num só local: ingerir, explorar, preparar, orquestrar, visualizar
* Produtividade líder na indústria para engenheiros de dados que escrevem código SQL ou Spark: autoria, depuração e otimização de desempenho
* Integrar-se com processos ci/CD da empresa

## <a name="engage-with-the-synapse-engineering-team"></a>Envolva-se com a equipa de engenharia da Sinapse

- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse): Faça perguntas de desenvolvimento.
- [Microsoft Q&Uma página de perguntas](/answers/topics/azure-synapse-analytics.html): Faça perguntas técnicas.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Synapse Analytics](get-started.md)
* [Criar uma área de trabalho](quickstart-create-workspace.md)
* [Utilizar conjunto de SQL sem servidor](quickstart-sql-on-demand.md)