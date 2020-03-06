---
title: Opções de ingestão de dados
titleSuffix: Azure Machine Learning
description: Conheça as opções de ingestão de dados para treinar os seus modelos de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 71a02e47db288890d1392f5423da0ef817ecd690
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303125"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Ingestão de dados em Aprendizagem automática azure

Neste artigo, você aprende os prós e contras das seguintes opções de ingestão de dados disponíveis com o Azure Machine Learning. 

1. Oleodutos [Azure Data Factory](#use-azure-data-factory)
2. [Pitão de aprendizagem de máquina sdk azure](#use-the-python-sdk)

A ingestão de dados é o processo em que os dados não estruturados são extraídos de uma ou múltiplas fontes e, em seguida, preparados para treinar modelos de aprendizagem automática. Também é intensivo em tempo, especialmente se feito manualmente, e se você tiver grandes quantidades de dados de várias fontes. Automatizar este esforço liberta recursos e garante que os seus modelos utilizam os dados mais recentes e aplicáveis.

Recomendamos que avalie inicialmente a utilização da Azure Data Factory (ADF), uma vez que foi especificamente construída para extrair, carregar e transformar dados. Se não conseguir cumprir os seus requisitos utilizando a ADF, pode utilizar o Python SDK para desenvolver uma solução de código personalizada, ou utilizar a ADF e o Python SDK em conjunto para criar um fluxo de trabalho global de ingestão de dados que satisfaça as suas necessidades.

## <a name="use-azure-data-factory"></a>Utilize o Azure Data Factory

[A Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) oferece suporte nativo para monitorização de fontes de dados e gatilhos para gasodutos de ingestão de dados.  

A tabela que se segue resume os prós e os contras para a utilização da Azure Data Factory para os seus fluxos de trabalho de ingestão de dados.

|Profissionais de TI|Contras
---|---
Especificamente construído para extrair, carregar e transformar dados.|Atualmente oferece um conjunto limitado de tarefas de pipeline Azure Data Factory 
Permite-lhe criar fluxos de trabalho baseados em dados para orquestrar o movimento de dados e transformações em escala.|Caro para construir e manter. Consulte [a página](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) de preços da Azure Data Factory para obter mais informações.
Integrado com várias ferramentas Azure como [Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) e [Funções Azure](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) | Não executa os scripts de forma nativa, em vez disso depende de computação separada para scriptruns 
Apoia nativamente fonte de dados desencadeado ingestão de dados| 
Os processos de preparação de dados e de formação de modelos são separados.|
Capacidade de linhagem de dados incorporada para fluxos de dados da Fábrica de Dados Azure|
Fornece uma interface de [utilizador](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) de baixa experiência de código para abordagens não-scripting |

Estes passos e o diagrama seguinte ilustram o fluxo de trabalho de ingestão de dados da Azure Data Factory.

1. Retire os dados das suas fontes
1. Transforme e guarde os dados para um recipiente de saída blob, que serve como armazenamento de dados para O Azure Machine Learning
1. Com os dados preparados armazenados, o oleoduto Azure Data Factory invoca um pipeline de aprendizagem automática de formação que recebe os dados preparados para a formação de modelos


    ![Ingestão de dados da ADF](media/concept-data-ingestion/data-ingest-option-one.svg)

## <a name="use-the-python-sdk"></a>Use o Python SDK 

Com o [Python SDK,](https://docs.microsoft.com/python/api/overview/azure/ml)pode incorporar tarefas de ingestão de dados num passo de [pipeline Azure Machine Learning.](how-to-create-your-first-pipeline.md)

A tabela que se segue resume os prós e os contra para a utilização do SDK e um passo de gasodutos ML para tarefas de ingestão de dados.

Profissionais de TI| Contras
---|---
Configure os seus próprios scripts Python | Não suporta nativamente a mudança de fonte de dados desencadeando. Requer aplicações lógicas ou implementações de funções azure
Preparação de dados como parte de cada execução de formação de modelos|Requer habilidades de desenvolvimento para criar um script de ingestão de dados
Suporta scripts de preparação de dados em vários alvos de computação, incluindo [o computação Azure Machine Learning](concept-compute-target.md#azure-machine-learning-compute-managed) |Não fornece uma interface de utilizador para criar o mecanismo de ingestão

No diagrama seguinte, o gasoduto Azure Machine Learning consiste em dois passos: ingestão de dados e formação de modelos. O passo de ingestão de dados engloba tarefas que podem ser realizadas usando bibliotecas Python e o Python SDK, tais como a extração de dados de fontes locais/web, e transformações básicas de dados, como a falta de imputação de valor. O passo de treino utiliza os dados preparados como entrada para o seu script de treino para treinar o seu modelo de aprendizagem automática. 

![Gasoduto Azure + Ingestão de dados sdk](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba como construir um pipeline de ingestão de dados para machine learning com [a Azure Data Factory](how-to-data-ingest-adf.md)
* Aprenda a automatizar e gerir os ciclos de vida de desenvolvimento dos seus gasodutos de ingestão de dados com [pipelines Azure](how-to-cicd-data-ingestion.md).