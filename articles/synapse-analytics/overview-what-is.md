---
title: O que é o Azure Synapse Analytics?
description: Uma visão geral da Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 96bb624472aa2053599765d5bfedeb96339d0973
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85808051"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>O que é Azure Synapse Analytics (pré-visualização de espaços de trabalho)?

[!INCLUDE [preview](includes/note-preview.md)]

Hoje em dia, a análise da empresa requer operar em grande escala em qualquer tipo de dados, sejam brutos, refinados ou altamente curados. No passado, a construção deste tipo de soluções de análise exigia que as empresas juntassem grandes tecnologias de armazenamento de dados e dados, como a Spark e a SQL. Em seguida, teriam de integrá-los em ricos oleodutos de dados que funcionam com dados em lojas relacionais e lagos de dados.  

Soluções como esta são difíceis de construir, configurar, proteger e manter, o que atrasa a rápida extração de uma visão inteligente.

**O Azure Synapse** é um serviço de análise integrado que acelera o tempo para insights a partir de todos os dados em qualquer escala, através de armazéns de dados e sistemas de análise de big data. Reúne as melhores tecnologias **SQL** utilizadas no armazenamento de dados empresariais, tecnologias **Spark** usadas em big data analytics, e Pipelines para orquestrar atividades e movimento de **dados.**

Azure Synapse vem com uma experiência de utilizador do **Estúdio** nativo da web que proporciona uma única experiência e modelo para gestão, monitorização, codificação e segurança.

O Azure Synapse fornece a forma mais simples e rápida de uma empresa recolher informações sobre quaisquer dados em qualquer tamanho, utilizando as análises que mais conhecem. Integra-se profundamente com **o Power BI** permitindo aos engenheiros de dados construir soluções de análise que trabalhem de ponta a ponta para fornecer Business Intelligence.

Além disso, a Azure Synapse facilita a construção de modelos preditivos e análises avançadas com machine learning através do seu suporte incorporado para **a AzureML.**

## <a name="key-features--benefits"></a>Principais funcionalidades & benefícios

### <a name="industry-leading-sql"></a>SQL líder da indústria

* **O Synapse SQL** é um sistema de consulta distribuído que permite às empresas implementar cenários de armazenamento de dados e virtualização de dados utilizando experiências padrão de T-SQL familiares aos engenheiros de dados. Também expande as capacidades do SQL para abordar cenários de streaming e machine learning.

* O Synapse SQL oferece modelos de recursos **sem servidor** e **aprovisionados,** oferecendo opções de consumo e faturação para adequar as suas necessidades. Para um desempenho e custo previsíveis, provisões para reservar o poder de processamento dos dados armazenados em tabelas SQL. Para cargas de trabalho não planeadas ou rebentadas, utilize o ponto final SQL sem servidor, sempre disponível.
* Use capacidades de **streaming** incorporadas para obter dados de fontes de dados em nuvem em tabelas SQL
* Integre a IA com o SQL, utilizando modelos **de machine learning** para obter dados utilizando a função T-SQL PREDICT

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

### <a name="built-in-orchestration-via-pipelines"></a>Orquestração incorporada através de oleodutos

A Azure Synapse vem integrado com o mesmo motor de integração de dados e experiências como Azure Data Factory, permitindo-lhe criar ricos oleodutos de dados sem usar um motor de orquestração separado.

* Mover dados entre Azure Synapse e 90+ fontes de dados no local
* Cadernos orquestrados, Oleodutos, Trabalhos de Faísca, Scripts SQL, Procedimentos armazenados
* ETL sem código com atividades de fluxo de dados

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

## <a name="next-steps"></a>Próximos passos

* [Criar uma área de trabalho](quickstart-create-workspace.md)
* [Use o Estúdio Synapse](quickstart-synapse-studio.md)
* [Criar uma piscina SQL](quickstart-create-sql-pool-portal.md)
* [Utilizar o SQL a pedido](quickstart-sql-on-demand.md)
* [Criar uma piscina Apache Spark](quickstart-create-apache-spark-pool-portal.md)
