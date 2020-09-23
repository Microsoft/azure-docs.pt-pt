---
title: O que são alvos de computação
titleSuffix: Azure Machine Learning
description: Defina onde pretende treinar ou implementar o seu modelo com a Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/27/2020
ms.openlocfilehash: 3e1832d656ea2bad772a686e52a357c1daf0ded5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90883206"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Quais são os alvos de computação em Azure Machine Learning? 

Um **alvo de cálculo** é um recurso/ambiente de computação designado onde executa o seu script de treino ou hospeda a sua implementação de serviço. Esta localização pode ser a sua máquina local ou um recurso computacional baseado na nuvem. A utilização de metas de computação facilita-lhe a mudança posterior do seu ambiente de computação sem ter de alterar o seu código.  

Num ciclo de vida típico de desenvolvimento de modelos, você pode:
1. Comece por desenvolver e experimentar uma pequena quantidade de dados. Nesta fase, recomendamos o seu ambiente local (computador local ou VM baseado na nuvem) como alvo de computação. 
2. Dimensione até dados maiores, ou faça treino distribuído usando um [destes alvos de computação de formação](#train).  
3. Assim que o seu modelo estiver pronto, desloque-o para um ambiente de hospedagem web ou dispositivo IoT com um [destes alvos](#deploy)de computação de implementação .

Os recursos de cálculo que utiliza para os seus alvos de cálculo estão ligados a um [espaço de trabalho.](concept-workspace.md) Os recursos de cálculo que não a máquina local são partilhados pelos utilizadores do espaço de trabalho.

## <a name="training-compute-targets"></a><a name="train"></a> Metas de computação de formação

A Azure Machine Learning tem um suporte variado em diferentes recursos compute.  Também pode anexar o seu próprio recurso de computação, embora o suporte para vários cenários possa variar.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Saiba mais sobre [a utilização de um alvo de computação para a formação de modelos.](how-to-set-up-training-targets.md)

## <a name="deployment-targets"></a><a name="deploy"></a>Destinos de implementação

Os seguintes recursos computacional podem ser utilizados para hospedar a sua implementação de modelo.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Saiba [onde e como implementar o seu modelo num alvo de computação.](how-to-deploy-and-where.md)

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning compute (gerido)

Um recurso de computação gerido é criado e gerido pela Azure Machine Learning. Este cálculo está otimizado para trabalhos de aprendizagem automática. Azure Machine Learning compute clusters e [compute instances](concept-compute-instance.md) são os únicos computas geridos. 

Pode criar casos de cálculo de aprendizagem automática Azure ou clusters de cálculo a partir de:
* [Azure Machine Learning studio](how-to-create-attach-compute-studio.md)
* Portal do Azure
* Aulas python SDK [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py&preserve-view=true) e [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py&preserve-view=true)
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (pré-visualização)
* Modelo de gestor de recursos. Para um modelo de exemplo, consulte o [modelo de computação de aprendizagem de máquinas Azure.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute)
* Extensão de aprendizagem [automática para o Azure CLI](reference-azure-machine-learning-cli.md#resource-management).  

Quando criados estes recursos computacional são automaticamente parte do seu espaço de trabalho, ao contrário de outros tipos de metas de computação.


|Funcionalidade  |Cluster computacional  |Instância de computação  |
|---------|---------|---------|
|Cluster de nó único ou multi-nódoa     |    **&check;**       |         |
|Autoescala cada vez que submete uma corrida     |     **&check;**      |         |
|Gestão automática de clusters e agendamento de empregos     |   **&check;**        |     **&check;**      |
|Apoio aos recursos da CPU e da GPU     |  **&check;**         |    **&check;**       |


> [!NOTE]
> Quando um cluster de computação está inativo, ele escala automaticamente para 0 nós, para que não pague quando não está em uso.  Uma *instância*computacional , no entanto, está sempre ligado e não autoescala.  Deve [parar a instância de cálculo](concept-compute-instance.md#managing-a-compute-instance) quando não estiver a usá-lo para evitar custos adicionais. 

### <a name="supported-vm-series-and-sizes"></a>Séries e tamanhos VM suportados

Quando selecionar um tamanho de nó para um recurso de computação gerido em Azure Machine Learning, pode escolher entre os tamanhos VM selecionados disponíveis no Azure. O Azure oferece uma gama de tamanhos para Linux e Windows para diferentes cargas de trabalho. Consulte aqui para saber mais sobre os [diferentes tipos e tamanhos VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

Existem algumas exceções e limitações à escolha de um tamanho VM:
* Algumas séries VM não são suportadas em Azure Machine Learning.
* Algumas séries VM são restritas. Para utilizar uma série restrita, contacte o suporte e solicite um aumento de quota para a série. Para obter informações sobre o suporte de contato, consulte [as opções de suporte do Azure](https://azure.microsoft.com/support/options/)

Consulte a tabela seguinte para saber mais sobre séries e restrições apoiadas. 

| **SérieS VM suportadas**  | **Restrições** |
|------------|------------|
| D | Nenhum |
| Dv2 | Nenhum |  
| DSv2 | Nenhum |  
| FSv2 | Nenhum | 
| HBv2 | Requer aprovação |  
| HCS | Requer aprovação |  
| M | Requer aprovação |
| NC | Nenhum |    
| NCsv2 | Requer aprovação |
| NCsv3 | Requer aprovação |  
| NDs | Requer aprovação |
| NDv2 | Requer aprovação |
| NV | Nenhum |
| NVv3 | Requer aprovação | 


Embora a Azure Machine Learning suporte estas séries VM, podem não estar disponíveis em todas as regiões do Azure. Pode consultar as séries VM: [Produtos disponíveis por Região.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)

## <a name="unmanaged-compute"></a>Computação não gerido

Um alvo computacional não gerido *não* é gerido pela Azure Machine Learning. Você cria este tipo de meta de computação fora do Azure Machine Learning e, em seguida, anexá-lo ao seu espaço de trabalho. Os recursos computacional não geridos podem exigir medidas adicionais para manter ou melhorar o desempenho das cargas de trabalho de aprendizagem automática.

## <a name="next-steps"></a>Passos seguintes

Aprenda a:
* [Use um alvo computacional para treinar o seu modelo](how-to-set-up-training-targets.md)
* [Implemente o seu modelo para um alvo de computação](how-to-deploy-and-where.md)
