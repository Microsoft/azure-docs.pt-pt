---
title: O que é
titleSuffix: Azure Machine Learning service
description: Visão geral do serviço de Azure Machine Learning – uma solução integrada de ciência de dados de ponta a ponta para cientistas de dados profissionais desenvolverem, experimentarem e implantarem aplicativos de análise avançada em escala de nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 08/05/2019
ms.custom: seodec18
ms.openlocfilehash: 7a9c0f24c7b6175b2289af39f2267d695894857d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855951"
---
# <a name="what-is-azure-machine-learning-service"></a>O que é o serviço Azure Machine Learning?

Azure Machine Learning serviço é um serviço de nuvem que você usa para treinar, implantar, automatizar e gerenciar modelos de aprendizado de máquina, tudo na ampla escala que a nuvem fornece.

## <a name="what-is-machine-learning"></a>O que é o Machine Learning?

A aprendizagem automática é uma técnica da ciência de dados que permite aos computadores utilizar dados existentes para prever futuros comportamentos, resultados e tendências. Usando o Machine Learning, os computadores aprendem sem serem programados explicitamente.

As previsões ou predições da aprendizagem automática podem tornar as aplicações e os dispositivos mais inteligentes. Por exemplo, quando você faz compras online, o Machine Learning ajuda a recomendar outros produtos que você possa querer com base no que comprou. Quando o seu cartão de crédito é utilizado, o machine learning compara a transação com uma base de dados de transações e ajuda a detetar fraudes. Quando o robô aspirador limpa uma sala, o machine learning ajuda-o a decidir se a tarefa está concluída.

## <a name="what-is-azure-machine-learning-service"></a>O que é o serviço Azure Machine Learning?

O serviço de Azure Machine Learning fornece um ambiente baseado em nuvem que você pode usar para preparar dados, treinar, testar, implantar, gerenciar e acompanhar modelos de aprendizado de máquina. Inicie o treinamento no computador local e, em seguida, escale horizontalmente para a nuvem. O serviço oferece suporte total a tecnologias de software livre como PyTorch, TensorFlow e scikit-Learn e pode ser usado para qualquer tipo de aprendizado de máquina, desde ml clássico até aprendizado profundo, aprendizado supervisionado e não supervisionado.

Explore e prepare dados, treine e teste modelos e implante-os usando ferramentas avançadas, como:
+ Uma [interface visual](ui-tutorial-automobile-price-train-score.md) na qual você pode arrastar-n-drop modules para criar seus experimentos e, em seguida, implantar modelos
+ [Jupyter notebooks](https://jupyter.org) em que você usa os [SDKs](https://docs.microsoft.com/azure/machine-learning/service/#reference) para escrever seu próprio código, como [esses blocos de anotações de exemplo](https://aka.ms/aml-notebooks)
+ [Extensão de Visual Studio Code](how-to-vscode-tools.md)

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>O que posso fazer com o serviço Azure Machine Learning?

Use o <a href="https://aka.ms/aml-sdk" target="_blank">SDK Azure Machine Learning Python</a> com pacotes python de software livre ou use a [interface visual (versão prévia)](ui-tutorial-automobile-price-train-score.md) para criar e treinar modelos de aprendizado de máquina e aprendizado profundo altamente precisos em um serviço Azure Machine Learning Espaço.

Você pode escolher entre vários componentes do Machine Learning disponíveis em pacotes python de software livre, como <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-Learn</a>, <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>, <a href="https://pytorch.org" target="_blank">PyTorch</a>e <a href="https://mxnet.io" target="_blank">MXNet</a>.

Se você escrever código ou usar a interface visual, poderá acompanhar várias execuções ao experimentar para encontrar a melhor solução, bem como gerenciar os modelos implantados.

### <a name="code-first-experience"></a>Experiência de primeiro código

Inicie o treinamento em seu computador local usando o <a href="https://aka.ms/aml-sdk" target="_blank">SDK do Azure Machine Learning Python</a> e, em seguida, escale horizontalmente para a nuvem. Com muitos [destinos de computação](how-to-set-up-training-targets.md)disponíveis, como Azure Machine Learning computação e [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks)e com [serviços avançados de ajuste](how-to-tune-hyperparameters.md)de hiperparâmetro, você pode criar modelos melhores mais rapidamente usando o poder da nuvem.

Você também pode [automatizar o treinamento e o ajuste do modelo](tutorial-auto-train-models.md) usando o SDK.

### <a name="code-free--low-code-experience"></a>Experiência de código sem código/baixa

Para treinamento sem código, tente:

+ A interface visual para os testes e a implantação de arrastar-n-drop

    ![Interface visual para serviço Azure Machine Learning](media/overview-what-is-azure-ml/visual-interface.png)

+ A opção de portal do Azure para experimentos de ML automatizados

### <a name="operationalization-mlops"></a>Operacionalização (MLOps)

Quando você tem o modelo correto, pode usá-lo facilmente em um serviço Web, em um dispositivo IoT ou em Power BI. Para obter mais informações, consulte o artigo sobre [como implantar e onde](how-to-deploy-and-where.md).

Em seguida, você pode gerenciar seus modelos implantados usando o [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk) ou o [portal do Azure](https://portal.azure.com/).

Esses modelos podem ser consumidos e retornar previsões em [tempo real](how-to-consume-web-service.md) ou de [forma assíncrona](how-to-run-batch-predictions.md) em grandes quantidades de dados.

E com pipelines avançados de [aprendizado de máquina](concept-ml-pipelines.md), você pode colaborar em cada etapa de preparação de dados, treinamento de modelo e avaliação, por meio da implantação. Os pipelines permitem que você:

* automatizar o processo de aprendizado de máquina de ponta a ponta na nuvem
* reutilize os componentes e apenas execute as etapas novamente quando precisar
* usar diferentes recursos de computação em cada etapa
* executar tarefas de Pontuação do lote

Para começar a usar o Azure Machine Learning Service, consulte [as próximas etapas](#next-steps).

## <a name="how-does-azure-machine-learning-service-differ-from-studio"></a>Como o serviço Azure Machine Learning difere do estúdio?

[Machine Learning Studio](../studio/what-is-ml-studio.md) é um espaço de trabalho do Visual colaborativo, arrastar e soltar, no qual você pode criar, testar e implantar soluções de aprendizado de máquina sem a necessidade de escrever código. Ele usa algoritmos predefinidos e pré-configurados de aprendizado de máquina e módulos de manipulação de dados, bem como uma plataforma de computação proprietária.

Azure Machine Learning serviço fornece SDKs **-e-** uma interface visual (versão prévia), para preparar dados rapidamente, treinar e implantar modelos de aprendizado de máquina. Essa interface visual (visualização) fornece uma experiência de arrastar e soltar semelhante ao estúdio. No entanto, diferentemente da plataforma de computação proprietária do estúdio, a interface visual usa seus próprios recursos de computação e é totalmente integrada ao serviço de Azure Machine Learning.

Aqui está uma comparação rápida.

|| Machine Learning Studio | Serviço de Azure Machine Learning:<br/>Interface visual|
|---| --- | --- |
|| Disponibilidade geral (GA) | Em pré-visualização|
|Módulos para interface| Muitos | Conjunto inicial de módulos populares|
|Treinamento de destinos de computação| Destino de computação proprietário, somente suporte de CPU| Dá suporte a Azure Machine Learning computação, GPU ou CPU.<br/>(Outros cálculos com suporte no SDK)|
|Destinos de computação de implantação| Formato de serviço da Web proprietário, não personalizável | Opções de segurança corporativa & serviço kubernetes do Azure. <br/>([Outros cálculos](how-to-deploy-and-where.md) com suporte no SDK) |
|Treinamento de modelo automatizado e ajuste de hiperparâmetro | Não | Ainda não está na interface visual. <br/> (Com suporte no SDK e portal do Azure.) |

Experimente a interface visual (versão prévia) com [o tutorial: Prever o preço do automóvel com a](ui-tutorial-automobile-price-train-score.md)interface visual.

> [!NOTE]
> Os modelos criados no estúdio não podem ser implantados nem gerenciados pelo serviço Azure Machine Learning. No entanto, os modelos criados e implantados na interface visual do serviço podem ser gerenciados por meio do espaço de trabalho do serviço Azure Machine Learning.

## <a name="free-trial"></a>Avaliação gratuita

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do serviço de Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

Receberá créditos para gastar em serviços do Azure. Depois de serem utilizados, pode manter a conta e utilizar os [serviços gratuitos do Azure](https://azure.microsoft.com/free/). O seu cartão de crédito não será cobrado, a menos que altere explicitamente as suas definições e peça para ser cobrado. Ou [ative os benefícios](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)para assinantes do MSDN, que fornecem créditos todos os meses que você pode usar para serviços pagos do Azure.

## <a name="next-steps"></a>Passos Seguintes

- [Crie um Machine Learning espaço de trabalho de serviço](how-to-manage-workspace.md) para começar.

- Siga os tutoriais de tamanho completo:
  + [Treinar um modelo de classificação de imagem com o serviço Azure Machine Learning](tutorial-train-models-with-aml.md)
  + [Preparar dados e usar o aprendizado de máquina automatizado para treinar automaticamente um modelo de regressão](tutorial-data-prep.md)

- Saiba mais sobre [pipelines de aprendizagem automática](/azure/machine-learning/service/concept-ml-pipelines) para criar, otimizar e gerir cenários da aprendizagem automática.

- Leia o aprofundada [arquitetura de serviço do Azure Machine Learning e conceitos](concept-azure-machine-learning-architecture.md) artigo.

- Para obter mais informações, consulte [outros produtos de aprendizado de máquina da Microsoft](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning).
