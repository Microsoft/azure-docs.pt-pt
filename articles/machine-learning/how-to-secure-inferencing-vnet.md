---
title: Ambientes seguros de inferenculação com redes virtuais
titleSuffix: Azure Machine Learning
description: Utilize uma Rede Virtual Azure isolada para proteger o seu ambiente de inferenculação da máquina Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/16/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 359c2a27099ca298076edc255b8c30e226af0a18
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90882948"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>Garantir um ambiente de aprendizagem automática Azure com redes virtuais

Neste artigo, aprende-se a garantir ambientes de inferenização com uma rede virtual em Azure Machine Learning.

Este artigo é parte quatro de uma série de cinco partes que o acompanha através da garantia de um fluxo de trabalho de Aprendizagem automática Azure. Recomendamos vivamente que leia a [primeira parte: visão geral do VNet](how-to-network-security-overview.md) para entender primeiro a arquitetura geral. 

Veja os outros artigos desta série:

[1. Visão geral do VNet](how-to-network-security-overview.md)  >  [Fixe o espaço de trabalho](how-to-secure-workspace-vnet.md)  >  [3. Proteja o ambiente de treino](how-to-secure-training-vnet.md)  >  **4. Fixe o ambiente de inferencção**  >  [5. Ativar a funcionalidade do estúdio](how-to-enable-studio-virtual-network.md)

Neste artigo aprende-se a assegurar os seguintes recursos de inferenculação numa rede virtual:
> [!div class="checklist"]
> - Cluster de serviço padrão Azure Kubernetes (AKS)
> - Aglomerado privado de AKS
> - Azure Container Instances (ACI)


## <a name="prerequisites"></a>Pré-requisitos

+ Leia o artigo [de visão geral de segurança da Rede](how-to-network-security-overview.md) para entender cenários de rede virtual comuns e arquitetura de rede virtual geral.

+ Uma rede virtual existente e uma sub-rede para usar com os seus recursos de computação.

+ Para implantar recursos numa rede virtual ou numa sub-rede, a sua conta de utilizador deve ter permissões para as seguintes ações nos controlos de acesso baseados em funções Azure (RBAC):

    - "Microsoft.Network/virtualNetworks/join/action" no recurso de rede virtual.
    - "Microsoft.Network/virtualNetworks/subnet/join/action" no recurso sub-rede.

    Para obter mais informações sobre o RBAC com as funções em rede, consulte as [funções embutimento em rede](/azure/role-based-access-control/built-in-roles#networking)

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Para utilizar um cluster AKS numa rede virtual, devem ser cumpridos os seguintes requisitos de rede:

> [!div class="checklist"]
> * Siga os pré-requisitos em [rede avançada Configure no Serviço Azure Kubernetes (AKS)](../aks/configure-azure-cni.md#prerequisites).
> * A instância AKS e a rede virtual devem estar na mesma região. Se proteger a Conta de Armazenamento Azure utilizada pelo espaço de trabalho numa rede virtual, deve estar também na mesma rede virtual que a aks.


Para adicionar AKS numa rede virtual ao seu espaço de trabalho, utilize os seguintes passos:

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com/)e, em seguida, selecione a sua subscrição e espaço de trabalho.

1. __Selecione Compute__ à esquerda.

1. Selecione __os agrupamentos__ de inferência do centro e, em seguida, selecione __+__ .

1. No __diálogo New Inference Cluster,__ selecione __Advanced__ in __Network .__

1. Para configurar este recurso computacional para utilizar uma rede virtual, execute as seguintes ações:

    1. Na lista de down-down do __grupo de recursos,__ selecione o grupo de recursos que contém a rede virtual.
    1. Na lista de drop-down da __rede Virtual,__ selecione a rede virtual que contém a sub-rede.
    1. Na lista de drop-down __sub-rede,__ selecione a sub-rede.
    1. Na caixa __de intervalo de endereços do Serviço Kubernetes,__ insira a gama de endereços de serviço Kubernetes. Esta gama de endereços utiliza uma gama IP de encaminhamento inter-domínio sem classe (CIDR) para definir os endereços IP disponíveis para o cluster. Não deve sobrepor-se a quaisquer gamas IP da sub-rede (por exemplo, 10.0.0.0/16).
    1. Na caixa de __endereço IP do serviço Deps de serviço Kubernetes,__ insira o endereço IP de serviço DE SERVIÇO Kubernetes. Este endereço IP é atribuído ao serviço DNS da Kubernetes. Deve estar dentro da gama de endereços de serviço Kubernetes (por exemplo, 10.0.0.10).
    1. Na caixa de endereços da __ponte Docker,__ insira o endereço da ponte Docker. Este endereço IP é atribuído à Ponte Docker. Não deve estar em nenhuma gama IP de sub-rede, nem na gama de endereços de serviço Kubernetes (por exemplo, 172.17.0.1/16).

   ![Azure Machine Learning: Machine Learning Compute configurações de rede virtual](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Certifique-se de que o grupo NSG que controla a rede virtual tem uma regra de segurança de entrada ativada para o ponto final de pontuação para que possa ser chamada de fora da rede virtual.
   > [!IMPORTANT]
   > Mantenha as regras de saída padrão para o NSG. Para obter mais informações, consulte as regras de segurança em [grupos de segurança.](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)

   [![Uma regra de segurança de entrada](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Também pode utilizar o Azure Machine Learning SDK para adicionar o Serviço Azure Kubernetes numa rede virtual. Se já tiver um cluster AKS numa rede virtual, prenda-o ao espaço de trabalho descrito como descrito em [Como implantar para AKS](how-to-deploy-and-where.md). O seguinte código cria uma nova instância AKS na `default` sub-rede de uma rede virtual chamada `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Quando o processo de criação estiver concluído, pode executar inferência, ou pontuação de modelos, num cluster AKS por trás de uma rede virtual. Para mais informações, consulte [Como implementar para a AKS](how-to-deploy-and-where.md).

## <a name="private-aks-cluster"></a>Aglomerado privado de AKS

Por padrão, os clusters AKS têm um plano de controlo, ou servidor API, com endereços IP públicos. Você pode configurar aKS para usar um plano de controlo privado criando um cluster AKS privado. Para obter mais informações, consulte [criar um cluster privado de Serviço Azure Kubernetes](../aks/private-clusters.md).

Depois de criar o cluster AKS privado, [ligue o cluster à rede virtual](how-to-create-attach-kubernetes.md) para utilizar com a Azure Machine Learning.

## <a name="internal-aks-load-balancer"></a>Equilibrador de carga AKS interno

Por predefinição, as implementações AKS utilizam um [balançador de carga pública](../aks/load-balancer-standard.md). Nesta secção, aprende-se a configurar a AKS para utilizar um equilibrador de carga interno. Um equilibrador de carga interno (ou privado) é utilizado onde apenas os IPs privados são permitidos como frontend. Os balançadores de carga internos são usados para carregar o tráfego de equilíbrio dentro de uma rede virtual

Um balançador de carga privado é ativado configurando a AKS para utilizar um _equilibrador de carga interno_. 

### <a name="network-contributor-role"></a>Papel de contribuinte de rede

> [!IMPORTANT]
> Se criar ou anexar um cluster AKS fornecendo uma rede virtual que criou anteriormente, deve conceder ao principal de serviço (SP) ou identidade gerida para o seu cluster AKS a função _de Contribuinte de Rede_ para o grupo de recursos que contém a rede virtual. Isto deve ser feito antes de tentar alterar o equilibrador de carga interno para IP privado.
>
> Para adicionar a identidade como contribuinte de rede, utilize os seguintes passos:

1. Para encontrar o principal de serviço ou iD de identidade gerido para AKS, utilize os seguintes comandos Azure CLI. `<aks-cluster-name>`Substitua-o pelo nome do cluster. `<resource-group-name>`Substitua-o pelo nome do grupo de recursos que _contém o cluster AKS:_

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    Se este comando devolver um valor `msi` de, use o seguinte comando para identificar o ID principal para a identidade gerida:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. Para encontrar o ID do grupo de recursos que contém a sua rede virtual, utilize o seguinte comando. `<resource-group-name>`Substitua-o pelo nome do grupo de recursos que _contém a rede virtual:_

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. Para adicionar o principal do serviço ou identidade gerida como contribuinte de rede, utilize o seguinte comando. `<SP-or-managed-identity>`Substitua-o pelo ID devolvido pelo principal de serviço ou identidade gerida. Substitua-o pelo `<resource-group-id>` ID devolvido pelo grupo de recursos que contém a rede virtual:

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
Para obter mais informações sobre a utilização do balançador de carga interno com AKS, consulte [utilizar o balançador interno de carga com o Serviço Azure Kubernetes](/azure/aks/internal-lb).

### <a name="enable-private-load-balancer"></a>Ativar o balançador de carga privado

> [!IMPORTANT]
> Não é possível ativar o IP privado ao criar o cluster de Serviço Azure Kubernetes. Deve ser ativado como uma atualização para um cluster existente.

O seguinte corte de código demonstra como __criar um novo cluster AKS__e, em seguida, atualizá-lo para usar um equilibrador de carga IP/interno privado:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__CLI do Azure__

```azurecli-interactive
az rest --method put --uri https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/computes/<compute>?api-version=2018-11-19 --body @body.json
```

O conteúdo do `body.json` ficheiro referenciado pelo comando é semelhante ao seguinte documento JSON:

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-name>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

Ao __fixar um cluster existente__ no seu espaço de trabalho, deve esperar até depois da operação do anexar para configurar o equilibrador de carga.

Para obter informações sobre a anexação de um cluster, consulte [anexar um cluster AKS existente](how-to-create-attach-kubernetes.md).

Depois de anexar o cluster existente, pode então atualizar o cluster para utilizar um IP privado.

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="enable-azure-container-instances-aci"></a>Ativar instâncias de contentores de Azure (ACI)

As instâncias do recipiente Azure são criadas dinamicamente ao implementar um modelo. Para permitir que o Azure Machine Learning crie ACI dentro da rede virtual, deve ativar a delegação de __sub-redes__ para a sub-rede utilizada pela implantação.

> [!WARNING]
> Ao utilizar as instâncias do Azure Container numa rede virtual, a rede virtual deve estar no mesmo grupo de recursos que o seu espaço de trabalho de Aprendizagem de Máquinas Azure.
>
> Ao utilizar as instâncias do contentor Azure dentro da rede virtual, o Registo de Contentores Azure (ACR) para o seu espaço de trabalho também não pode estar na rede virtual.

Para utilizar o ACI numa rede virtual para o seu espaço de trabalho, utilize os seguintes passos:

1. Para ativar a delegação de sub-redes na sua rede virtual, utilize as informações no Add ou remova um artigo [de delegação de sub-rede.](../virtual-network/manage-subnet-delegation.md) Pode ativar a delegação ao criar uma rede virtual ou adicioná-la a uma rede existente.

    > [!IMPORTANT]
    > Ao ativar a delegação, utilize `Microsoft.ContainerInstance/containerGroups` como __sub-rede delegado o__ valor de serviço.

2. Utilize o modelo utilizando [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-&preserve-view=true), utilize os `vnet_name` parâmetros e `subnet_name` parâmetros. Deslo sente estes parâmetros no nome e na sub-rede de rede virtuais onde permitiu a delegação.


## <a name="next-steps"></a>Passos seguintes

Este artigo é parte três de uma série de rede virtual em quatro partes. Veja o resto dos artigos para aprender a proteger uma rede virtual:

* [Parte 1: Visão geral da rede virtual](how-to-network-security-overview.md)
* [Parte 2: Garantir os recursos do espaço de trabalho](how-to-secure-workspace-vnet.md)
* [Parte 3: Assegurar o ambiente de treino](how-to-secure-training-vnet.md)
* [Parte 5:Ativar a funcionalidade do estúdio](how-to-enable-studio-virtual-network.md)