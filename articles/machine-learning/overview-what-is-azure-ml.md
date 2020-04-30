---
title: O que é Azure Machine Learning
description: Visão geral da Azure Machine Learning - uma solução integrada e de ciência dos dados de ponta a ponta para os cientistas profissionais de dados desenvolverem, experimentarem e implementarem aplicações avançadas de análise à escala de nuvens.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.openlocfilehash: b8dbbb2810277bef20cb3b9b47a63deeea3e0ff9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79241613"
---
# <a name="what-is-azure-machine-learning"></a>O que é o Azure Machine Learning?

Neste artigo, você aprende sobre o Azure Machine Learning, um ambiente baseado em nuvem que você pode usar para treinar, implementar, automatizar, gerir e rastrear modelos ML. 

O Azure Machine Learning pode ser usado para qualquer tipo de aprendizagem automática, desde o ml clássico até à aprendizagem profunda, supervisionado e sem supervisão. Quer prefira escrever python ou código R ou opções de código zero/código baixo, como o [designer,](tutorial-designer-automobile-price-train-score.md)pode construir, treinar e rastrear modelos de aprendizagem automática altamente precisos e modelos de aprendizagem profunda num espaço de trabalho de aprendizagem automática Azure. 

Comece a treinar na sua máquina local e depois escale para a nuvem. 

O serviço também opera com ferramentas populares de código aberto, tais como PyTorch, TensorFlow e scikit-learn.

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Julgamento livre!**  Se não tiver uma subscrição Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree) Receberá créditos para gastar em serviços do Azure. Depois de serem utilizados, pode manter a conta e utilizar os [serviços gratuitos do Azure](https://azure.microsoft.com/free/). O seu cartão de crédito não será cobrado, a menos que altere explicitamente as suas definições e peça para ser cobrado.


## <a name="what-is-machine-learning"></a>O que é o Machine Learning?

A aprendizagem automática é uma técnica da ciência de dados que permite aos computadores utilizar dados existentes para prever futuros comportamentos, resultados e tendências. Ao utilizar a aprendizagem automática, os computadores aprendem sem serem explicitamente programados.

As previsões ou predições da aprendizagem automática podem tornar as aplicações e os dispositivos mais inteligentes. Por exemplo, quando faz compras online, o machine learning ajuda a recomendar outros produtos que você pode querer com base no que comprou. Quando o seu cartão de crédito é utilizado, o machine learning compara a transação com uma base de dados de transações e ajuda a detetar fraudes. Quando o robô aspirador limpa uma sala, o machine learning ajuda-o a decidir se a tarefa está concluída.

## <a name="machine-learning-tools-to-fit-each-task"></a>Ferramentas de aprendizagem automática para se adaptar em cada tarefa 

O Azure Machine Learning fornece todas as ferramentas que os desenvolvedores e cientistas de dados precisam para os seus fluxos de trabalho de aprendizagem automática, incluindo:
+ O [designer de machine learning Azure](tutorial-designer-automobile-price-train-score.md) (pré-visualização): módulos de drag-n-drop para construir as suas experiências e, em seguida, implementar oleodutos.

+ Cadernos jupyter: use os [nossos cadernos de exemplo](https://aka.ms/aml-notebooks) ou crie os seus próprios cadernos para alavancar o nosso <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK para</a> amostras Python para a sua aprendizagem automática. 

+ Scripts R ou cadernos em que utiliza o <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK para R</a> para escrever o seu próprio código ou utilizar os módulos R no designer.

+ [Extensão do Visual Studio Code](tutorial-setup-vscode-extension.md)

+ [ClI de aprendizagem automática](reference-azure-machine-learning-cli.md)

+ Quadros de código aberto como PyTorch, TensorFlow e scikit-learn e muito mais

Pode até utilizar [o MLflow para rastrear métricas e implementar modelos](how-to-use-mlflow.md) ou Kubeflow para construir gasodutos de fluxo de [trabalho de ponta a ponta.](https://www.kubeflow.org/docs/azure/)

## <a name="build-ml-models-in-python-or-r"></a>Construa modelos ML em Python ou R

Comece a treinar na sua máquina local utilizando o Azure Machine Learning <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK</a> ou <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK</a>. Depois, podes escalar para a nuvem. 

Com muitos alvos de cálculo disponíveis , como o Azure Machine Learning Compute e [o Azure Databricks,](/azure/azure-databricks/what-is-azure-databricks)e com serviços avançados de [afinação de hiperparâmetros,](how-to-tune-hyperparameters.md)pode construir [melhores](how-to-set-up-training-targets.md)modelos mais rapidamente utilizando a potência da nuvem.

Também pode [automatizar o treino e a sintonização](tutorial-auto-train-models.md) do modelo utilizando o SDK.

## <a name="build-ml-models-with-no-code-tools"></a>Construa modelos ML com ferramentas sem código

Para formação e implantação sem código ou de código baixo, tente:

+ **Designer de Aprendizagem automática Azure (pré-visualização)**

  Utilize o designer para preparar dados, treinar, testar, implementar, gerir e rastrear modelos de aprendizagem automática sem escrever qualquer código. Não é necessária programação, liga-se visualmente conjuntos de dados e módulos para construir o seu modelo. Experimente o tutorial do [designer.](tutorial-designer-automobile-price-train-score.md)

  Saiba mais no artigo geral do designer de [aprendizagem automática Azure.](concept-designer.md) 

  ![Exemplo de designer de aprendizagem automática azure](./media/overview-what-is-azure-ml/designer-drag-and-drop.gif)

+ **UI de aprendizagem automática de máquinas**

  Aprenda a criar [experiências automáticas](tutorial-first-experiment-automated-ml.md) de ML na interface fácil de usar. 

  [![Painel de navegação do estúdio Azure Machine Learning](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

## <a name="mlops-deploy--lifecycle-management"></a>MLOps: Implementar & gestão do ciclo de vida
Quando tiver o modelo certo, pode usá-lo facilmente num serviço web, num dispositivo IoT ou no Power BI. Para mais informações, consulte o artigo sobre [como implementar e onde.](how-to-deploy-and-where.md)

Em seguida, você pode gerir os seus modelos implantados usando o [Azure Machine Learning SDK para Python,](https://aka.ms/aml-sdk) [estúdio Azure Machine Learning,](https://ml.azure.com)ou o clI de [aprendizagem automática](reference-azure-machine-learning-cli.md).

Estes modelos podem ser consumidos e devolver previsões em [tempo real](how-to-consume-web-service.md) ou [assincronicamente](how-to-use-parallel-run-step.md) em grandes quantidades de dados.

E com oleodutos avançados de machine learning, pode colaborar em cada passo da preparação de dados, formação e avaliação de [modelos,](concept-ml-pipelines.md)através da implantação. Os gasodutos permitem::

* Automatizar o processo de aprendizagem automática de ponta a ponta na nuvem
* Reutilizar componentes e só reexecutar passos quando necessário
* Use diferentes recursos computacionais em cada passo
* Executar tarefas de pontuação de lote

Se pretender utilizar scripts para automatizar o fluxo de trabalho de aprendizagem automática, o CLI de [aprendizagem automática](reference-azure-machine-learning-cli.md) fornece ferramentas de linha de comando que executam tarefas comuns, tais como submeter um treino ou implementar um modelo.

Para começar a usar o Azure Machine Learning, consulte os [próximos passos.](#next-steps)

## <a name="integration-with-other-services"></a>Integração com outros serviços

O Azure Machine Learning trabalha com outros serviços na plataforma Azure, e integra-se também com ferramentas de código aberto como o Git e o MLFlow.

+ Alvos computacionais como __o Serviço Azure Kubernetes,__ instâncias de __contentores Azure,__ __Tijolos de Dados Azure,__ __Azure Data Lake Analytics__e __Azure HDInsight.__ Para obter mais informações sobre alvos de cálculo, veja [quais são os alvos da computação?](concept-compute-target.md)
+ __Grelha de Eventos Azure.__ Para mais informações, consulte consumir eventos de [Aprendizagem automática Azure.](concept-event-grid-integration.md)
+ __Monitor Azure__. Para mais informações, consulte Monitorização de [Machine Learning Azure](monitor-azure-machine-learning.md).
+ Lojas de dados como contas de __armazenamento Azure,__ Armazenamento de __Lagos De Dados Azure,__ Base de Dados __Azure SQL,__ __Base de Dados Azure para PostgreSQL__e Conjuntos de Dados __Abertos Azure.__ Para mais informações, consulte [os dados do Access nos serviços](how-to-access-data.md) de armazenamento do Azure e crie conjuntos de dados com conjuntos de dados [abertos do Azure.](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets)
+ __Redes Virtuais Azure.__ Para mais informações, consulte a experimentação e a [inferência seguras numa rede virtual](how-to-enable-virtual-network.md).
+ __Gasodutos Azure.__ Para mais informações, consulte [O Comboio e implemente modelos de aprendizagem automática.](/azure/devops/pipelines/targets/azure-machine-learning)
+ __Git registos de repositório.__ Para mais informações, consulte a integração de [Git.](concept-train-model-git-integration.md)
+ __MLFlow__. Para mais informações, consulte [mLflow para rastrear métricas e implementar modelos](how-to-use-mlflow.md) 
+ __Kubeflow.__ Para obter mais informações, consulte a construção de [gasodutos de fluxo](https://www.kubeflow.org/docs/azure/)de trabalho de ponta a ponta .

### <a name="secure-communications"></a>Comunicações seguras

A sua conta de Armazenamento Azure, alvos de cálculo e outros recursos podem ser usados de forma segura dentro de uma rede virtual para treinar modelos e realizar inferências. Para mais informações, consulte a experimentação e a [inferência seguras numa rede virtual](how-to-enable-virtual-network.md).

## <a name="basic--enterprise-editions"></a><a name="sku"></a>Edições Básicas & Enterprise

O Azure Machine Learning oferece duas edições adaptadas às suas necessidades de aprendizagem automática:
+ Básico (geralmente disponível)
+ Empresa (pré-visualização)

Estas edições determinam quais as ferramentas de machine learning disponíveis para programadores e cientistas de dados do seu espaço de trabalho.   

Os espaços básicos de trabalho permitem-lhe continuar a utilizar o Azure Machine Learning e pagar apenas pelos recursos Azure consumidos durante o processo de aprendizagem automática. Os espaços de trabalho da edição empresarial serão cobrados apenas pelo seu consumo Azure enquanto a edição estiver em pré-visualização. Saiba mais sobre o que está disponível na edição de Web Machine Learning & página de [preços.](https://azure.microsoft.com/pricing/details/machine-learning/) 

Atribua a edição sempre que cria um espaço de trabalho. E, os espaços de trabalho pré-existentes foram convertidos para a edição Básica para si. A edição básica inclui todas as funcionalidades que já estavam geralmente disponíveis a partir de outubro de 2019. Quaisquer experiências nesses espaços de trabalho que foram construídos usando funcionalidades da edição da Enterprise continuarão a estar disponíveis para si apenas em leitura até que faça o upgrade para a Enterprise. Saiba como atualizar um espaço de [trabalho Básico para a edição enterprise.](how-to-manage-workspace.md#upgrade) 

Os clientes são responsáveis pelos custos incorridos na computação e outros recursos Azure durante este período.

## <a name="next-steps"></a>Passos seguintes

- Crie a sua primeira experiência com o seu método preferido:
  + [Use os cadernos Python para treinar & implementar modelos ML](tutorial-1st-experiment-sdk-setup.md)
  + [Use R Markdown para treinar & implementar modelos ML](tutorial-1st-r-experiment.md) 
  + [Utilize machine learning automatizado para formar & implementar modelos ML](tutorial-first-experiment-automated-ml.md) 
  + [Use as capacidades de queda de drag & do designer para treinar & implantação](tutorial-designer-automobile-price-train-score.md) 
  + [Use o CLI de aprendizagem automática para treinar e implementar um modelo](tutorial-train-deploy-model-cli.md)

- Saiba mais sobre [pipelines de aprendizagem automática](concept-ml-pipelines.md) para criar, otimizar e gerir cenários da aprendizagem automática.

- Leia o artigo de [arquitetura e conceitos azure machine learning](concept-azure-machine-learning-architecture.md) aprofundado.
