---
title: Criar formação & implementar computas (estúdio)
titleSuffix: Azure Machine Learning
description: Utilize o estúdio para criar recursos de formação e implementação de cálculo (metas de computação) para a aprendizagem automática
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: 6cb455880852295d7176e813208a93919a2c14bb
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318262"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Criar metas de computação para formação de modelos e implantação no estúdio Azure Machine Learning

Neste artigo, aprenda a criar e gerir alvos de computação no estúdio Azure Machine.  Também pode criar e gerir alvos de computação com:

* Extensão Azure Machine Learning Learning SDK ou CLI para aprendizagem automática Azure
  * [Instância de computação](how-to-create-manage-compute-instance.md)
  * [Cluster de cálculo](how-to-create-attach-compute-cluster.md)
  * [Aglomerado de serviçoS Azure Kubernetes](how-to-create-attach-kubernetes.md)
  * [Outros recursos computacional](how-to-attach-compute-targets.md)
* A [extensão do Código VS](how-to-manage-resources-vscode.md#compute-clusters) para Azure Machine Learning.


## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree)
* [Um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>O que é um alvo computacional? 

Com a Azure Machine Learning, pode treinar o seu modelo numa variedade de recursos ou ambientes, coletivamente referidos como [__metas de computação.__](concept-azure-machine-learning-architecture.md#compute-targets) Um alvo de computação pode ser uma máquina local ou um recurso em nuvem, como um Azure Machine Learning Compute, Azure HDInsight ou uma máquina virtual remota.  Também pode criar metas de computação para a implementação do modelo, conforme descrito em ["Onde e como implementar os seus modelos".](how-to-deploy-and-where.md)

## <a name="view-compute-targets"></a><a id="portal-view"></a>Ver metas de cálculo

Para ver todos os alvos de cálculo para o seu espaço de trabalho, utilize os seguintes passos:

1. Navegue para [o estúdio Azure Machine Learning.](https://ml.azure.com)
 
1. Em __Gestão__ , __selecione Compute__.

1. Selecione separadores no topo para mostrar cada tipo de alvo de computação.

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="Ver lista de alvos de computação":::

## <a name="create-compute-target"></a><a id="portal-create"></a>Criar alvo de cálculo

Siga os passos anteriores para ver a lista de alvos de computação. Em seguida, use estes passos para criar um alvo de computação:

1. Selecione o separador na parte superior correspondente ao tipo de cálculo que irá criar.

1. Se não tiver alvos de cálculo,  **selecione Criar** no meio da página.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="Criar alvo de cálculo":::

1. Se vir uma lista de recursos de computação, selecione **+Novo** acima da lista.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="Selecione novo":::


1. Preencha o formulário para o seu tipo de cálculo:

  * [Instância de computação](#compute-instance)
  * [Clusters computacional](#amlcompute)
  * [Aglomerados de inferência](#inference-clusters)
  * [Cálculo anexo](#attached-compute)

1. Selecione __Criar__.

1. Ver o estado da operação de criação selecionando o alvo do cálculo na lista:

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="Ver estado do cálculo a partir de uma lista":::


### <a name="compute-instance"></a>Instância de computação

Use os [passos acima](#portal-create) para criar a instância de computação.  Em seguida, preencha o formulário da seguinte forma:

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Criar uma nova instância computacional":::


|Campo  |Descrição  |
|---------|---------|
|Nome da computação     |  <li>O nome é necessário e deve ter entre 3 a 24 caracteres de comprimento.</li><li>Os caracteres válidos são letras maiúsculas e minúsculas, dígitos e o  **-** personagem.</li><li>O nome deve começar com uma carta</li><li>O nome tem de ser único em todos os cálculos existentes dentro de uma região de Azure. Verá um alerta se o nome que escolher não for único</li><li>Se **-**  o caráter é usado, então precisa ser seguido por pelo menos uma letra mais tarde no nome</li>     |
|Tipo de máquina virtual |  Escolha CPU ou GPU. Este tipo não pode ser alterado após a criação     |
|Tamanho da máquina virtual     |  Os tamanhos de máquina virtual suportados podem ser restringidos na sua região. Consulte a [lista de disponibilidades](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Ativar/desativar o acesso ao SSH     |   O acesso ao SSH é desativado por predefinição.  O acesso ao SSH não pode ser. mudou após a criação. Certifique-se de que permite o acesso se planeia depurar interativamente com [o vs Code Remote](how-to-set-up-vs-code-remote.md)   |
|Definições avançadas     |  Opcional. Configurar uma rede virtual. Especifique o **grupo de Recursos** , rede **virtual** e **sub-rede** para criar a instância computacional dentro de uma Rede Virtual Azure (vnet). Para mais informações, consulte estes [requisitos de rede](./how-to-secure-training-vnet.md) para vnet.  |

### <a name="compute-clusters"></a><a name="amlcompute"></a> Clusters computacional

Crie um cluster de computação de nó único ou multi-nós para a sua formação, inferenculação de lote ou cargas de aprendizagem de reforço. Use os [passos acima](#portal-create) para criar o cluster de cálculo.  Em seguida, preencha o formulário da seguinte forma:


|Campo  |Descrição  |
|---------|---------|
|Nome da computação     |  <li>O nome é necessário e deve ter entre 3 a 24 caracteres de comprimento.</li><li>Os caracteres válidos são letras maiúsculas e minúsculas, dígitos e o  **-** personagem.</li><li>O nome deve começar com uma carta</li><li>O nome tem de ser único em todos os cálculos existentes dentro de uma região de Azure. Verá um alerta se o nome que escolher não for único</li><li>Se **-**  o caráter é usado, então precisa ser seguido por pelo menos uma letra mais tarde no nome</li>     |
|Tipo de máquina virtual |  Escolha CPU ou GPU. Este tipo não pode ser alterado após a criação     |
|Prioridade da máquina virtual | Escolha **prioridade dedicada** ou **baixa.**  As máquinas virtuais de baixa prioridade são mais baratas, mas não garantem os nós de computação. O seu trabalho pode ser antecipado.
|Tamanho da máquina virtual     |  Os tamanhos de máquina virtual suportados podem ser restringidos na sua região. Consulte a [lista de disponibilidades](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Número mínimo de nós | Número mínimo de nós que pretende providenciar. Se quiser um número dedicado de nós, desemote a contagem aqui. Poupe dinheiro definindo o mínimo para 0, para que não pague por nenhum nós quando o cluster estiver inativo. |
|Número máximo de nós | Número máximo de nós que pretende providenciar. O cálculo irá autodimensionar-se automaticamente para um máximo desta contagem de nós quando um trabalho é submetido. |
|Definições avançadas     |  Opcional. Configurar uma rede virtual. Especifique o **grupo de Recursos** , rede **virtual** e **sub-rede** para criar a instância computacional dentro de uma Rede Virtual Azure (vnet). Para mais informações, consulte estes [requisitos de rede](./how-to-secure-training-vnet.md) para vnet.   Também anexar [identidades geridas](#managed-identity) para conceder acesso a recursos     |

#### <a name="set-up-managed-identity"></a><a name="managed-identity"></a> Configurar identidade gerida

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Durante a criação do cluster ou ao editar detalhes do cluster compute, nas **definições Avançadas,** **alternar Atribuir uma identidade gerida** e especificar uma identidade atribuída ao sistema ou identidade atribuída ao utilizador.

#### <a name="managed-identity-usage"></a>Utilização de identidade gerida

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

### <a name="inference-clusters"></a>Aglomerados de inferência

> [!IMPORTANT]
> A utilização do Serviço Azure Kubernetes com Azure Machine Learning tem várias opções de configuração. Alguns cenários, como o networking, requerem configuração e configuração adicionais. Para obter mais informações sobre a utilização da AKS com Azure ML, consulte [Criar e anexar um cluster de serviço Azure Kubernetes](how-to-create-attach-kubernetes.md).

Crie ou anexe um cluster Azure Kubernetes Service (AKS) para inferenização em larga escala. Utilize os [passos acima](#portal-create) para criar o cluster AKS.  Em seguida, preencha o formulário da seguinte forma:


|Campo  |Descrição  |
|---------|---------|
|Nome da computação     |  <li>O nome é necessário. O nome deve ter entre 2 a 16 caracteres. </li><li>Os caracteres válidos são letras maiúsculas e minúsculas, dígitos e o  **-** personagem.</li><li>O nome deve começar com uma carta</li><li>O nome tem de ser único em todos os cálculos existentes dentro de uma região de Azure. Verá um alerta se o nome que escolher não for único</li><li>Se **-**  o caráter é usado, então precisa ser seguido por pelo menos uma letra mais tarde no nome</li>     |
|Kubernetes Service | Selecione **Create New** e preencha o resto do formulário.  Ou selecione **Use existente** e, em seguida, selecione um cluster AKS existente a partir da sua subscrição.
|Region |  Selecione a região onde o cluster será criado |
|Tamanho da máquina virtual     |  Os tamanhos de máquina virtual suportados podem ser restringidos na sua região. Consulte a [lista de disponibilidades](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Finalidade do cluster  | Selecione **Produção** ou **Teste de Dev** |
|Número de nós | O número de nós multiplicados pelo número de núcleos (vCPUs) da máquina virtual deve ser superior ou igual a 12. |
| Configuração de rede | Selecione **Advanced** para criar o cálculo dentro de uma rede virtual existente. Para obter mais informações sobre a AKS numa rede virtual, consulte [o isolamento da Rede durante o treino e inferência com pontos finais privados e redes virtuais.](./how-to-secure-inferencing-vnet.md) |
| Ativar a configuração SSL | Utilize isto para configurar o certificado SSL no cálculo |

### <a name="attached-compute"></a>Cálculo anexo

Para utilizar alvos de cálculo criados fora do espaço de trabalho Azure Machine Learning, deve anexá-los. A fixação de um alvo de computação torna-o disponível para o seu espaço de trabalho.  Utilize **o cálculo anexado** para anexar um alvo de computação para **o treino**.  Utilize **clusters de inferência** para anexar um cluster AKS para **inferenar**.

Utilize os [passos acima](#portal-create) para anexar um cálculo.  Em seguida, preencha o formulário da seguinte forma:

1. Insira um nome para o alvo do cálculo. 
1. Selecione o tipo de computação a fixar. Nem todos os tipos de cálculo podem ser ligados a partir do estúdio Azure Machine Learning. Os tipos de cálculo que podem ser atualmente ligados para a formação incluem:
    * Um VM remoto
    * Azure Databricks (para utilização em gasodutos de aprendizagem automática)
    * Azure Data Lake Analytics (para utilização em gasodutos de aprendizagem automática)
    * Azure HDInsight

1. Preencha o formulário e forneça valores para as propriedades necessárias.

    > [!NOTE]
    > A Microsoft recomenda que utilize as chaves SSH, que são mais seguras do que as palavras-passe. As palavras-passe são vulneráveis a ataques de força bruta. As chaves SSH dependem de assinaturas criptográficas. Para obter informações sobre como criar chaves SSH para utilização com Máquinas Virtuais Azure, consulte os seguintes documentos:
    >
    > * [Criar e utilizar chaves SSH no Linux ou no macOS](../virtual-machines/linux/mac-create-ssh-keys.md)
    > * [Criar e utilizar chaves SSH no Windows](../virtual-machines/linux/ssh-from-windows.md)

1. __Selecione Anexar__. 


## <a name="next-steps"></a>Passos seguintes

Depois de um alvo ser criado e ligado ao seu espaço de trabalho, utiliza-o na [configuração de execução](how-to-set-up-training-targets.md) com um `ComputeTarget` objeto:

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* Utilize o recurso computacional para [submeter uma formação](how-to-set-up-training-targets.md).
* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md) usa um alvo de computação gerido para treinar um modelo.
* Aprenda a [sintonizar eficientemente os hiperparmetros](how-to-tune-hyperparameters.md) para construir modelos melhores.
* Uma vez treinado, aprenda [como e onde implementar modelos.](how-to-deploy-and-where.md)
* [Use a azure machine learning com redes virtuais Azure](./how-to-network-security-overview.md)