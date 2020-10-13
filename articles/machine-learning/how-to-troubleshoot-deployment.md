---
title: Resolução de problemas de implantação de estivadores
titleSuffix: Azure Machine Learning
description: Aprenda a trabalhar, resolver e resolver problemas com os erros comuns de implementação do Docker com o Serviço Azure Kubernetes e as instâncias do contentor Azure utilizando a Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4, devx-track-python
ms.openlocfilehash: 82b9db2f3575e50367ed154246f9fb69b74c60cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333777"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>Resolução de problemas Colocação de Docker de modelos com serviço Azure Kubernetes e Instâncias de Contentores Azure 

Aprenda a resolver problemas e a resolver, ou a trabalhar em torno de erros comuns de implantação do Docker com o Azure Container Instances (ACI) e o Serviço Azure Kubernetes (AKS) utilizando a Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição do Azure**. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* [O Azure Machine Learning SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* O [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)
* A [extensão CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Para depurar localmente, você deve ter uma instalação Docker funcionando no seu sistema local.

    Para verificar a instalação do Docker, utilize o comando `docker run hello-world` a partir de um terminal ou de um pedido de comando. Para obter informações sobre a instalação do Docker, ou sobre os erros do Docker, consulte a [Documentação](https://docs.docker.com/)do Docker .

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Passos para implantação de Docker de modelos de aprendizagem automática

Ao implementar um modelo em Azure Machine Learning, utilize o [modelo.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API e um objeto [Ambiente.](how-to-use-environments.md) O serviço cria uma imagem base de estivador durante a fase de implantação e monta os modelos necessários, tudo numa só chamada. As tarefas básicas de implantação são:

1. Registe o modelo no registo do modelo do espaço de trabalho.

2. Definir Configuração de inferência:
    1. Criar um objeto [ambiente.](how-to-use-environments.md) Este objeto pode usar as dependências em um arquivo de yaml ambiente, um dos nossos ambientes curados.
    2. Crie uma configuração de inferência (objeto InferenceConfig) com base no ambiente e no script de pontuação.

3. Implemente o modelo para o serviço Azure Container Instance (ACI) ou para o Serviço Azure Kubernetes (AKS).

Saiba mais sobre este processo na introdução da [Gestão](concept-model-management-and-deployment.md) de Modelos.

## <a name="before-you-begin"></a>Antes de começar

Se encontrar algum problema, a primeira coisa a fazer é quebrar a tarefa de implantação (anteriormente descrita) em passos individuais para isolar o problema.

Ao utilizar [o Modelo.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) com um objeto [Ambiente](how-to-use-environments.md) como parâmetro de entrada, o seu código pode ser dividido em três passos principais:

1. Registar o modelo. Aqui está um código de amostra:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Definir configuração de inferência para implantação:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Implementar o modelo utilizando a configuração de inferência criada no passo anterior:

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

Quebrar o processo de implantação de ti em tarefas individuais facilita a identificação de alguns dos erros mais comuns.

## <a name="debug-locally"></a>Debug localmente

Se tiver problemas ao implementar um modelo para ACI ou AKS, implemente-o como um serviço web local. A utilização de um serviço Web local facilita a resolução de problemas.

Pode encontrar uma amostra [de um caderno de implementação local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) no repo  [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) para explorar um exemplo runnable.

> [!WARNING]
> As implementações de serviços web locais não são suportadas para cenários de produção.

Para implementar localmente, modifique o seu código para utilizar `LocalWebservice.deploy_configuration()` para criar uma configuração de implementação. Em seguida, utilize `Model.deploy()` para implantar o serviço. O exemplo a seguir implementa um modelo (contido na variável modelo) como um serviço web local:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Se estiver a definir a sua própria especificação conda YAML, liste a versão azureml-defaults >= 1.0.45 como uma dependência de pip. Este pacote é necessário para hospedar o modelo como um serviço web.

Neste momento, pode trabalhar com o serviço normalmente. O seguinte código demonstra o envio de dados para o serviço:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Para obter mais informações sobre a personalização do seu ambiente Python, consulte [Criar e gerir ambientes para treino e implantação.](how-to-use-environments.md) 

### <a name="update-the-service"></a>Atualizar o serviço

Durante os testes locais, poderá ter de atualizar o `score.py` ficheiro para adicionar registo ou tentar resolver quaisquer problemas que tenha descoberto. Para recarregar as alterações no `score.py` ficheiro, utilize `reload()` . Por exemplo, o seguinte código recarrega o script para o serviço e, em seguida, envia dados para o mesmo. Os dados são pontuados utilizando o `score.py` ficheiro atualizado:

> [!IMPORTANT]
> O `reload` método só está disponível para implantações locais. Para obter informações sobre a atualização de uma implementação para outro alvo de computação, consulte [como atualizar o seu serviço web](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> O script é recarregado a partir do local especificado pelo `InferenceConfig` objeto utilizado pelo serviço.

Para alterar o modelo, as dependências da Conda ou a configuração de implantação, utilize [a atualização()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueupdate--args-). O exemplo a seguir atualiza o modelo utilizado pelo serviço:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Apagar o serviço

Para eliminar o serviço, utilize [eliminar()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Inspecione o registo do Docker

Pode imprimir mensagens detalhadas de registo do motor Do Docker a partir do objeto de serviço. Pode visualizar o registo de implementações ACI, AKS e Local. O exemplo a seguir demonstra como imprimir os registos.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
Se vires a linha `Booting worker with pid: <pid>` a ocorrer várias vezes nos registos, significa que não há memória suficiente para ligar o trabalhador.
Pode resolver o erro aumentando o valor de `memory_gb` in `deployment_config`
 
## <a name="container-cannot-be-scheduled"></a>O contentor não pode ser programado

Ao implementar um serviço para um alvo de computação do Serviço Azure Kubernetes, a Azure Machine Learning tentará agendar o serviço com a quantidade de recursos solicitada. Se não houver nós disponíveis no cluster com a quantidade adequada de recursos após 5 minutos, a implantação falhará. A mensagem de falha é `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Pode resolver este erro adicionando mais nós, alterando o SKU dos seus nós ou alterando os requisitos de recursos do seu serviço. 

A mensagem de erro normalmente indica qual o recurso de que precisa mais - por exemplo, se vir uma mensagem de erro `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` indicando que o serviço requer GPUs e existem três nós no cluster que não têm GPUs disponíveis. Isto poderia ser abordado adicionando mais nós se estiver a usar um GPU SKU, mudando para um SKU ativado por GPU se não estiver ou mudar o seu ambiente para não exigir GPUs.  

## <a name="service-launch-fails"></a>Falha no lançamento do serviço

Depois de a imagem ser construída com sucesso, o sistema tenta iniciar um contentor utilizando a sua configuração de implantação. Como parte do processo de arranque do contentor, a `init()` função no seu script de pontuação é invocada pelo sistema. Se houver exceções não conseguindo na `init()` função, poderá ver o erro **do CrashLoopBackOff** na mensagem de erro.

Utilize a informação na secção [de registo do Docker](#dockerlog) para verificar os registos.

## <a name="function-fails-get_model_path"></a>Falha na função: get_model_path()

Frequentemente, `init()` na função no script de pontuação, [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) a função é chamada para localizar um ficheiro modelo ou uma pasta de ficheiros de modelos no recipiente. Se o ficheiro ou a pasta do modelo não puderem ser encontrados, a função falha. A maneira mais fácil de depurar este erro é executar o código Python abaixo na concha do recipiente:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Este exemplo imprime o caminho local `/var/azureml-app` (relativo a) no recipiente onde o seu script de pontuação espera encontrar o ficheiro ou pasta do modelo. Em seguida, pode verificar se o ficheiro ou a pasta estão realmente onde se espera que esteja.

A definição do nível de registo para DEBUG pode fazer com que sejam registadas informações adicionais, o que pode ser útil na identificação da falha.

## <a name="function-fails-runinput_data"></a>Falha na função: execução (input_data)

Se o serviço for implementado com sucesso, mas falhar quando publicar dados no ponto final de pontuação, pode adicionar uma declaração de captura de erros na sua `run(input_data)` função para que retorne uma mensagem de erro detalhada. Por exemplo:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Nota:** As mensagens de erro de devolução da `run(input_data)` chamada devem ser feitas apenas para depurar. Por razões de segurança, não deve devolver mensagens de erro desta forma num ambiente de produção.

## <a name="http-status-code-502"></a>Código de estado HTTP 502

Um código de estado 502 indica que o serviço lançou uma exceção ou se despenhou no `run()` método do ficheiro score.py. Utilize as informações deste artigo para depurar o ficheiro.

## <a name="http-status-code-503"></a>Código de estado HTTP 503

As implementações do Serviço Azure Kubernetes suportam a autoscalagem, que permite adicionar réplicas para suportar carga adicional. O autoescalador foi concebido para lidar com alterações **graduais** na carga. Se receber grandes picos de pedidos por segundo, os clientes poderão receber um código de estado HTTP 503. Mesmo que o autoescalador reaja rapidamente, a AKS leva uma quantidade significativa de tempo para criar recipientes adicionais.

As decisões de escala para cima/para baixo baseiam-se na utilização das réplicas atuais do contentor. O número de réplicas que estão ocupadas (processando um pedido) divididas pelo número total de réplicas atuais é a utilização atual. Se este número `autoscale_target_utilization` exceder, mais réplicas são criadas. Se for mais baixo, as réplicas são reduzidas. As decisões de adicionar réplicas são ansiosas e rápidas (cerca de 1 segundo). As decisões de remover réplicas são conservadoras (cerca de 1 minuto). Por predefinição, a utilização do alvo de autoscalização é definida em **70%,** o que significa que o serviço pode lidar com picos em pedidos por segundo (RPS) **até 30%.**

Há duas coisas que podem ajudar a prevenir 503 códigos de estado:

> [!TIP]
> Estas duas abordagens podem ser utilizadas individualmente ou em combinação.

* Alterar o nível de utilização em que a autoscalagem cria novas réplicas. Pode ajustar o alvo de utilização definindo o `autoscale_target_utilization` valor mais baixo.

    > [!IMPORTANT]
    > Esta alteração não faz com que as réplicas sejam criadas *mais rapidamente.* Em vez disso, são criados num limiar de utilização mais baixo. Em vez de esperar até que o serviço seja utilizado em 70%, alterar o valor para 30% faz com que as réplicas sejam criadas quando ocorrem 30% de utilização.
    
    Se o serviço web já estiver a utilizar as réplicas max atuais e ainda estiver a ver 503 códigos de estado, aumente o `autoscale_max_replicas` valor para aumentar o número máximo de réplicas.

* Altere o número mínimo de réplicas. Aumentar as réplicas mínimas proporciona uma piscina maior para lidar com os picos de entrada.

    Para aumentar o número mínimo de réplicas, definido `autoscale_min_replicas` para um valor mais elevado. Pode calcular as réplicas necessárias utilizando o seguinte código, substituindo valores por valores específicos do seu projeto:

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

    > [!NOTE]
    > Se receber picos de pedido maiores do que as novas réplicas mínimas podem manusear, poderá receber 503s novamente. Por exemplo, à medida que o tráfego para o seu serviço aumenta, poderá ter de aumentar as réplicas mínimas.

Para obter mais informações sobre a definição `autoscale_target_utilization` `autoscale_max_replicas` , e `autoscale_min_replicas` para, consulte a referência do módulo [AksWebservice.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true)

## <a name="http-status-code-504"></a>Código de estado HTTP 504

Um código de estado 504 indica que o pedido está esgotado. O tempo limite de tempo padrão é de 1 minuto.

Pode aumentar o tempo limite ou tentar acelerar o serviço modificando o score.py para remover chamadas desnecessárias. Se estas ações não corrigirem o problema, utilize as informações deste artigo para depurar o ficheiro score.py. O código pode estar num estado não responsivo ou num ciclo infinito.

## <a name="advanced-debugging"></a>Depuragem avançada

Poderá ser necessário depurar interativamente o código Python contido na sua implantação de modelo. Por exemplo, se o script de entrada estiver a falhar e a razão não puder ser determinada por registo adicional. Ao utilizar o Código de Estúdio Visual e o depuratório, pode anexar-se ao código que está a decorrer dentro do contentor Docker.

Para mais informações, visite a [depuragem interativa no guia do Código VS.](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments)

## <a name="model-deployment-user-forum"></a>[Fórum de utilizador de implementação de modelos](https://docs.microsoft.com/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a implementação:

* [Como implantar e onde](how-to-deploy-and-where.md)
* [Tutorial: Modelos de implantação de & de comboios](tutorial-train-models-with-aml.md)
