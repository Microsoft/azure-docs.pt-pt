---
title: O que é
titleSuffix: Azure Machine Learning service
description: Descrição geral do serviço do Azure Machine Learning - uma solução de ciência de dados ponto a ponto integrada para cientistas de dados profissionais desenvolver, experimentar e implementar aplicações de análise avançada à escala da cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: c9f8e13ad089fd40cde22bb76587b171a1c52c7b
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361902"
---
# <a name="what-is-azure-machine-learning-service"></a>O que é o serviço Azure Machine Learning?

O serviço do Azure Machine Learning é um serviço em nuvem que utiliza para preparar, implementar, automatizar e gerir modelos de aprendizagem automática, tudo na escala amplo que a cloud oferece.

## <a name="what-is-machine-learning"></a>O que é o Machine Learning?

A aprendizagem automática é uma técnica da ciência de dados que permite aos computadores utilizar dados existentes para prever futuros comportamentos, resultados e tendências. Com o machine learning, os computadores aprendem sem serem programados explicitamente.

As previsões ou predições da aprendizagem automática podem tornar as aplicações e os dispositivos mais inteligentes. Por exemplo, quando faz compras online, machine learning recomenda outros produtos que pode desejar com base no que comprou. Quando o seu cartão de crédito é utilizado, o machine learning compara a transação com uma base de dados de transações e ajuda a detetar fraudes. Quando o robô aspirador limpa uma sala, o machine learning ajuda-o a decidir se a tarefa está concluída.

## <a name="what-is-azure-machine-learning-service"></a>O que é o serviço Azure Machine Learning?

O serviço do Azure Machine Learning fornece um ambiente com base na cloud, que pode utilizar para de preparação de dados, formar, testar, implementar, gerir e controlar os modelos de aprendizagem automática.

[![Fluxo de trabalho de serviço do Azure Machine Learning](./media/overview-what-is-azure-ml/aml.png)](./media/overview-what-is-azure-ml/aml.png#lightbox)

O serviço do Azure Machine Learning totalmente compatível com tecnologias de código aberto. Portanto, pode usar dezenas de milhares de pacotes de Python de código-fonte aberto com componentes de aprendizagem automática. Os exemplos são PyTorch, o TensorFlow e scikit-Saiba mais.
Suporte para ferramentas avançadas torna mais fácil de forma interativa explorar e preparar dados e, em seguida, desenvolver e testar modelos. Os exemplos são [blocos de notas do Jupyter](https://jupyter.org) ou o [do Azure Machine Learning para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai#overview) extensão.
O serviço Azure Machine Learning também inclui funcionalidades que [automatizam a otimização e a geração de modelos](tutorial-auto-train-models.md) para ajudá-lo a criar modelos com facilidade, eficiência e precisão.

Ao utilizar o serviço Azure Machine Learning, pode iniciar o treinamento no seu computador local e, em seguida, aumentar horizontalmente para a cloud. Em muitas disponíveis [destinos de computação](how-to-set-up-training-targets.md), como a computação do Azure Machine Learning e [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks)e com [avançada dos serviços de otimização de hiper-parâmetros](how-to-tune-hyperparameters.md), pode criar modelos melhores mais rapidamente ao utilizar o poder da cloud.

Quando tiver o modelo certo, pode implementá-lo facilmente num contentor, como o Docker. Portanto, é simples de implementar no Azure Container Instances ou serviço Kubernetes do Azure. Em alternativa, pode utilizar o contentor em suas próprias implementações, no local ou na cloud. Para obter mais informações, consulte o artigo sobre [como implementar e, em que](how-to-deploy-and-where.md).

Pode gerir os modelos implementados e controlar várias execuções, à medida que testar para determinar a melhor solução.
Após a sua implementação, o seu modelo pode retornar previsões na [tempo real](how-to-consume-web-service.md) ou [assincronamente](how-to-run-batch-predictions.md) em grandes quantidades de dados.

E com avançados [de machine learning pipelines](concept-ml-pipelines.md), pode colaborar em todos os passos de preparação de dados, preparação de modelos e avaliação e implantação.

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>O que posso fazer com o serviço Azure Machine Learning?

Utilizar o <a href="https://aka.ms/aml-sdk" target="_blank">principal SDK de Python</a> e o <a href="https://aka.ms/data-prep-sdk" target="_blank">SDK de preparação de dados</a> para o Azure Machine Learning, bem como pacotes de Python de código-fonte aberto, que pode criar e treinar a aprendizagem automática altamente precisos e modelos de aprendizagem profunda -se num área de trabalho de serviço do Azure Machine Learning.
Pode escolher entre muitas do machine learning componentes disponíveis em pacotes de Python de código-fonte aberto, como os exemplos seguintes:

- <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/en-us/cognitive-toolkit/" target="_blank">CNTK</a>
- <a href="https://mxnet.io" target="_blank">MXNet</a>

O serviço do Azure Machine Learning também pode autotrain um modelo e autotune-lo para.
Por exemplo, veja [preparar um modelo de regressão automatizada de machine Learning](tutorial-auto-train-models.md).

Depois de ter um modelo, usá-lo para criar um contentor, como o Docker, o que pode ser implementado localmente para fins de teste. Depois de teste é realizado, pode implementar o modelo como um serviço da web de produção no Azure Container Instances ou no serviço Kubernetes do Azure. Para obter mais informações, consulte o artigo sobre [como implementar e, em que](how-to-deploy-and-where.md).

Em seguida, pode gerir os seus modelos implementados utilizando o [do Azure Machine Learning SDK para Python](https://aka.ms/aml-sdk) ou o [portal do Azure](https://portal.azure.com/).
Pode avaliar as métricas de modelo, reformular e voltar a implementar novas versões do modelo, ao mesmo tempo experimentações do modelo de controlo.

Para começar a utilizar o serviço Azure Machine Learning, veja [próximos passos](#next-steps).

## <a name="how-is-azure-machine-learning-service-different-from-machine-learning-studio"></a>Como serviço Azure Machine Learning é diferente do Machine Learning Studio?

[O Azure Machine Learning Studio](../studio/what-is-ml-studio.md) é uma colaboração, de arrastar e soltar visual área de trabalho onde pode criar, testar e implementar soluções de aprendizagem automática sem a necessidade de escrever código. Ele usa algoritmos e módulos de manipulação de dados de aprendizagem automática de pré-criados e pré-configuradas.

Utilize o Machine Learning Studio quando quiser experimentar modelos de machine learning de forma rápida e fácil, e quando os algoritmos de machine learning incorporados forem suficientes para as suas soluções.

Utilize o serviço Machine Learning se trabalhar num ambiente Python, quiser mais controlo sobre os algoritmos de machine learning ou quiser utilizar bibliotecas de machine learning de open source.

> [!NOTE]
> Modelos criados no Azure Machine Learning Studio não podem ser implementados ou gerenciados pelo serviço Azure Machine Learning.

## <a name="free-trial"></a>Avaliação gratuita

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

Receberá créditos para gastar em serviços do Azure. Depois de serem utilizados, pode manter a conta e utilizar os [serviços gratuitos do Azure](https://azure.microsoft.com/free/). O seu cartão de crédito não será cobrado, a menos que altere explicitamente as suas definições e peça para ser cobrado. Ou [ativar os benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), que dão-lhe créditos todos os meses que pode utilizar para os serviços do Azure pagos.

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma área de trabalho do serviço de Machine Learning](setup-create-workspace.md) para começar a utilizar.

- Siga os tutoriais completo: 
  + [Preparar um modelo de classificação de imagem com o serviço Azure Machine Learning](tutorial-train-models-with-aml.md) 
  + [Preparar dados e utilizar automatizada de machine learning para auto-preparar um modelo de regressão](tutorial-data-prep.md)

- Utilize o [SDK do Azure Machine Learning Data Prep](https://aka.ms/data-prep-sdk) para preparar seus dados.

- Saiba mais sobre [pipelines de aprendizagem automática](/azure/machine-learning/service/concept-ml-pipelines) para criar, otimizar e gerir cenários da aprendizagem automática.

- Leia o aprofundada [arquitetura de serviço do Azure Machine Learning e conceitos](concept-azure-machine-learning-architecture.md) artigo.

- Para obter mais informações, consulte [outros produtos da Microsoft de aprendizagem](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning).
