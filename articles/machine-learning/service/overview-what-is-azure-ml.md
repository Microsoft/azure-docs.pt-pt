---
title: O que é
titleSuffix: Azure Machine Learning service
description: Descrição geral do serviço do Azure Machine Learning - uma solução de ciência de dados ponto a ponto integrada para cientistas de dados profissionais desenvolver, experimentar e implementar aplicações de análise avançada à escala da cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: c9ebacd8caf992874969106df58531538ea399ed
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510433"
---
# <a name="what-is-azure-machine-learning-service"></a>O que é o serviço Azure Machine Learning?

O serviço do Azure Machine Learning é um serviço em nuvem que utiliza para preparar, implementar, automatizar e gerir modelos de aprendizagem automática, tudo na escala amplo que a cloud oferece.

## <a name="what-is-machine-learning"></a>O que é o Machine Learning?

A aprendizagem automática é uma técnica da ciência de dados que permite aos computadores utilizar dados existentes para prever futuros comportamentos, resultados e tendências. Com o machine learning, os computadores aprendem sem serem programados explicitamente.

As previsões ou predições da aprendizagem automática podem tornar as aplicações e os dispositivos mais inteligentes. Por exemplo, quando faz compras online, machine learning recomenda outros produtos que pode desejar com base no que comprou. Quando o seu cartão de crédito é utilizado, o machine learning compara a transação com uma base de dados de transações e ajuda a detetar fraudes. Quando o robô aspirador limpa uma sala, o machine learning ajuda-o a decidir se a tarefa está concluída.

## <a name="what-is-azure-machine-learning-service"></a>O que é o serviço Azure Machine Learning?

O serviço do Azure Machine Learning fornece um ambiente com base na cloud, que pode utilizar para de preparação de dados, formar, testar, implementar, gerir e controlar os modelos de aprendizagem automática. Inicie o treinamento no seu computador local e, em seguida, aumentar horizontalmente para a cloud. O serviço totalmente compatível com tecnologias de open source como PyTorch, TensorFlow e scikit-saiba e pode ser usado para qualquer tipo de machine learning, desde ml clássicas para aprendizagem profunda, supervisionado e learning não supervisionado. 

Explorar e preparar dados, formar e testar modelos e implementá-las com ferramentas avançadas, como:
+ R [visual interface](ui-quickstart-run-experiment.md) no qual pode módulos de arrastar-soltar-de-n para criar suas experimentações e, em seguida, implementar modelos
+ [Blocos de notas do Jupyter](https://jupyter.org) em que usar o [SDKs](https://docs.microsoft.com/azure/machine-learning/service/#reference) escrever seu próprio código, como [estes blocos de notas de exemplo](https://aka.ms/aml-notebooks)
+ [Extensão do Visual Studio Code](how-to-vscode-tools.md)

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>O que posso fazer com o serviço Azure Machine Learning?

Utilizar o <a href="https://aka.ms/aml-sdk" target="_blank">Azure Machine Learning Python SDK</a> com pacotes Python de código-fonte aberto, ou utilize o [interface visual (pré-visualização)](ui-quickstart-run-experiment.md) para criar e treinar a aprendizagem automática altamente precisos e modelos de aprendizagem profunda -se num área de trabalho de serviço do Azure Machine Learning.

Pode escolher entre vários componentes de aprendizado de máquina disponíveis em pacotes de Python de código-fonte aberto, como <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-Saiba</a>, <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>, <a href="https://pytorch.org" target="_blank">PyTorch</a>e <a href="https://mxnet.io" target="_blank">MXNet</a>.

Se escreve código ou utilize a interface visual, pode monitorizar várias execuções conforme experimentar para encontrar a melhor solução, bem como gerir os modelos implementados.

### <a name="code-first-experience"></a>Experiência de código em primeiro lugar

Iniciar o treinamento no seu computador local com o <a href="https://aka.ms/aml-sdk" target="_blank">SDK de Python do Azure Machine Learning</a> e, em seguida, aumentar horizontalmente para a cloud. Em muitas disponíveis [destinos de computação](how-to-set-up-training-targets.md), como a computação do Azure Machine Learning e [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks)e com [avançada dos serviços de otimização de hiper-parâmetros](how-to-tune-hyperparameters.md), pode criar modelos melhores mais rapidamente ao utilizar o poder da cloud.

Também pode [automatizar a preparação de modelos e ajuste](tutorial-auto-train-models.md) utilizando o SDK.

### <a name="code-free--low-code-experience"></a>Experiência de código sem código / baixa

Para obter treinamento sem código, tente:

+ A interface visual para experimentar arrastar-n-soltar e implementação
    
    ![Interface visual para o serviço Azure Machine Learning](media/overview-what-is-azure-ml/visual-interface.png)

+ A opção do portal do Azure para experiências de ML automatizadas

### <a name="operationalization-mlops"></a>Operacionalização (MLOps)

Quando tiver o modelo certo, pode usá-lo facilmente num serviço da web, um dispositivo IoT ou do Power BI. Para obter mais informações, consulte o artigo sobre [como implementar e, em que](how-to-deploy-and-where.md). 

Em seguida, pode gerir os seus modelos implementados utilizando o [do Azure Machine Learning SDK para Python](https://aka.ms/aml-sdk) ou o [portal do Azure](https://portal.azure.com/). 

Esses modelos podem ser consumidos e retornar previsões na [tempo real](how-to-consume-web-service.md) ou [assincronamente](how-to-run-batch-predictions.md) em grandes quantidades de dados.

E com avançados [de machine learning pipelines](concept-ml-pipelines.md), pode colaborar em cada etapa de preparação de dados, a preparação de modelos e a avaliação, por meio da implantação.

Para começar a utilizar o serviço Azure Machine Learning, veja [próximos passos](#next-steps).

## <a name="how-does-azure-machine-learning-service-differ-from-studio"></a>Serviço Azure Machine Learning difere do Studio?

[Machine Learning Studio](../studio/what-is-ml-studio.md) é uma colaboração, de arrastar e soltar visual área de trabalho onde pode criar, testar e implementar soluções de aprendizagem automática sem a necessidade de escrever código. Ela usa algoritmos de aprendizagem pré-criados e pré-configuradas e plataforma de computação de módulos de manipulação de dados, bem como proprietário.

O serviço do Azure Machine Learning fornece os dois SDKs **- e -** um interface(preview) visual, a rapidez de preparação de dados, preparar e implementar modelos de aprendizagem automática. Essa interface visual (pré-visualização) fornece uma experiência de arrastar e largar semelhante ao Studio. No entanto, ao contrário da plataforma de computação proprietárias do Studio, a interface visual utiliza os seus próprios recursos de computação e está totalmente integrada no serviço Azure Machine Learning.

Aqui está uma comparação rápida.

|| Machine Learning Studio | Azure Machine Learning service:<br/>Interface visual|
|---| --- | --- |
|| Geralmente disponível (GA) | Em pré-visualização|
|Módulos para interface de rede| Muitos | Conjunto inicial de módulos populares|
|Destinos de computação de treinamento| Destino de computação proprietárias, apenas suporte de CPU| Oferece suporte a computação do Azure Machine Learning, GPU ou CPU.<br/>(Outras computações suportadas no SDK)|
|Destinos de computação de implementação| Formato do proprietário de web service, não podem ser personalizado | Opções de segurança empresarial e o serviço Kubernetes do Azure. <br/>([Outras computações](how-to-deploy-and-where.md) suportado no SDK) |
|Preparação de modelos automatizados e a otimização de hiper-parâmetros | Não | Ainda não na visual interface. <br/> (Suportado no portal do SDK e o Azure). | 

Experimente a interface visual (pré-visualização) com [início rápido: Preparar e visualize os dados sem escrever código](ui-quickstart-run-experiment.md)

> [!NOTE]
> Modelos criados no Studio não podem ser implementados ou gerenciados pelo serviço Azure Machine Learning. No entanto, os modelos criados e implementados na interface visual do serviço podem ser geridos através a área de trabalho do serviço do Azure Machine Learning.

## <a name="free-trial"></a>Versão de avaliação gratuita

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

Receberá créditos para gastar em serviços do Azure. Depois de serem utilizados, pode manter a conta e utilizar os [serviços gratuitos do Azure](https://azure.microsoft.com/free/). O seu cartão de crédito não será cobrado, a menos que altere explicitamente as suas definições e peça para ser cobrado. Ou [ativar os benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), que dão-lhe créditos todos os meses que pode utilizar para os serviços do Azure pagos.

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma área de trabalho do serviço de Machine Learning](setup-create-workspace.md) para começar a utilizar.

- Siga os tutoriais completo: 
  + [Preparar um modelo de classificação de imagem com o serviço Azure Machine Learning](tutorial-train-models-with-aml.md) 
  + [Preparar dados e utilizar automatizada de machine learning para auto-preparar um modelo de regressão](tutorial-data-prep.md)

- Saiba mais sobre [pipelines de aprendizagem automática](/azure/machine-learning/service/concept-ml-pipelines) para criar, otimizar e gerir cenários da aprendizagem automática.

- Leia o aprofundada [arquitetura de serviço do Azure Machine Learning e conceitos](concept-azure-machine-learning-architecture.md) artigo.

- Para obter mais informações, consulte [outros produtos da Microsoft de aprendizagem](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning).
