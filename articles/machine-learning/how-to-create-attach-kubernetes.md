---
title: Criar e anexar o Serviço Azure Kubernetes
titleSuffix: Azure Machine Learning
description: O Serviço Azure Kubernetes (AKS) pode ser usado para implementar um modelo de machine learning como um serviço web. Aprenda a criar um novo cluster AKS através do Azure Machine Learning. Você também vai aprender a anexar um cluster AKS existente ao seu espaço de trabalho Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: edd4cc28c6d59f1d6e0c9cabfd5855c72bd3fe73
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661843"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Criar e anexar um cluster de serviço Azure Kubernetes
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

A Azure Machine Learning pode implementar modelos de aprendizagem automática treinados para o Serviço Azure Kubernetes. No entanto, primeiro deve __criar__ um cluster Azure Kubernetes Service (AKS) a partir do seu espaço de trabalho Azure ML ou __anexar__ um cluster AKS existente. Este artigo fornece informações sobre a criação e anexação de um cluster.

## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

- A [extensão Azure CLI para o serviço de aprendizagem automática](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)ou a extensão do [Código do Estúdio Visual Azure Machine Learning.](tutorial-setup-vscode-extension.md)

- Se planeia utilizar uma Rede Virtual Azure para garantir a comunicação entre o seu espaço de trabalho Azure ML e o cluster AKS, leia o isolamento da Rede durante o treino & artigo [de inferência.](how-to-enable-virtual-network.md)

## <a name="limitations"></a>Limitações

- Se precisar de um **Balancer de Carga Padrão (SLB)** implantado no seu cluster em vez de um Balanceador de Carga Básica (BLB), crie um cluster no portal AKS/CLI/SDK e, em seguida, **prenda-o** ao espaço de trabalho AML.

- Se tiver uma Política Azure que restringe a criação de endereços IP públicos, então a criação de clusters AKS falhará. A AKS requer um IP público para [tráfego de saídas](/azure/aks/limit-egress-traffic). O artigo de tráfego de saídas também fornece orientações para bloquear o tráfego de saídas do cluster através do IP público, exceto para alguns nomes de domínio totalmente qualificados. Existem 2 formas de permitir um IP público:
    - O cluster pode usar o IP público criado por padrão com o BLB ou SLB, ou
    - O cluster pode ser criado sem um IP público e, em seguida, um IP público é configurado com uma firewall com uma rota definida pelo utilizador. Para obter mais informações, consulte [Personalizar a saída do cluster com uma rota definida pelo utilizador.](/azure/aks/egress-outboundtype)
    
    O avião de controlo AML não fala com este IP Público. Fala com o avião de controlo da AKS para implantações. 

- Se **ligar** um cluster AKS, que tem uma [gama IP autorizada habilitada a aceder ao servidor API,](/azure/aks/api-server-authorized-ip-ranges)ative as gamas IP do plano de controlo AML para o cluster AKS. O plano de controlo AML é implantado em regiões emparelhadas e implanta cápsulas de inferência no cluster AKS. Sem acesso ao servidor API, as cápsulas de inferência não podem ser implantadas. Utilize as [gamas IP](https://www.microsoft.com/download/confirmation.aspx?id=56519) para ambas as [regiões emparelhadas](/azure/best-practices-availability-paired-regions) ao permitir as gamas IP num cluster AKS.

    Os intervalos de IP autorizados só funcionam com o Balanceador de Carga Padrão.

- Se quiser utilizar um cluster AKS privado (utilizando o Azure Private Link), deve criar primeiro o cluster e, em seguida, **anexá-lo** ao espaço de trabalho. Para obter mais informações, consulte [criar um cluster privado de Serviço Azure Kubernetes](/azure/aks/private-clusters).

- O nome computacional do cluster AKS DEVE ser único dentro do seu espaço de trabalho Azure ML.
    - O nome é necessário e deve ter entre 3 a 24 caracteres de comprimento.
    - Caracteres válidos são letras maiúsculas e minúsculas, dígitos e o - personagem.
    - O nome deve começar com uma carta.
    - O nome tem de ser único em todos os cálculos existentes dentro de uma região de Azure. Verá um alerta se o nome que escolher não for único.
   
 - Se pretender implantar modelos para nós **GPU** ou nóns **FPGA** (ou qualquer SKU específico), então deve criar um cluster com o SKU específico. Não existe suporte para a criação de um conjunto de nó secundário num cluster existente e modelos de implantação na piscina de nó secundário.
 
- Ao criar ou anexar um cluster, pode selecionar se deve criar o cluster para __teste dev__ ou __produção__. Se pretender criar um cluster AKS para __desenvolvimento,__ __validação__e __teste__ em vez de produção, desabrava o objetivo do __cluster__ __para dev-teste__. Se não especificar o objetivo do cluster, é criado um cluster de __produção.__ 

    > [!IMPORTANT]
    > Um aglomerado __de teste dev__ não é adequado para o tráfego de nível de produção e pode aumentar os tempos de inferência. Os agrupamentos de dev/teste também não garantem a tolerância à falha.

- Ao criar ou anexar um cluster, se o cluster for utilizado para __a produção,__ deve conter pelo menos 12 __CPUs virtuais__. O número de CPUs virtuais pode ser calculado multiplicando o número de nós no cluster pelo __número de núcleos__ fornecidos pelo tamanho VM selecionado. __number of nodes__ Por exemplo, se utilizar um tamanho VM de "Standard_D3_v2", que tem 4 núcleos virtuais, então deve selecionar 3 ou mais como o número de nós.

    Para um cluster __de teste dev,__ recomodámos pelo menos 2 CPUs virtuais.

- O Azure Machine Learning SDK não fornece suporte para escalar um cluster AKS. Para escalar os nós no cluster, use o UI para o seu cluster AKS no estúdio Azure Machine Learning. Só se pode alterar a contagem de nós, não o tamanho VM do cluster. Para obter mais informações sobre a escala dos nóles num cluster AKS, consulte os seguintes artigos:

    - [Dimensione manualmente a contagem de nó num cluster AKS](../aks/scale-cluster.md)
    - [Configurar o autoescalador de cluster em AKS](../aks/cluster-autoscaler.md)

## <a name="create-a-new-aks-cluster"></a>Criar um novo cluster AKS

**Estimativa de tempo:** Aproximadamente 10 minutos.

Criar ou anexar um cluster AKS é um processo único para o seu espaço de trabalho. Pode reutilizar este cluster para múltiplas implementações. Se eliminar o cluster ou o grupo de recursos que o contém, terá de criar um novo cluster da próxima vez que precisar de ser implantado. Pode ter vários clusters AKS ligados ao seu espaço de trabalho.

O exemplo a seguir demonstra como criar um novo cluster AKS utilizando o SDK e o CLI:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Para obter mais informações sobre as classes, métodos e parâmetros utilizados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py&preserve-view=true)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

Para obter mais informações, consulte o [computação az ml para criar referência aks.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para obter informações sobre a criação de um cluster AKS no portal, consulte [Criar metas de computação no estúdio Azure Machine Learning](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="attach-an-existing-aks-cluster"></a>Anexar um cluster AKS existente

**Estimativa do tempo:** Aproximadamente 5 minutos.

Se já tiver cluster AKS na sua subscrição Azure, e for a versão 1.17 ou inferior, pode usá-lo para implementar a sua imagem.

> [!TIP]
> O cluster AKS existente pode estar numa região de Azure que não seja o seu espaço de trabalho de Aprendizagem automática Azure.


> [!WARNING]
> Não crie ligações múltiplas e simultâneas ao mesmo cluster AKS do seu espaço de trabalho. Por exemplo, anexar um cluster AKS a um espaço de trabalho utilizando dois nomes diferentes. Cada novo acessório quebrará os acessórios existentes anteriores.
>
> Se pretender voltar a anexar um cluster AKS, por exemplo para alterar o TLS ou outra configuração de configuração do cluster, deve primeiro remover o acessório existente utilizando [atach.AksCompute.(](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--)

Para obter mais informações sobre a criação de um cluster AKS utilizando o Azure CLI ou portal, consulte os seguintes artigos:

* [Criar um cluster do AKS (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Criar um cluster AKS (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)
* [Crie um cluster AKS (modelo ARM em modelos de arranque rápido azure)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

O exemplo a seguir demonstra como anexar um cluster AKS existente ao seu espaço de trabalho:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

Para obter mais informações sobre as classes, métodos e parâmetros utilizados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py&preserve-view=true)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para anexar um cluster existente utilizando o CLI, é necessário obter o ID de recursos do cluster existente. Para obter este valor, utilize o seguinte comando. `myexistingcluster`Substitua-o pelo nome do seu cluster AKS. `myresourcegroup`Substitua-se pelo grupo de recursos que contém o cluster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Este comando devolve um valor semelhante ao seguinte texto:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Para fixar o cluster existente ao seu espaço de trabalho, utilize o seguinte comando. `aksresourceid`Substitua-o pelo valor devolvido pelo comando anterior. `myresourcegroup`Substitua-o pelo grupo de recursos que contém o seu espaço de trabalho. `myworkspace`Substitua-o pelo nome do seu espaço de trabalho.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Para obter mais informações, consulte a referência [aks attach aks.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para obter informações sobre a anexação de um cluster AKS no portal, consulte [Crie metas de computação no estúdio Azure Machine Learning](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="next-steps"></a>Próximos passos

* [Como e onde implementar um modelo](how-to-deploy-and-where.md)
* [Implementar um modelo para um cluster de serviço Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md)