---
title: Métodos de formação de modelos
titleSuffix: Azure Machine Learning
description: Aprenda os diferentes métodos que pode usar para treinar modelo com o Azure Machine Learning. Os estimadores fornecem uma maneira fácil de trabalhar com estruturas populares como Scikit-learn, TensorFlow, Keras, PyTorch e Chainer. Os gasodutos de Machine Learning facilitam o agendamento de corridas sem supervisão, utilizam ambientes computação heterogéneos e reutilizam partes do seu fluxo de trabalho. E as configurações de execução fornecem controlo granular sobre os alvos da computação que o processo de treino continua.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 68b7dfbf61f5d353f394bfbc034e00fddd262cd1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78344017"
---
# <a name="train-models-with-azure-machine-learning"></a>Utilizar modelos com o Azure Machine Learning

O Azure Machine Learning fornece várias formas de treinar os seus modelos, desde as primeiras soluções de código utilizando o SDK até soluções de código baixo, como o machine learning automatizado e o designer visual. Utilize a seguinte lista para determinar qual o método de treino certo para si:

+ [Azure Machine Learning SDK para Python](#python-sdk): O Python SDK fornece várias formas de treinar modelos, cada um com diferentes capacidades.

    | Método de formação | Descrição |
    | ----- | ----- |
    | [Configuração de execução](#run-configuration) | Uma **forma genérica de treinar modelos** é usar um roteiro de treino e executar a configuração. A configuração de execução fornece as informações necessárias para configurar o ambiente de treino usado para treinar o seu modelo. Você pode pegar uma configuração de execução, seu script de treino, e um alvo de computação (o ambiente de treino) e executar um trabalho de treino. |
    | [Aprendizagem automática de máquinas](#automated-machine-learning) | O machine learning automatizado permite-lhe **treinar modelos sem uma ciência de dados extensiva ou conhecimento de programação.** Para pessoas com um fundo de ciência de dados e programação, fornece uma forma de poupar tempo e recursos automatizando a seleção de algoritmos e a finação de hiperparâmetros. Não é preciso preocupar-se em definir uma configuração de execução ao utilizar aprendizagem automática de máquinas. |
    | [Estimadores](#estimators) | As classes de estimativa **tornam mais fácil formar modelos com base em estruturas populares de aprendizagem automática.** Existem classes de estimadores para **scikit-learn**, **PyTorch,** **TensorFlow**e **Chainer**. Há também um estimador genérico que pode ser usado com quadros que ainda não têm uma classe estimada dedicada. Não tem que se preocupar em definir uma configuração de execução ao usar estimadores. |
    | [Gasoduto de aprendizagem automática](#machine-learning-pipeline) | Os gasodutos não são um método de treino diferente, mas uma **forma de definir um fluxo de trabalho utilizando passos modulares e reutilizáveis,** que podem incluir a formação como parte do fluxo de trabalho. Os gasodutos de aprendizagem automática suportam a utilização de machine learning automatizado, estimadores e configuração de execução para treinar modelos. Uma vez que os gasodutos não se centram especificamente na formação, as razões da utilização de um gasoduto são mais variadas do que os outros métodos de formação. Geralmente, pode usar um oleoduto quando:<br>* Pretende **agendar processos sem supervisão,** tais como trabalhos de formação de longa duração ou preparação de dados.<br>* Utilize **vários passos** que são coordenados através de recursos computacionais heterogéneos e locais de armazenamento.<br>* Utilize o gasoduto como **modelo reutilizável** para cenários específicos, tais como reconversão ou pontuação de lotes.<br>* rastrear e ver ver fontes de **dados, inputs e saídas** para o seu fluxo de trabalho.<br>* O seu fluxo de trabalho é **implementado por diferentes equipas que trabalham em passos específicos de forma independente**. Os passos podem então ser unidos num oleoduto para implementar o fluxo de trabalho. |

+ [Azure Machine Learning SDK para Python](#r-sdk): O SDK usa o pacote reticulado para se ligar ao Python SDK da Azure Machine Learning. Isto permite-lhe aceder a objetos e métodos fundamentais implementados no SDK Python a partir de qualquer ambiente R.

+ **Designer**: Azure Machine Learning designer (pré-visualização) fornece um ponto de entrada fácil no machine learning para construir provas de conceitos, ou para utilizadores com pouca experiência de codificação. Permite-lhe treinar modelos usando um UI baseado na web. Pode utilizar o código Python como parte do design, ou treinar modelos sem escrever qualquer código.

+ **CLI**: O CLI de aprendizagem automática fornece comandos para tarefas comuns com o Azure Machine Learning, e é frequentemente utilizado para **scripts e tarefas automatizadas.** Por exemplo, uma vez criado um script ou pipeline de treino, pode utilizar o CLI para iniciar uma execução de treino num horário ou quando os ficheiros de dados utilizados para o treino forem atualizados. Para modelos de formação, fornece comandos que submetem trabalhos de formação. Pode submeter trabalhos utilizando configurações de execução ou oleodutos.

Cada um destes métodos de formação pode utilizar diferentes tipos de recursos computacionais para a formação. Colectivamente, estes recursos são referidos como [__alvos de cálculo.__ ](concept-azure-machine-learning-architecture.md#compute-targets) Um alvo computacional pode ser uma máquina local ou um recurso em nuvem, como uma Computação de Aprendizagem automática Azure, Azure HDInsight ou uma máquina virtual remota.

## <a name="python-sdk"></a>SDK Python

O Azure Machine Learning SDK para Python permite-lhe construir e executar fluxos de trabalho de aprendizagem automática com o Azure Machine Learning. Você pode interagir com o serviço a partir de uma sessão interativa python, Cadernos Jupyter, Código de Estúdio Visual, ou outro IDE.

* [O que é o Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Instalar/atualizar o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Configure um ambiente de desenvolvimento para azure machine learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Configuração de execução

Um trabalho de formação genérico com o Azure Machine Learning pode ser definido usando a [Configuração run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). A configuração do percurso é então utilizada, juntamente com os seus scripts de treino para treinar um modelo num alvo de cálculo.

Pode começar com uma configuração de execução para o seu computador local e, em seguida, mudar para um para um alvo de computação baseado em nuvem, conforme necessário. Ao alterar o alvo da computação, apenas altere a configuração de execução que utiliza. Uma corrida também regista informações sobre o trabalho de formação, tais como as inputs, saídas e registos.

* [O que é uma configuração de execução?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Tutorial: Treine o seu primeiro modelo ML](tutorial-1st-experiment-sdk-train.md)
* [Exemplos: Exemplos de jupyter Notebook de modelos de formação](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [Como: Configurar e utilizar alvos de computação para formação de modelos](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Aprendizagem automática de máquinas

Defina as iterações, as definições do hiperparâmetro, a caracterização e outras definições. Durante o treino, o Azure Machine Learning tenta diferentes algoritmos e parâmetros em paralelo. O treino para assim que atinge os critérios de saída que definiu. Não tem que se preocupar em definir uma configuração de execução ao usar estimadores.

> [!TIP]
> Além do Python SDK, também pode utilizar ml automatizado através do [estúdio Azure Machine Learning.](https://ml.azure.com)

* [O que é o machine learning automatizado?](concept-automated-ml.md)
* [Tutorial: Crie o seu primeiro modelo de classificação com machine learning automatizado](tutorial-first-experiment-automated-ml.md)
* [Tutorial: Use machine learning automatizado para prever tarifas de táxi](tutorial-auto-train-models.md)
* [Exemplos: Exemplos de Jupyter Notebook para aprendizagem automática de máquinas](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Como: Configurar experiências automatizadas de ML em Python](how-to-configure-auto-train.md)
* [Como: Autotreinar um modelo de previsão de série de tempo](how-to-auto-train-forecast.md)
* [Como: Criar, explorar e implementar experiências automatizadas de aprendizagem automática com [o estúdio Azure Machine Learning](how-to-create-portal-experiments.md)

### <a name="estimators"></a>Estimadores

Os estimadores facilitam a formação de modelos utilizando estruturas populares de ML. Se estiver a usar **scikit-learn**, **PyTorch,** **TensorFlow**ou **Chainer,** deve considerar a utilização de um estimador para treino. Há também um estimador genérico que pode ser usado com quadros que ainda não têm uma classe estimada dedicada. Não tem que se preocupar em definir uma configuração de execução ao usar estimadores.

* [O que são estimadores?](concept-azure-machine-learning-architecture.md#estimators)
* [Tutorial: Modelos de classificação de imagem de treinam com dados MNIST e scikit-learn usando Azure Machine Learning](tutorial-train-models-with-aml.md)
* [Exemplos: Exemplos de Jupyter Notebook de utilização de estimadores](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [Como: Criar estimadores em formação](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Gasoduto de aprendizagem automática

Os gasodutos de aprendizagem automática podem utilizar os métodos de formação anteriormente mencionados (configuração de execução, estimadores e aprendizagem automática de máquinas). Os oleodutos são mais sobre a criação de um fluxo de trabalho, por isso englobam mais do que apenas a formação de modelos. Num oleoduto, pode treinar um modelo utilizando machine learning, estimadores ou configurações de execução automatizadas.

* [O que são os oleodutos ML em Azure Machine Learning?](concept-ml-pipelines.md)
* [Criar e executar gasodutos de aprendizagem automática com Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Tutorial: Utilize pipelines de aprendizagem automática azure para pontuação de lotes](tutorial-pipeline-batch-scoring-classification.md)
* [Exemplos: Exemplos de Jupyter Notebook para gasodutos de aprendizagem automática](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Exemplos: Pipeline com machine learning automatizado](https://aka.ms/pl-automl)
* [Exemplos: Gasoduto com estimadores](https://aka.ms/pl-estimator)

## <a name="r-sdk"></a>SDK para R

O R SDK permite-lhe utilizar a linguagem R com aprendizagem automática azure. O SDK usa o pacote reticulado para se ligar ao Python SDK da Azure Machine Learning. Isto permite-lhe aceder a objetos e métodos fundamentais implementados no SDK Python a partir de qualquer ambiente R.

Para obter mais informações, veja os artigos seguintes:

* [Tutorial: Criar um modelo de regressão logística](tutorial-1st-r-experiment.md)
* [Azure Machine Learning SDK para referência R](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Designer de aprendizagem automática Azure

O designer permite-lhe treinar modelos usando uma interface de arrasto e gota no seu navegador web.

+ [O que é o designer?](concept-designer.md)
+ [Tutorial : Prever o preço do automóvel](tutorial-designer-automobile-price-train-score.md)
+ [Regressão: Prever preço](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Classificação: Prever rendimento](how-to-designer-sample-classification-predict-income.md)
+ [Classificação: Prever churn, appetence e up-selling](how-to-designer-sample-classification-churn.md)
+ [Classificação com script R personalizado: Prever atrasos de voo](how-to-designer-sample-classification-flight-delay.md)
+ [Classificação de Texto: Wikipedia SP 500 Dataset](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>CLI

O CLI de aprendizagem automática é uma extensão para o ClI Azure. Fornece comandos CLI de plataforma cruzada para trabalhar com o Azure Machine Learning. Normalmente, você usa o CLI para automatizar tarefas, tais como treinar um modelo de aprendizagem automática.

* [Utilize a extensão CLI para aprendizagem automática azure](reference-azure-machine-learning-cli.md)
* [MLOps em Azure](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>Passos seguintes

Saiba como [configurar ambientes](how-to-set-up-training-targets.md)de treino.
