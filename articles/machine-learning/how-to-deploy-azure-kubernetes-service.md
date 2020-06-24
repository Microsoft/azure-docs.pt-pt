---
title: Implementar modelos ML para o Serviço Kubernetes
titleSuffix: Azure Machine Learning
description: Saiba como implementar os seus modelos Azure Machine Learning como um serviço web utilizando o Serviço Azure Kubernetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: bc99b18c4ab4f98945a1b1f85a6eb87772af852f
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85298944"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Implementar um modelo para um cluster de serviço Azure Kubernetes
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a usar a Azure Machine Learning para implementar um modelo como serviço web no Serviço Azure Kubernetes (AKS). O Serviço Azure Kubernetes é bom para implantações de produção de alta escala. Utilize o serviço Azure Kubernetes se precisar de uma ou mais das seguintes capacidades:

- __Tempo de resposta rápida.__
- __Autoscalagem__ do serviço implantado.
- Opções de aceleração de __hardware,__ tais como GPU e arrays de portão programáveis de campo (FPGA).

> [!IMPORTANT]
> O dimensionamento do cluster não é fornecido através do Azure Machine Learning SDK. Para obter mais informações sobre a escala dos nós num cluster AKS, consulte [a contagem de nós em escala num cluster AKS](../aks/scale-cluster.md).

Ao ser implantado no Serviço Azure Kubernetes, você implementa para um cluster AKS que está __ligado ao seu espaço de trabalho.__ Existem duas formas de ligar um cluster AKS ao seu espaço de trabalho:

* Crie o cluster AKS utilizando o Azure Machine Learning SDK, o Estúdio de Aprendizagem automática CLI ou [Azure Machine Learning](https://ml.azure.com). Este processo liga automaticamente o cluster ao espaço de trabalho.
* Ligue um cluster AKS existente ao seu espaço de trabalho de aprendizagem de máquinas Azure. Um cluster pode ser ligado usando o Azure Machine Learning SDK, Machine Learning CLI ou Azure Machine Learning studio.

> [!IMPORTANT]
> O processo de criação ou anexo é uma tarefa única. Uma vez que um cluster AKS esteja ligado ao espaço de trabalho, você pode usá-lo para implementações. Pode desprender ou apagar o cluster AKS se já não precisar. Uma vez desligado ou apagado, deixará de poder implantar-se no cluster.

## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

- Um modelo de machine learning registado no seu espaço de trabalho. Se não tiver um modelo registado, consulte [como e onde implementar modelos.](how-to-deploy-and-where.md)

- A [extensão Azure CLI para o serviço de aprendizagem automática](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a extensão do [Código do Estúdio Visual Azure Machine Learning.](tutorial-setup-vscode-extension.md)

- Os snippets de código __Python__ neste artigo assumem que as seguintes variáveis são definidas:

    * `ws`- Prepara-te para o teu espaço de trabalho.
    * `model`- Desa couso para o seu modelo registado.
    * `inference_config`- Definir para a configuração de inferência para o modelo.

    Para obter mais informações sobre a definição destas variáveis, consulte [como e onde implementar modelos.](how-to-deploy-and-where.md)

- Os cortes __do CLI__ neste artigo assumem que criaste um `inferenceconfig.json` documento. Para obter mais informações sobre a criação deste documento, consulte [como e onde implementar modelos.](how-to-deploy-and-where.md)

## <a name="create-a-new-aks-cluster"></a>Criar um novo cluster AKS

**Estimativa de tempo**: Aproximadamente 20 minutos.

Criar ou anexar um cluster AKS é um processo único para o seu espaço de trabalho. Pode reutilizar este cluster para múltiplas implementações. Se eliminar o cluster ou o grupo de recursos que o contém, terá de criar um novo cluster da próxima vez que precisar de ser implantado. Pode ter vários clusters AKS ligados ao seu espaço de trabalho.

> [!TIP]
> Se pretender proteger o seu cluster AKS utilizando uma Rede Virtual Azure, tem de criar primeiro a rede virtual. Para obter mais informações, consulte [a experimentação segura e a inferência com a Rede Virtual Azure.](how-to-enable-virtual-network.md#aksvnet)

Se pretender criar um cluster AKS para __desenvolvimento,__ __validação__e __teste__ em vez de produção, pode especificar a __finalidade__ do cluster para __o teste de dev__.

> [!WARNING]
> Se `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` definir, o cluster que é criado não é adequado para o tráfego de nível de produção e pode aumentar os tempos de inferência. Os agrupamentos de dev/teste também não garantem a tolerância à falha. Recomendamos pelo menos 2 CPUs virtuais para aglomerados dev/teste.

Os exemplos a seguir demonstram como criar um novo cluster AKS utilizando o SDK e o CLI:

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
> Pois, [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) se escolher valores personalizados para `agent_count` e , e não é , `vm_size` `cluster_purpose` `DEV_TEST` então precisa de se certificar de que `agent_count` multiplicado por é maior ou igual `vm_size` a 12 CPUs virtuais. Por exemplo, se usar um `vm_size` de "Standard_D3_v2", que tem 4 CPUs virtuais, então deve escolher um `agent_count` de 3 ou mais.
>
> O Azure Machine Learning SDK não fornece suporte para escalar um cluster AKS. Para escalar os nós no cluster, use o UI para o seu cluster AKS no estúdio Azure Machine Learning. Só se pode alterar a contagem de nós, não o tamanho VM do cluster.

Para obter mais informações sobre as classes, métodos e parâmetros utilizados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Utilização do CLI**

```azurecli
az ml computetarget create aks -n myaks
```

Para obter mais informações, consulte o [computação az ml para criar referência aks.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks)

## <a name="attach-an-existing-aks-cluster"></a>Anexar um cluster AKS existente

**Estimativa do tempo:** Aproximadamente 5 minutos.

Se já tiver cluster AKS na sua subscrição Azure, e for a versão 1.17 ou inferior, pode usá-lo para implementar a sua imagem.

> [!TIP]
> O cluster AKS existente pode estar numa região de Azure que não seja o seu espaço de trabalho de Aprendizagem automática Azure.
>
> Se pretender proteger o seu cluster AKS utilizando uma Rede Virtual Azure, tem de criar primeiro a rede virtual. Para obter mais informações, consulte [a experimentação segura e a inferência com a Rede Virtual Azure.](how-to-enable-virtual-network.md#aksvnet)

Ao ligar um cluster AKS a um espaço de trabalho, pode definir como utilizará o cluster definindo o `cluster_purpose` parâmetro.

Se não definir o `cluster_purpose` parâmetro, ou `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD` definido, então o cluster deve ter pelo menos 12 CPUs virtuais disponíveis.

Se `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` definir, então o cluster não precisa de ter 12 CPUs virtuais. Recomendamos pelo menos 2 CPUs virtuais para dev/teste. No entanto, um cluster configurado para dev/teste não é adequado para o tráfego de nível de produção e pode aumentar os tempos de inferência. Os agrupamentos de dev/teste também não garantem a tolerância à falha.

> [!WARNING]
> Não crie ligações múltiplas e simultâneas ao mesmo cluster AKS do seu espaço de trabalho. Por exemplo, anexar um cluster AKS a um espaço de trabalho utilizando dois nomes diferentes. Cada novo acessório quebrará os acessórios existentes anteriores.
>
> Se pretender voltar a anexar um cluster AKS, por exemplo para alterar o TLS ou outra configuração de configuração do cluster, deve primeiro remover o acessório existente utilizando [atach.AksCompute.(](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--)

Para obter mais informações sobre a criação de um cluster AKS utilizando o Azure CLI ou portal, consulte os seguintes artigos:

* [Criar um cluster do AKS (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Criar um cluster AKS (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)
* [Crie um cluster AKS (modelo ARM em modelos de arranque rápido azure)](https://github.com/cloudmelon/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

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

**Utilização do CLI**

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

## <a name="deploy-to-aks"></a>Implementar para AKS

Para implementar um modelo para o Serviço Azure Kubernetes, crie uma __configuração de implementação__ que descreva os recursos de computação necessários. Por exemplo, número de núcleos e memória. Também precisa de uma __configuração de inferência__, que descreve o ambiente necessário para hospedar o modelo e o serviço web. Para obter mais informações sobre a criação da configuração de inferência, consulte [como e onde implementar modelos.](how-to-deploy-and-where.md)

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
* [Modelo.implementar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Utilização do CLI

Para utilizar o CLI, utilize o seguinte comando. `myaks`Substitua-o pelo nome do alvo de computação AKS. `mymodel:1`Substitua-o pelo nome e versão do modelo registado. `myservice`Substitua-o pelo nome para prestar este serviço:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Para obter mais informações, consulte a referência de implantação do [modelo az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

### <a name="using-vs-code"></a>Utilizar o VS Code

Para obter informações sobre a utilização do Código VS, consulte [a implementação para AKS através da extensão do Código VS](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> A implementação através do Código VS requer que o cluster AKS seja criado ou ligado antecipadamente ao seu espaço de trabalho.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Implementar modelos para AKS utilizando o lançamento controlado (pré-visualização)

Analise e promova as versões de modelos de forma controlada utilizando pontos finais. Pode implementar até seis versões atrás de um único ponto final. Os pontos finais fornecem as seguintes capacidades:

* Configure a __percentagem de tráfego de pontuação enviada para cada ponto final__. Por exemplo, a rota 20% do tráfego para o ponto final "teste" e 80% para a "produção".

    > [!NOTE]
    > Se não representar 100% do tráfego, qualquer percentagem restante é encaminhada para a versão __padrão__ do ponto final. Por exemplo, se configurar a versão de ponto final 'test' para obter 10% do tráfego e 'prod' por 30%, os restantes 60% são enviados para a versão padrão do ponto final.
    >
    > A primeira versão do ponto final criada é configurada automaticamente como o padrão. Pode alterá-lo definindo `is_default=True` ao criar ou atualizar uma versão de ponto final.
     
* Marque uma versão de ponto final como __controlo__ ou __tratamento__. Por exemplo, a versão atual do ponto final de produção pode ser o controlo, enquanto os potenciais novos modelos são implementados como versões de tratamento. Após a avaliação do desempenho das versões de tratamento, se superarmos o controlo atual, poderá ser promovido à nova produção/controlo.

    > [!NOTE]
    > Só se pode ter __um__ controlo. Pode fazer vários tratamentos.

Pode permitir que os insights das aplicações vejam as métricas operacionais dos pontos finais e versões implementadas.

### <a name="create-an-endpoint"></a>Criar um ponto final
Assim que estiver pronto para implementar os seus modelos, crie um ponto final de pontuação e implemente a sua primeira versão. O exemplo a seguir mostra como implantar e criar o ponto final utilizando o SDK. A primeira implementação será definida como a versão padrão, o que significa que o percentil de tráfego não especificado em todas as versões irá para a versão padrão.  

> [!TIP]
> No exemplo seguinte, a configuração define a versão inicial do ponto final para lidar com 20% do tráfego. Uma vez que este é o primeiro ponto final, é também a versão padrão. E como não temos outras versões para os outros 80% do tráfego, também é encaminhado para o padrão. Até que outras versões que levam uma percentagem do tráfego sejam implantadas, esta recebe efetivamente 100% do tráfego.

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

Adicione outra versão ao seu ponto final e configuure o percentil de tráfego de pontuação que vai para a versão. Existem dois tipos de versões, um controlo e uma versão de tratamento. Pode haver várias versões de tratamento para ajudar a comparar com uma única versão de controlo.

> [!TIP]
> A segunda versão, criada pelo seguinte código, aceita 10% do tráfego. A primeira versão está configurada para 20%, pelo que apenas 30% do tráfego está configurado para versões específicas. Os restantes 70% são enviados para a primeira versão do ponto final, pois é também a versão padrão.

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

Atualize as versões existentes ou elimine-as num ponto final. Pode alterar o tipo predefinido da versão, o tipo de controlo e o percentil de tráfego. No exemplo seguinte, a segunda versão aumenta o seu tráfego para 40% e é agora o padrão.

> [!TIP]
> Depois do seguinte corte de código, a segunda versão está agora por defeito. Está agora configurado para 40%, enquanto a versão original ainda está configurada para 20%. Isto significa que 40% do tráfego não é contabilizado pelas configurações da versão. O tráfego remanescente será encaminhado para a segunda versão, porque agora é padrão. Recebe efetivamente 80% do tráfego.

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


## <a name="web-service-authentication"></a>Autenticação de serviço web

Ao ser implantado no Serviço Azure Kubernetes, a autenticação __baseada em chaves__ é ativada por padrão. Também pode ativar a autenticação __baseada em fichas.__ A autenticação baseada em token requer que os clientes utilizem uma conta do Azure Ative Directory para solicitar um token de autenticação, que é usado para fazer pedidos ao serviço implantado.

Para __desativar__ a autenticação, desative o `auth_enabled=False` parâmetro ao criar a configuração de implementação. O exemplo a seguir desativa a autenticação utilizando o SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Para obter informações sobre a autenticação a partir de uma aplicação do cliente, consulte o [modelo Consumir um modelo de Aprendizagem automática Azure implementado como serviço web](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Autenticação com chaves

Se a autenticação da chave estiver ativada, pode utilizar o `get_keys` método para recuperar uma chave de autenticação primária e secundária:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se precisar de regenerar uma chave, use[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Autenticação com fichas

Para ativar a autenticação simbólica, detena o `token_auth_enabled=True` parâmetro quando estiver a criar ou a atualizar uma implementação. O exemplo a seguir permite a autenticação simbólica utilizando o SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Se a autenticação simbólica estiver ativada, pode utilizar o `get_token` método para recuperar um token JWT e o tempo de validade do token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Terá de pedir um novo símbolo depois da hora do `refresh_by` token.
>
> A Microsoft recomenda vivamente que crie o seu espaço de trabalho Azure Machine Learning na mesma região que o seu cluster de Serviço Azure Kubernetes. Para autenticar com um token, o serviço web fará uma chamada para a região em que o seu espaço de trabalho Azure Machine Learning é criado. Se a região do seu espaço de trabalho não estiver disponível, então não poderá obter um símbolo para o seu serviço web, mesmo, se o seu cluster estiver numa região diferente do seu espaço de trabalho. Isto resulta efetivamente na indisponibilidade da autenticação baseada em Token até que a região do seu espaço de trabalho esteja novamente disponível. Além disso, quanto maior for a distância entre a região do seu aglomerado e a região do seu espaço de trabalho, mais tempo demorará a obter um símbolo.
>
> Para obter um token, você deve usar o Azure Machine Learning SDK ou o [comando az ml get-token de acesso.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-get-access-token)

## <a name="update-the-web-service"></a>Atualizar o serviço web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Passos seguintes

* [Experiências e inferências seguras numa rede virtual](how-to-enable-virtual-network.md)
* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Resolução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Utilize o TLS para garantir um serviço web através do Azure Machine Learning](how-to-secure-web-service.md)
* [Consumir um Modelo ML implantado como um serviço web](how-to-consume-web-service.md)
* [Monitorize os seus modelos de machine learning Azure com Insights de Aplicações](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
