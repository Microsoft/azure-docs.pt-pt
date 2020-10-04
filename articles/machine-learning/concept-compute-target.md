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
ms.date: 09/29/2020
ms.openlocfilehash: ca23bb49a3592dcc139bcc04875f3867018e158d
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91707744"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Quais são os alvos de computação em Azure Machine Learning? 

Um **alvo de cálculo** é um recurso/ambiente de computação designado onde executa o seu script de treino ou hospeda a sua implementação de serviço. Esta localização pode ser a sua máquina local ou um recurso computacional baseado na nuvem. A utilização de metas de computação facilita-lhe a mudança posterior do seu ambiente de computação sem ter de alterar o seu código.  

Num ciclo de vida típico de desenvolvimento de modelos, você pode:
1. Comece por desenvolver e experimentar uma pequena quantidade de dados. Nesta fase, recomendamos o seu ambiente local (computador local ou VM baseado na nuvem) como alvo de computação. 
2. Dimensione até dados maiores, ou faça treino distribuído usando um [destes alvos de computação de formação](#train).  
3. Assim que o seu modelo estiver pronto, desloque-o para um ambiente de hospedagem web ou dispositivo IoT com um [destes alvos](#deploy)de computação de implementação .

Os recursos de cálculo que utiliza para os seus alvos de cálculo estão ligados a um [espaço de trabalho.](concept-workspace.md) Os recursos de cálculo que não a máquina local são partilhados pelos utilizadores do espaço de trabalho.

## <a name="training-compute-targets"></a><a name="train"></a> Metas de computação de formação
A Azure Machine Learning tem um suporte variado em diferentes alvos de computação. Um ciclo de vida típico de desenvolvimento de modelos começa com dev/experimentação em uma pequena quantidade de dados. Nesta fase, recomendamos a utilização de um ambiente local. Por exemplo, o seu computador local ou um VM baseado na nuvem. À medida que aumenta a sua formação em conjuntos de dados maiores, ou realiza treino distribuído, recomendamos a utilização do Azure Machine Learning Compute para criar um cluster único ou multi-nós que se autoescala cada vez que submete uma corrida. Também pode anexar o seu próprio recurso compute, embora o suporte para vários cenários possa variar conforme descrito abaixo:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Saiba mais sobre como submeter uma corrida de [treino a um alvo de computação](how-to-set-up-training-targets.md).

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> Metas de cálculo para inferência

Os seguintes recursos computacional podem ser utilizados para hospedar a sua implementação de modelo.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Ao realizar inferência, a Azure Machine Learning cria um recipiente Docker que acolhe o modelo e os recursos associados necessários para a sua utilização. Este recipiente é então utilizado num dos seguintes cenários de implantação:

* Como um __serviço web__ que é usado para inferência em tempo real. As implementações de serviço web utilizam um dos seguintes alvos de computação:

    * [Computador local](how-to-attach-compute-targets.md#local)
    * [Instância de computação do Azure Machine Learning](how-to-create-manage-compute-instance.md)
    * [Azure Container Instances](how-to-attach-compute-targets.md#aci)
    * [Azure Kubernetes Service](how-to-create-attach-kubernetes.md)
    * Funções Azure (pré-visualização). A implementação para as funções Azure depende apenas da Aprendizagem automática Azure para construir o recipiente Docker. A partir daí, é implantado utilizando funções Azure. Para obter mais informações, consulte [Implementar um modelo de aprendizagem automática para funções Azure (pré-visualização)](how-to-deploy-functions.md).

* Como um ponto final __de inferência de lote__ que é usado periodicamente para processar lotes de dados. As inferências do lote utilizam [o cluster de cálculo Azure Machine Learning](how-to-create-attach-compute-cluster.md).

* Para um __dispositivo IoT__ (pré-visualização). A implantação num dispositivo IoT depende apenas da Azure Machine Learning para construir o recipiente Docker. A partir daí, é implantado utilizando a Azure IoT Edge. Para obter mais informações, consulte [implementar como um módulo IoT Edge (pré-visualização)](/azure/iot-edge/tutorial-deploy-machine-learning).

Saiba [onde e como implementar o seu modelo num alvo de computação.](how-to-deploy-and-where.md)

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning compute (gerido)

Um recurso de computação gerido é criado e gerido pela Azure Machine Learning. Este cálculo está otimizado para trabalhos de aprendizagem automática. Azure Machine Learning compute clusters e [compute instances](concept-compute-instance.md) são os únicos computas geridos. 

Pode criar casos de cálculo de aprendizagem automática Azure ou clusters de cálculo a partir de:
* [Estúdio Azure Machine Learning](how-to-create-attach-compute-studio.md)
* Python SDK e CLI:
    * [Instância de computação](how-to-create-manage-compute-instance.md)
    * [Cluster computacional](how-to-create-attach-compute-cluster.md)
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
> Quando um cluster de computação está inativo, ele escala automaticamente para 0 nós, para que não pague quando não está em uso.  Uma *instância*computacional , no entanto, está sempre ligado e não autoescala.  Deve [parar a instância de cálculo](how-to-create-manage-compute-instance.md#manage) quando não estiver a usá-lo para evitar custos adicionais. 

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
| Dv3 | Nenhum|
| DSv2 | Nenhum | 
| DSv3 | Nenhum|
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

### <a name="compute-isolation"></a>Isolamento computacional

O Azure Machine Learning Compute oferece tamanhos de máquinas virtuais que são isolados a um tipo de hardware específico e dedicados a um único cliente. Os tamanhos de máquinas virtuais isolados são mais adequados para cargas de trabalho que requerem um alto grau de isolamento das cargas de trabalho de outros clientes por razões que incluem o cumprimento dos requisitos de conformidade e regulamentação. Utilizar um tamanho isolado garante que a sua máquina virtual será a única a funcionar nessa instância específica do servidor.

As atuais ofertas de máquinas virtuais isoladas incluem:
* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3*

*Com capacidade RDMA

Consulte aqui para saber mais sobre [o Isolamento na Nuvem Pública do Azure.](https://docs.microsoft.com/azure/security/fundamentals/isolation-choices)

## <a name="unmanaged-compute"></a>Computação não gerido

Um alvo computacional não gerido *não* é gerido pela Azure Machine Learning. Você cria este tipo de meta de computação fora do Azure Machine Learning e, em seguida, anexá-lo ao seu espaço de trabalho. Os recursos computacional não geridos podem exigir medidas adicionais para manter ou melhorar o desempenho das cargas de trabalho de aprendizagem automática.

## <a name="next-steps"></a>Próximas etapas

Aprenda a:
* [Use um alvo computacional para treinar o seu modelo](how-to-set-up-training-targets.md)
* [Implemente o seu modelo para um alvo de computação](how-to-deploy-and-where.md)
