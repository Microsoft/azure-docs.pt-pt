---
title: Métodos de treinamento de modelo
titleSuffix: Azure Machine Learning
description: Conheça os diferentes métodos que você pode usar para treinar o modelo com Azure Machine Learning. Os estimadores fornecem uma maneira fácil de trabalhar com estruturas populares, como Scikit-learn, TensorFlow, Keras, PyTorch e Chainer. Machine Learning pipelines facilitam o agendamento de execuções autônomas, o uso de ambientes de computação heterogêneos e a reutilização de partes do fluxo de trabalho. E as configurações de execução fornecem controle granular sobre os destinos de computação em que o processo de treinamento é executado.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: ac7ae0d7933e1d1b4d716eb157bf74152155a969
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497330"
---
# <a name="train-models-with-azure-machine-learning"></a>Treinar modelos com Azure Machine Learning

O Azure Machine Learning fornece várias maneiras de treinar seus modelos, desde soluções Code First usando o SDK até soluções de código baixo, como o Machine Learning automatizado e o designer visual. Use a lista a seguir para determinar qual método de treinamento é o ideal para você:

+ [SDK do Azure Machine Learning para Python](#python-sdk): o SDK do Python fornece várias maneiras de treinar modelos, cada um com recursos diferentes.

    | Método de treinamento | Descrição |
    | ----- | ----- |
    | [Configuração de execução](#run-configuration) | Uma **maneira genérica de treinar modelos** é usar um script de treinamento e executar a configuração. A configuração de execução fornece as informações necessárias para configurar o ambiente de treinamento usado para treinar seu modelo. Você pode executar uma configuração de execução, seu script de treinamento e um destino de computação (o ambiente de treinamento) e executar um trabalho de treinamento. |
    | [Machine Learning automatizado](#automated-machine-learning) | O aprendizado de máquina automatizado permite **treinar modelos sem amplo conhecimento de programação ou ciência de dados**. Para pessoas com um plano de fundo de ciência de dados e programação, ele fornece uma maneira de economizar tempo e recursos ao automatizar a seleção de algoritmos e o ajuste de hiperparâmetro. Você não precisa se preocupar em definir uma configuração de execução ao usar o Machine Learning automatizado. |
    | [Estimativas](#estimators) | As classes estimadoras facilitam o **treinamento de modelos com base em estruturas de aprendizado de máquina populares**. Há classes Estimator para **Scikit-Learn**, **PyTorch**, **TensorFlow**e **Chainer**. Também há um estimador genérico que pode ser usado com estruturas que ainda não têm uma classe de estimador dedicada. Você não precisa se preocupar em definir uma configuração de execução ao usar os estimadores. |
    | [Pipeline do Machine Learning](#machine-learning-pipeline) | Pipelines não são um método de treinamento diferente, mas uma **maneira de definir um fluxo de trabalho usando etapas modulares reutilizáveis**, que podem incluir treinamento como parte do fluxo de trabalho. Os pipelines do Machine Learning dão suporte ao uso de aprendizado de máquina automatizado, avaliadores e configurações de execução para treinar modelos. Como os pipelines não se concentram especificamente no treinamento, os motivos para usar um pipeline são mais variados do que os outros métodos de treinamento. Em geral, você pode usar um pipeline quando:<br>* Você deseja **agendar processos autônomos** , como trabalhos de treinamento de longa execução ou preparação de dados.<br>* Use **várias etapas** coordenadas em recursos de computação heterogêneos e locais de armazenamento.<br>* Use o pipeline como um **modelo reutilizável** para cenários específicos, como retreinamento ou pontuação de lote.<br>* **controle e as fontes de dados de versão, entradas e saídas** de seu fluxo de trabalho.<br>* O fluxo de trabalho é **implementado por equipes diferentes que trabalham em etapas específicas de forma independente**. As etapas podem ser Unidas juntas em um pipeline para implementar o fluxo de trabalho. |

+ **Designer**: o designer de Azure Machine Learning (versão prévia) fornece um ponto de entrada fácil para o aprendizado de máquina para a criação de conceitos de prova ou para usuários com pouca experiência de codificação. Ele permite treinar modelos usando uma interface do usuário baseada na Web de arrastar e soltar. Você pode usar o código do Python como parte do design ou treinar modelos sem escrever nenhum código.

+ **CLI**: a CLI do Machine Learning fornece comandos para tarefas comuns com Azure Machine Learning e é geralmente usada para **criar scripts e automatizar tarefas**. Por exemplo, depois de criar um script ou pipeline de treinamento, você pode usar a CLI para iniciar uma execução de treinamento em um agendamento ou quando os arquivos de dados usados para treinamento forem atualizados. Para modelos de treinamento, ele fornece comandos que enviam trabalhos de treinamento. Ele pode enviar trabalhos usando configurações de execução ou pipelines.

Cada um desses métodos de treinamento pode usar diferentes tipos de recursos de computação para treinamento. Coletivamente, esses recursos são chamados de [__destinos de computação__](concept-azure-machine-learning-architecture.md#compute-targets). Um destino de computação pode ser um computador local ou um recurso de nuvem, como um Azure Machine Learning computação, Azure HDInsight ou uma máquina virtual remota.

## <a name="python-sdk"></a>Python SDK

O SDK do Azure Machine Learning para Python permite que você crie e execute fluxos de trabalho do Machine Learning com Azure Machine Learning. Você pode interagir com o serviço de uma sessão interativa do Python, notebooks Jupyter, Visual Studio Code ou outro IDE.

* [O que é o SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Instalar/atualizar o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Configurar um ambiente de desenvolvimento para Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Configuração de execução

Um trabalho de treinamento genérico com Azure Machine Learning pode ser definido usando o [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). Em seguida, a configuração de execução é usada, juntamente com seus scripts de treinamento para treinar um modelo em um destino de computação.

Você pode começar com uma configuração de execução para seu computador local e, em seguida, alternar para uma para um destino de computação baseado em nuvem, conforme necessário. Ao alterar o destino de computação, você só altera a configuração de execução usada. Uma execução também registra informações sobre o trabalho de treinamento, como entradas, saídas e logs.

* [O que é uma configuração de execução?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Tutorial: treinar seu primeiro modelo de ML](tutorial-1st-experiment-sdk-train.md)
* [Exemplos: Jupyter Notebook exemplos de modelos de treinamento](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [Como configurar e usar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Machine Learning automatizado

Defina as iterações, configurações de hiperparâmetro, personalização e outras configurações. Durante o treinamento, o Azure Machine Learning tenta algoritmos e parâmetros diferentes em paralelo. O treinamento é interrompido quando atinge os critérios de saída que você definiu. Você não precisa se preocupar em definir uma configuração de execução ao usar os estimadores.

> [!TIP]
> Além do SDK do Python, você também pode usar o ML automatizado por meio do [Azure Machine Learning Studio](https://ml.azure.com).

* [O que é o Machine Learning automatizado?](concept-automated-ml.md)
* [Tutorial: criar seu primeiro modelo de classificação com o Machine Learning automatizado](tutorial-first-experiment-automated-ml.md)
* [Tutorial: usar o Machine Learning automatizado para prever as tarifas de táxi](tutorial-auto-train-models.md)
* [Exemplos: Jupyter Notebook exemplos para o aprendizado de máquina automatizado](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Como: configurar experimentos de ML automatizados em Python](how-to-configure-auto-train.md)
* [Como: treinar a autoatendimento de um modelo de previsão de série temporal](how-to-auto-train-forecast.md)
* [Como: criar, explorar e implantar experimentos de aprendizado de máquina automatizados com o [Azure Machine Learning Studio](how-to-create-portal-experiments.md)

### <a name="estimators"></a>Estimativas

Os estimadores facilitam o treinamento de modelos usando estruturas de ML populares. Se você estiver usando **Scikit-Learn**, **PyTorch**, **TensorFlow**ou **Chainer**, considere o uso de um estimador para treinamento. Também há um estimador genérico que pode ser usado com estruturas que ainda não têm uma classe de estimador dedicada. Você não precisa se preocupar em definir uma configuração de execução ao usar os estimadores.

* [O que são os estimadores?](concept-azure-machine-learning-architecture.md#estimators)
* [Tutorial: treinar modelos de classificação de imagem com dados do MNIST e scikit-aprender a usar Azure Machine Learning](tutorial-train-models-with-aml.md)
* [Exemplos: Jupyter Notebook exemplos de como usar os estimadores](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [Como criar estimadores no treinamento](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Pipeline do Machine Learning

Os pipelines do Machine Learning podem usar os métodos de treinamento mencionados anteriormente (configuração de execução, estimadores e aprendizado de máquina automatizado). Pipelines são mais sobre a criação de um fluxo de trabalho, de modo que abrangem mais do que apenas o treinamento de modelos. Em um pipeline, você pode treinar um modelo usando o aprendizado de máquina automatizado, estimações ou configurações de execução.

* [O que são pipelines de ML no Azure Machine Learning?](concept-ml-pipelines.md)
* [Criar e executar pipelines do Machine Learning com o SDK do Azure Machine Learning](how-to-create-your-first-pipeline.md)
* [Tutorial: usar pipelines de Azure Machine Learning para Pontuação de lote](tutorial-pipeline-batch-scoring-classification.md)
* [Exemplos: Jupyter Notebook exemplos para pipelines do Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Exemplos: pipeline com o Machine Learning automatizado](https://aka.ms/pl-automl)
* [Exemplos: pipeline com avaliadores](https://aka.ms/pl-estimator)

## <a name="azure-machine-learning-designer"></a>Designer de Azure Machine Learning

O designer permite treinar modelos usando uma interface de arrastar e soltar em seu navegador da Web.

+ [O que é o designer?](concept-designer.md)
+ [Tutorial: prever o preço do automóvel](tutorial-designer-automobile-price-train-score.md)
+ [Regressão: preço de previsão](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Classificação: Previsão de renda](how-to-designer-sample-classification-predict-income.md)
+ [Classificação: prever a rotatividade, a desejo e a venda vertical](how-to-designer-sample-classification-churn.md)
+ [Classificação com script R personalizado: prever atrasos de voo](how-to-designer-sample-classification-flight-delay.md)
+ [Classificação de texto: conjunto de uma da Wikipédia SP 500](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>CLI

A CLI do Machine Learning é uma extensão para o CLI do Azure. Ele fornece comandos da CLI de plataforma cruzada para trabalhar com Azure Machine Learning. Normalmente, você usa a CLI para automatizar tarefas, como treinar um modelo de aprendizado de máquina.

* [Usar a extensão da CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps no Azure](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>Passos seguintes

Saiba como [configurar ambientes de treinamento](how-to-set-up-training-targets.md).
