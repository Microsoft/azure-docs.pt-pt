---
title: Implementar modelos ML para o Serviço Kubernetes
titleSuffix: Azure Machine Learning
description: Saiba como implementar os seus modelos Azure Machine Learning como um serviço web utilizando o Serviço Azure Kubernetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: da6554ae3b7df9962e1f57ac652567c282227d64
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661650"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Implementar um modelo para um cluster de serviço Azure Kubernetes
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a usar a Azure Machine Learning para implementar um modelo como serviço web no Serviço Azure Kubernetes (AKS). O Serviço Azure Kubernetes é bom para implantações de produção de alta escala. Utilize o serviço Azure Kubernetes se precisar de uma ou mais das seguintes capacidades:

- __Tempo de resposta rápida__
- __Autoscalagem__ do serviço implantado
- __Registo__
- __Recolha de dados de modelos__
- __Autenticação__
- __Rescisão de TLS__
- __Opções de aceleração de hardware,__ tais como GPU e arrays de portão programáveis de campo (FPGA)

Ao ser implantado no Serviço Azure Kubernetes, você implementa para um cluster AKS que está __ligado ao seu espaço de trabalho.__ Para obter informações sobre a ligação de um cluster AKS ao seu espaço de trabalho, consulte [Criar e anexar um cluster de serviço Azure Kubernetes](how-to-create-attach-kubernetes.md).

> [!IMPORTANT]
> Recomendamos que depure localmente antes de implementar no serviço web. Para mais informações, consulte [Debug Localmente](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally)
>
> Também pode consultar o Azure Machine Learning – [Implementar no Bloco de Notas Local](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

- Um modelo de machine learning registado no seu espaço de trabalho. Se não tiver um modelo registado, consulte [como e onde implementar modelos.](how-to-deploy-and-where.md)

- A [extensão Azure CLI para o serviço de aprendizagem automática](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)ou a extensão do [Código do Estúdio Visual Azure Machine Learning.](tutorial-setup-vscode-extension.md)

- Os snippets de código __Python__ neste artigo assumem que as seguintes variáveis são definidas:

    * `ws` - Prepara-te para o teu espaço de trabalho.
    * `model` - Desa couso para o seu modelo registado.
    * `inference_config` - Definir para a configuração de inferência para o modelo.

    Para obter mais informações sobre a definição destas variáveis, consulte [como e onde implementar modelos.](how-to-deploy-and-where.md)

- Os cortes __do CLI__ neste artigo assumem que criaste um `inferenceconfig.json` documento. Para obter mais informações sobre a criação deste documento, consulte [como e onde implementar modelos.](how-to-deploy-and-where.md)

- Um cluster de serviço Azure Kubernetes ligado ao seu espaço de trabalho. Para obter mais informações, consulte [Criar e anexar um cluster de Serviço Azure Kubernetes](how-to-create-attach-kubernetes.md).

    - Se pretender implantar modelos para nós GPU ou nóns FPGA (ou qualquer SKU específico), então deve criar um cluster com o SKU específico. Não existe suporte para a criação de um conjunto de nó secundário num cluster existente e modelos de implantação na piscina de nó secundário.

## <a name="deploy-to-aks"></a>Implementar para AKS

Para implementar um modelo para o Serviço Azure Kubernetes, crie uma __configuração de implementação__ que descreva os recursos de computação necessários. Por exemplo, número de núcleos e memória. Também precisa de uma __configuração de inferência__, que descreve o ambiente necessário para hospedar o modelo e o serviço web. Para obter mais informações sobre a criação da configuração de inferência, consulte [como e onde implementar modelos.](how-to-deploy-and-where.md)

> [!NOTE]
> O número de modelos a implementar limita-se a 1.000 modelos por implantação (por contentor).

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

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py&preserve-view=true)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py&preserve-view=true)
* [Modelo.implementar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#&preserve-view=truewait-for-deployment-show-output-false-)

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

### <a name="understand-the-deployment-processes"></a>Compreender os processos de implantação

A palavra "implantação" é usada tanto em Kubernetes como em Azure Machine Learning. "Implantação" tem significados diferentes nestes dois contextos. Em Kubernetes, a `Deployment` é uma entidade concreta, especificada com um arquivo YAML declarativo. A Kubernetes `Deployment` tem um ciclo de vida definido e relações concretas com outras entidades kubernetes tais como e `Pods` `ReplicaSets` . Você pode aprender sobre Kubernetes de docs e vídeos em [What is Kubernetes?](https://aka.ms/k8slearning)

No Azure Machine Learning, a "implantação" é usada no sentido mais geral de disponibilizar e limpar os recursos do seu projeto. Os passos que a Azure Machine Learning considera parte da implantação são:

1. Fechar os ficheiros na pasta do projeto, ignorando os especificados em .amlignore ou .gitignore
1. Escalonamento do seu cluster de cálculo (Relaciona-se com Kubernetes)
1. Construção ou download do ficheiro de estiva para o nó computacional (Relaciona-se com Kubernetes)
    1. O sistema calcula um haxixe de: 
        - A imagem base 
        - Passos personalizados do estivador (ver [Implementar um modelo utilizando uma imagem base personalizada do Docker)](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image)
        - A definição conda YAML (ver [Criar & utilizar ambientes de software em Azure Machine Learning)](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments)
    1. O sistema utiliza este haxixe como chave numa procura do espaço de trabalho Registo do Contentor Azure (ACR)
    1. Se não for encontrado, procura um jogo no ACR global
    1. Se não for encontrado, o sistema constrói uma nova imagem (que será em cache e registada com o espaço de trabalho ACR)
1. Baixar o ficheiro do projeto zipped para armazenamento temporário no nó de computação
1. Desapertar o ficheiro do projeto
1. A execução do nó computativo `python <entry script> <arguments>`
1. Guardar registos, ficheiros de modelos e outros ficheiros escritos `./outputs` para a conta de armazenamento associada ao espaço de trabalho
1. Escalonamento do cálculo, incluindo a remoção do armazenamento temporário (Relaciona-se com Kubernetes)

Quando estiver a utilizar aKS, a escala para cima e para baixo do cálculo é controlada pela Kubernetes, utilizando o estivador construído ou encontrado como descrito acima. 

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
> Se precisar de regenerar uma chave, use [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py&preserve-view=true)

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

## <a name="next-steps"></a>Próximos passos

* [Ambiente de inferenização seguro com rede virtual Azure](how-to-secure-inferencing-vnet.md)
* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Resolução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Atualizar serviços Web](how-to-deploy-update-web-service.md)
* [Utilizar o TLS para proteger um serviço Web através do Azure Machine Learning](how-to-secure-web-service.md)
* [Consumir um Modelo ML implantado como um serviço web](how-to-consume-web-service.md)
* [Monitorize os seus modelos de machine learning Azure com Insights de Aplicações](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
