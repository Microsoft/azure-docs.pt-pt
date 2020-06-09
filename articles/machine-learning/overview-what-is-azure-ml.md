---
title: O que é Azure Machine Learning
description: Visão geral do Azure Machine Learning - Uma solução integrada de ciência de dados de ponta a ponta para cientistas profissionais de dados desenvolverem, experimentarem e implementarem aplicações de análise avançadas à escala de nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.custom: tracking-python
ms.openlocfilehash: ebc30f995c44685dfd58e627706e4ca60e8ecfd5
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84556826"
---
# <a name="what-is-azure-machine-learning"></a>O que é o Azure Machine Learning?

Neste artigo, você aprende sobre Azure Machine Learning, um ambiente baseado na nuvem que você pode usar para treinar, implantar, automatizar, gerir e rastrear modelos ML. 

O Azure Machine Learning pode ser usado para qualquer tipo de aprendizagem automática, desde o ml clássico à aprendizagem profunda, supervisionado e sem supervisão. Quer prefira escrever python ou código R ou opções de código zero/código baixo, como o [designer,](tutorial-designer-automobile-price-train-score.md)pode construir, treinar e rastrear modelos de aprendizagem automática altamente precisos e de aprendizagem profunda num espaço de trabalho de aprendizagem automática Azure. 

Comece a treinar na sua máquina local e depois escale para a nuvem. 

O serviço também interopera com populares ferramentas de deep learning e reforço de código aberto tais como PyTorch, TensorFlow, scikit-learn, e Ray RLlib. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Julgamento livre!**  Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree) Receberá créditos para gastar em serviços do Azure. Depois de serem utilizados, pode manter a conta e utilizar os [serviços gratuitos do Azure](https://azure.microsoft.com/free/). O seu cartão de crédito não será cobrado, a menos que altere explicitamente as suas definições e peça para ser cobrado.


## <a name="what-is-machine-learning"></a>O que é o Machine Learning?

A aprendizagem automática é uma técnica da ciência de dados que permite aos computadores utilizar dados existentes para prever futuros comportamentos, resultados e tendências. Ao utilizar o machine learning, os computadores aprendem sem serem explicitamente programados.

As previsões ou predições da aprendizagem automática podem tornar as aplicações e os dispositivos mais inteligentes. Por exemplo, quando faz compras online, a aprendizagem automática ajuda a recomendar outros produtos que possa querer com base no que comprou. Quando o seu cartão de crédito é utilizado, o machine learning compara a transação com uma base de dados de transações e ajuda a detetar fraudes. Quando o robô aspirador limpa uma sala, o machine learning ajuda-o a decidir se a tarefa está concluída.

## <a name="machine-learning-tools-to-fit-each-task"></a>Ferramentas de aprendizagem automática para se adaptar a cada tarefa 

A Azure Machine Learning fornece todas as ferramentas que os desenvolvedores e cientistas de dados precisam para os seus fluxos de trabalho de aprendizagem automática, incluindo:
+ O [designer Azure Machine Learning](tutorial-designer-automobile-price-train-score.md) (pré-visualização): módulos de arrastar-n-drop para construir as suas experiências e, em seguida, implementar gasodutos.

+ Cadernos Jupyter: use os [nossos cadernos de exemplo](https://aka.ms/aml-notebooks) ou crie os seus próprios cadernos para aproveitar as nossas amostras <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">de SDK para as</a> amostras de Python para a sua aprendizagem automática. 

+ Scripts r ou cadernos nos quais você usa o <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK para R</a> para escrever o seu próprio código, ou usar os módulos R no designer.

+ + O [Acelerador de Soluções de Muitos Modelos](https://aka.ms/many-models) (pré-visualização) baseia-se no Azure Machine Learning e permite-lhe treinar, operar e gerir centenas ou mesmo milhares de modelos de machine learning.

+ [Extensão do Visual Studio Code](tutorial-setup-vscode-extension.md)

+ [Aprendizagem automática CLI](reference-azure-machine-learning-cli.md)

+ Quadros de código aberto como PyTorch, TensorFlow, e scikit-learn e muito mais

+ [Aprendizagem de reforço](how-to-use-reinforcement-learning.md) com Ray RLlib

Pode até utilizar [o MLflow para rastrear métricas e implementar modelos](how-to-use-mlflow.md) ou Kubeflow para [construir gasodutos de fluxo de trabalho de ponta a ponta](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-in-python-or-r"></a>Construa modelos ML em Python ou R

Comece a treinar na sua máquina local utilizando o Azure Machine Learning <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK</a> ou <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK</a>. Depois, pode escalar para a nuvem. 

Com [muitos alvos](how-to-set-up-training-targets.md)de computação disponíveis , como o Azure Machine Learning Compute e [o Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), e com serviços avançados de [afinação de hiperparímetros,](how-to-tune-hyperparameters.md)é possível construir melhores modelos mais rapidamente utilizando a potência da nuvem.

Também pode [automatizar o treino e afinação](tutorial-auto-train-models.md) de modelos utilizando o SDK.

## <a name="build-ml-models-with-no-code-tools"></a>Construa modelos ML sem ferramentas sem código

Para treino e implantação sem código ou de baixo código, tente:

+ **Azure Machine Learning designer (pré-visualização)**

  Utilize o designer para preparar dados, treinar, testar, implementar, gerir e rastrear modelos de aprendizagem automática sem escrever nenhum código. Não é necessária programação, liga-se visualmente conjuntos de dados e módulos para construir o seu modelo. Experimente o tutorial do [designer.](tutorial-designer-automobile-price-train-score.md)

  Saiba mais [no artigo de visão geral do designer de aprendizagem automática Azure.](concept-designer.md) 

  ![Exemplo de designer de aprendizagem de máquinas Azure](./media/overview-what-is-azure-ml/designer-drag-and-drop.gif)

+ **UI de aprendizagem automática de máquinas**

  Aprenda a criar [experiências automatizadas](tutorial-first-experiment-automated-ml.md) de ML na interface fácil de usar. 

  [![Painel de navegação do estúdio Azure Machine Learning](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

## <a name="mlops-deploy--lifecycle-management"></a>MLOps: Implementar & gestão do ciclo de vida
Quando tiver o modelo certo, pode facilmente usá-lo num serviço web, num dispositivo IoT ou na Power BI. Para mais informações, consulte o artigo sobre [como implementar e onde](how-to-deploy-and-where.md).

Em seguida, pode gerir os seus modelos implantados utilizando o [Azure Machine Learning SDK para Python,](https://aka.ms/aml-sdk) [o estúdio Azure Machine Learning](https://ml.azure.com)ou o [CLI de aprendizagem automática](reference-azure-machine-learning-cli.md).

Estes modelos podem ser consumidos e as previsões de retorno em [tempo real](how-to-consume-web-service.md) ou [assíncrona](how-to-use-parallel-run-step.md) em grandes quantidades de dados.

E com oleodutos de aprendizagem automática avançados, pode colaborar em cada passo desde a preparação de [dados,](concept-ml-pipelines.md)formação de modelos e avaliação, através da implantação. Os gasodutos permitem-lhe:

* Automatize o processo de aprendizagem automática de ponta a ponta na nuvem
* Reutilizar componentes e apenas refazer etapas quando necessário
* Use diferentes recursos computacional em cada passo
* Executar tarefas de pontuação de lotes

Se pretender utilizar scripts para automatizar o fluxo de trabalho de aprendizagem automática, o CLI de [aprendizagem automática](reference-azure-machine-learning-cli.md) fornece ferramentas de linha de comando que executam tarefas comuns, tais como submeter uma corrida de treino ou implementar um modelo.

Para começar a utilizar o Azure Machine Learning, consulte [os próximos passos](#next-steps).

## <a name="integration-with-other-services"></a>Integração com outros serviços

A Azure Machine Learning trabalha com outros serviços na plataforma Azure, e também integra com ferramentas de código aberto como Git e MLFlow.

+ Metas de computação como __Azure Kubernetes Service__, __Azure Container Instances__, __Azure Databricks__, __Azure Data Lake Analytics__, e __Azure HDInsight__. Para obter mais informações sobre os alvos de computação, veja [quais são os alvos de computação?](concept-compute-target.md)
+ __Grelha de eventos Azure__. Para mais informações, consulte [os eventos consume Azure Machine Learning](concept-event-grid-integration.md).
+ __Monitor Azure__. Para obter mais informações, consulte [monitorar a Azure Machine Learning](monitor-azure-machine-learning.md).
+ Lojas de dados como __contas de armazenamento Azure,__ __Azure Data Lake Storage__, __Azure SQL Database__, __Azure Database for PostgreSQL__, e __Azure Open Datasets__. Para obter mais informações, consulte [os dados do Access nos serviços de armazenamento Azure](how-to-access-data.md) e [crie conjuntos de dados com conjuntos de dados Azure Open](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets).
+ __Redes Virtuais Azure__. Para obter mais informações, consulte [a experimentação e a inferência Secure numa rede virtual.](how-to-enable-virtual-network.md)
+ __Gasodutos Azure__. Para obter mais informações, consulte [Train e implemente modelos de machine learning](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Troncos de repositório de Git.__ Para mais informações, consulte [a integração de Git.](concept-train-model-git-integration.md)
+ __MLFlow__. Para obter mais informações, consulte [mLflow para rastrear métricas e implementar modelos](how-to-use-mlflow.md) 
+ __Kubeflow.__ Para obter mais informações, consulte [os gasodutos de fluxo de trabalho de ponta a ponta.](https://www.kubeflow.org/docs/azure/)

### <a name="secure-communications"></a>Comunicações seguras

A sua conta de Armazenamento Azure, metas de computação e outros recursos podem ser utilizados de forma segura dentro de uma rede virtual para treinar modelos e realizar inferência. Para obter mais informações, consulte [a experimentação e a inferência Secure numa rede virtual.](how-to-enable-virtual-network.md)

## <a name="basic--enterprise-editions"></a><a name="sku"></a>Edições básicas & Enterprise

A Azure Machine Learning oferece duas edições adaptadas às suas necessidades de aprendizagem automática:
+ Básico (geralmente disponível)
+ Empresa (pré-visualização)

Estas edições determinam quais as ferramentas de machine learning disponíveis para desenvolvedores e cientistas de dados a partir do seu espaço de trabalho.   

Os espaços básicos de trabalho permitem-lhe continuar a utilizar a Azure Machine Learning e pagar apenas os recursos Azure consumidos durante o processo de aprendizagem automática. Os espaços de trabalho da edição empresarial serão cobrados apenas pelo seu consumo Azure enquanto a edição estiver em pré-visualização. Saiba mais sobre o que está disponível na [edição](https://azure.microsoft.com/pricing/details/machine-learning/)do Azure Machine Learning & página de preços . 

Atribui-se a edição sempre que cria um espaço de trabalho. E, os espaços de trabalho pré-existentes foram convertidos para a edição Básica para si. A edição básica inclui todas as funcionalidades que já estavam geralmente disponíveis a partir de outubro de 2019. Quaisquer experiências nesses espaços de trabalho que foram construídos usando funcionalidades de edição enterprise continuarão disponíveis para você apenas em leitura até atualizar para Enterprise. Saiba como [atualizar um espaço de trabalho básico para a edição Enterprise.](how-to-manage-workspace.md#upgrade) 

Os clientes são responsáveis pelos custos incorridos no cálculo e outros recursos da Azure durante este período.

## <a name="next-steps"></a>Passos seguintes

- Crie a sua primeira experiência com o seu método preferido:
  + [Use cadernos Python para treinar & implementar modelos ML](tutorial-1st-experiment-sdk-setup.md)
  + [Use R Markdown para treinar & implementar modelos ML](tutorial-1st-r-experiment.md) 
  + [Utilize machine learning automatizado para treinar & implementar modelos ML](tutorial-first-experiment-automated-ml.md) 
  + [Use as capacidades de arrastar & de arrastar do designer para treinar & implementar](tutorial-designer-automobile-price-train-score.md) 
  + [Use o CLI de aprendizagem automática para treinar e implementar um modelo](tutorial-train-deploy-model-cli.md)

- Saiba mais sobre [pipelines de aprendizagem automática](concept-ml-pipelines.md) para criar, otimizar e gerir cenários da aprendizagem automática.

- Leia o artigo de arquitetura e conceitos de [aprendizagem de máquinas Azure.](concept-azure-machine-learning-architecture.md)
