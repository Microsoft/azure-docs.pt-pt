---
title: Ingestão de dados com o Azure Data Factory
titleSuffix: Azure Machine Learning
description: Saiba como construir um pipeline de ingestão de dados com a Azure Data Factory.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ad04566699b2eebb0cbd7a9f242de38bc75e2015
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986404"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Ingestão de dados com o Azure Data Factory

Neste artigo, aprende-se a construir um pipeline de ingestão de dados com a Azure Data Factory (ADF). Este gasoduto é utilizado para ingerir dados para utilização com a Azure Machine Learning. A Azure Data Factory permite-lhe extrair, transformar e carregar facilmente dados (ETL). Uma vez que os dados foram transformados e carregados em armazenamento, pode ser usado para treinar os seus modelos de aprendizagem automática.

A transformação simples de dados pode ser tratada com atividades autóctones da ADF e instrumentos como [o fluxo de dados.](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity) Quando se trata de cenários mais complicados, os dados podem ser processados com algum código personalizado. Por exemplo, código Python ou R.

Existem várias técnicas comuns de utilização da Azure Data Factory para transformar dados durante a ingestão. Cada técnica tem prós e contras que determinam se é um bom ajuste para um caso de uso específico:

| Técnica | Vantagens | Desvantagens |
| ----- | ----- | ----- |
| Funções ADF + Azure | Baixa latência, computação sem servidor</br>Funções imponentes</br>Funções reutilizáveis | Só é bom para o processamento de curto prazo |
| Componente ADF + personalizado | Computação paralela em larga escala</br>Adequado para algoritmos pesados | Embrulhá-lo num executável</br>Complexidade do tratamento das dependências e IO |
| Caderno ADF + Azure Databricks | Apache Spark</br>Ambiente python nativo | Pode ser caro</br>Criar clusters inicialmente leva tempo e adiciona latência

## <a name="adf-with-azure-functions"></a>ADF com funções Azure

![O diagrama mostra um pipeline Azure Data Factory, com Azure Function e Run M L Pipeline, e um pipeline Azure Machine Learning, com o Train Model, e como interagem com dados brutos e dados preparados.](media/how-to-data-ingest-adf/adf-function.png)

As Funções Azure permitem executar pequenas peças de código (funções) sem se preocupar com a infraestrutura de aplicações. Nesta opção, os dados são processados com código Python personalizado embrulhado numa Função Azure. 

A função é invocada com a [atividade ADF Azure Function](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity). Esta abordagem é uma boa opção para transformações leves de dados. 

* Prós:
    * Os dados são processados num cálculo sem servidor com uma latência relativamente baixa
    * O oleoduto ADF pode invocar uma [Função Azure Durável](/azure/azure-functions/durable/durable-functions-overview) que pode implementar um fluxo sofisticado de transformação de dados 
    * Os detalhes da transformação de dados são resumidos pela Função Azure que pode ser reutilizada e invocada de outros lugares
* Contras:
    * As Funções Azure devem ser criadas antes da utilização com a ADF
    * As funções Azure só são boas para o processamento de dados de curto prazo

## <a name="adf-with-custom-component-activity"></a>ADF com atividade de componente personalizado

![O diagrama mostra um pipeline Azure Data Factory, com um componente personalizado e um Pipeline Run M L, e um pipeline de Aprendizagem automática Azure, com o Train Model, e como interagem com dados brutos e dados preparados.](media/how-to-data-ingest-adf/adf-customcomponent.png)

Nesta opção, os dados são processados com código Python personalizado embrulhado num executável. É invocado com uma [atividade de Componente Personalizado ADF.](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity) Esta abordagem é mais adequada para dados grandes do que a técnica anterior.

* Prós:
    * Os dados são processados no [conjunto Azure Batch,](https://docs.microsoft.com/azure/batch/batch-technical-overview) que fornece computação paralela e de alto desempenho em larga escala
    * Pode ser usado para executar algoritmos pesados e processar quantidades significativas de dados
* Contras:
    * A piscina Azure Batch deve ser criada antes de ser usada com a ADF
    * Sobre engenharia relacionada a envolver o código Python num executável. Complexidade das dependências de manuseamento e parâmetros de entrada/saída

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF com Azure Databricks Python caderno

![O diagrama mostra um oleoduto Azure Data Factory, com Azure Databricks Python e Run M L Pipeline, e um pipeline Azure Machine Learning, com o Train Model, e como interagem com dados brutos e dados preparados.](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) é uma plataforma de análise baseada em Apache Spark na nuvem da Microsoft.

Nesta técnica, a transformação de dados é realizada por um [caderno Python,](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)que funciona num cluster Azure Databricks. Esta é provavelmente a abordagem mais comum que aproveita todo o poder de um serviço Azure Databricks. É projetado para o processamento de dados distribuídos em escala.

* Prós:
    * Os dados são transformados no serviço Azure de processamento de dados mais poderoso, que é apoiado pelo ambiente Apache Spark
    * Apoio nativo da Python juntamente com quadros e bibliotecas de ciência de dados, incluindo TensorFlow, PyTorch e scikit-learn
    * Não é necessário envolver o código Python em funções ou módulos executáveis. O código funciona como está.
* Contras:
    * A infraestrutura Azure Databricks deve ser criada antes de ser utilizada com a ADF
    * Pode ser caro dependendo da configuração do Azure Databricks
    * Girar os clusters computacional do modo "frio" leva algum tempo que traz alta latência à solução 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Dados de consumo em gasodutos de aprendizagem automática Azure

![O diagrama mostra um oleoduto Azure Data Factory e um oleoduto Azure Machine Learning e como interagem com dados brutos e dados preparados. O oleoduto Data Factory fornece dados à base de dados de dados preparados, que alimenta uma loja de dados, que alimenta conjuntos de dados no espaço de trabalho machine learning.](media/how-to-data-ingest-adf/aml-dataset.png)

Os dados transformados do gasoduto ADF são guardados para armazenamento de dados (como a Azure Blob). O Azure Machine Learning pode aceder a estes dados utilizando [datastores](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) e [conjuntos de dados.](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)

Cada vez que o gasoduto ADF funciona, os dados são guardados para um local diferente no armazenamento. Para passar a localização para Azure Machine Learning, o oleoduto ADF chama um oleoduto Azure Machine Learning. Ao ligar para o gasoduto ML, a localização dos dados e o ID de execução são enviados como parâmetros. O gasoduto ML pode então criar uma datastore/dataset utilizando a localização dos dados. 

> [!TIP]
> A [versão de suporte](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets)de conjuntos de dados , para que o gasoduto ML possa registar uma nova versão do conjunto de dados que aponta para os dados mais recentes do pipeline ADF.

Uma vez que os dados são acessíveis através de uma datastore ou conjunto de dados, pode usá-lo para treinar um modelo ML. O processo de formação pode fazer parte do mesmo oleoduto ML que é chamado da ADF. Ou pode ser um processo separado, como a experimentação num caderno jupyter.

Uma vez que os conjuntos de dados suportam a versão, e cada corrida a partir do pipeline cria uma nova versão, é fácil entender que versão dos dados foi usada para treinar um modelo.

## <a name="next-steps"></a>Passos seguintes

* [Executar um caderno Databricks na Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Dados de acesso nos serviços de armazenamento Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Treinar modelos com conjuntos de dados em Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [DevOps para um oleoduto de ingestão de dados](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

