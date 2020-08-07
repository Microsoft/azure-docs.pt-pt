---
title: Construa modelos de comboio &
titleSuffix: Azure Machine Learning
description: Aprenda os diferentes métodos que pode usar para treinar modelo com Azure Machine Learning. Os estimadores fornecem uma maneira fácil de trabalhar com estruturas populares como Scikit-learn, TensorFlow, Keras, PyTorch e Chainer. Os oleodutos machine learning facilitam o agendamento de corridas sem supervisão, utilizam ambientes de computação heterogéneo e reutilizam partes do seu fluxo de trabalho. E as configurações de execução fornecem controlo granular sobre os alvos de computação que o processo de treino funciona.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 05/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: dee74c787f6546494d12ea582eab383fbd99079d
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876908"
---
# <a name="train-models-with-azure-machine-learning"></a>Modelos de trem com Azure Machine Learning

O Azure Machine Learning fornece várias formas de treinar os seus modelos, desde as primeiras soluções de código que utilizam o SDK até soluções de código baixo, como a aprendizagem automática de máquinas e o designer visual. Utilize a seguinte lista para determinar qual o método de treino adequado para si:

+ [Azure Machine Learning SDK para Python](#python-sdk): O Python SDK oferece várias formas de treinar modelos, cada um com diferentes capacidades.

    | Método de formação | Descrição |
    | ----- | ----- |
    | [Configuração de execução](#run-configuration) | Uma **forma genérica de treinar modelos** é usar um script de treino e executar a configuração. A configuração de execução fornece as informações necessárias para configurar o ambiente de treino utilizado para treinar o seu modelo. Você pode fazer uma configuração de execução, seu script de treino, e um alvo de computação (o ambiente de treino) e executar um trabalho de treino. |
    | [Aprendizagem automática automatizada](#automated-machine-learning) | A aprendizagem automática de máquinas **permite-lhe formar modelos sem uma vasta ciência de dados ou conhecimentos de programação.** Para pessoas com um fundo de ciência de dados e programação, ele fornece uma maneira de economizar tempo e recursos automatizando a seleção de algoritmos e afinação hiperparítmica. Não tem de se preocupar em definir uma configuração de execução quando se utiliza aprendizagem automática de máquinas. |
    | [Estimadores](#estimators) | As aulas de estimadores **facilitam a formação de modelos baseados em quadros populares de aprendizagem automática.** Existem aulas de estimadores para **Scikit-learn**, **PyTorch,** **TensorFlow,** **Chainer**e **Ray RLlib.** Há também um estimador genérico que pode ser usado com quadros que ainda não têm uma classe de estimador dedicado. Não tem de se preocupar em definir uma configuração de execução quando se utiliza estimativas. |
    | [Gasoduto de aprendizagem automática](#machine-learning-pipeline) | Os gasodutos não são um método de treino diferente, mas uma **forma de definir um fluxo de trabalho utilizando passos modulares e reutilizáveis,** que podem incluir a formação como parte do fluxo de trabalho. Os oleodutos de aprendizagem automática suportam utilizando machine learning automatizado, estimadores e configuração de execução para treinar modelos. Uma vez que os gasodutos não se concentram especificamente na formação, as razões da utilização de um gasoduto são mais variadas do que os outros métodos de formação. Geralmente, pode utilizar um oleoduto quando:<br>* Pretende **agendar processos não acompanhados,** tais como trabalhos de formação de longa duração ou preparação de dados.<br>* Utilize **vários passos** que são coordenados através de recursos de computação heterogéneos e locais de armazenamento.<br>* Utilize o gasoduto como **modelo reutilizável** para cenários específicos, tais como a reconversão ou a pontuação do lote.<br>* **Rastrear e verrativas fontes de dados, entradas e saídas** para o seu fluxo de trabalho.<br>* O seu fluxo de trabalho é **implementado por diferentes equipas que trabalham em passos específicos de forma independente**. Os passos podem então ser unidos num oleoduto para implementar o fluxo de trabalho. |

+ [Azure Machine Learning SDK para R](#r-sdk): O SDK para R utiliza o pacote reticulado para se ligar ao Python SDK da Azure Machine Learning. Isto permite-lhe aceder a objetos e métodos principais implementados no Python SDK a partir de qualquer ambiente R.

+ **Designer**: O designer de machine learning (pré-visualização) proporciona um fácil ponto de entrada na aprendizagem automática para construir a prova de conceitos, ou para utilizadores com pouca experiência de codificação. Permite-lhe treinar modelos usando uma UI baseada na web. Você pode usar o código Python como parte do design, ou modelos de comboio sem escrever qualquer código.

+ **CLI**: O CLI de aprendizagem automática fornece comandos para tarefas comuns com a Azure Machine Learning, e é frequentemente utilizado para **scripts e automatização de tarefas.** Por exemplo, uma vez criado um script de treino ou um pipeline, poderá utilizar o CLI para iniciar uma corrida de treino num horário ou quando os ficheiros de dados utilizados para a formação forem atualizados. Para modelos de formação, fornece comandos que submetem trabalhos de formação. Pode submeter trabalhos utilizando configurações ou oleodutos de execução.

Cada um destes métodos de treino pode utilizar diferentes tipos de recursos computacional para a formação. Colectivamente, estes recursos são referidos como [__objetivos de computação.__](concept-azure-machine-learning-architecture.md#compute-targets) Um alvo de computação pode ser uma máquina local ou um recurso em nuvem, como um Azure Machine Learning Compute, Azure HDInsight ou uma máquina virtual remota.

## <a name="python-sdk"></a>SDK Python

O Azure Machine Learning SDK para Python permite-lhe construir e executar fluxos de trabalho de aprendizagem automática com Azure Machine Learning. Você pode interagir com o serviço a partir de uma sessão interativa python, Cadernos Jupyter, Código de Estúdio Visual, ou outro IDE.

* [O que é o Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Instalar/atualizar o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Configure um ambiente de desenvolvimento para a aprendizagem automática Azure](how-to-configure-environment.md)

### <a name="run-configuration"></a>Configuração de execução

Um trabalho de formação genérico com Azure Machine Learning pode ser definido usando a [Configuração run.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) A configuração de execução é então usada, juntamente com o seu(s script(s) de treino para treinar um modelo num alvo de computação.

Pode começar com uma configuração de execução para o seu computador local e, em seguida, mudar para um para um alvo de computação baseado na nuvem, conforme necessário. Ao alterar o alvo do cálculo, só altera a configuração de execução que utiliza. Uma corrida também regista informações sobre o trabalho de formação, como as entradas, saídas e registos.

* [O que é uma configuração de execução?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Tutorial: Treine o seu primeiro modelo ML](tutorial-1st-experiment-sdk-train.md)
* [Exemplos: Exemplos do Jupyter Notebook de modelos de formação](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [Como: Configurar e utilizar metas de computação para a formação de modelos](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Machine Learning Automatizado

Defina as iterações, as definições do hiperparímetro, a aparição e outras definições. Durante o treino, a Azure Machine Learning tenta diferentes algoritmos e parâmetros em paralelo. O treino para assim que atinge os critérios de saída definidos. Não tem de se preocupar em definir uma configuração de execução quando se utiliza estimativas.

> [!TIP]
> Além do Python SDK, você também pode usar ML automatizado através do [estúdio Azure Machine Learning.](https://ml.azure.com)

* [O que é o machine learning automatizado?](concept-automated-ml.md)
* [Tutorial: Crie o seu primeiro modelo de classificação com aprendizagem automática de máquinas](tutorial-first-experiment-automated-ml.md)
* [Tutorial: Use machine learning automatizado para prever tarifas de táxi](tutorial-auto-train-models.md)
* [Exemplos: Exemplos do Jupyter Notebook para aprendizagem automática de máquinas](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Como: Configurar experiências automatizadas de ML em Python](how-to-configure-auto-train.md)
* [Como: Autotrain um modelo de previsão de séries tempor as vezes](how-to-auto-train-forecast.md)
* [Como: Criar, explorar e implementar experiências automatizadas de aprendizagem automática com o estúdio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>Estimadores

Os estimadores facilitam a formação de modelos utilizando quadros ML populares. Se estiver a utilizar **Scikit-learn**, **PyTorch,** **TensorFlow,** **Chainer**ou **Ray RLlib,** deve considerar usar um estimador para o treino. Há também um estimador genérico que pode ser usado com quadros que ainda não têm uma classe de estimador dedicado. Não tem de se preocupar em definir uma configuração de execução quando se utiliza estimativas.

* [O que são estimadores?](concept-azure-machine-learning-architecture.md#estimators)
* [Tutorial: Modelos de classificação de imagem de comboio com dados do MNIST e scikit-learn usando Azure Machine Learning](tutorial-train-models-with-aml.md)
* [Exemplos: Exemplos do Jupyter Notebook de utilização de estimadores](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [Como: Criar estimadores em formação](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Gasoduto de aprendizagem automática

Os oleodutos de aprendizagem automática podem utilizar os métodos de treino anteriormente mencionados (configuração de execução, estimadores e aprendizagem automática de máquinas). Os gasodutos são mais sobre a criação de um fluxo de trabalho, por isso englobam mais do que apenas a formação de modelos. Num oleoduto, pode treinar um modelo utilizando máquinas automáticas de aprendizagem, estimadores ou configurações de execução.

* [O que são os oleodutos ML em Azure Machine Learning?](concept-ml-pipelines.md)
* [Criar e executar gasodutos de aprendizagem automática com Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Tutorial: Use gasodutos de aprendizagem de máquinas Azure para pontuação de lotes](tutorial-pipeline-batch-scoring-classification.md)
* [Exemplos: Exemplos do Jupyter Notebook para oleodutos de aprendizagem automática](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Exemplos: Pipeline com aprendizagem automática de máquinas](https://aka.ms/pl-automl)
* [Exemplos: Pipeline com estimadores](https://aka.ms/pl-estimator)

### <a name="understand-what-happens-when-you-submit-a-training-job"></a>Entenda o que acontece quando se submete um trabalho de formação

O ciclo de vida de formação Azure consiste em:

1. Fechar os ficheiros na pasta do projeto, ignorando os especificados em _.amlignore_ ou _.gitignore_
1. Escalonando o seu cluster de computação 
1. Construindo ou descarregando o estival para o nó computacional 
    1. O sistema calcula um haxixe de: 
        - A imagem base 
        - Passos personalizados do estivador (ver [Implementar um modelo utilizando uma imagem base personalizada do Docker)](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image)
        - A definição conda YAML (ver [Criar & utilizar ambientes de software em Azure Machine Learning)](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments)
    1. O sistema utiliza este haxixe como chave numa procura do espaço de trabalho Registo do Contentor Azure (ACR)
    1. Se não for encontrado, procura um jogo no ACR global
    1. Se não for encontrado, o sistema constrói uma nova imagem (que será em cache e registada com o espaço de trabalho ACR)
1. Baixar o ficheiro do projeto zipped para armazenamento temporário no nó de computação
1. Desapertar o ficheiro do projeto
1. A execução do nó computativo`python <entry script> <arguments>`
1. Guardar registos, ficheiros de modelos e outros ficheiros escritos `./outputs` para a conta de armazenamento associada ao espaço de trabalho
1. Escalonamento do cálculo, incluindo a remoção do armazenamento temporário 

Se optar por treinar na sua máquina local ("configurar como corrida local"), não precisa de usar o Docker. Pode utilizar o Docker localmente se escolher (consulte a secção [Configure o gasoduto ML](https://docs.microsoft.com/azure/machine-learning/how-to-debug-pipelines#configure-ml-pipeline ) por exemplo).

## <a name="r-sdk"></a>SDK para R

O R SDK permite-lhe utilizar a linguagem R com Azure Machine Learning. O SDK usa o pacote reticulado para ligar-se ao Python SDK da Azure Machine Learning. Isto dá-lhe acesso a objetos e métodos principais implementados no Python SDK a partir de qualquer ambiente R.

Para obter mais informações, veja os seguintes artigos:

* [Tutorial: Criar um modelo de regressão logística](tutorial-1st-r-experiment.md)
* [Azure Machine Learning SDK para referência R](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Designer de aprendizagem de máquinas Azure

O designer permite-lhe treinar modelos usando uma interface de arrasto e queda no seu navegador web.

+ [O que é o designer?](concept-designer.md)
+ [Tutorial: Prever preço do automóvel](tutorial-designer-automobile-price-train-score.md)
+ [Regressão: Prever preço](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Classificação: Prever rendimentos](how-to-designer-sample-classification-predict-income.md)
+ [Classificação: Prever churn, appetency e up-selling](how-to-designer-sample-classification-churn.md)
+ [Classificação com script R personalizado: Prever atrasos de voo](how-to-designer-sample-classification-flight-delay.md)
+ [Classificação de texto: Wikipedia SP 500 Dataset](how-to-designer-sample-text-classification.md)

## <a name="many-models-solution-accelerator"></a>Muitos modelos acelerador de solução

O [Acelerador de Soluções de Muitos Modelos](https://aka.ms/many-models) (pré-visualização) baseia-se no Azure Machine Learning e permite-lhe treinar, operar e gerir centenas ou mesmo milhares de modelos de machine learning.

Por exemplo, a construção de um modelo __para cada instância ou indivíduo__ nos seguintes cenários pode levar a melhores resultados:

* Previsão de vendas para cada loja individual
* Manutenção preditiva para centenas de poços de petróleo
* Adaptação de uma experiência para utilizadores individuais.

Para mais informações, consulte o [Acelerador de Soluções de Muitos Modelos](https://aka.ms/many-models) no GitHub.

## <a name="cli"></a>CLI

O CLI de aprendizagem automática é uma extensão para o CLI Azure. Fornece comandos CLI de plataforma cruzada para trabalhar com a Azure Machine Learning. Normalmente, você usa o CLI para automatizar tarefas, como treinar um modelo de aprendizagem automática.

* [Utilize a extensão CLI para aprendizagem automática Azure](reference-azure-machine-learning-cli.md)
* [MLOps em Azure](https://github.com/microsoft/MLOps)

## <a name="vs-code"></a>Código VS

Pode utilizar a extensão do Código VS para executar e gerir os seus trabalhos de formação. Consulte o [guia de gestão de recursos do Código VS para](how-to-manage-resources-vscode.md#experiments) saber mais.

## <a name="next-steps"></a>Passos seguintes

Saiba como [configurar ambientes de treino.](how-to-set-up-training-targets.md)
