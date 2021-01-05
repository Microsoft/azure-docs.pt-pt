---
title: Implementar modelos ML para o Serviço Kubernetes
titleSuffix: Azure Machine Learning
description: Saiba como implementar os seus modelos Azure Machine Learning como um serviço web utilizando o Serviço Azure Kubernetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: 7ba01139e365b2f0023ef0784b6ed83e7bde609a
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831737"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Implementar um modelo para um cluster de serviço Azure Kubernetes

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
> Recomendamos que depure localmente antes de implementar no serviço web. Para mais informações, consulte [Debug Localmente](./how-to-troubleshoot-deployment-local.md)
>
> Também pode consultar o Azure Machine Learning – [Implementar no Bloco de Notas Local](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

- Um modelo de machine learning registado no seu espaço de trabalho. Se não tiver um modelo registado, consulte [como e onde implementar modelos.](how-to-deploy-and-where.md)

- A [extensão Azure CLI para o serviço de aprendizagem automática](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK,](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)ou a extensão do [Código do Estúdio Visual Azure Machine Learning.](tutorial-setup-vscode-extension.md)

- Os snippets de código __Python__ neste artigo assumem que as seguintes variáveis são definidas:

    * `ws` - Prepara-te para o teu espaço de trabalho.
    * `model` - Desa couso para o seu modelo registado.
    * `inference_config` - Definir para a configuração de inferência para o modelo.

    Para obter mais informações sobre a definição destas variáveis, consulte [como e onde implementar modelos.](how-to-deploy-and-where.md)

- Os cortes __do CLI__ neste artigo assumem que criaste um `inferenceconfig.json` documento. Para obter mais informações sobre a criação deste documento, consulte [como e onde implementar modelos.](how-to-deploy-and-where.md)

- Um cluster de serviço Azure Kubernetes ligado ao seu espaço de trabalho. Para obter mais informações, consulte [Criar e anexar um cluster de Serviço Azure Kubernetes](how-to-create-attach-kubernetes.md).

    - Se pretender implantar modelos para nós GPU ou nóns FPGA (ou qualquer SKU específico), então deve criar um cluster com o SKU específico. Não existe suporte para a criação de um conjunto de nó secundário num cluster existente e modelos de implantação na piscina de nó secundário.

## <a name="understand-the-deployment-processes"></a>Compreender os processos de implantação

A palavra "implantação" é usada tanto em Kubernetes como em Azure Machine Learning. "Implantação" tem significados diferentes nestes dois contextos. Em Kubernetes, a `Deployment` é uma entidade concreta, especificada com um arquivo YAML declarativo. A Kubernetes `Deployment` tem um ciclo de vida definido e relações concretas com outras entidades kubernetes tais como e `Pods` `ReplicaSets` . Você pode aprender sobre Kubernetes de docs e vídeos em [What is Kubernetes?](https://aka.ms/k8slearning)

No Azure Machine Learning, a "implantação" é usada no sentido mais geral de disponibilizar e limpar os recursos do seu projeto. Os passos que a Azure Machine Learning considera parte da implantação são:

1. Fechar os ficheiros na pasta do projeto, ignorando os especificados em .amlignore ou .gitignore
1. Escalonamento do seu cluster de cálculo (Relaciona-se com Kubernetes)
1. Construção ou download do ficheiro de estiva para o nó computacional (Relaciona-se com Kubernetes)
    1. O sistema calcula um haxixe de: 
        - A imagem base 
        - Passos personalizados do estivador (ver [Implementar um modelo utilizando uma imagem base personalizada do Docker)](./how-to-deploy-custom-docker-image.md)
        - A definição conda YAML (ver [Criar & utilizar ambientes de software em Azure Machine Learning)](./how-to-use-environments.md)
    1. O sistema utiliza este haxixe como chave numa procura do espaço de trabalho Registo do Contentor Azure (ACR)
    1. Se não for encontrado, procura um jogo no ACR global
    1. Se não for encontrado, o sistema constrói uma nova imagem (que será em cache e empurrada para o espaço de trabalho ACR)
1. Baixar o ficheiro do projeto zipped para armazenamento temporário no nó de computação
1. Desapertar o ficheiro do projeto
1. A execução do nó computativo `python <entry script> <arguments>`
1. Guardar registos, ficheiros de modelos e outros ficheiros escritos `./outputs` para a conta de armazenamento associada ao espaço de trabalho
1. Escalonamento do cálculo, incluindo a remoção do armazenamento temporário (Relaciona-se com Kubernetes)

### <a name="azure-ml-router"></a>Router Azure ML

O componente frontal (azureml-fe) que encaminha os pedidos de inferência de entrada para serviços implantados escala automaticamente, se necessário. A escala de azureml-fe baseia-se na finalidade e tamanho do cluster AKS (número de nós). O objetivo do cluster e os nós são configurados quando [cria ou anexa um cluster AKS](how-to-create-attach-kubernetes.md). Há um serviço azureml-fe por cluster, que pode estar funcionando em várias cápsulas.

> [!IMPORTANT]
> Ao utilizar um cluster configurado como __teste dev,__ o auto-escalador é **desativado**.

Azureml-fe escala tanto (verticalmente) para usar mais núcleos, como para fora (horizontalmente) para usar mais cápsulas. Ao tomar a decisão de aumentar a escala, é utilizado o tempo que demora a encaminhar os pedidos de inferência recebidas. Se este tempo exceder o limiar, ocorre uma escala. Se o tempo de encaminhar os pedidos de entrada continuar a exceder o limiar, ocorre uma escala.

Ao escalonar e entrar, é utilizada a utilização do CPU. Se o limiar de utilização do CPU for atingido, a extremidade frontal será primeiro reduzida. Se a utilização do CPU descer para o limiar de escala, uma operação de escala-in acontece. A escalada e a saída só ocorrerão se houver recursos de cluster suficientes disponíveis.

## <a name="understand-connectivity-requirements-for-aks-inferencing-cluster"></a>Compreender os requisitos de conectividade para o cluster de inferenculação AKS

Quando o Azure Machine Learning cria ou anexa um cluster AKS, o cluster AKS é implantado com um dos dois modelos de rede seguintes:
* Rede Kubenet - Os recursos de rede são tipicamente criados e configurados à medida que o cluster AKS é implantado.
* Redes do Azure Container Networking Interface (CNI) – o cluster do AKS está associado aos recursos e configurações existentes da rede virtual.

Para o primeiro modo de rede, a rede é criada e configurada adequadamente para o serviço Azure Machine Learning. Para o segundo modo de rede, uma vez que o cluster está ligado à rede virtual existente, especialmente quando o DNS personalizado é usado para a rede virtual existente, o cliente precisa prestar uma atenção extra aos requisitos de conectividade para o cluster de inferencing AKS e garantir a resolução de DNS e conectividade de saída para o inferencing AKS.

O diagrama seguinte captura todos os requisitos de conectividade para a inferencing AKS. As setas pretas representam a comunicação real, e as setas azuis representam os nomes de domínio, que o DNS controlado pelo cliente deve resolver.

 ![Requisitos de conectividade para a inferencing AKS](./media/how-to-deploy-aks/aks-network.png)

### <a name="overall-dns-resolution-requirements"></a>Requisitos globais de resolução de DNS
A resolução DNS dentro do VNET existente está sob o controlo do cliente. As seguintes entradas dns devem ser resolúveis:
* Servidor AKS API sob a forma de \<cluster\> .hcp. \<region\> . . azmk8s.io
* Microsoft Container Registry (MCR): mcr.microsoft.com
* Registo do Contentor Azure (ARC) do cliente sob a forma de \<ACR name\> .azurecr.io
* Conta de Armazenamento Azure sob a forma de \<account\> .table.core.windows.net e \<account\> .blob.core.windows.net
* (Opcional) Para autenticação AAD: api.azureml.ms
* Marcar o nome de domínio do ponto final, gerado automaticamente por Azure ML ou nome de domínio personalizado. O nome de domínio gerado automaticamente seria: \<leaf-domain-label \+ auto-generated suffix\> \<region\> . . cloudapp.azure.com

### <a name="connectivity-requirements-in-chronological-order-from-cluster-creation-to-model-deployment"></a>Requisitos de conectividade por ordem cronológica: desde a criação de clusters até à implantação de modelos

No processo de criação ou anexação da AKS, o router Azure ML (azureml-fe) é implantado no cluster AKS. Para implantar o router Azure ML, o nó AKS deve ser capaz de:
* Resolver DNS para servidor AKS API
* Resolver DNS para MCR para descarregar imagens de docker para router Azure ML
* Descarregue imagens da MCR, onde é necessária conectividade de saída

Logo após a implantação do azureml-fe, tentará iniciar-se e isto requer:
* Resolver DNS para servidor AKS API
* Consulta o servidor AKS API para descobrir outras instâncias de si mesma (é um serviço multi-pod)
* Ligar-se a outras instâncias de si mesmo

Uma vez iniciado o azureml-fe, requer conectividade adicional para funcionar corretamente:
* Ligue-se ao Azure Storage para descarregar configuração dinâmica
* Resolver o DNS para o servidor de autenticação AAD api.azureml.ms e comunicar com ele quando o serviço implantado utilizar a autenticação AAD.
* Consulta o servidor AKS API para descobrir modelos implementados
* Comunicar para os PODs de modelo implantados

Na hora de implantação do modelo, para uma implementação bem sucedida do nó AKS deve ser capaz de: 
* Resolver DNS para ACR do cliente
* Baixar imagens do ACR do cliente
* Resolver DNS para Azure BLOBs onde o modelo está armazenado
* Baixar modelos a partir de Azure BLOBs

Após o início do modelo e o arranque do serviço, o azureml-fe descobrirá automaticamente utilizando a AKS API e estará pronto para encaminhar o pedido para o mesmo. Deve ser capaz de comunicar com pods modelo.
>[!Note]
>Se o modelo implementado necessitar de qualquer conectividade (por exemplo, consulta de bases de dados externas ou outro serviço REST, descarregamento de um BLOG etc), então deve ser ativada a resolução de DNS e a comunicação de saída para estes serviços.

## <a name="deploy-to-aks"></a>Implementar para AKS

Para implementar um modelo para o Serviço Azure Kubernetes, crie uma __configuração de implementação__ que descreva os recursos de computação necessários. Por exemplo, número de núcleos e memória. Também precisa de uma __configuração de inferência__, que descreve o ambiente necessário para hospedar o modelo e o serviço web. Para obter mais informações sobre a criação da configuração de inferência, consulte [como e onde implementar modelos.](how-to-deploy-and-where.md)

> [!NOTE]
> O número de modelos a implementar limita-se a 1.000 modelos por implantação (por contentor).

<a id="using-the-cli"></a>

# <a name="python"></a>[Python](#tab/python)

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

* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute?preserve-view=true&view=azure-ml-py)
* [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?preserve-view=true&view=azure-ml-py)
* [Modelo.implementar](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truewait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para utilizar o CLI, utilize o seguinte comando. `myaks`Substitua-o pelo nome do alvo de computação AKS. `mymodel:1`Substitua-o pelo nome e versão do modelo registado. `myservice`Substitua-o pelo nome para prestar este serviço:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Para obter mais informações, consulte a referência de implantação do [modelo az ml.](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Para obter informações sobre a utilização do Código VS, consulte [a implementação para AKS através da extensão do Código VS](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> A implementação através do Código VS requer que o cluster AKS seja criado ou ligado antecipadamente ao seu espaço de trabalho.

---

### <a name="autoscaling"></a>Dimensionamento automático

O componente que lida com a autoscalagem para implementações de modelos Azure ML é azureml-fe, que é um router de pedido inteligente. Uma vez que todos os pedidos de inferência passam por ele, tem os dados necessários para escalar automaticamente os modelos implantados.

> [!IMPORTANT]
> * **Não ative o Pod Horizontal De Kubernetes (HPA) para implementações de modelos**. Fazê-lo faria com que os dois componentes de auto-escalas competissem entre si. Azureml-fe foi concebida para modelos de escala automática implantados pela Azure ML, onde o HPA teria de adivinhar ou aproximar a utilização do modelo a partir de uma métrica genérica como o uso de CPU ou uma configuração métrica personalizada.
> 
> * **Azureml-fe não escala o número de nós num cluster AKS,** porque isso pode levar a aumentos inesperados de custos. Em vez **disso, escala o número de réplicas para o modelo** dentro dos limites do cluster físico. Se precisar de escalar o número de nós dentro do cluster, pode escalar manualmente o cluster ou [configurar o autoescalador do cluster AKS](../aks/cluster-autoscaler.md).

A autoscalagem pode ser controlada por definição `autoscale_target_utilization` , e para o serviço web `autoscale_min_replicas` `autoscale_max_replicas` AKS. O exemplo a seguir demonstra como permitir a autoscalagem:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

As decisões de escala para cima/para baixo baseiam-se na utilização das réplicas atuais do contentor. O número de réplicas que estão ocupadas (processando um pedido) divididas pelo número total de réplicas atuais é a utilização atual. Se este número `autoscale_target_utilization` exceder, mais réplicas são criadas. Se for mais baixo, as réplicas são reduzidas. Por defeito, a utilização do alvo é de 70%.

As decisões de adicionar réplicas são ansiosas e rápidas (cerca de 1 segundo). As decisões de remover réplicas são conservadoras (cerca de 1 minuto).

Pode calcular as réplicas necessárias utilizando o seguinte código:

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

Para obter mais informações sobre a definição `autoscale_target_utilization` `autoscale_max_replicas` , `autoscale_min_replicas` e, consulte a referência do módulo [AksWebservice.](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py)

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

## <a name="web-service-authentication"></a>Autenticação do serviço Web

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
> Se precisar de regenerar uma chave, use [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py)

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
> Para obter um token, você deve usar o Azure Machine Learning SDK ou o [comando az ml get-token de acesso.](/cli/azure/ext/azure-cli-ml/ml/service?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-get-access-token)


### <a name="vulnerability-scanning"></a>Análise de vulnerabilidades

O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção contra ameaças avançada entre cargas de trabalho na cloud híbrida. Deve permitir que o Azure Security Center digitalize os seus recursos e siga as suas recomendações. Para mais informações, consulte [a integração dos Serviços Azure Kubernetes com o Security Center.](../security-center/defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>Passos seguintes

* [Use a Azure RBAC para a autorização de Kubernetes](../aks/manage-azure-rbac.md)
* [Ambiente de inferenização seguro com rede virtual Azure](how-to-secure-inferencing-vnet.md)
* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Resolução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Atualizar serviços Web](how-to-deploy-update-web-service.md)
* [Utilizar o TLS para proteger um serviço Web através do Azure Machine Learning](how-to-secure-web-service.md)
* [Consumir um Modelo ML implantado como um serviço web](how-to-consume-web-service.md)
* [Monitorize os seus modelos de machine learning Azure com Insights de Aplicações](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
