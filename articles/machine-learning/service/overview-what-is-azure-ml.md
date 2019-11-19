---
title: O que é Azure Machine Learning
description: Visão geral do Azure Machine Learning-uma solução de ciência de dados integrada e de ponta a ponta para cientistas de dados profissionais desenvolverem, experimentarem e implantarem aplicativos de análise avançada em escala de nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.openlocfilehash: 488616ab2068337331d589bbf5c61ae06b299f5d
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173981"
---
# <a name="what-is-azure-machine-learning"></a>O que é o Azure Machine Learning?

Neste artigo, você aprende sobre Azure Machine Learning, um ambiente baseado em nuvem que pode ser usado para treinar, implantar, automatizar, gerenciar e rastrear modelos de ML. 

Azure Machine Learning pode ser usado para qualquer tipo de aprendizado de máquina, do ml clássico até aprendizado profundo, supervisionado e aprendizado não supervisionado. Se você preferir escrever código Python ou R ou opções de código zero ou de código baixo, como o [Designer](ui-tutorial-automobile-price-train-score.md), você pode criar, treinar e acompanhar modelos de aprendizado de máquina e aprendizado profundo altamente precisos em um Workspace do Azure Machine Learning. 

Inicie o treinamento no computador local e, em seguida, escale horizontalmente para a nuvem. 

O serviço também interopera com ferramentas populares de software livre, como PyTorch, TensorFlow e scikit-learn.

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Avaliação gratuita!**  Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje. Receberá créditos para gastar em serviços do Azure. Depois de serem utilizados, pode manter a conta e utilizar os [serviços gratuitos do Azure](https://azure.microsoft.com/free/). O seu cartão de crédito não será cobrado, a menos que altere explicitamente as suas definições e peça para ser cobrado.


## <a name="what-is-machine-learning"></a>O que é o Machine Learning?

A aprendizagem automática é uma técnica da ciência de dados que permite aos computadores utilizar dados existentes para prever futuros comportamentos, resultados e tendências. Usando o Machine Learning, os computadores aprendem sem serem programados explicitamente.

As previsões ou predições da aprendizagem automática podem tornar as aplicações e os dispositivos mais inteligentes. Por exemplo, quando você faz compras online, o Machine Learning ajuda a recomendar outros produtos que você possa querer com base no que comprou. Quando o seu cartão de crédito é utilizado, o machine learning compara a transação com uma base de dados de transações e ajuda a detetar fraudes. Quando o robô aspirador limpa uma sala, o machine learning ajuda-o a decidir se a tarefa está concluída.

## <a name="machine-learning-tools-to-fit-each-task"></a>Ferramentas de Machine Learning para ajustar cada tarefa 

Azure Machine Learning fornece todos os desenvolvedores de ferramentas e cientistas de dados necessários para seus fluxos de trabalho de aprendizado de máquina, incluindo:
+ O [Designer de Azure Machine Learning](ui-tutorial-automobile-price-train-score.md) (versão prévia): arrastar-n-drop modules para compilar seus experimentos e, em seguida, implantar pipelines.

+ Jupyter notebooks: Use nossos [notebooks de exemplo](https://aka.ms/aml-notebooks) ou crie seus próprios blocos de anotações para aproveitar nosso <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK para</a> exemplos de Python para o aprendizado de máquina. 

+ Scripts r ou notebooks nos quais você usa o <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK para R</a> para escrever seu próprio código ou usar os módulos do R no designer.

+ [Extensão de Visual Studio Code](how-to-vscode-tools.md)

+ [CLI do Machine Learning](reference-azure-machine-learning-cli.md)

+ Estruturas de código-fonte aberto como PyTorch, TensorFlow e scikit-Learn e muito mais

Você pode até mesmo usar o [MLflow para acompanhar as métricas e implantar modelos](how-to-use-mlflow.md) ou Kubeflow para [Compilar pipelines de fluxo de trabalho de ponta a ponta](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-in-python-or-r"></a>Criar modelos de ML em Python ou R

Inicie o treinamento em seu computador local usando o <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK</a> do Azure Machine Learning Python ou o <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK do R</a>. Em seguida, você pode escalar horizontalmente para a nuvem. 

Com muitos [destinos de computação](how-to-set-up-training-targets.md)disponíveis, como Azure Machine Learning computação e [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks)e com [serviços avançados de ajuste de hiperparâmetro](how-to-tune-hyperparameters.md), você pode criar modelos melhores mais rapidamente usando o poder da nuvem.

Você também pode [automatizar o treinamento e o ajuste do modelo](tutorial-auto-train-models.md) usando o SDK.

## <a name="build-ml-models-with-no-code-tools"></a>Criar modelos de ML com ferramentas sem código

Para treinamento e implantação sem código ou de baixo código, tente:

+ **Designer de Azure Machine Learning (versão prévia)**

  Use o designer para preparar dados, treinar, testar, implantar, gerenciar e acompanhar modelos de aprendizado de máquina sem escrever nenhum código. Não há nenhuma programação necessária, você conecta visualmente conjuntos de os e módulos para construir seu modelo. Experimente o [tutorial do designer](tutorial-designer-automobile-price-train-score.md).

  Saiba mais no [artigo Visão geral do Azure Machine Learning designer](concept-designer.md). 

  ![Exemplo de designer de Azure Machine Learning](media/concept-ml-pipelines/designer-drag-and-drop.gif)

+ **Interface do usuário do Machine Learning automatizada**

  Saiba como criar [experimentos de ml automatizados](tutorial-first-experiment-automated-ml.md) na interface fácil de usar. 

  [painel de navegação do ![Azure Machine Learning Studio](media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

## <a name="mlops-deploy--lifecycle-management"></a>MLOps: implantar o gerenciamento do ciclo de vida &
Quando você tem o modelo correto, pode usá-lo facilmente em um serviço Web, em um dispositivo IoT ou em Power BI. Para obter mais informações, consulte o artigo sobre [como implantar e onde](how-to-deploy-and-where.md).

Em seguida, você pode gerenciar seus modelos implantados usando o [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk), [Azure Machine Learning Studio](https://ml.azure.com)ou a [CLI do Machine Learning](reference-azure-machine-learning-cli.md).

Esses modelos podem ser consumidos e retornar previsões em [tempo real](how-to-consume-web-service.md) ou de [forma assíncrona](how-to-run-batch-predictions.md) em grandes quantidades de dados.

E com [pipelines](concept-ml-pipelines.md)avançados de aprendizado de máquina, você pode colaborar em cada etapa de preparação de dados, treinamento de modelo e avaliação, por meio da implantação. Os pipelines permitem que você:

* Automatizar o processo de aprendizado de máquina de ponta a ponta na nuvem
* Reutilizar componentes e executar apenas as etapas quando necessário
* Usar diferentes recursos de computação em cada etapa
* Executar tarefas de Pontuação do lote

Se você quiser usar scripts para automatizar o fluxo de trabalho do Machine Learning, a [CLI do Machine Learning](reference-azure-machine-learning-cli.md) fornece ferramentas de linha de comando que executam tarefas comuns, como enviar uma execução de treinamento ou implantar um modelo.

Para começar a usar o Azure Machine Learning, consulte [próximas etapas](#next-steps).

## <a name="integration-with-other-services"></a>Integração com outros serviços

Azure Machine Learning trabalha com outros serviços na plataforma do Azure e também se integra a ferramentas de software livre, como git e MLFlow.

+ Destinos de computação, como o __serviço kubernetes do Azure__, __instâncias de contêiner do Azure__, __Azure Databricks__, __Azure data Lake Analytics__e __Azure HDInsight__. Para obter mais informações sobre destinos de computação, consulte [o que são destinos de computação?](concept-compute-target.md).
+ __Grade de eventos do Azure__. Para obter mais informações, consulte [consumir eventos de Azure Machine Learning](concept-event-grid-integration.md).
+ __O Azure Monitor__. Para obter mais informações, consulte [monitoring Azure Machine Learning](monitor-azure-machine-learning.md).
+ Armazenamentos de dados, como __contas de armazenamento do Azure__, __Azure data Lake Storage__, banco de dados __SQL do Azure__, __banco de dados do Azure para PostgreSQL e conjuntos de dados__ __abertos do Azure__. Para obter mais informações, consulte [acessar dados nos serviços de armazenamento do Azure](how-to-access-data.md) e [criar conjuntos de dados com conjuntos de dados abertos do Azure](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets).
+ __Redes virtuais do Azure__. Para obter mais informações, consulte [experimentação e inferência de segurança em uma rede virtual](how-to-enable-virtual-network.md).
+ __Pipelines do Azure__. Para obter mais informações, consulte [treinar e implantar modelos de aprendizado de máquina](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Logs do repositório git__. Para obter mais informações, consulte [integração com o Git](concept-train-model-git-integration.md).
+ __MLFlow__. Para obter mais informações, consulte [MLflow para acompanhar métricas e implantar modelos](how-to-use-mlflow.md) 
+ __Kubeflow__. Para obter mais informações, consulte [criar pipelines de fluxo de trabalho de ponta a ponta](https://www.kubeflow.org/docs/azure/).

### <a name="secure-communications"></a>Comunicações seguras

Sua conta de armazenamento do Azure, destinos de computação e outros recursos podem ser usados com segurança dentro de uma rede virtual para treinar modelos e realizar a inferência. Para obter mais informações, consulte [experimentação e inferência de segurança em uma rede virtual](how-to-enable-virtual-network.md).

## <a name="sku"></a>Edições do Basic & Enterprise

O Azure Machine Learning oferece duas edições personalizadas para suas necessidades de aprendizado de máquina:
+ Básico (disponível para o público)
+ Enterprise (visualização)

Essas edições determinam quais ferramentas de Machine Learning estão disponíveis para desenvolvedores e cientistas de dados de seu espaço de trabalho.   

Os espaços de trabalho básicos permitem que você continue usando Azure Machine Learning e pague apenas pelos recursos do Azure consumidos durante o processo de aprendizado de máquina. Os espaços de trabalho do Enterprise Edition serão cobrados somente pelo consumo do Azure enquanto a edição estiver em visualização. Saiba mais sobre o que está disponível na [visão geral do Azure Machine Learning edition & página de preços](https://azure.microsoft.com/pricing/details/machine-learning/). 

Você atribui a edição sempre que cria um espaço de trabalho. E os espaços de trabalho já existentes foram convertidos para a edição básica para você. A edição básica inclui todos os recursos que já estavam disponíveis em geral a partir de outubro de 2019. Quaisquer experimentos nesses espaços de trabalho criados com os recursos da Enterprise Edition continuarão disponíveis para você em somente leitura até que você atualize para o Enterprise. Saiba como [atualizar um espaço de trabalho básico para o Enterprise Edition](how-to-manage-workspace.md#upgrade). 

Os clientes são responsáveis pelos custos incorridos na computação e em outros recursos do Azure durante esse tempo.

## <a name="next-steps"></a>Passos seguintes

- Crie seu primeiro experimento com o método preferencial:
  + [Usar blocos de anotações do Python para treinar & implantar modelos de ML](tutorial-1st-experiment-sdk-setup.md)
  + [Usar R Markdown para treinar & implantar modelos de ML](tutorial-1st-r-experiment.md) 
  + [Usar o Machine Learning automatizado para treinar & implantar modelos de ML](  tutorial-first-experiment-automated-ml.md) 
  + [Use os recursos de arrastar & do designer para treinar & implantar](tutorial-designer-automobile-price-train-score.md) 
  + [Usar a CLI do Machine Learning para treinar e implantar um modelo](tutorial-train-deploy-model-cli.md)

- Saiba mais sobre [pipelines de aprendizagem automática](/azure/machine-learning/service/concept-ml-pipelines) para criar, otimizar e gerir cenários da aprendizagem automática.

- Leia o artigo [arquitetura e conceitos de Azure Machine Learning](concept-azure-machine-learning-architecture.md) detalhada.
