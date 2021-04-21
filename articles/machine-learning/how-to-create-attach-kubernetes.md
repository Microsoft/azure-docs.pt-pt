---
title: Criar e anexar o Serviço Azure Kubernetes
titleSuffix: Azure Machine Learning
description: Aprenda a criar um novo cluster de Serviço Azure Kubernetes através do Azure Machine Learning, ou como anexar um cluster AKS existente ao seu espaço de trabalho.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 04/08/2021
ms.openlocfilehash: 1c9434d137114560b5585b081961497412dfbf69
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770260"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Criar e anexar um cluster de serviço Azure Kubernetes

A Azure Machine Learning pode implementar modelos de aprendizagem automática treinados para o Serviço Azure Kubernetes. No entanto, primeiro deve __criar__ um cluster Azure Kubernetes Service (AKS) a partir do seu espaço de trabalho Azure ML ou __anexar__ um cluster AKS existente. Este artigo fornece informações sobre a criação e anexação de um cluster.

## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

- A [extensão Azure CLI para o serviço de aprendizagem automática](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK,](/python/api/overview/azure/ml/intro)ou a extensão do [Código do Estúdio Visual Azure Machine Learning.](tutorial-setup-vscode-extension.md)

- Se planeia utilizar uma Rede Virtual Azure para garantir a comunicação entre o seu espaço de trabalho Azure ML e o cluster AKS, leia o isolamento da Rede durante o treino & artigo [de inferência.](./how-to-network-security-overview.md)

## <a name="limitations"></a>Limitações

- Se precisar de um **Balancer de Carga Padrão (SLB)** implantado no seu cluster em vez de um Balanceador de Carga Básica (BLB), crie um cluster no portal AKS/CLI/SDK e, em seguida, **prenda-o** ao espaço de trabalho AML.

- Se tiver uma Política Azure que restringe a criação de endereços IP públicos, então a criação de clusters AKS falhará. A AKS requer um IP público para [tráfego de saídas](../aks/limit-egress-traffic.md). O artigo de tráfego de saídas também fornece orientações para bloquear o tráfego de saída do cluster através do IP público, exceto para alguns nomes de domínio totalmente qualificados. Existem 2 formas de permitir um IP público:
    - O cluster pode usar o IP público criado por padrão com o BLB ou SLB, ou
    - O cluster pode ser criado sem um IP público e, em seguida, um IP público é configurado com uma firewall com uma rota definida pelo utilizador. Para obter mais informações, consulte [Personalizar a saída do cluster com uma rota definida pelo utilizador.](../aks/egress-outboundtype.md)
    
    O avião de controlo AML não fala com este IP Público. Fala com o avião de controlo da AKS para implantações. 

- Se **ligar** um cluster AKS, que tem uma [gama IP autorizada habilitada a aceder ao servidor API,](../aks/api-server-authorized-ip-ranges.md)ative as gamas IP do plano de controlo AML para o cluster AKS. O plano de controlo AML é implantado em regiões emparelhadas e implanta cápsulas de inferência no cluster AKS. Sem acesso ao servidor API, as cápsulas de inferência não podem ser implantadas. Utilize as [gamas IP](https://www.microsoft.com/download/confirmation.aspx?id=56519) para ambas as [regiões emparelhadas](../best-practices-availability-paired-regions.md) ao permitir as gamas IP num cluster AKS.

    Os intervalos de IP autorizados só funcionam com o Balanceador de Carga Padrão.

- Ao **anexar** um cluster AKS, deve estar na mesma subscrição Azure que o seu espaço de trabalho Azure Machine Learning.

- Se quiser utilizar um cluster AKS privado (utilizando o Azure Private Link), deve criar primeiro o cluster e, em seguida, **anexá-lo** ao espaço de trabalho. Para obter mais informações, consulte [criar um cluster privado de Serviço Azure Kubernetes](../aks/private-clusters.md).

- O nome computacional do cluster AKS DEVE ser único dentro do seu espaço de trabalho Azure ML. Pode incluir letras, dígitos e traços. Deve começar com uma letra, terminar com uma letra ou dígito, e ter entre 3 e 24 caracteres de comprimento.
 
 - Se pretender implantar modelos para nós **GPU** ou nóns **FPGA** (ou qualquer SKU específico), então deve criar um cluster com o SKU específico. Não existe suporte para a criação de um conjunto de nó secundário num cluster existente e modelos de implantação na piscina de nó secundário.
 
- Ao criar ou anexar um cluster, pode selecionar se deve criar o cluster para __teste dev__ ou __produção__. Se pretender criar um cluster AKS para __desenvolvimento,__ __validação__ e __teste__ em vez de produção, desabrava o objetivo do __cluster__ __para dev-teste__. Se não especificar o objetivo do cluster, é criado um cluster de __produção.__ 

    > [!IMPORTANT]
    > Um aglomerado __de teste dev__ não é adequado para o tráfego de nível de produção e pode aumentar os tempos de inferência. Os agrupamentos de dev/teste também não garantem a tolerância à falha.

- Ao criar ou anexar um cluster, se o cluster for utilizado para __a produção,__ deve conter pelo menos 12 __CPUs virtuais__. O número de CPUs virtuais pode ser calculado multiplicando o número de nós no cluster pelo __número de núcleos__ fornecidos pelo tamanho VM selecionado.  Por exemplo, se utilizar um tamanho VM de "Standard_D3_v2", que tem 4 núcleos virtuais, então deve selecionar 3 ou mais como o número de nós.

    Para um cluster __de teste dev,__ recomodámos pelo menos 2 CPUs virtuais.

- O Azure Machine Learning SDK não fornece suporte para escalar um cluster AKS. Para escalar os nós no cluster, use o UI para o seu cluster AKS no estúdio Azure Machine Learning. Só se pode alterar a contagem de nós, não o tamanho VM do cluster. Para obter mais informações sobre a escala dos nóles num cluster AKS, consulte os seguintes artigos:

    - [Dimensione manualmente a contagem de nó num cluster AKS](../aks/scale-cluster.md)
    - [Configurar o autoescalador de cluster em AKS](../aks/cluster-autoscaler.md)

- __Não atualize diretamente o cluster utilizando uma configuração YAML__. Enquanto a Azure Kubernetes Services suporta atualizações através da configuração YAML, as implementações de Machine Learning do Azure irão sobrepor-se às suas alterações. Os únicos dois campos YAML que não serão substituídos são __os limites__ de pedido e __o CPU e a memória.__

- Criar um cluster AKS usando o estúdio Azure Machine Learning UI, SDK ou extensão CLI __não__ é idempotente. Tentar criar novamente o recurso resultará num erro que já existe um cluster com o mesmo nome.
    
    - A utilização de um modelo de Gestor de Recursos Azure e o recurso [Microsoft.MachineLearningServices/workspaces/computes](/azure/templates/microsoft.machinelearningservices/2019-11-01/workspaces/computes) para criar um cluster AKS também __não__ é idempotente. Se tentar utilizar novamente o modelo para atualizar um recurso já existente, receberá o mesmo erro.

## <a name="azure-kubernetes-service-version"></a>Versão do Azure Kubernetes Service

O Serviço Azure Kubernetes permite-lhe criar um cluster utilizando uma variedade de versões Kubernetes. Para obter mais informações sobre as versões disponíveis, consulte as [versões Kubernetes suportadas no Serviço Azure Kubernetes](../aks/supported-kubernetes-versions.md).

Ao **criar** um cluster de serviço Azure Kubernetes utilizando um dos seguintes métodos, *não tem escolha na versão* do cluster que é criado:

* Estúdio Azure Machine Learning, ou a secção Azure Machine Learning do portal Azure.
* Extensão de Aprendizagem automática para Azure CLI.
* Azure Machine Learning SDK.

Estes métodos de criação de um cluster AKS utilizam a versão __padrão__ do cluster. *A versão padrão muda ao longo do tempo à* medida que as novas versões Kubernetes ficam disponíveis.

Ao **anexar** um cluster AKS existente, apoiamos todas as versões AKS suportadas atualmente.

> [!NOTE]
> Pode haver casos de borda em que você tem um cluster mais antigo que já não é suportado. Neste caso, a operação de anexação devolverá um erro e listará as versões atualmente suportadas.
>
> Pode anexar versões de **pré-visualização.** A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. O suporte para a utilização de versões de pré-visualização pode ser limitado. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="available-and-default-versions"></a>Versões disponíveis e padrão

Para encontrar as versões AKS disponíveis e predefinidas, utilize as [versões get-vers do](/cli/azure/aks#az_aks_get_versions)comando [Azure CLI](/cli/azure/install-azure-cli) . Por exemplo, o seguinte comando devolve as versões disponíveis na região oeste dos EUA:

```azurecli-interactive
az aks get-versions -l westus -o table
```

A saída deste comando é semelhante ao seguinte texto:

```text
KubernetesVersion    Upgrades
-------------------  ----------------------------------------
1.18.6(preview)      None available
1.18.4(preview)      1.18.6(preview)
1.17.9               1.18.4(preview), 1.18.6(preview)
1.17.7               1.17.9, 1.18.4(preview), 1.18.6(preview)
1.16.13              1.17.7, 1.17.9
1.16.10              1.16.13, 1.17.7, 1.17.9
1.15.12              1.16.10, 1.16.13
1.15.11              1.15.12, 1.16.10, 1.16.13
```

Para encontrar a versão padrão que é usada ao **criar** um cluster através do Azure Machine Learning, pode utilizar o `--query` parâmetro para selecionar a versão predefinida:

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

A saída deste comando é semelhante ao seguinte texto:

```text
Result
--------
1.16.13
```

Se quiser verificar **programáticamente as versões disponíveis,** utilize o Cliente de Serviço de [Contentores - List Orchestrators](/rest/api/container-service/container%20service%20client/listorchestrators) REST API. Para encontrar as versões disponíveis, veja as entradas onde `orchestratorType` está `Kubernetes` . As `orchestrationVersion` entradas associadas contêm as versões disponíveis que podem ser **anexadas** ao seu espaço de trabalho.

Para encontrar a versão padrão que é usada ao **criar** um cluster através do Azure Machine Learning, encontre a entrada onde `orchestratorType` está e está `Kubernetes` `default` `true` . O valor associado `orchestratorVersion` é a versão padrão. O seguinte snippet JSON mostra uma entrada de exemplo:

```json
...
 {
        "orchestratorType": "Kubernetes",
        "orchestratorVersion": "1.16.13",
        "default": true,
        "upgrades": [
          {
            "orchestratorType": "",
            "orchestratorVersion": "1.17.7",
            "isPreview": false
          }
        ]
      },
...
```

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

* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

Para obter mais informações, consulte o [computação az ml para criar referência aks.](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-aks)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para obter informações sobre a criação de um cluster AKS no portal, consulte [Criar metas de computação no estúdio Azure Machine Learning](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="attach-an-existing-aks-cluster"></a>Anexar um cluster AKS existente

**Estimativa do tempo:** Aproximadamente 5 minutos.

Se já tem cluster AKS na sua subscrição Azure, pode usá-lo com o seu espaço de trabalho.

> [!TIP]
> O cluster AKS existente pode estar numa região de Azure que não seja o seu espaço de trabalho de Aprendizagem automática Azure.


> [!WARNING]
> Não crie ligações múltiplas e simultâneas ao mesmo cluster AKS do seu espaço de trabalho. Por exemplo, anexar um cluster AKS a um espaço de trabalho utilizando dois nomes diferentes. Cada novo acessório quebrará os acessórios existentes anteriores.
>
> Se pretender voltar a anexar um cluster AKS, por exemplo para alterar o TLS ou outra configuração de configuração do cluster, deve primeiro remover o acessório existente utilizando [atach.AksCompute.(](/python/api/azureml-core/azureml.core.compute.akscompute#detach--)

Para obter mais informações sobre a criação de um cluster AKS utilizando o Azure CLI ou portal, consulte os seguintes artigos:

* [Criar um cluster do AKS (CLI)](/cli/azure/aks?bc=%2fazure%2fbread%2ftoc.json&toc=%2fazure%2faks%2fTOC.json#az_aks_create)
* [Criar um cluster AKS (portal)](../aks/kubernetes-walkthrough-portal.md)
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

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.attach](/python/api/azureml-core/azureml.core.compute.computetarget#attach-workspace--name--attach-configuration-)

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

Para obter mais informações, consulte a referência [aks attach aks.](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para obter informações sobre a anexação de um cluster AKS no portal, consulte [Crie metas de computação no estúdio Azure Machine Learning](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="create-or-attach-an-aks-cluster-with-tls-termination"></a>Criar ou anexar um cluster AKS com rescisão de TLS
Quando [criar ou anexar um cluster AKS,](how-to-create-attach-kubernetes.md)pode ativar a terminação do TLS com AksCompute.provisioning_configuration **[e](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** objetos de configuração. Ambos os métodos devolvem um objeto de configuração que tem um método **enable_ssl,** e pode utilizar **enable_ssl** método para ativar o TLS.

O exemplo seguinte mostra como ativar a terminação do TLS com a geração e configuração automáticas de certificados TLS utilizando o certificado Microsoft sob o capot.
```python
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # Enable TLS termination when you create an AKS cluster by using provisioning_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
   # where "######" is a random series of characters
   provisioning_config.enable_ssl(leaf_domain_label = "contoso")
   
   # Enable TLS termination when you attach an AKS cluster by using attach_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
   # where "######" is a random series of characters
   attach_config.enable_ssl(leaf_domain_label = "contoso")


```
O exemplo seguinte mostra como ativar a rescisão de TLS com certificado personalizado e nome de domínio personalizado. Com domínio e certificado personalizados, tem de atualizar o seu registo DNS para apontar para o endereço IP do ponto final de pontuação, consulte [atualização do seu DNS](how-to-secure-web-service.md#update-your-dns)

```python
   from azureml.core.compute import AksCompute, ComputeTarget

   # Enable TLS termination with custom certificate and custom domain when creating an AKS cluster
   
   provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    
   # Enable TLS termination with custom certificate and custom domain when attaching an AKS cluster

   attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")


```
>[!NOTE]
> Para obter mais informações sobre como garantir a implementação do modelo no cluster AKS, consulte [o uso de TLS para garantir um serviço web através do Azure Machine Learning](how-to-secure-web-service.md)

## <a name="create-or-attach-an-aks-cluster-to-use-internal-load-balancer-with-private-ip"></a>Criar ou anexar um cluster AKS para utilizar o Balançador de Carga Interna com IP privado
Quando criar ou anexar um cluster AKS, pode configurar o cluster para utilizar um Balançador de Carga Interna. Com um Balançador de Carga Interna, os pontos finais de pontuação para as suas implementações para AKS utilizarão um IP privado dentro da rede virtual. Os cortes de código que seguem mostram como configurar um Balançador de Carga Interna para um cluster AKS.
```python
   
   from azureml.core.compute.aks import AksUpdateConfiguration
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # When you create an AKS cluster, you can specify Internal Load Balancer to be created with provisioning_config object
   provisioning_config = AksCompute.provisioning_configuration(load_balancer_type = 'InternalLoadBalancer')

   # when you attach an AKS cluster, you can update the cluster to use internal load balancer after attach
   aks_target = AksCompute(ws,"myaks")

   # Change to the name of the subnet that contains AKS
   subnet_name = "default"
   # Update AKS configuration to use an internal load balancer
   update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
   aks_target.update(update_config)
   # Wait for the operation to complete
   aks_target.wait_for_completion(show_output = True)
   
   
```
>[!IMPORTANT]
> A Azure Machine Learning não suporta a rescisão de TLS com o Balançador de Carga Interna. O Balancer de Carga Interna tem um IP privado e esse IP privado pode estar noutra rede e o certificado pode ser recusado. 

>[!NOTE]
> Para obter mais informações sobre como garantir ambiente de inferencção, consulte [Secure a Azure Machine Learning Inferencing Environment](how-to-secure-inferencing-vnet.md)

## <a name="detach-an-aks-cluster"></a>Desprender um cluster AKS

Para separar um cluster do seu espaço de trabalho, utilize um dos seguintes métodos:

> [!WARNING]
> A utilização do estúdio Azure Machine Learning, SDK ou a extensão Azure CLI para aprendizagem automática para separar um cluster AKS **não elimina o cluster AKS**. Para eliminar o cluster, consulte [utilizar o CLI Azure com AKS](../aks/kubernetes-walkthrough.md#delete-the-cluster).

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para desprender o cluster existente ao seu espaço de trabalho, utilize o seguinte comando. `myaks`Substitua-o pelo nome de que o cluster AKS está ligado ao seu espaço de trabalho como. `myresourcegroup`Substitua-o pelo grupo de recursos que contém o seu espaço de trabalho. `myworkspace`Substitua-o pelo nome do seu espaço de trabalho.

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

No estúdio Azure Machine Learning, __selecione Compute,__ __Inference clusters__ e o cluster que deseja remover. Utilize a __ligação Detach__ para separar o cluster.

---

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="update-the-cluster"></a>Atualizar o cluster

As atualizações dos componentes de Aprendizagem automática Azure instalados num cluster de serviço Azure Kubernetes devem ser aplicadas manualmente. 

Pode aplicar estas atualizações desprendendo o cluster do espaço de trabalho Azure Machine Learning e, em seguida, recolocar o cluster no espaço de trabalho. Se o TLS estiver ativado no cluster, terá de fornecer o certificado TLS/SSL e a chave privada ao voltar a ligar o cluster. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Se já não tiver o certificado TLS/SSL e a chave privada, ou estiver a utilizar um certificado gerado pela Azure Machine Learning, poderá recuperar os ficheiros antes de desvincular o cluster, ligando-se ao cluster utilizando `kubectl` e recuperando o `azuremlfessl` segredo.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes armazena os segredos em formato codificado base-64. Terá de descodificar os `cert.pem` `key.pem` e componentes dos segredos antes de os fornecer `attach_config.enable_ssl` a . 

### <a name="webservice-failures"></a>Falhas no serviço web

Muitas falhas de webservice em AKS podem ser depuradas ligando-se ao cluster usando `kubectl` . Você pode obter o `kubeconfig.json` para um cluster AKS correndo

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="next-steps"></a>Passos seguintes

* [Use a Azure RBAC para a autorização de Kubernetes](../aks/manage-azure-rbac.md)
* [Como e onde implementar um modelo](how-to-deploy-and-where.md)
* [Implementar um modelo para um cluster de serviço Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md)