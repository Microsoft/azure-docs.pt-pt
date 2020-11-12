---
title: O que é Azure Machine Learning
description: O Azure Machine Learning é uma solução integrada de ciência de dados para cientistas de dados e MLops para modelar e implementar aplicações ML à escala de nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.custom: devx-track-python
ms.openlocfilehash: af38f68a9709b21989ba787fb5b2c623e07dc440
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541288"
---
# <a name="what-is-azure-machine-learning"></a>O que é o Azure Machine Learning?

Neste artigo, você aprende sobre Azure Machine Learning, um ambiente baseado na nuvem que você pode usar para treinar, implantar, automatizar, gerir e rastrear modelos ML. 

O Azure Machine Learning pode ser usado para qualquer tipo de aprendizagem automática, desde o ml clássico à aprendizagem profunda, supervisionado e sem supervisão. Quer prefira escrever Python ou código R com o SDK ou trabalhar com opções sem código/código baixo no [estúdio,](#build-ml-models-in-the-studio)pode construir, treinar e rastrear modelos de aprendizagem automática e de aprendizagem profunda num espaço de trabalho de aprendizagem automática Azure. 

Comece a treinar na sua máquina local e depois escale para a nuvem. 

O serviço também interopera com populares ferramentas de deep learning e reforço de código aberto tais como PyTorch, TensorFlow, scikit-learn, e Ray RLlib. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Julgamento livre!**  Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree) Receberá créditos para gastar em serviços do Azure. Depois de serem utilizados, pode manter a conta e utilizar os [serviços gratuitos do Azure](https://azure.microsoft.com/free/). O seu cartão de crédito não será cobrado, a menos que altere explicitamente as suas definições e peça para ser cobrado.


## <a name="what-is-machine-learning"></a>O que é machine learning?

A aprendizagem automática é uma técnica da ciência de dados que permite aos computadores utilizar dados existentes para prever futuros comportamentos, resultados e tendências. Ao utilizar o machine learning, os computadores aprendem sem serem explicitamente programados.

As previsões ou predições de machine learning podem tornar as aplicações e os dispositivos mais inteligentes. Por exemplo, quando faz compras online, a aprendizagem automática ajuda a recomendar outros produtos que possa querer com base no que comprou. Em alternativa, quando introduz os dados do seu cartão de crédito, a machine learning compara a transação com uma base de dados de transações e ajuda a detetar fraudes. Quando o robô aspirador limpa uma sala, a machine learning ajuda-o a decidir se a tarefa está concluída.

## <a name="machine-learning-tools-to-fit-each-task"></a>Ferramentas de aprendizagem automática para se adaptar a cada tarefa 

A Azure Machine Learning fornece todas as ferramentas que os desenvolvedores e cientistas de dados precisam para os seus fluxos de trabalho de aprendizagem automática, incluindo:
+ O [designer Azure Machine Learning](tutorial-designer-automobile-price-train-score.md): módulos de arrastar-n-drop para construir as suas experiências e, em seguida, implementar oleodutos.

+ Cadernos Jupyter: use os [nossos cadernos de exemplo](https://github.com/Azure/MachineLearningNotebooks) ou crie os seus próprios cadernos para aproveitar as nossas amostras <a href="/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">de SDK para as</a> amostras de Python para a sua aprendizagem automática. 

+ Scripts r ou cadernos nos quais você usa o <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK para R</a> para escrever o seu próprio código, ou usar os módulos R no designer.

+ + O [Acelerador de Soluções de Muitos Modelos](https://aka.ms/many-models) (pré-visualização) baseia-se no Azure Machine Learning e permite-lhe treinar, operar e gerir centenas ou mesmo milhares de modelos de machine learning.

+ [Extensão do Visual Studio Code](tutorial-setup-vscode-extension.md)

+ [CLI de Aprendizagem automática](reference-azure-machine-learning-cli.md)

+ Quadros de código aberto como PyTorch, TensorFlow, e scikit-learn e muito mais

+ [Aprendizagem de reforço](how-to-use-reinforcement-learning.md) com Ray RLlib

Pode até utilizar [o MLflow para rastrear métricas e implementar modelos](how-to-use-mlflow.md) ou Kubeflow para [construir gasodutos de fluxo de trabalho de ponta a ponta](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-in-python-or-r"></a>Construa modelos ML em Python ou R

Comece a treinar na sua máquina local utilizando o Azure Machine Learning <a href="/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK</a> ou <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK</a>. Depois, pode escalar para a nuvem. 

Com [muitos alvos](how-to-create-attach-compute-studio.md)de computação disponíveis , como o Azure Machine Learning Compute e [o Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks), e com serviços avançados de [afinação de hiperparímetros,](how-to-tune-hyperparameters.md)é possível construir melhores modelos mais rapidamente utilizando a potência da nuvem.

Também pode [automatizar o treino e afinação](tutorial-auto-train-models.md) de modelos utilizando o SDK.

## <a name="build-ml-models-in-the-studio"></a>Construa modelos ML no estúdio

[O Azure Machine Learning studio](https://studio.azureml.net) é um portal web em Azure Machine Learning para opções de baixo código e sem código para formação de modelos, implementação e gestão de ativos. O estúdio integra-se com o Azure Machine Learning SDK para uma experiência perfeita. Para mais informações, consulte [o estúdio Azure Machine Learning.](overview-what-is-machine-learning-studio.md)

+ **Estruturador do Azure Machine Learning**

  Utilize [o designer](concept-designer.md) para treinar e implementar modelos de aprendizagem automática sem escrever nenhum código. Experimente o tutorial do [designer](tutorial-designer-automobile-price-train-score.md) para começar. 

  ![Gif animado da interface drag-and-drop do designer de Aprendizagem de Máquinas Azure](media/concept-designer/designer-drag-and-drop.gif)

+ **Experiências de rastreio**

  Aprenda a [rastrear e visualizar experiências de ciência de dados](tutorial-first-experiment-automated-ml.md) no estúdio. 

    ![Execute detalhes no estúdio Azure Machine Learning](media/how-to-track-experiments/experimentation-tab.gif)


+ **E muito mais...**

  Visite o estúdio Azure Machine Learning na [ml.azure.com.](https://studio.azureml.net) 


## <a name="mlops-deploy--lifecycle-management"></a>MLOps: Implementar & gestão do ciclo de vida
Quando tiver o modelo certo, pode facilmente usá-lo num serviço web, num dispositivo IoT ou na Power BI. Para mais informações, consulte o artigo sobre [como implementar e onde](how-to-deploy-and-where.md).

Em seguida, pode gerir os seus modelos implantados utilizando o [Azure Machine Learning SDK para Python,](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py) [o estúdio Azure Machine Learning](https://ml.azure.com)ou o [CLI de aprendizagem automática](reference-azure-machine-learning-cli.md).

Estes modelos podem ser consumidos e as previsões de retorno em [tempo real](how-to-consume-web-service.md) ou [assíncrona](./tutorial-pipeline-batch-scoring-classification.md) em grandes quantidades de dados.

E com oleodutos de aprendizagem automática avançados, pode colaborar em cada passo desde a preparação de [dados,](concept-ml-pipelines.md)formação de modelos e avaliação, através da implantação. Os gasodutos permitem-lhe:

* Automatize o processo de aprendizagem automática de ponta a ponta na nuvem
* Reutilizar componentes e apenas refazer etapas quando necessário
* Use diferentes recursos computacional em cada passo
* Executar tarefas de pontuação de lotes

Se pretender utilizar scripts para automatizar o fluxo de trabalho de aprendizagem automática, o CLI de [aprendizagem automática](reference-azure-machine-learning-cli.md) fornece ferramentas de linha de comando que executam tarefas comuns, tais como submeter uma corrida de treino ou implementar um modelo.

Para começar a utilizar o Azure Machine Learning, consulte [os próximos passos](#next-steps).

## <a name="integration-with-other-services"></a>Integração noutros serviços

A Azure Machine Learning trabalha com outros serviços na plataforma Azure, e também integra com ferramentas de código aberto como Git e MLFlow.

+ Metas de computação como __Azure Kubernetes Service__ , __Azure Container Instances__ , __Azure Databricks__ , __Azure Data Lake Analytics__ , e __Azure HDInsight__. Para obter mais informações sobre os alvos de computação, veja [quais são os alvos de computação?](concept-compute-target.md)
+ __Grelha de eventos Azure__. Para mais informações, consulte [os eventos consume Azure Machine Learning](./how-to-use-event-grid.md).
+ __Monitor Azure__. Para obter mais informações, consulte [monitorar a Azure Machine Learning](monitor-azure-machine-learning.md).
+ Lojas de dados como __contas de armazenamento Azure,__ __Azure Data Lake Storage__ , __Azure SQL Database__ , __Azure Database for PostgreSQL__ , e __Azure Open Datasets__. Para obter mais informações, consulte [os dados do Access nos serviços de armazenamento Azure](how-to-access-data.md) e [crie conjuntos de dados com conjuntos de dados Azure Open](how-to-create-register-datasets.md).
+ __Redes Virtuais Azure__. Para mais informações, consulte [o isolamento da rede virtual e a visão geral da privacidade.](how-to-network-security-overview.md)
+ __Gasodutos Azure__. Para obter mais informações, consulte [Train e implemente modelos de machine learning](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Troncos de repositório de Git.__ Para mais informações, consulte [a integração de Git.](concept-train-model-git-integration.md)
+ __MLFlow__. Para obter mais informações, consulte [mLflow para rastrear métricas e implementar modelos](how-to-use-mlflow.md) 
+ __Kubeflow.__ Para obter mais informações, consulte [os gasodutos de fluxo de trabalho de ponta a ponta.](https://www.kubeflow.org/docs/azure/)

### <a name="secure-communications"></a>Comunicações seguras

A sua conta de Armazenamento Azure, metas de computação e outros recursos podem ser utilizados de forma segura dentro de uma rede virtual para treinar modelos e realizar inferência. Para mais informações, consulte [o isolamento da rede virtual e a visão geral da privacidade.](how-to-network-security-overview.md)

## <a name="next-steps"></a>Passos seguintes

- Crie a sua primeira experiência com o seu método preferido:
- + [Começar no seu próprio ambiente de desenvolvimento](tutorial-1st-experiment-sdk-setup-local.md)
  + [Use cadernos Jupyter em uma instância de computação para treinar & implementar modelos ML](tutorial-1st-experiment-sdk-setup.md)
  + [Use R Markdown para treinar & implementar modelos ML](tutorial-1st-r-experiment.md) 
  + [Utilize machine learning automatizado para treinar & implementar modelos ML](tutorial-first-experiment-automated-ml.md) 
  + [Use as capacidades de arrastar & de arrastar do designer para treinar & implementar](tutorial-designer-automobile-price-train-score.md) 
  + [Use o CLI de aprendizagem automática para treinar e implementar um modelo](tutorial-train-deploy-model-cli.md)

- Saiba mais sobre [pipelines de aprendizagem automática](concept-ml-pipelines.md) para criar, otimizar e gerir cenários da aprendizagem automática.

- Leia o artigo de arquitetura e conceitos de [aprendizagem de máquinas Azure.](concept-azure-machine-learning-architecture.md)
