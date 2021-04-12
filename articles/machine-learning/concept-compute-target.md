---
title: O que são alvos de computação
titleSuffix: Azure Machine Learning
description: Saiba como designar um recurso ou ambiente computacional para treinar ou implementar o seu modelo com a Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
ms.openlocfilehash: 16c3ac10af7d39ec35cde1cd9d279bced54fd8aa
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062510"
---
# <a name="what-are-compute-targets-in-azure-machine-learning"></a>Quais são os destinos de computação no Azure Machine Learning?

Um *alvo de cálculo* é um recurso ou ambiente de computação designado onde executa o seu script de treino ou hospeda a sua implementação de serviço. Esta localização pode ser a sua máquina local ou um recurso computacional baseado em nuvem. A utilização de metas de computação facilita-lhe a mudança posterior do seu ambiente de computação sem ter de alterar o seu código.

Num ciclo de vida típico de desenvolvimento de modelos, você pode:

1. Comece por desenvolver e experimentar uma pequena quantidade de dados. Nesta fase, utilize o seu ambiente local, como um computador local ou uma máquina virtual baseada em nuvem (VM), como alvo de computação.
1. Dimensione até dados maiores, ou faça treino distribuído utilizando um [destes alvos de computação de formação](#train).
1. Depois de o seu modelo estar pronto, desloque-o para um ambiente de hospedagem web ou dispositivo IoT com um [destes alvos](#deploy)de computação de implementação .

Os recursos de cálculo que utiliza para os seus alvos de cálculo estão ligados a um [espaço de trabalho.](concept-workspace.md) Os recursos de cálculo que não a máquina local são partilhados pelos utilizadores do espaço de trabalho.

## <a name="training-compute-targets"></a><a name="train"></a> Metas de computação de formação

A Azure Machine Learning tem um suporte variado em diferentes alvos de computação. Um ciclo de vida típico de desenvolvimento de modelos começa com desenvolvimento ou experimentação em uma pequena quantidade de dados. Nesta fase, use um ambiente local como o seu computador local ou um VM baseado em nuvem. À medida que aumenta a sua formação em conjuntos de dados maiores ou realiza treino distribuído, use o cálculo Azure Machine Learning para criar um cluster de nó único ou multi-nós que escala automaticamente cada vez que submete uma corrida. Também pode anexar o seu próprio recurso de computação, embora o suporte para diferentes cenários possa variar.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Saiba mais sobre como submeter uma corrida de [treino a um alvo de computação](how-to-set-up-training-targets.md).

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> Metas de cálculo para inferência

Os seguintes recursos computacional podem ser utilizados para hospedar a sua implementação de modelo.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Ao realizar inferência, a Azure Machine Learning cria um recipiente Docker que acolhe o modelo e os recursos associados necessários para a sua utilização. Este recipiente é então utilizado num dos seguintes cenários de implantação:

* Como um *serviço web* que é usado para inferência em tempo real. As implementações de serviço web utilizam um dos seguintes alvos de computação:

    * [Computador local](how-to-attach-compute-targets.md#local)
    * [Instância de computação do Azure Machine Learning](how-to-create-manage-compute-instance.md)
    * [Azure Container Instances](how-to-attach-compute-targets.md#aci)
    * [Azure Kubernetes Service](how-to-create-attach-kubernetes.md)
    * Funções Azure (pré-visualização). A implementação para funções depende apenas da Aprendizagem automática Azure para construir o recipiente Docker. A partir daí, é implantado usando Funções. Para obter mais informações, consulte [Implementar um modelo de aprendizagem automática para funções Azure (pré-visualização)](how-to-deploy-functions.md).

* Como um ponto final de _inferência_ de lote que é usado periodicamente para processar lotes de dados. As inferências do lote utilizam [clusters computacional Azure Machine Learning](how-to-create-attach-compute-cluster.md).

* Para um _dispositivo IoT_ (pré-visualização). A implantação num dispositivo IoT depende apenas da Azure Machine Learning para construir o recipiente Docker. A partir daí, é implantado usando Azure IoT Edge. Para obter mais informações, consulte [implementar como um módulo IoT Edge (pré-visualização)](../iot-edge/tutorial-deploy-machine-learning.md).

Saiba [onde e como implementar o seu modelo num alvo de computação.](how-to-deploy-and-where.md)

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning compute (gerido)

Um recurso de computação gerido é criado e gerido pela Azure Machine Learning. Este cálculo está otimizado para trabalhos de aprendizagem automática. Azure Machine Learning compute clusters e [compute instances](concept-compute-instance.md) são os únicos computas geridos.

Pode criar casos de cálculo de aprendizagem automática Azure ou clusters de cálculo a partir de:

* [Estúdio Azure Machine Learning](how-to-create-attach-compute-studio.md).
* O Python SDK e o CLI:
    * [Instância computacional](how-to-create-manage-compute-instance.md).
    * [Cluster computacional](how-to-create-attach-compute-cluster.md).
* O [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (pré-visualização).
* Um modelo de gestor de recursos Azure. Para obter um modelo de exemplo, consulte [Criar um cluster de cálculo de aprendizagem automática Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute).
* Uma extensão de aprendizagem [automática para o Azure CLI](reference-azure-machine-learning-cli.md#resource-management).

Quando criados, estes recursos computacional são automaticamente parte do seu espaço de trabalho, ao contrário de outros tipos de metas de computação.


|Funcionalidade  |Cluster de cálculo  |Instância de computação  |
|---------|---------|---------|
|Cluster de nó único ou multi-nódoa     |    **&check;**       |         |
|Autoescala cada vez que submete uma corrida     |     **&check;**      |         |
|Gestão automática de clusters e agendamento de empregos     |   **&check;**        |     **&check;**      |
|Apoio aos recursos da CPU e da GPU     |  **&check;**         |    **&check;**       |


> [!NOTE]
> Quando um *cluster* de computação está inativo, ele escala automaticamente para 0 nós, para que não pague quando não está em uso. Um *caso de* computação está sempre ligado e não se auto-escala. Deve [parar a instância de cálculo](how-to-create-manage-compute-instance.md#manage) quando não está a usá-lo para evitar custos adicionais.

### <a name="supported-vm-series-and-sizes"></a>Séries e tamanhos VM suportados

Quando selecionar um tamanho de nó para um recurso de computação gerido em Azure Machine Learning, pode escolher entre os tamanhos VM selecionados disponíveis no Azure. O Azure oferece uma gama de tamanhos para Linux e Windows para diferentes cargas de trabalho. Para saber mais, consulte [os tipos e tamanhos VM](../virtual-machines/sizes.md).

Existem algumas exceções e limitações à escolha de um tamanho VM:

* Algumas séries de VM não são suportadas no Azure Machine Learning.
* Algumas séries VM são restritas. Para utilizar uma série restrita, contacte o suporte e solicite um aumento de quota para a série. Para obter informações sobre como contactar o suporte, consulte [as opções de suporte do Azure](https://azure.microsoft.com/support/options/).

Consulte a tabela seguinte para saber mais sobre séries e restrições apoiadas.

| **SérieS VM suportadas**  | **Restrições** | **Categoria** | **Apoiado por** |
|------------|------------|------------|------------|
| D | Nenhum. | Fins gerais | Aglomerados e instâncias computacional |
| DDSv4 | Nenhum. | Fins gerais | Aglomerados e instâncias computacional |
| Dv2 | Nenhum. | Fins gerais | Aglomerados e instâncias computacional |
| Dv3 | Nenhum.| Fins gerais | Aglomerados e instâncias computacional |
| DSv2 | Nenhum. | Fins gerais | Aglomerados e instâncias computacional |
| DSv3 | Nenhum.| Fins gerais | Aglomerados e instâncias computacional |
| EAv4 | Nenhum. | Otimizada para memória | Aglomerados e instâncias computacional |
| Ev3 | Nenhum. | Otimizada para memória | Aglomerados e instâncias computacional |
| FSv2 | Nenhum. | Otimizado para computação | Aglomerados e instâncias computacional |
| H | Nenhum. | Computação de elevado desempenho | Aglomerados e instâncias computacional |
| HB | Requer aprovação. | Computação de elevado desempenho | Aglomerados e instâncias computacional |
| HBv2 | Requer aprovação. |  Computação de elevado desempenho | Aglomerados e instâncias computacional |
| HCS | Requer aprovação. |  Computação de elevado desempenho | Aglomerados e instâncias computacional |
| M | Requer aprovação. | Otimizada para memória | Aglomerados e instâncias computacional |
| NC | Nenhum. |  GPU | Aglomerados e instâncias computacional |
| NC Promo | Nenhum. | GPU | Aglomerados e instâncias computacional |
| NCsv2 | Requer aprovação. | GPU | Aglomerados e instâncias computacional |
| NCsv3 | Requer aprovação. | GPU | Aglomerados e instâncias computacional |  
| NDs | Requer aprovação. | GPU | Aglomerados e instâncias computacional | 
| NDv2 | Requer aprovação. | GPU | Aglomerados e instâncias computacional | 
| NV | Nenhum. | GPU | Aglomerados e instâncias computacional | 
| NVv3 | Requer aprovação. | GPU | Aglomerados e instâncias computacional | 


Embora a Azure Machine Learning suporte estas séries VM, podem não estar disponíveis em todas as regiões do Azure. Para verificar se as séries VM estão disponíveis, consulte [os Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)

> [!NOTE]
> A Azure Machine Learning não suporta todos os tamanhos VM que o Azure Compute suporta. Para listar os tamanhos VM disponíveis, utilize um dos seguintes métodos:
> * [API REST](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable/2020-08-01/examples/ListVMSizesResult.json)
> * [Python SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
>

### <a name="compute-isolation"></a>Isolamento computacional

O Azure Machine Learning compute oferece tamanhos VM que são isolados a um tipo de hardware específico e dedicados a um único cliente. Os tamanhos de VM isolados são mais adequados para cargas de trabalho que requerem um alto grau de isolamento das cargas de trabalho de outros clientes por razões que incluem o cumprimento dos requisitos de conformidade e regulamentação. Utilizar um tamanho isolado garante que o seu VM será o único a funcionar nessa instância específica do servidor.

As atuais ofertas de VM isoladas incluem:

* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3*

*Com capacidade RDMA

Para saber mais sobre o isolamento, veja [o Isolamento na nuvem pública azul.](../security/fundamentals/isolation-choices.md)

## <a name="unmanaged-compute"></a>Computação não gerido

Um alvo computacional não gerido *não* é gerido pela Azure Machine Learning. Cria este tipo de meta de computação fora do Azure Machine Learning e, em seguida, anexá-lo ao seu espaço de trabalho. Os recursos computacional não geridos podem exigir medidas adicionais para manter ou melhorar o desempenho das cargas de trabalho de aprendizagem automática.

## <a name="next-steps"></a>Passos seguintes

Aprenda a:
* [Use um alvo computacional para treinar o seu modelo](how-to-set-up-training-targets.md)
* [Implemente o seu modelo para um alvo de computação](how-to-deploy-and-where.md)
