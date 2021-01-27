---
title: Ingestão de dados & automatização
titleSuffix: Azure Machine Learning
description: Aprenda os prós e contras das opções de ingestão de dados disponíveis para treinar os seus modelos de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: a096375e32e3d8a6760da88fe5ec86a70d364aff
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98872100"
---
# <a name="data-ingestion-options-for-azure-machine-learning-workflows"></a>Opções de ingestão de dados para fluxos de trabalho de aprendizagem automática Azure

Neste artigo, você aprende os prós e contras das opções de ingestão de dados disponíveis com Azure Machine Learning. 

Escolha entre:
+ [Oleodutos Azure Data Factory,](#azure-data-factory) especificamente construídos para extrair, carregar e transformar dados

+ [Azure Machine Learning Python SDK,](#azure-machine-learning-python-sdk)fornecendo uma solução de código personalizada para tarefas de ingestão de dados.

+ uma combinação de ambos

A ingestão de dados é o processo em que os dados não estruturados são extraídos de uma ou múltiplas fontes e depois preparados para treinar modelos de aprendizagem automática. É também tempo intensivo, especialmente se feito manualmente, e se tiver grandes quantidades de dados de várias fontes. Automatizar este esforço liberta recursos e garante que os seus modelos utilizam os dados mais recentes e aplicáveis.

## <a name="azure-data-factory"></a>Azure Data Factory

[A Azure Data Factory](../data-factory/introduction.md) oferece suporte nativo para monitorização de fontes de dados e gatilhos para os oleodutos de ingestão de dados.  

A tabela seguinte resume os prós e os contras para a utilização da Azure Data Factory para os seus fluxos de trabalho de ingestão de dados.

|Vantagens|Desvantagens
---|---
Especificamente construído para extrair, carregar e transformar dados.|Atualmente oferece um conjunto limitado de tarefas de pipeline Azure Data Factory 
Permite-lhe criar fluxos de trabalho orientados por dados para orquestrar movimentos de dados e transformações em escala.|Caro para construir e manter. Consulte a página de [preços](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) da Azure Data Factory para obter mais informações.
Integrado com várias ferramentas Azure como [Azure Databricks](../data-factory/transform-data-using-databricks-notebook.md) e [Azure Functions](../data-factory/control-flow-azure-function-activity.md) | Não executa scripts de forma nativa, em vez disso baseia-se em cálculos separados para execuções de scripts 
Suporta de forma nativa fonte de dados ativada ingestão de dados| 
Os processos de preparação de dados e de formação de modelos são separados.|
Capacidade de linhagem de dados incorporada para fluxos de dados da Azure Data Factory|
Fornece uma interface de [utilizador](../data-factory/quickstart-create-data-factory-portal.md) de experiência de código baixo para abordagens não scripting |

Estes passos e o diagrama seguinte ilustram o fluxo de trabalho de ingestão de dados da Azure Data Factory.

1. Retire os dados das suas fontes
1. Transforme e guarde os dados para um recipiente de bolha de saída, que serve como armazenamento de dados para Azure Machine Learning
1. Com os dados preparados armazenados, o oleoduto Azure Data Factory invoca um pipeline de aprendizagem automática de formação que recebe os dados preparados para a formação de modelos


    ![Ingestão de dados da ADF](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Saiba como construir um pipeline de ingestão de dados para machine learning com [a Azure Data Factory.](how-to-data-ingest-adf.md)

## <a name="azure-machine-learning-python-sdk"></a>Azure Máquina aprendendo Python SDK 

Com o [Python SDK,](/python/api/overview/azure/ml)pode incorporar tarefas de ingestão de dados num passo [de pipeline Azure Machine Learning.](./how-to-create-machine-learning-pipelines.md)

A tabela seguinte resume os prós e o contra para a utilização do SDK e um passo de gasoduto ml para tarefas de ingestão de dados.

Vantagens| Desvantagens
---|---
Configure os seus próprios scripts Python | Não suporta de forma nativa o desencadeamento de alterações na fonte de dados. Requer implementações de Aplicação Lógica ou Função Azure
Preparação de dados como parte de cada execução de formação de modelos|Requer competências de desenvolvimento para criar um script de ingestão de dados
Suporta scripts de preparação de dados em vários alvos de computação, incluindo [o cálculo Azure Machine Learning](concept-compute-target.md#azure-machine-learning-compute-managed) |Não fornece uma interface de utilizador para criar o mecanismo de ingestão

No diagrama seguinte, o gasoduto Azure Machine Learning consiste em dois passos: ingestão de dados e formação de modelos. O passo de ingestão de dados abrange tarefas que podem ser realizadas usando bibliotecas Python e o Python SDK, como extrair dados de fontes locais/web, e transformações de dados, como a imputação de valor em falta. O passo de treino utiliza então os dados preparados como entrada para o seu roteiro de treino para treinar o seu modelo de aprendizagem automática. 

![Pipeline Azure + Ingestão de dados SDK](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Próximos passos

Siga estes artigos como fazer:
* [Construir um pipeline de ingestão de dados com a Azure Data Factory](how-to-data-ingest-adf.md)

* [Automatizar e gerir os gasodutos de ingestão de dados com a Azure Pipelines](how-to-cicd-data-ingestion.md).