---
title: O que é o Azure Synapse Analytics?
description: Uma visão geral da Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 09/12/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: c4338152579170bf809577262992f0db9a1a95ff
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90524951"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>O que é Azure Synapse Analytics (pré-visualização de espaços de trabalho)?

[!INCLUDE [preview](includes/note-preview.md)]

A análise empresarial deve trabalhar em grande escala em qualquer tipo de dados, sejam crus, refinados ou altamente curados. Isto exigia que as empresas juntassem grandes tecnologias de armazenamento de dados e de armazenamento de dados, como a Spark e a SQL, em ricos oleodutos de dados que funcionam em dados em lojas relacionais e lagos de dados. Soluções como esta são difíceis de construir, proteger e manter. A complexidade atrasa a entrega da perspicácia necessária.

**O Azure Synapse** é um serviço de análise integrado que acelera o tempo para conhecer os armazéns de dados e os sistemas de análise de big data. No seu cerne, a Azure Synapse reúne as melhores tecnologias **SQL** utilizadas no armazenamento de dados empresariais, tecnologias **Spark** utilizadas para big data, e Pipelines para integração de **dados** e ETL/ELT. A Synapse tem um **Estúdio** baseado na web que fornece um único lugar para gestão, monitorização, codificação e segurança. A Sinapse apresenta uma profunda integração com outros serviços Azure, tais como **PowerBI,** **CosmosDB**e **AzureML.**

## <a name="key-features--benefits"></a>Principais funcionalidades & benefícios

### <a name="industry-leading-sql"></a>SQL líder da indústria

* **O Synapse SQL** é um sistema de consulta distribuído que permite às empresas implementar cenários de armazenamento de dados e virtualização de dados utilizando experiências padrão de T-SQL familiares aos engenheiros de dados. Também expande as capacidades do SQL para abordar cenários de streaming e machine learning.

* O Synapse SQL oferece modelos de recursos **sem servidor** e **dedicados,** oferecendo opções de consumo e faturação para adequar as suas necessidades. Para um desempenho e custo previsíveis, crie piscinas SQL dedicadas para reservar o poder de processamento para os dados armazenados em tabelas SQL. Para cargas de trabalho não planeadas ou rebentadas, utilize o ponto final SQL sempre disponível e sem servidor.
* Use capacidades de **streaming** incorporadas para obter dados de fontes de dados em nuvem em tabelas SQL
* Integre a IA com o SQL, utilizando modelos **de machine learning** para obter dados utilizando a [função T-SQL PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)

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

A Azure Synapse vem integrado com o mesmo motor de integração de dados e experiências como Azure Data Factory, permitindo-lhe criar ricos oleodutos ETL em escala sem sair da Synapse Analytics.

* Ingerir dados de mais de 90 fontes de dados
* ETL sem código com atividades de fluxo de dados
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

## <a name="next-steps"></a>Passos seguintes

* [Começa com o Azure Synapse Analytics](get-started.md)
* [Criar uma área de trabalho](quickstart-create-workspace.md)
* [Utilizar o SQL a pedido](quickstart-sql-on-demand.md)
