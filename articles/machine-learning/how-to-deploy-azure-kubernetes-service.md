---
title: Como implementar modelos para o Serviço Azure Kubernetes
titleSuffix: Azure Machine Learning
description: Aprenda a implementar os seus modelos Azure Machine Learning como um serviço web utilizando o Serviço Azure Kubernetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 01/16/2020
ms.openlocfilehash: aec1b7f7bf60be34d21d52ca652a776cf3275fe8
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811763"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Implementar um modelo para um cluster de serviço Azure Kubernetes
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a utilizar o Azure Machine Learning para implementar um modelo como serviço web no Serviço Azure Kubernetes (AKS). O Serviço Azure Kubernetes é bom para implantações de produção em larga escala. Utilize o serviço Azure Kubernetes se precisar de uma ou mais das seguintes capacidades:

- __Tempo de resposta rápida.__
- __Autoscalcificação__ do serviço implantado.
- __Opções__ de aceleração de hardware tais como GPU e matrizes de porta programáveis de campo (FPGA).

> [!IMPORTANT]
> A escala de cluster não é fornecida através do Azure Machine Learning SDK. Para obter mais informações sobre a escala dos nós num cluster AKS, consulte [Scale o nó contando num cluster AKS](../aks/scale-cluster.md).

Ao ser implantado no Serviço Azure Kubernetes, desdobre-se num cluster AKS que está __ligado ao seu espaço__de trabalho . Existem duas formas de ligar um cluster AKS ao seu espaço de trabalho:

* Crie o cluster AKS utilizando o Azure Machine Learning SDK, o Machine Learning CLI ou o [estúdio Azure Machine Learning.](https://ml.azure.com) Este processo liga automaticamente o cluster ao espaço de trabalho.
* Fixe um cluster AKS existente ao seu espaço de trabalho azure machine learning. Um cluster pode ser ligado utilizando o Azure Machine Learning SDK, Machine Learning CLI ou azure machine learning studio.

> [!IMPORTANT]
> O processo de criação ou de apego é uma tarefa única. Uma vez que um cluster AKS esteja ligado ao espaço de trabalho, pode usá-lo para implementações. Pode desprender ou eliminar o cluster AKS se já não precisar. Uma vez destacado ou eliminado, deixará de ser capaz de se deslocar para o cluster.

## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho do Azure Machine Learning. Para mais informações, consulte Criar um espaço de [trabalho azure machine learning](how-to-manage-workspace.md).

- Um modelo de aprendizagem automática registado no seu espaço de trabalho. Se não tem um modelo registado, veja [como e onde implementar modelos.](how-to-deploy-and-where.md)

- A [extensão Azure CLI para o serviço de Machine Learning,](reference-azure-machine-learning-cli.md) [Azure Machine Learning Python SDK,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a extensão do Código visual de [aprendizagem automática Azure.](tutorial-setup-vscode-extension.md)

- Os fragmentos de código __Python__ neste artigo assumem que as seguintes variáveis são definidas:

    * `ws`- Coloque no seu espaço de trabalho.
    * `model`- Definido para o seu modelo registado.
    * `inference_config`- Definir para a configuração de inferência para o modelo.

    Para obter mais informações sobre a definição destas variáveis, consulte [Como e onde implementar modelos](how-to-deploy-and-where.md).

- Os cortes __cli__ neste artigo assumem que `inferenceconfig.json` criou um documento. Para obter mais informações sobre a criação deste documento, consulte [Como e onde implementar modelos.](how-to-deploy-and-where.md)

## <a name="create-a-new-aks-cluster"></a>Criar um novo cluster AKS

**Estimativa de tempo**: Aproximadamente 20 minutos.

Criar ou anexar um cluster AKS é um processo único para o seu espaço de trabalho. Pode reutilizar este cluster para múltiplas implementações. Se eliminar o cluster ou o grupo de recursos que o contém, terá de criar um novo cluster da próxima vez que precisar de ser implantado. Pode ter vários clusters AKS ligados ao seu espaço de trabalho.

> [!TIP]
> Se quiser proteger o seu cluster AKS utilizando uma Rede Virtual Azure, tem de criar a rede virtual primeiro. Para mais informações, consulte [A experimentação segura e a inferência com](how-to-enable-virtual-network.md#aksvnet)a Rede Virtual Azure .

Se pretender criar um cluster AKS para __desenvolvimento,__ __validação__e __teste__ em vez de produção, pode especificar o objetivo do __cluster__ para __dev test__.

> [!WARNING]
> Se definir, `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`o cluster que é criado não é adequado para o tráfego de nível de produção e pode aumentar os tempos de inferência. Os clusters de dev/teste também não garantem a tolerância à falha. Recomendamos pelo menos 2 CPUs virtuais para aglomerados de v/teste.

Os seguintes exemplos demonstram como criar um novo cluster AKS utilizando o SDK e o CLI:

**Utilizar o SDK**

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
> Pois, [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)se escolher valores personalizados para `agent_count` e `vm_size` `cluster_purpose` , e não `DEV_TEST`é , então precisa de se certificar `agent_count` de que multiplicado por `vm_size` é maior ou igual a 12 CPUs virtuais. Por exemplo, se `vm_size` utilizar um "Standard_D3_v2", que tem 4 CPUs `agent_count` virtuais, então deve escolher um de 3 ou mais.
>
> O Azure Machine Learning SDK não fornece suporte para escalar um cluster AKS. Para escalar os nós do cluster, use o UI para o seu cluster AKS no estúdio Azure Machine Learning. Só se pode alterar a contagem do nó, não o tamanho VM do cluster.

Para obter mais informações sobre as classes, métodos e parâmetros utilizados neste exemplo, consulte os seguintes documentos de referência:

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

Se já tem o cluster AKS na sua subscrição Azure, e é a versão 1.17 ou inferior, pode usá-lo para implementar a sua imagem.

> [!TIP]
> O aglomerado AKS existente pode estar numa região azure diferente do seu espaço de trabalho Azure Machine Learning.
>
> Se quiser proteger o seu cluster AKS utilizando uma Rede Virtual Azure, tem de criar a rede virtual primeiro. Para mais informações, consulte [A experimentação segura e a inferência com](how-to-enable-virtual-network.md#aksvnet)a Rede Virtual Azure .

Ao ligar um cluster AKS a um espaço de trabalho, pode `cluster_purpose` definir como utilizará o cluster definindo o parâmetro.

Se não definir `cluster_purpose` o parâmetro, `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`ou definir, então o cluster deve ter pelo menos 12 CPUs virtuais disponíveis.

Se se `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`definir, então o cluster não precisa de ter 12 CPUs virtuais. Recomendamos pelo menos 2 CPUs virtuais para dev/teste. No entanto, um cluster configurado para dev/teste não é adequado para o tráfego de nível de produção e pode aumentar os tempos de inferência. Os clusters de dev/teste também não garantem a tolerância à falha.

> [!WARNING]
> Não crie múltiplos anexos simultâneos ao mesmo cluster AKS do seu espaço de trabalho. Por exemplo, anexar um cluster AKS a um espaço de trabalho usando dois nomes diferentes. Cada novo anexo quebrará o(s) anexo existente anterior.
>
> Se pretender voltar a ligar um cluster AKS, por exemplo, para alterar tLS ou outra configuração de configuração de cluster, deve primeiro remover o acessório existente utilizando [aksCompute.detach()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Para obter mais informações sobre a criação de um cluster AKS utilizando o Azure CLI ou portal, consulte os seguintes artigos:

* [Criar um cluster do AKS (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Criar um cluster AKS (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Os seguintes exemplos demonstram como anexar um cluster AKS existente ao seu espaço de trabalho:

**Utilizar o SDK**

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

Para obter mais informações sobre as classes, métodos e parâmetros utilizados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Usando o CLI**

Para anexar um cluster existente utilizando o CLI, é necessário obter o ID de recurso do cluster existente. Para obter este valor, use o seguinte comando. Substitua-o `myexistingcluster` pelo nome do seu cluster AKS. Substitua-o `myresourcegroup` pelo grupo de recursos que contém o cluster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Este comando devolve um valor semelhante ao seguinte texto:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Para fixar o cluster existente ao seu espaço de trabalho, utilize o seguinte comando. Substitua-a `aksresourceid` pelo valor devolvido pelo comando anterior. Substitua-o `myresourcegroup` pelo grupo de recursos que contém o seu espaço de trabalho. Substitua-o `myworkspace` pelo nome do espaço de trabalho.

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

Para obter mais informações sobre as classes, métodos e parâmetros utilizados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Modelo.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Serviço web.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Usando o CLI

Para ser acionado utilizando o CLI, utilize o seguinte comando. Substitua-o `myaks` pelo nome do alvo de cálculo AKS. Substitua `mymodel:1` pelo nome e versão do modelo registado. Substitua `myservice` pelo nome para prestar este serviço:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Para mais informações, consulte a referência de implementação do [modelo Az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

### <a name="using-vs-code"></a>Utilizar o VS Code

Para obter informações sobre a utilização do Código VS, consulte [a sua implementação para AKS através da extensão do Código VS](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> A implementação através do Código VS requer que o cluster AKS seja criado ou ligado ao seu espaço de trabalho com antecedência.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Implementar modelos para AKS utilizando o rollout controlado (pré-visualização)

Analise e promova versões de modelo sem termo utilizando pontos finais. Pode implantar até seis versões atrás de um único ponto final. Os pontos finais fornecem as seguintes capacidades:

* Configure a percentagem de tráfego de __pontuação enviado para cada ponto final__. Por exemplo, a rota de 20% do tráfego para o ponto final 'teste' e 80% para a "produção".

    > [!NOTE]
    > Se não representar 100% do tráfego, qualquer percentagem restante é encaminhada para a versão __final padrão.__ Por exemplo, se configurar o 'teste' da versão endpoint para obter 10% do tráfego, e 'prod' por 30%, os restantes 60% são enviados para a versão de ponto final padrão.
    >
    > A primeira versão de ponto final criada é configurada automaticamente como predefinição. Pode alterar isto `is_default=True` definindo-se ao criar ou atualizar uma versão de ponto final.
     
* Marque uma versão de ponto final como __controlo__ ou __tratamento.__ Por exemplo, a versão final de produção atual pode ser o controlo, enquanto potenciais novos modelos são implementados como versões de tratamento. Após avaliar o desempenho das versões de tratamento, se se superar o controlo atual, poderá ser promovido à nova produção/controlo.

    > [!NOTE]
    > Só podeter __um__ controlo. Pode fazer vários tratamentos.

Pode permitir que as informações das aplicações vejam métricas operacionais de pontos finais e versões implementadas.

### <a name="create-an-endpoint"></a>Criar um ponto final
Assim que estiver pronto para implementar os seus modelos, crie um ponto final de pontuação e implemente a sua primeira versão. O exemplo seguinte mostra como implantar e criar o ponto final usando o SDK. A primeira implementação será definida como a versão padrão, o que significa que o percentil de tráfego não especificado em todas as versões irá para a versão padrão.  

> [!TIP]
> No exemplo seguinte, a configuração define a versão final inicial para lidar com 20% do tráfego. Uma vez que este é o primeiro ponto final, é também a versão padrão. E como não temos outras versões para os outros 80% do tráfego, também é encaminhado para o padrão. Até que outras versões que levam uma percentagem de tráfego sejam implantadas, esta recebe efetivamente 100% do tráfego.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Atualizar e adicionar versões a um ponto final

Adicione outra versão ao seu ponto final e configure o percentil de tráfego de pontuação indo para a versão. Existem dois tipos de versões, um controlo e uma versão de tratamento. Pode haver múltiplas versões de tratamento para ajudar a comparar com uma única versão de controlo.

> [!TIP]
> A segunda versão, criada pelo seguinte código, aceita 10% do tráfego. A primeira versão está configurada para 20%, pelo que apenas 30% do tráfego está configurado para versões específicas. Os restantes 70% são enviados para a versão final, porque é também a versão padrão.

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
endpoint.wait_for_deployment(True)
```

Atualize as versões existentes ou elimine-as num ponto final. Pode alterar o tipo padrão da versão, o tipo de controlo e o percentil de tráfego. No exemplo seguinte, a segunda versão aumenta o seu tráfego para 40% e é agora o padrão.

> [!TIP]
> Após o seguinte corte de código, a segunda versão está agora predefinida. Está agora configurado por 40%, enquanto a versão original ainda está configurada para 20%. Isto significa que 40% do tráfego não é contabilizado pelas configurações da versão. O tráfego que sobra será encaminhado para a segunda versão, porque agora está em incumprimento. Efetivamente recebe 80% do tráfego.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Autenticação do serviço web

Ao ser implantado no Serviço Azure Kubernetes, a autenticação __baseada em chaves__ é ativada por padrão. Também pode ativar a autenticação __baseada em token.__ A autenticação baseada em token requer que os clientes utilizem uma conta azure Ative Diretório para solicitar um token de autenticação, que é usado para fazer pedidos ao serviço implantado.

Para __desativar__ `auth_enabled=False` a autenticação, defina o parâmetro ao criar a configuração de implantação. O exemplo seguinte desativa a autenticação utilizando o SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Para obter informações sobre a autenticação a partir de uma aplicação de cliente, consulte o modelo Consumir um Modelo de [Aprendizagem Automática Azure implementado como um serviço web](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Autenticação com chaves

Se a autenticação chave estiver ativada, pode utilizar o `get_keys` método para recuperar uma chave de autenticação primária e secundária:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se precisar regenerar uma chave, use[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Autenticação com fichas

Para permitir a autenticação `token_auth_enabled=True` simbólica, defina o parâmetro quando estiver a criar ou atualizar uma implementação. O exemplo seguinte permite a autenticação simbólica utilizando o SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Se a autenticação simbólica estiver ativada, pode utilizar o `get_token` método para recuperar um símbolo JWT e o tempo de validade do token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Terá de pedir um novo sinal depois do `refresh_by` tempo do símbolo.
>
> A Microsoft recomenda vivamente que crie o seu espaço de trabalho Azure Machine Learning na mesma região que o seu cluster de Serviço Azure Kubernetes. Para autenticar com um símbolo, o serviço web fará uma chamada para a região em que o seu espaço de trabalho Azure Machine Learning é criado. Se a região do seu espaço de trabalho não estiver disponível, então não poderá obter um símbolo para o seu serviço web mesmo, se o seu cluster estiver numa região diferente do seu espaço de trabalho. Isto resulta efetivamente na indisponibilidade de autenticação baseada em Token até que a região do seu espaço de trabalho esteja novamente disponível. Além disso, quanto maior for a distância entre a região do seu cluster e a região do seu espaço de trabalho, mais tempo demorará a obter um símbolo.

## <a name="update-the-web-service"></a>Atualizar o serviço web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Passos seguintes

* [Experimentação segura e inferência numa rede virtual](how-to-enable-virtual-network.md)
* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Resolução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Use tLS para garantir um serviço web através de Azure Machine Learning](how-to-secure-web-service.md)
* [Consumir um Modelo ML implantado como um serviço web](how-to-consume-web-service.md)
* [Monitorize os seus modelos de Aprendizagem automática Azure com Insights de Aplicação](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
