---
title: O que é o Azure Synapse Analytics?
description: Uma visão geral da Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 03/24/2021
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 652f98659f96b36e3185432e50d9d36dc569bd43
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537942"
---
# <a name="what-is-azure-synapse-analytics"></a>O que é o Azure Synapse Analytics?

**Azure Synapse** é um serviço de análise empresarial que acelera o tempo para conhecer armazéns de dados e grandes sistemas de dados. A Azure Synapse reúne o melhor das tecnologias **SQL** utilizadas no armazenamento de dados empresariais, tecnologias **Spark** utilizadas para big data, **Pipelines** para integração de dados e ETL/ELT, e integração profunda com outros serviços Azure, como **Power BI,** **CosmosDB,** e **AzureML.**

![Diagrama da arquitetura Azure Synapse Analytics.](./media/overview-what-is/synapse-architecture.png)

## <a name="industry-leading-sql"></a>SQL líder da indústria

**O Synapse SQL** é um sistema de consulta distribuído para T-SQL que permite cenários de armazenamento de dados e virtualização de dados e estende o T-SQL para abordar cenários de streaming e machine learning.

* O Synapse SQL oferece modelos de recursos **sem servidor** e **dedicados.** Para obter um desempenho e custo previsíveis, crie conjuntos de SQL dedicados para reservar a capacidade de processamento para dados armazenados em tabelas SQL. Para cargas de trabalho não planeadas ou rebentadas, utilize o ponto final SQL sempre disponível e sem servidor.
* Use capacidades de **streaming** incorporadas para obter dados de fontes de dados em nuvem em tabelas SQL
* Integre a IA com o SQL utilizando modelos **de machine learning** para obter dados utilizando a [função T-SQL PREDICT](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="industry-standard-apache-spark"></a>Faísca Apache padrão da indústria

**Apache Spark for Azure Synapse** integra profundamente e sem problemas o Apache Spark -- o mais popular motor de dados de código aberto usado para a preparação de dados, engenharia de dados, ETL e machine learning.

* Modelos ML com algoritmos SparkML e integração AzureML para Apache Spark 2.4 com suporte incorporado para Linux Foundation Delta Lake.
* Modelo de recursos simplificado que o liberta de ter que se preocupar com a gestão de clusters.
* Arranque rápido da Spark e autoscalagem agressiva.
* Suporte incorporado para .NET for Spark permitindo-lhe reutilizar a sua experiência C# e o código .NET existente dentro de uma aplicação Spark.

## <a name="working-with-your-data-lake"></a>Trabalhando com o seu Lago de Dados

A Azure Synapse remove as barreiras tecnológicas tradicionais entre a utilização de SQL e Spark juntos. Pode misturar e combinar perfeitamente com base nas suas necessidades e conhecimentos.

* As tabelas definidas em ficheiros no lago de dados são perfeitamente consumidas por Spark ou Hive.
* SQL e Spark podem explorar e analisar diretamente os ficheiros Parquet, CSV, TSV e JSON armazenados no lago de dados.
* Carregamento rápido e escalável de dados entre bases de dados SQL e Spark

## <a name="built-in-data-integration"></a>Integração de dados incorporados

O Azure Synapse contém o mesmo motor e experiências de Integração de Dados que a Azure Data Factory, permitindo-lhe criar oleodutos ETL ricos em escala sem sair da Azure Synapse Analytics.

* Ingerir dados de mais de 90 fontes de dados
* Code-Free ETL com atividades de fluxo de dados
* Orquestrar cadernos, trabalhos de faísca, procedimentos armazenados, scripts SQL, e muito mais

## <a name="unified-experience"></a>Experiência unificada 

**O Synapse Studio** oferece uma única forma de as empresas construirem soluções, manterem e garantirem tudo numa única experiência de utilizador

* Executar tarefas-chave: ingerir, explorar, preparar, orquestrar, visualizar
* Monitorizar recursos, uso e utilizadores em sql e Faísca
* Utilizar o controlo de acesso baseado em funções para simplificar o acesso a recursos analíticos
* Escreva código SQL ou Spark e integre-se com processos ci/CD da empresa

## <a name="engage-with-the-synapse-community"></a>Envolva-se com a comunidade sinapse

- [Microsoft Q&A](/answers/topics/azure-synapse-analytics.html): Faça perguntas técnicas.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse): Faça perguntas de desenvolvimento.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Synapse Analytics](get-started.md)
* [Criar uma área de trabalho](quickstart-create-workspace.md)
* [Utilizar conjunto de SQL sem servidor](quickstart-sql-on-demand.md)
