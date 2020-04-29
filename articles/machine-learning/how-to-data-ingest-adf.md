---
title: Ingestão de dados com o Azure Data Factory
titleSuffix: Azure Machine Learning
description: Saiba como construir um pipeline de ingestão de dados com a Azure Data Factory.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.openlocfilehash: 5b4ed40048aab815397c9726098880b2125b732c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78274791"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Ingestão de dados com o Azure Data Factory

Neste artigo, aprende-se a construir um pipeline de ingestão de dados com a Azure Data Factory (ADF). Este gasoduto é utilizado para ingerir dados para utilização com o Azure Machine Learning. A Azure Data Factory permite-lhe extrair, transformar e carregar facilmente dados (ETL). Uma vez que os dados foram transformados e carregados em armazenamento, pode ser usado para treinar os seus modelos de aprendizagem automática.

A simples transformação de dados pode ser tratada com atividades e instrumentos nativos da ADF, tais como o fluxo de [dados.](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity) Quando se trata de cenários mais complicados, os dados podem ser processados com algum código personalizado. Por exemplo, código Python ou R.

Existem várias técnicas comuns de utilização da Azure Data Factory para transformar dados durante a ingestão. Cada técnica tem prós e contras que determinam se é um bom ajuste para um caso de uso específico:

| Técnica | Vantagens | Contras |
| ----- | ----- | ----- |
| Funções ADF + Azure | Baixa latência, computação sem servidor</br>Funções imponentes</br>Funções reutilizáveis | Apenas bom para processamento de curto prazo |
| ADF + componente personalizado | Computação paralela em larga escala</br>Adequado para algoritmos pesados | Código de embrulho em um executável</br>Complexidade do manuseamento de dependências e OI |
| Caderno ADF + Azure Databricks | Apache Spark</br>Ambiente Python nativo | Pode ser caro</br>Criar clusters inicialmente leva tempo e adiciona latência

## <a name="adf-with-azure-functions"></a>ADF com funções Azure

![adf-função](media/how-to-data-ingest-adf/adf-function.png)

As Funções Azure permitem-lhe executar pequenos pedaços de código (funções) sem se preocupar com a infraestrutura de aplicação. Nesta opção, os dados são processados com código Python personalizado embrulhado numa Função Azure. 

A função é invocada com a [atividade da Função ADF Azure](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity). Esta abordagem é uma boa opção para transformações leves de dados. 

* Prós:
    * Os dados são processados numa computação sem servidor com uma latência relativamente baixa
    * O oleoduto ADF pode invocar uma [Função Azure Durável](/azure/azure-functions/durable/durable-functions-overview) que pode implementar um fluxo sofisticado de transformação de dados 
    * Os detalhes da transformação de dados são abstraídos pela Função Azure que podem ser reutilizados e invocados de outros lugares
* Contras:
    * As Funções Azure devem ser criadas antes da utilização com a ADF
    * As Funções Azure são boas apenas para o processamento de dados de curto prazo

## <a name="adf-with-custom-component-activity"></a>ADF com Atividade de Componente Personalizado

![adf-customcomponent](media/how-to-data-ingest-adf/adf-customcomponent.png)

Nesta opção, os dados são processados com código Python personalizado embrulhado num executável. É invocado com uma atividade de [Componente Personalizado ADF.](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity) Esta abordagem é mais adequada para dados grandes do que a técnica anterior.

* Prós:
    * Os dados são processados no pool [Azure Batch,](https://docs.microsoft.com/azure/batch/batch-technical-overview) que fornece computação paralela e de alto desempenho em larga escala
    * Pode ser usado para executar algoritmos pesados e processar quantidades significativas de dados
* Contras:
    * Piscina de Lote Azure deve ser criada antes de ser utilizada com ADF
    * Sobre engenharia relacionada com embrulhar o código Python em um executável. Complexidade das dependências de manuseamento e dos parâmetros de entrada/saída

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF com caderno Azure Databricks Python

![adf-databricks](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) é uma plataforma de análise baseada em Apache Spark na nuvem da Microsoft.

Nesta técnica, a transformação de dados é realizada por um [caderno Python,](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)em execução num cluster de Databricks Azure. Esta é provavelmente a abordagem mais comum que aproveita toda a potência de um serviço Azure Databricks. Destina-se a um tratamento de dados distribuídos em escala.

* Prós:
    * Os dados são transformados no mais poderoso serviço de processamento de dados Azure, que é apoiado pelo ambiente Apache Spark
    * Apoio nativo da Python juntamente com quadros e bibliotecas de ciência de dados, incluindo TensorFlow, PyTorch e scikit-learn
    * Não há necessidade de embrulhar o código Python em funções ou módulos executáveis. O código funciona como está.
* Contras:
    * Infraestrutura de Tijolos De Dados Azure deve ser criada antes de ser utilizada com ADF
    * Pode ser caro dependendo da configuração de Azure Databricks
    * Girar os clusters computacionais do modo "frio" leva algum tempo que traz alta latência à solução 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Consumir dados em oleodutos azure machine learning

![aml-dataset](media/how-to-data-ingest-adf/aml-dataset.png)

Os dados transformados do gasoduto ADF são guardados para armazenamento de dados (como o Azure Blob). O Azure Machine Learning pode aceder a estes dados através de [datastores](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) e [conjuntos de dados.](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)

Cada vez que o gasoduto ADF funciona, os dados são guardados para um local diferente no armazenamento. Para passar a localização para o Azure Machine Learning, o oleoduto ADF chama um oleoduto de Aprendizagem automática Azure. Ao ligar para o gasoduto ML, a localização dos dados e o ID de execução são enviados como parâmetros. O pipeline ML pode então criar uma datastore/conjunto de dados utilizando a localização dos dados. 

> [!TIP]
> Datasets [suportam a versão](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets), para que o pipeline ML possa registar uma nova versão do conjunto de dados que aponta para os dados mais recentes do pipeline ADF.

Uma vez que os dados são acessíveis através de uma datastore ou dataset, pode usá-lo para treinar um modelo ML. O processo de formação pode fazer parte do mesmo oleoduto ML que é chamado da ADF. Ou pode ser um processo separado, como a experimentação num caderno jupyter.

Uma vez que os conjuntos de dados suportam a versão, e cada execução do pipeline cria uma nova versão, é fácil entender qual a versão dos dados que foi usada para treinar um modelo.

## <a name="next-steps"></a>Passos seguintes

* [Executar um caderno databricks na Fábrica de Dados Azure](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Dados de acesso nos serviços de armazenamento do Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Treine modelos com conjuntos de dados em Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [DevOps para um pipeline de ingestão de dados](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

