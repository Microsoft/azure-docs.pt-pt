---
title: Como implantar modelos no serviço kubernetes do Azure
titleSuffix: Azure Machine Learning
description: Saiba como implantar seus modelos de Azure Machine Learning como um serviço Web usando o serviço kubernetes do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 01/16/2020
ms.openlocfilehash: db2e80ebb6cbe5f31f2d99a1403a15daf38fd877
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722412"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Implantar um modelo em um cluster do serviço kubernetes do Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como usar Azure Machine Learning para implantar um modelo como um serviço Web no AKS (serviço kubernetes do Azure). O serviço kubernetes do Azure é bom para implantações de produção de grande escala. Use o serviço kubernetes do Azure se você precisar de um ou mais dos seguintes recursos:

- __Tempo de resposta rápida.__
- __Autoscalcificação__ do serviço implantado.
- __Opções__ de aceleração de hardware tais como GPU e matrizes de porta programáveis de campo (FPGA).

> [!IMPORTANT]
> O dimensionamento do cluster não é fornecido por meio do SDK do Azure Machine Learning. Para obter mais informações sobre a escala dos nós num cluster AKS, consulte [Scale o nó contando num cluster AKS](../aks/scale-cluster.md).

Ao ser implantado no Serviço Azure Kubernetes, desdobre-se num cluster AKS que está __ligado ao seu espaço__de trabalho . Há duas maneiras de conectar um cluster AKS ao seu espaço de trabalho:

* Crie o cluster AKS utilizando o Azure Machine Learning SDK, o Machine Learning CLI ou o [estúdio Azure Machine Learning.](https://ml.azure.com) Esse processo conecta automaticamente o cluster ao espaço de trabalho.
* Anexe um cluster AKS existente ao seu espaço de trabalho Azure Machine Learning. Um cluster pode ser anexado usando o SDK do Azure Machine Learning, Machine Learning CLI ou Azure Machine Learning Studio.

> [!IMPORTANT]
> O processo de criação ou anexo é uma tarefa ocasional. Depois que um cluster AKS estiver conectado ao espaço de trabalho, você poderá usá-lo para implantações. Você pode desanexar ou excluir o cluster AKS se não precisar mais dele. Uma vez destacado ou eliminado, deixará de ser capaz de se deslocar para o cluster.

## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho do Azure Machine Learning. Para mais informações, consulte Criar um espaço de [trabalho azure machine learning](how-to-manage-workspace.md).

- Um modelo de aprendizado de máquina registrado em seu espaço de trabalho. Se não tem um modelo registado, veja [como e onde implementar modelos.](how-to-deploy-and-where.md)

- A [extensão Azure CLI para o serviço de Machine Learning,](reference-azure-machine-learning-cli.md) [Azure Machine Learning Python SDK,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a extensão do Código visual de [aprendizagem automática Azure.](tutorial-setup-vscode-extension.md)

- Os fragmentos de código __Python__ neste artigo assumem que as seguintes variáveis são definidas:

    * `ws` - Coloque no seu espaço de trabalho.
    * `model` - Desloque-se ao seu modelo registado.
    * `inference_config` - Definir para a configuração de inferência para o modelo.

    Para obter mais informações sobre a definição destas variáveis, consulte [Como e onde implementar modelos](how-to-deploy-and-where.md).

- Os cortes __cli__ neste artigo assumem que criou um documento `inferenceconfig.json`. Para obter mais informações sobre a criação deste documento, consulte [Como e onde implementar modelos.](how-to-deploy-and-where.md)

## <a name="create-a-new-aks-cluster"></a>Criar um novo cluster AKS

**Estimativa de tempo**: Aproximadamente 20 minutos.

Criar ou anexar um cluster AKS é um processo de uma vez para seu espaço de trabalho. Pode reutilizar este cluster para várias implementações. Se você excluir o cluster ou o grupo de recursos que o contém, deverá criar um novo cluster na próxima vez que precisar implantá-lo. Você pode ter vários clusters AKS anexados ao seu espaço de trabalho.

> [!TIP]
> Se você quiser proteger o cluster AKS usando uma rede virtual do Azure, primeiro deverá criar a rede virtual. Para mais informações, consulte [A experimentação segura e a inferência com](how-to-enable-virtual-network.md#aksvnet)a Rede Virtual Azure .

Se pretender criar um cluster AKS para __desenvolvimento,__ __validação__e __teste__ em vez de produção, pode especificar o objetivo do __cluster__ para __dev test__.

> [!WARNING]
> Se definir `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, o cluster que é criado não é adequado para o tráfego de nível de produção e pode aumentar os tempos de inferência. Os clusters de desenvolvimento/teste também não garantem a tolerância a falhas. Recomendamos pelo menos duas CPUs virtuais para clusters de desenvolvimento/teste.

Os exemplos a seguir demonstram como criar um novo cluster AKS usando o SDK e a CLI:

**Usando o SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Para [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), se escolher valores personalizados para `agent_count` e `vm_size`, e `cluster_purpose` não é `DEV_TEST`, então você precisa ter certeza de que `agent_count` multiplicado por `vm_size` é maior ou igual a 12 CPUs virtuais. Por exemplo, se utilizar um `vm_size` de "Standard_D3_v2", que tem 4 CPUs virtuais, então deve escolher uma `agent_count` de 3 ou mais.
>
> O SDK do Azure Machine Learning não fornece suporte para dimensionar um cluster AKS. Para dimensionar os nós no cluster, use a interface do usuário para o cluster AKS no Azure Machine Learning Studio. Você só pode alterar a contagem de nós, não o tamanho da VM do cluster.

Para obter mais informações sobre as classes, os métodos e os parâmetros usados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Usando o CLI**

```azurecli
az ml computetarget create aks -n myaks
```

Para mais informações, consulte o [az ml computetarget criar referência aks.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks)

## <a name="attach-an-existing-aks-cluster"></a>Anexar um cluster AKS existente

**Estimativa de tempo:** Aproximadamente 5 minutos.

Se você já tiver o cluster AKS em sua assinatura do Azure e for a versão 1,17 ou inferior, poderá usá-lo para implantar a imagem.

> [!TIP]
> O cluster AKS existente pode estar em uma região do Azure que não seja seu espaço de trabalho Azure Machine Learning.
>
> Se você quiser proteger o cluster AKS usando uma rede virtual do Azure, primeiro deverá criar a rede virtual. Para mais informações, consulte [A experimentação segura e a inferência com](how-to-enable-virtual-network.md#aksvnet)a Rede Virtual Azure .

Ao ligar um cluster AKS a um espaço de trabalho, pode definir como utilizará o cluster definindo o parâmetro `cluster_purpose`.

Se não definir o parâmetro `cluster_purpose`, ou definir `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, então o cluster deve ter pelo menos 12 CPUs virtuais disponíveis.

Se configurar `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, então o cluster não precisa de ter 12 CPUs virtuais. Recomendamos pelo menos duas CPUs virtuais para desenvolvimento/teste. No entanto, um cluster configurado para desenvolvimento/teste não é adequado para o tráfego de nível de produção e pode aumentar os tempos de inferência. Os clusters de desenvolvimento/teste também não garantem a tolerância a falhas.

> [!WARNING]
> Não crie vários anexos simultâneos para o mesmo cluster AKS do seu espaço de trabalho. Por exemplo, anexar um cluster AKS a um espaço de trabalho usando dois nomes diferentes. Cada novo anexo interromperá os anexos existentes anteriores.
>
> Se pretender voltar a ligar um cluster AKS, por exemplo, para alterar a configuração do SSL ou de outro cluster, tem primeiro de remover o acessório existente utilizando [aksCompute.detach()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Para obter mais informações sobre como criar um cluster AKS usando o CLI do Azure ou o portal, consulte os seguintes artigos:

* [Criar um cluster AKS (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Criar um cluster AKS (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Os exemplos a seguir demonstram como anexar um cluster AKS existente ao seu espaço de trabalho:

**Usando o SDK**

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
```

Para obter mais informações sobre as classes, os métodos e os parâmetros usados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Usando o CLI**

Para anexar um cluster existente usando a CLI, você precisa obter a ID de recurso do cluster existente. Para obter esse valor, use o comando a seguir. Substitua `myexistingcluster` pelo nome do seu cluster AKS. Substitua `myresourcegroup` pelo grupo de recursos que contém o cluster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Esse comando retorna um valor semelhante ao seguinte texto:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Para anexar o cluster existente ao seu espaço de trabalho, use o comando a seguir. Substitua `aksresourceid` pelo valor devolvido pelo comando anterior. Substitua `myresourcegroup` pelo grupo de recursos que contém o seu espaço de trabalho. Substitua `myworkspace` pelo seu nome no espaço de trabalho.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Para mais informações, consulte a referência [az ml de cálculo.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

## <a name="deploy-to-aks"></a>Implementar para AKS

Para implementar um modelo para o Serviço Azure Kubernetes, crie uma configuração de __implementação__ que descreva os recursos computacionais necessários. Por exemplo, número de núcleos e memória. Também precisa de uma configuração de __inferência,__ que descreve o ambiente necessário para acolher o modelo e o serviço web. Para obter mais informações sobre a criação da configuração de inferência, consulte [Como e onde implementar modelos](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Utilizar o SDK

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Para obter mais informações sobre as classes, os métodos e os parâmetros usados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Modelo.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Serviço web.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Usando a CLI

Para implantar usando a CLI, use o comando a seguir. Substitua `myaks` pelo nome do alvo de cálculo AKS. Substitua `mymodel:1` pelo nome e versão do modelo registado. Substitua `myservice` com o nome para prestar este serviço:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Para mais informações, consulte a referência de implementação do [modelo Az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

### <a name="using-vs-code"></a>Utilizar o VS Code

Para obter informações sobre a utilização do Código VS, consulte [a sua implementação para AKS através da extensão do Código VS](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> A implantação por meio do VS Code requer que o cluster AKS seja criado ou anexado ao seu espaço de trabalho com antecedência.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Implantar modelos no AKS usando a distribuição controlada (versão prévia)
Analise e promova versões de modelo de maneira controlada usando pontos de extremidade. Implementar até 6 versões atrás de um único ponto final e configurar a % de pontuação do tráfego para cada versão implantada. Você pode habilitar o app insights para exibir as métricas operacionais de pontos de extremidade e versões implantadas.

### <a name="create-an-endpoint"></a>Criar um ponto final
Quando você estiver pronto para implantar seus modelos, crie um ponto de extremidade de Pontuação e implante sua primeira versão. A etapa a seguir mostra como implantar e criar o ponto de extremidade usando o SDK. A primeira implantação será definida como a versão padrão, o que significa que o percentual de tráfego não especificado em todas as versões vai para a versão padrão.  

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint",
version_name= "versiona",
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Atualizar e adicionar versões a um ponto de extremidade

Adicione outra versão ao ponto de extremidade e configure o percentual de tráfego de Pontuação indo para a versão. Há dois tipos de versões, um controle e uma versão de tratamento. Pode haver várias versões de tratamento para ajudar a comparar com uma única versão de controle.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
```

Atualize as versões existentes ou exclua-as em um ponto de extremidade. Você pode alterar o tipo padrão da versão, o tipo de controle e o percentual de tráfego.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)

# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Autenticação do serviço Web

Ao ser implantado no Serviço Azure Kubernetes, a autenticação __baseada em chaves__ é ativada por padrão. Também pode ativar a autenticação __baseada em token.__ A autenticação baseada em token exige que os clientes usem uma conta de Azure Active Directory para solicitar um token de autenticação, que é usado para fazer solicitações ao serviço implantado.

Para __desativar__ a autenticação, defina o parâmetro `auth_enabled=False` ao criar a configuração de implantação. O exemplo a seguir desabilita a autenticação usando o SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Para obter informações sobre a autenticação a partir de uma aplicação de cliente, consulte o modelo Consumir um Modelo de [Aprendizagem Automática Azure implementado como um serviço web](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Autenticação com chaves

Se a autenticação chave estiver ativada, pode utilizar o método `get_keys` para recuperar uma chave de autenticação primária e secundária:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se precisar regenerar uma chave, use [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Autenticação com tokens

Para permitir a autenticação simbólica, defina o parâmetro `token_auth_enabled=True` quando estiver a criar ou atualizar uma implementação. O exemplo a seguir habilita a autenticação de token usando o SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Se a autenticação simbólica estiver ativada, pode utilizar o método `get_token` para recuperar um símbolo JWT e o tempo de validade do token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Terá de pedir um novo sinal depois do `refresh_by` tempo do token.
>
> A Microsoft recomenda enfaticamente que você crie seu espaço de trabalho Azure Machine Learning na mesma região que o cluster do serviço kubernetes do Azure. Para autenticar com um token, o serviço Web fará uma chamada para a região em que seu espaço de trabalho Azure Machine Learning é criado. Se a região do seu espaço de trabalho estiver indisponível, você não poderá buscar um token para o serviço Web mesmo que o cluster esteja em uma região diferente do seu espaço de trabalho. Isso efetivamente resulta na indisponibilidade da autenticação baseada em token até que a região do seu espaço de trabalho esteja disponível novamente. Além disso, quanto maior a distância entre a região do cluster e a região do seu espaço de trabalho, mais tempo será levado para buscar um token.

## <a name="update-the-web-service"></a>Atualizar o serviço web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Passos Seguintes

* [Experimentação segura e inferência numa rede virtual](how-to-enable-virtual-network.md)
* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Resolução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Serviços web Secure Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um Modelo ML implantado como um serviço web](how-to-consume-web-service.md)
* [Monitorize os seus modelos de Aprendizagem automática Azure com Insights de Aplicação](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
