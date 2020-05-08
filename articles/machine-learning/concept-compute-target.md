---
title: Quais são os alvos da computação
titleSuffix: Azure Machine Learning
description: Defina onde pretende treinar ou implementar o seu modelo com o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 03/30/2020
ms.openlocfilehash: ed65d69c18f2dbcd53324fe3cc18af8c51c546b2
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780118"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Quais são os alvos da computação no Azure Machine Learning? 

Um **alvo computacional** é um recurso/ambiente computacional designado onde executa o seu script de treino ou hospeda a sua implementação de serviço. Esta localização pode ser a sua máquina local ou um recurso computacional baseado em nuvem. A utilização de alvos computacionais facilita-lhe a alteração posterior do seu ambiente de cálculo sem ter de alterar o seu código.  

Num modelo típico de ciclo de vida de desenvolvimento, pode:
1. Comece por desenvolver e experimentar uma pequena quantidade de dados. Nesta fase, recomendamos o seu ambiente local (computador local ou VM baseado em nuvem) como o seu alvo de computação. 
2. Estensa até dados maiores ou faça formação distribuída utilizando um destes alvos de [computação de formação.](#train)  
3. Assim que o seu modelo estiver pronto, desemponha-o para um ambiente de hospedagem web ou dispositivo IoT com um [destes alvos](#deploy)de computação de implementação .

Os recursos computacionais que utiliza para os seus alvos de computação estão ligados a um espaço de [trabalho.](concept-workspace.md) Os recursos computacionais que não a máquina local são partilhados pelos utilizadores do espaço de trabalho.

## <a name="training-compute-targets"></a><a name="train"></a>Metas de computação de formação

O Azure Machine Learning tem um suporte variado em diferentes recursos computacionais.  Também pode anexar o seu próprio recurso computacional, embora o suporte para vários cenários possa variar.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Saiba mais sobre [a configuração e utilização de um alvo](how-to-set-up-training-targets.md)de cálculo para a formação de modelos.

## <a name="deployment-targets"></a><a name="deploy"></a>Metas de implantação

Os seguintes recursos computacionais podem ser usados para hospedar a implementação do seu modelo.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Saiba onde e como implementar o seu modelo para um alvo de [cálculo.](how-to-deploy-and-where.md)

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Computação azure machine learning (gerida)

Um recurso computacional gerido é criado e gerido pela Azure Machine Learning. Este cálculo está otimizado para cargas de trabalho de aprendizagem automática. Os clusters de computação azure Machine Learning e os [exemplos](concept-compute-instance.md) de cálculo são os únicos computas geridos. No futuro, poderão ser adicionados recursos computacionais adicionais.

Pode criar instâncias computacionais de Aprendizagem automática Azure (pré-visualização) ou calcular clusters a partir de:
* Azure Machine Learning studio
* Portal do Azure
* Aulas de Python SDK [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py) e [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py)
* [SDK para R](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets)
* Modelo do Resource Manager

Também pode criar clusters computacionais utilizando a extensão de [aprendizagem automática para o Azure CLI](tutorial-train-deploy-model-cli.md#create-the-compute-target-for-training).

Quando criados estes recursos computacionais fazem automaticamente parte do seu espaço de trabalho, ao contrário de outros tipos de alvos computacionais.

### <a name="compute-clusters"></a>Clusters computacionais

Pode utilizar clusters de computação Azure Machine Learning para treino e para inferência de lotes (pré-visualização).  Com este recurso de computação, você tem:

* Cluster de nó único ou multi-nó
* Autoscalcificação cada vez que submete uma corrida 
* Gestão automática de clusters e agendamento de emprego 
* Apoio tanto aos recursos da CPU como da GPU

### <a name="supported-vm-series-and-sizes"></a>Séries e tamanhos VM suportados

Ao selecionar um tamanho de nó para um recurso de computação gerido em Azure Machine Learning, pode escolher entre os tamanhos vm selecionados disponíveis em Azure. O Azure oferece uma gama de tamanhos para Linux e Windows para diferentes cargas de trabalho. Consulte aqui para saber mais sobre os diferentes [tipos e tamanhos vm](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

Existem algumas exceções e limitações para escolher um tamanho VM:
* Algumas séries VM não são suportadas no Azure Machine Learning.
* Algumas séries VM são restritas. Para utilizar uma série restrita, contacte o suporte e solicite um aumento de quota para a série. Para obter informações sobre o suporte de contato, consulte opções de [suporte Azure](https://azure.microsoft.com/support/options/)

Consulte a tabela seguinte para saber mais sobre séries e restrições apoiadas. 

| **Série VM suportada**  | **Restrições** |
|------------|------------|
| D | Nenhum |
| Dv2 | Nenhum |  
| DSv2 | Nenhum |  
| FSv2 | Nenhum |  
| M | Requer aprovação |
| NC | Nenhum |    
| NCsv2 | Requer aprovação |
| NCsv3 | Requer aprovação |  
| NDs | Requer aprovação |
| NDv2 | Requer aprovação |
| NV | Nenhum |
| NVv3 | Requer aprovação | 


Embora o Azure Machine Learning apoie estas séries VM, podem não estar disponíveis em todas as regiões do Azure. Pode consultar as séries VM aqui: [Produtos Disponíveis por Região](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

## <a name="unmanaged-compute"></a>Computação não gerida

Um alvo de cálculo não gerido *não* é gerido pela Azure Machine Learning. Cria este tipo de alvo de computação fora do Azure Machine Learning e, em seguida, fixa-o ao seu espaço de trabalho. Os recursos computacionais não geridos podem exigir passos adicionais para manter ou melhorar o desempenho das cargas de trabalho de aprendizagem automática.

## <a name="next-steps"></a>Passos seguintes

Aprenda a:
* [Configurar um alvo de computação para treinar o seu modelo](how-to-set-up-training-targets.md)
* [Implante o seu modelo para um alvo de computação](how-to-deploy-and-where.md)
