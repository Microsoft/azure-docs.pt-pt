---
title: O que é o Azure Synapse Analytics?
description: Uma visão geral da Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 5c458b1c04a7f3be1a43d725591426e619286b7d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587885"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>O que é Azure Synapse Analytics (pré-visualização de espaços de trabalho)?

[!INCLUDE [preview](includes/note-preview.md)]

Hoje em dia, a análise da empresa requer operar em larga escala em qualquer tipo de dados, sejam crus, refinados ou altamente curados. No passado, a construção deste tipo de soluções de análise exigia que as empresas cosem tecnologias de armazenamento de grandes dados e dados, como a Spark e a SQL. Em seguida, teriam de integrá-los em ricos oleodutos de dados que funcionam através de dados em lojas relacionais e lagos de dados.  

Soluções como esta são difíceis de construir, configurar, proteger e manter, o que retarda a rápida extração de insights inteligentes.

**O Azure Synapse** é um serviço de análise integrado que acelera o tempo para a perceção de todos os dados em qualquer escala, através de armazéns de dados e sistemas de análise de big data. Reúne o melhor das tecnologias **SQL** utilizadas no armazenamento de dados da empresa, tecnologias **Spark** usadas na análise de big data e **Pipelines** para orquestrar atividades e movimento de dados.

A Azure Synapse vem com uma experiência de utilizador do **Estúdio** nativo da Web que proporciona uma única experiência e modelo de gestão, monitorização, codificação e segurança.

A Azure Synapse fornece a forma mais simples e rápida de uma empresa recolher informações sobre quaisquer dados de qualquer tamanho, usando a análise que mais lhes é familiar. Integra-se profundamente com o **Power BI** permitindo que os engenheiros de dados construam soluções de análise que trabalhem de ponta a ponta para fornecer Business Intelligence.

Além disso, o Azure Synapse facilita a construção de modelos preditivos e a análise avançada com machine learning através do seu suporte incorporado para o **AzureML.**

## <a name="key-features--benefits"></a>Principais características & benefícios

### <a name="industry-leading-sql"></a>SQL líder da indústria

* **O Synapse SQL** é um sistema de consulta distribuído que permite às empresas implementar cenários de armazenamento de dados e de virtualização de dados utilizando experiências padrão de T-SQL familiares a engenheiros de dados. Também expande as capacidades da SQL para abordar cenários de streaming e machine learning.

* A Synapse SQL oferece modelos de recursos **sem servidor** e **provisionados,** oferecendo opções de consumo e faturação para se adequar às suas necessidades. Para um desempenho e custo previsíveis, a provisionamento de piscinas para reservar o poder de processamento de dados armazenados em tabelas SQL. Para cargas de trabalho não planeadas ou rebentadas, utilize o ponto final SQL sempre disponível.
* Utilize capacidades de **streaming** incorporadas para aterrar dados de fontes de dados em nuvem em tabelas SQL
* Integrar a IA com o SQL, utilizando modelos de **machine learning** para obter dados utilizando a função T-SQL PREDICT

### <a name="industry-standard-apache-spark"></a>Apache Spark padrão da indústria

**A Apache Spark para Azure Synapse** integra profundamente e sem problemas a Apache Spark- o mais popular motor de dados aberto usado para a preparação de dados, engenharia de dados, ETL e machine learning.

* Modelos ML com algoritmos SparkML e integração AzureML para Apache Spark 2.4 com suporte incorporado para linux Foundation Delta Lake.
* Modelo de recursos simplificado que o liberta de ter que se preocupar com a gestão de clusters.
* Arranque rápido da Spark e autoscalagem agressiva.
* Suporte incorporado para .NET para Spark permitindo-lhe reutilizar a sua experiência C# e código .NET existente dentro de uma aplicação Spark.

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>Interop de SQL e Apache Spark no seu Lago de Dados

O Azure Synapse remove as barreiras tecnológicas tradicionais entre a utilização de SQL e Spark em conjunto. Pode misturar e combinar perfeitamente com base nas suas necessidades e conhecimentos.

* Um sistema de metadados compatível com a Hive permite que as tabelas definidas em ficheiros no lago de dados sejam perfeitamente consumidas por Spark ou Hive.
* Os ficheiros SQL e Spark podem explorar e analisar diretamente ficheiros Parquet, CSV, TSV e JSON armazenados no lago de dados.
* Carga e descarga escaláveis rápidas para dados que vão entre as bases de dados SQL e Spark

### <a name="built-in-orchestration-via-pipelines"></a>Orquestração incorporada através de oleodutos

O Azure Synapse vem integrado com o mesmo motor de integração de dados e experiências como a Azure Data Factory, permitindo-lhe criar ricos gasodutos de dados sem utilizar um motor de orquestração separado.

* Mover dados entre synapse e 85+ fontes de dados no local
* Cadernos orquestrados, oleodutos, spark jobs, Scripts SQL, procedimentos armazenados
* ETL livre de códigocom atividades de fluxo de dados

### <a name="unified-management-monitoring-and-security"></a>Gestão unificada, monitorização e segurança

A Azure Synapse fornece uma única forma de as empresas gerirem os recursos de análise, monitorizarem a utilização e a atividade e imporem a segurança.

* Atribuir aos utilizadores o Papel para simplificar o acesso aos recursos de análise
* Controlo de acesso de grãos finos em dados e código
* Um único dashboard para monitorizar recursos, uso e utilizadores em toda a SQL e Spark

### <a name="synapse-studio"></a>Synapse Studio

**O Synapse Studio** é a experiência nativa da web que une tudo para engenheiros de dados, permitindo-lhes num único local fazer todas as tarefas que precisam para construir uma solução completa.

* Construa uma solução de análise de ponta a ponta num só local: ingerir, explorar, preparar, orquestrar, visualizar
* Produtividade líder do setor para engenheiros de dados que escrevem código SQL ou Spark: autoria, depuração e otimização de desempenho
* Integrar com os processos CI/CD da empresa

## <a name="next-steps"></a>Passos seguintes

* [Criar uma área de trabalho](quickstart-create-workspace.md)
* [Use o Estúdio Synapse](quickstart-synapse-studio.md)
* [Criar uma piscina SQL](quickstart-create-sql-pool.md)
* [Utilizar o SQL a pedido](quickstart-sql-on-demand.md)
* [Crie uma piscina apache spark](quickstart-create-apache-spark-pool.md)
