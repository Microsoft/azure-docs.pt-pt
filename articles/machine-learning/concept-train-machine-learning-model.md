---
title: Métodos de treinamento de modelo
titleSuffix: Azure Machine Learning
description: Conheça os diferentes métodos que você pode usar para treinar o modelo com Azure Machine Learning. Os estimadores fornecem uma maneira fácil de trabalhar com estruturas populares, como Scikit-learn, TensorFlow, Keras, PyTorch e Chainer. Os gasodutos de Machine Learning facilitam o agendamento de corridas sem supervisão, utilizam ambientes computação heterogéneos e reutilizam partes do seu fluxo de trabalho. E as configurações de execução fornecem controle granular sobre os destinos de computação em que o processo de treinamento é executado.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: f46dd2b30ca84a7e6a1b0fc34ef0fa5bafffaef5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721120"
---
# <a name="train-models-with-azure-machine-learning"></a>Utilizar modelos com o Azure Machine Learning

O Azure Machine Learning fornece várias maneiras de treinar seus modelos, desde soluções Code First usando o SDK até soluções de código baixo, como o Machine Learning automatizado e o designer visual. Use a lista a seguir para determinar qual método de treinamento é o ideal para você:

+ [Azure Machine Learning SDK para Python](#python-sdk): O Python SDK fornece várias formas de treinar modelos, cada um com diferentes capacidades.

    | Método de treinamento | Descrição |
    | ----- | ----- |
    | [Configuração de execução](#run-configuration) | Uma **forma genérica de treinar modelos** é usar um roteiro de treino e executar a configuração. A configuração de execução fornece as informações necessárias para configurar o ambiente de treinamento usado para treinar seu modelo. Você pode executar uma configuração de execução, seu script de treinamento e um destino de computação (o ambiente de treinamento) e executar um trabalho de treinamento. |
    | [Aprendizagem automática de máquinas](#automated-machine-learning) | O machine learning automatizado permite-lhe **treinar modelos sem uma ciência de dados extensiva ou conhecimento de programação.** Para pessoas com um plano de fundo de ciência de dados e programação, ele fornece uma maneira de economizar tempo e recursos ao automatizar a seleção de algoritmos e o ajuste de hiperparâmetro. Você não precisa se preocupar em definir uma configuração de execução ao usar o Machine Learning automatizado. |
    | [Estimadores](#estimators) | As classes de estimativa **tornam mais fácil formar modelos com base em estruturas populares de aprendizagem automática.** Existem classes de estimadores para **scikit-learn**, **PyTorch,** **TensorFlow**e **Chainer**. Também há um estimador genérico que pode ser usado com estruturas que ainda não têm uma classe de estimador dedicada. Você não precisa se preocupar em definir uma configuração de execução ao usar os estimadores. |
    | [Gasoduto de aprendizagem automática](#machine-learning-pipeline) | Os gasodutos não são um método de treino diferente, mas uma **forma de definir um fluxo de trabalho utilizando passos modulares e reutilizáveis,** que podem incluir a formação como parte do fluxo de trabalho. Os pipelines do Machine Learning dão suporte ao uso de aprendizado de máquina automatizado, avaliadores e configurações de execução para treinar modelos. Como os pipelines não se concentram especificamente no treinamento, os motivos para usar um pipeline são mais variados do que os outros métodos de treinamento. Em geral, você pode usar um pipeline quando:<br>* Pretende **agendar processos sem supervisão,** tais como trabalhos de formação de longa duração ou preparação de dados.<br>* Utilize **vários passos** que são coordenados através de recursos computacionais heterogéneos e locais de armazenamento.<br>* Utilize o gasoduto como **modelo reutilizável** para cenários específicos, tais como reconversão ou pontuação de lotes.<br>* rastrear e ver ver fontes de **dados, inputs e saídas** para o seu fluxo de trabalho.<br>* O seu fluxo de trabalho é **implementado por diferentes equipas que trabalham em passos específicos de forma independente**. As etapas podem ser Unidas juntas em um pipeline para implementar o fluxo de trabalho. |

+ **Designer**: Azure Machine Learning designer (pré-visualização) fornece um ponto de entrada fácil no machine learning para construir provas de conceitos, ou para utilizadores com pouca experiência de codificação. Ele permite treinar modelos usando uma interface do usuário baseada na Web de arrastar e soltar. Você pode usar o código do Python como parte do design ou treinar modelos sem escrever nenhum código.

+ **CLI**: O CLI de aprendizagem automática fornece comandos para tarefas comuns com o Azure Machine Learning, e é frequentemente utilizado para **scripts e tarefas automatizadas.** Por exemplo, depois de criar um script ou pipeline de treinamento, você pode usar a CLI para iniciar uma execução de treinamento em um agendamento ou quando os arquivos de dados usados para treinamento forem atualizados. Para modelos de treinamento, ele fornece comandos que enviam trabalhos de treinamento. Ele pode enviar trabalhos usando configurações de execução ou pipelines.

Cada um desses métodos de treinamento pode usar diferentes tipos de recursos de computação para treinamento. Colectivamente, estes recursos são referidos como [__alvos de cálculo.__ ](concept-azure-machine-learning-architecture.md#compute-targets) Um destino de computação pode ser um computador local ou um recurso de nuvem, como um Azure Machine Learning computação, Azure HDInsight ou uma máquina virtual remota.

## <a name="python-sdk"></a>SDK Python

O SDK do Azure Machine Learning para Python permite que você crie e execute fluxos de trabalho do Machine Learning com Azure Machine Learning. Você pode interagir com o serviço de uma sessão interativa do Python, notebooks Jupyter, Visual Studio Code ou outro IDE.

* [O que é o Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Instalar/atualizar o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Configure um ambiente de desenvolvimento para azure machine learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Configuração de execução

Um trabalho de formação genérico com o Azure Machine Learning pode ser definido usando a [Configuração run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). Em seguida, a configuração de execução é usada, juntamente com seus scripts de treinamento para treinar um modelo em um destino de computação.

Você pode começar com uma configuração de execução para seu computador local e, em seguida, alternar para uma para um destino de computação baseado em nuvem, conforme necessário. Ao alterar o destino de computação, você só altera a configuração de execução usada. Uma execução também registra informações sobre o trabalho de treinamento, como entradas, saídas e logs.

* [O que é uma configuração de execução?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Tutorial: Treine o seu primeiro modelo ML](tutorial-1st-experiment-sdk-train.md)
* [Exemplos: Exemplos de jupyter Notebook de modelos de formação](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [Como: Configurar e utilizar alvos de computação para formação de modelos](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Aprendizagem automática de máquinas

Defina as iterações, configurações de hiperparâmetro, personalização e outras configurações. Durante o treinamento, o Azure Machine Learning tenta algoritmos e parâmetros diferentes em paralelo. O treinamento é interrompido quando atinge os critérios de saída que você definiu. Você não precisa se preocupar em definir uma configuração de execução ao usar os estimadores.

> [!TIP]
> Além do Python SDK, também pode utilizar ml automatizado através do [estúdio Azure Machine Learning.](https://ml.azure.com)

* [O que é aprendizagem automática de máquinas?](concept-automated-ml.md)
* [Tutorial: Crie o seu primeiro modelo de classificação com machine learning automatizado](tutorial-first-experiment-automated-ml.md)
* [Tutorial: Use machine learning automatizado para prever tarifas de táxi](tutorial-auto-train-models.md)
* [Exemplos: Exemplos de Jupyter Notebook para aprendizagem automática de máquinas](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Como: Configurar experiências automatizadas de ML em Python](how-to-configure-auto-train.md)
* [Como: Autotreinar um modelo de previsão de série de tempo](how-to-auto-train-forecast.md)
* [Como: Criar, explorar e implementar experiências automatizadas de aprendizagem automática com [o estúdio Azure Machine Learning](how-to-create-portal-experiments.md)

### <a name="estimators"></a>Estimativas

Os estimadores facilitam o treinamento de modelos usando estruturas de ML populares. Se estiver a usar **scikit-learn**, **PyTorch,** **TensorFlow**ou **Chainer,** deve considerar a utilização de um estimador para treino. Também há um estimador genérico que pode ser usado com estruturas que ainda não têm uma classe de estimador dedicada. Você não precisa se preocupar em definir uma configuração de execução ao usar os estimadores.

* [O que são estimadores?](concept-azure-machine-learning-architecture.md#estimators)
* [Tutorial: Modelos de classificação de imagem de treinam com dados MNIST e scikit-learn usando Azure Machine Learning](tutorial-train-models-with-aml.md)
* [Exemplos: Exemplos de Jupyter Notebook de utilização de estimadores](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [Como: Criar estimadores em formação](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Pipeline do Machine Learning

Os pipelines do Machine Learning podem usar os métodos de treinamento mencionados anteriormente (configuração de execução, estimadores e aprendizado de máquina automatizado). Pipelines são mais sobre a criação de um fluxo de trabalho, de modo que abrangem mais do que apenas o treinamento de modelos. Em um pipeline, você pode treinar um modelo usando o aprendizado de máquina automatizado, estimações ou configurações de execução.

* [O que são os oleodutos ML em Azure Machine Learning?](concept-ml-pipelines.md)
* [Criar e executar gasodutos de aprendizagem automática com Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Tutorial: Utilize pipelines de aprendizagem automática azure para pontuação de lotes](tutorial-pipeline-batch-scoring-classification.md)
* [Exemplos: Exemplos de Jupyter Notebook para gasodutos de aprendizagem automática](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Exemplos: Pipeline com machine learning automatizado](https://aka.ms/pl-automl)
* [Exemplos: Gasoduto com estimadores](https://aka.ms/pl-estimator)

## <a name="azure-machine-learning-designer"></a>Designer de Azure Machine Learning

O designer permite treinar modelos usando uma interface de arrastar e soltar em seu navegador da Web.

+ [O que é o designer?](concept-designer.md)
+ [Tutorial : Prever o preço do automóvel](tutorial-designer-automobile-price-train-score.md)
+ [Regressão: Prever preço](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Classificação: Prever rendimento](how-to-designer-sample-classification-predict-income.md)
+ [Classificação: Prever churn, appetence e up-selling](how-to-designer-sample-classification-churn.md)
+ [Classificação com script R personalizado: Prever atrasos de voo](how-to-designer-sample-classification-flight-delay.md)
+ [Classificação de Texto: Wikipedia SP 500 Dataset](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>CLI

A CLI do Machine Learning é uma extensão para o CLI do Azure. Ele fornece comandos da CLI de plataforma cruzada para trabalhar com Azure Machine Learning. Normalmente, você usa a CLI para automatizar tarefas, como treinar um modelo de aprendizado de máquina.

* [Utilize a extensão CLI para aprendizagem automática azure](reference-azure-machine-learning-cli.md)
* [MLOps em Azure](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>Passos Seguintes

Saiba como [configurar ambientes](how-to-set-up-training-targets.md)de treino.
