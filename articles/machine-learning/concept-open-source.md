---
title: Integração de aprendizagem automática de código aberto
titleSuffix: Azure Machine Learning
description: Aprenda a usar quadros de aprendizagem automática python de código aberto para treinar, implementar e gerir soluções de aprendizagem automática de ponta a ponta em Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 01/14/2020
ms.openlocfilehash: 983e037376be48f497118b06cce8b23c430b1501
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223079"
---
# <a name="open-source-integration-with-azure-machine-learning-projects"></a>Integração de código aberto com projetos de Aprendizagem automática Azure

Você pode treinar, implementar e gerir o processo de aprendizagem automática de ponta a ponta em Azure Machine Learning usando bibliotecas e plataformas de aprendizagem de máquinas python de código aberto.  Utilize ferramentas de desenvolvimento, como os Jupyter Notebooks e o Visual Studio Code, para alavancar os seus modelos e scripts existentes no Azure Machine Learning.  

Neste artigo, saiba mais sobre estas bibliotecas e plataformas de código aberto.

## <a name="train-open-source-machine-learning-models"></a>Treine modelos de aprendizagem automática de código aberto

O processo de formação de machine learning envolve a aplicação de algoritmos aos seus dados de forma a conseguir uma tarefa ou resolver um problema. Dependendo do problema, pode escolher diferentes algoritmos que melhor se adaptem à tarefa e aos seus dados. Para obter mais informações sobre os diferentes ramos da aprendizagem automática, consulte o artigo de [aprendizagem profunda vs machine learning](./concept-deep-learning-vs-machine-learning.md) e a folha de batota do algoritmo de [aprendizagem automática.](algorithm-cheat-sheet.md)

### <a name="preserve-data-privacy-using-differential-privacy"></a>Preservar a privacidade dos dados usando privacidade diferencial

Para treinar um modelo de aprendizagem automática, é preciso dados. Às vezes esses dados são sensíveis, e é importante ter certeza de que os dados são seguros e privados. A privacidade diferencial é uma técnica de preservação da confidencialidade da informação num conjunto de dados. Para saber mais, consulte o artigo sobre [a preservação da privacidade dos dados.](concept-differential-privacy.md) 

Kits de ferramentas de privacidade diferenciais de código aberto como [o SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core-python) ajudam-no [a preservar a privacidade dos dados](how-to-differential-privacy.md) em soluções Azure Machine Learning.

### <a name="classical-machine-learning-scikit-learn"></a>Aprendizagem clássica de máquinas: scikit-learn

Para tarefas de treino envolvendo algoritmos clássicos de aprendizagem de máquinas tarefas tais classificação, agrupamento e regressão você pode usar algo como Scikit-learn. Para aprender a treinar um modelo de classificação de flores, veja [como treinar com o artigo Scikit-learn](how-to-train-scikit-learn.md).

### <a name="neural-networks-pytorch-tensorflow-keras"></a>Redes neurais: PyTorch, TensorFlow, Keras

Algoritmos de aprendizagem automática de código aberto conhecidos como redes neurais, um subconjunto de aprendizagem automática, são úteis para treinar modelos de aprendizagem profunda em Azure Machine Learning.

Os quadros de aprendizagem profunda de código aberto e os guias de como fazer incluem:

 *  [PyTorch](https://github.com/pytorch/pytorch): [Treine um modelo de classificação de imagem de aprendizagem profunda usando a aprendizagem de transferência](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow): [Reconhecer dígitos manuscritos usando o TensorFlow](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras): [Construir uma rede neural para analisar imagens usando Keras](how-to-train-keras.md)

Treinar um modelo de aprendizagem profunda do zero requer muitas vezes grandes quantidades de tempo, dados e recursos de computação. Você pode encurtar o processo de treino usando a aprendizagem de transferência. A aprendizagem de transferência é uma técnica que aplica conhecimentos adquiridos desde a resolução de um problema para um problema diferente, mas relacionado. Isto significa que pode tomar um modelo existente reutilizá-lo. Veja o [artigo de aprendizagem profunda vs machine learning](concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) para saber mais sobre a aprendizagem de transferências.

### <a name="reinforcement-learning-ray-rllib"></a>Aprendizagem de reforço: Ray RLLib

A aprendizagem do reforço é uma técnica de inteligência artificial que treina modelos usando ações, estados e recompensas: Os agentes de aprendizagem de reforço aprendem a tomar um conjunto de ações predefinidas que maximizam as recompensas especificadas com base no estado atual do seu ambiente. 

O projeto [Ray RLLib](https://github.com/ray-project/ray) tem um conjunto de funcionalidades que permitem uma alta escalabilidade ao longo do processo de treino. O processo iterativo é simultaneamente intensivo em termos de tempo e recursos, à medida que os agentes de aprendizagem de reforço tentam aprender a forma ideal de alcançar uma tarefa.  Ray RLLib também apoia nativamente estruturas de aprendizagem profunda como TensorFlow e PyTorch.  

Para aprender a usar Ray RLLib com Azure Machine Learning, veja [como treinar um modelo de aprendizagem de reforço.](how-to-use-reinforcement-learning.md)

### <a name="monitor-model-performance-tensorboard"></a>Desempenho do modelo monitor: TensorBoard

A formação de um único ou vários modelos requer a visualização e inspeção das métricas desejadas para garantir que o modelo funciona como esperado. Você pode [usar o TensorBoard em Azure Machine Learning para rastrear e visualizar métricas de experiência](./how-to-monitor-tensorboard.md)

### <a name="frameworks-for-interpretable-and-fair-models"></a>Quadros para modelos interpretáveis e justos

Os sistemas de aprendizagem automática são utilizados em diferentes áreas da sociedade, como a banca, a educação e os cuidados de saúde. Como tal, é importante que estes sistemas sejam responsáveis pelas previsões e recomendações que fazem para evitar consequências não intencionais.

Quadros de código aberto como [InterpretML](https://github.com/interpretml/interpret/) e Fairlearn https://github.com/fairlearn/fairlearn) (trabalhar com a Azure Machine Learning para criar modelos de aprendizagem automática mais transparentes e equitativos.

Para obter mais informações sobre como construir modelos justos e interpretacionais, consulte os seguintes artigos:

- [Capacidade de interpretação de modelos no Azure Machine Learning](how-to-machine-learning-interpretability.md)
- [Interpretar e explicar modelos de aprendizagem automática](how-to-machine-learning-interpretability-aml.md)
- [Explique os modelos AutoML](how-to-machine-learning-interpretability-automl.md)
- [Mitigar a equidade nos modelos de aprendizagem automática](concept-fairness-ml.md)
- [Use Azure Machine Learning para obter a equidade do modelo de ativos](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>Implementação do modelo

Uma vez treinados e prontos para a produção, tem de escolher como implantá-lo. A Azure Machine Learning fornece vários alvos de implantação. Para mais informações, consulte [o local e como implementar o artigo.](./how-to-deploy-and-where.md)

### <a name="standardize-model-formats-with-onnx"></a>Padriizar formatos de modelo com ONNX

Após o treino, o conteúdo do modelo, como parâmetros aprendidos, é serializado e guardado num ficheiro. Cada quadro tem o seu próprio formato de serialização. Ao trabalhar com diferentes quadros e ferramentas, significa que tem de implementar modelos de acordo com os requisitos do quadro. Para normalizar este processo, pode utilizar o formato Open Neural Network Exchange (ONNX). ONNX é um formato de código aberto para modelos de inteligência artificial. A ONNX apoia a interoperabilidade entre quadros. Isto significa que você pode treinar um modelo em um dos muitos quadros populares de aprendizagem de máquinas como PyTorch, convertê-lo em formato ONNX, e consumir o modelo ONNX em um quadro diferente como ML.NET.

Para obter mais informações sobre o ONNX e como consumir os modelos ONNX, consulte os seguintes artigos:

- [Criar e acelerar os modelos ML com ONNX](concept-onnx.md)
- [Utilize modelos ONNX em aplicações .NET](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>Pacote e implante de modelos como contentores

Tecnologias de contentores como o Docker são uma forma de implementar modelos como serviços web. Os contentores fornecem uma plataforma e uma forma agnóstica de recursos para construir e orquestrar ambientes de software reprodutíveis. Com estas tecnologias centrais, pode utilizar [ambientes pré-configurados, imagens](./how-to-use-environments.md) [de recipientes pré-configurados](./how-to-deploy-custom-docker-image.md) ou personalizadas para implementar os seus modelos de aprendizagem automática, como [clusters Kubernetes.](./how-to-deploy-azure-kubernetes-service.md?tabs=python) Para fluxos de trabalho intensivos da GPU, pode utilizar ferramentas como o servidor de inferência NVIDIA Triton para [fazer previsões usando GPUs](how-to-deploy-with-triton.md?tabs=python).

### <a name="secure-deployments-with-homomorphic-encryption"></a>Implementações seguras com encriptação homomórfica

A segurança das implementações é uma parte importante do processo de implantação. Para [implementar serviços de inferenculação encriptados,](how-to-homomorphic-encryption-seal.md)utilize a `encrypted-inference` biblioteca Python de código aberto. O `encrypted inferencing` pacote fornece encadernações com base no [Microsoft SEAL,](https://github.com/Microsoft/SEAL)uma biblioteca de encriptação homomórfica.

## <a name="machine-learning-operations-mlops"></a>Operações de Aprendizagem automática (MLOps)

Machine Learning Operations (MLOps), comumente pensado como DevOps para machine learning permite-lhe construir fluxos de trabalho de aprendizagem automática mais transparentes, resilientes e reprodutíveis. Veja o [que é artigo da MLOps](./concept-model-management-and-deployment.md) para saber mais sobre MLOps. 

Utilizando práticas de DevOps como integração contínua (CI) e implementação contínua (CD), pode automatizar o ciclo de vida de aprendizagem automática de ponta a ponta e capturar dados de governação à sua volta. Pode definir o seu [pipeline de aprendizagem automática de CI/CD nas ações do GitHub](./how-to-github-actions-machine-learning.md) para executar tarefas de formação e implantação de machine learning do Azure Machine Learning. 

A captura de dependências de software, métricas, metadados, dados e modelação são uma parte importante do processo de MLOps, a fim de construir oleodutos transparentes, reprodutíveis e auditáveis. Para esta tarefa, pode [utilizar o MLFlow em Machine Learning Azure,](how-to-use-mlflow.md) bem como ao [treinar modelos de aprendizagem automática em Azure Databricks](./how-to-use-mlflow-azure-databricks.md). Também pode [implementar os modelos MLflow como um serviço web Azure](how-to-deploy-mlflow-models.md). 
