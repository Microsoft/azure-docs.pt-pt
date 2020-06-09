---
title: Resolução de problemas de implantação de estivadores
titleSuffix: Azure Machine Learning
description: Aprenda a trabalhar, resolver e resolver problemas com os erros comuns de implementação do Docker com o Serviço Azure Kubernetes e as instâncias do contentor Azure utilizando a Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 012d183a20e5fdcf39d72813051d745a3f9787a7
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560125"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>Resolução de problemas Colocação de Docker de modelos com serviço Azure Kubernetes e Instâncias de Contentores Azure 

Aprenda a resolver problemas e a resolver, ou a trabalhar em torno de erros comuns de implantação do Docker com o Azure Container Instances (ACI) e o Serviço Azure Kubernetes (AKS) utilizando a Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

* Uma **assinatura Azure**. Se não tiver uma, experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* [O Azure Machine Learning SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* O [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* A [extensão CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Para depurar localmente, você deve ter uma instalação Docker funcionando no seu sistema local.

    Para verificar a instalação do Docker, utilize o comando `docker run hello-world` a partir de um terminal ou de um pedido de comando. Para obter informações sobre a instalação do Docker, ou sobre os erros do Docker, consulte a [Documentação](https://docs.docker.com/)do Docker .

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Passos para implantação de Docker de modelos de aprendizagem automática

Ao implementar um modelo em Azure Machine Learning, o sistema executa uma série de tarefas.

A abordagem recomendada para a implementação do modelo é através da [API modelo.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) usando um objeto [Ambiente](how-to-use-environments.md) como parâmetro de entrada. Neste caso, o serviço cria uma imagem base de estivador durante a fase de implantação e monta os modelos necessários, tudo numa só chamada. As tarefas básicas de implantação são:

1. Registe o modelo no registo do modelo do espaço de trabalho.

2. Definir Configuração de inferência:
    1. Crie um objeto [Ambiente](how-to-use-environments.md) com base nas dependências que especifique no ficheiro yaml ambiente ou use um dos nossos ambientes adquiridos.
    2. Crie uma configuração de inferência (objeto InferenceConfig) com base no ambiente e no script de pontuação.

3. Implemente o modelo para o serviço Azure Container Instance (ACI) ou para o Serviço Azure Kubernetes (AKS).

Saiba mais sobre este processo na introdução da [Gestão](concept-model-management-and-deployment.md) de Modelos.

## <a name="before-you-begin"></a>Antes de começar

Se encontrar algum problema, a primeira coisa a fazer é quebrar a tarefa de implantação (anteriormente descrita) em passos individuais para isolar o problema.

Assumindo que está a utilizar o novo método de implantação/recomendado via [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API com um objeto [ambiente](how-to-use-environments.md) como parâmetro de entrada, o seu código pode ser dividido em três passos principais:

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

Uma vez que tenha dividido o processo de implantação em tarefas individuais, podemos olhar para alguns dos erros mais comuns.

## <a name="debug-locally"></a>Debug localmente

Se encontrar problemas na implementação de um modelo para ACI ou AKS, tente implantá-lo como um serviço web local. A utilização de um serviço web local facilita a resolução de problemas. A imagem do Docker que contém o modelo é descarregada e iniciada no seu sistema local.

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

Se estiver a definir a sua própria especificação conda YAML, deve listar os padrão de azureml com a versão >= 1.0.45 como uma dependência de pip. Este pacote contém a funcionalidade necessária para hospedar o modelo como um serviço web.

Neste momento, pode trabalhar com o serviço normalmente. Por exemplo, o seguinte código demonstra o envio de dados para o serviço:

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
> O `reload` método só está disponível para implantações locais. Para obter informações sobre a atualização de uma implementação para outro alvo de computação, consulte a secção de atualização dos [modelos Implementar](how-to-deploy-and-where.md#update).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> O script é recarregado a partir do local especificado pelo `InferenceConfig` objeto utilizado pelo serviço.

Para alterar o modelo, as dependências da Conda ou a configuração de implantação, utilize [a atualização()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). O exemplo a seguir atualiza o modelo utilizado pelo serviço:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Apagar o serviço

Para eliminar o serviço, utilize [eliminar()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>Inspecione o registo do Docker

Pode imprimir mensagens detalhadas de registo do motor Do Docker a partir do objeto de serviço. Pode visualizar o registo de implementações ACI, AKS e Local. O exemplo a seguir demonstra como imprimir os registos.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
## <a name="container-cannot-be-scheduled"></a>O contentor não pode ser programado

Ao implementar um serviço para um alvo de computação do Serviço Azure Kubernetes, a Azure Machine Learning tentará agendar o serviço com a quantidade de recursos solicitada. Se, após 5 minutos, não houver nós disponíveis no cluster com a quantidade adequada de recursos disponíveis, a implantação falhará com a mensagem `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Pode resolver este erro adicionando mais nós, alterando o SKU dos seus nós ou alterando os requisitos de recursos do seu serviço. 

A mensagem de erro normalmente indica qual o recurso de que precisa mais - por exemplo, se vir uma mensagem de erro `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` indicando que o serviço requer GPUs e existem três nós no cluster que não têm GPUs disponíveis. Isto poderia ser abordado adicionando mais nós se estiver a usar um GPU SKU, mudando para um SKU ativado por GPU se não estiver ou mudar o seu ambiente para não exigir GPUs.  

## <a name="service-launch-fails"></a>Falha no lançamento do serviço

Depois de a imagem ser construída com sucesso, o sistema tenta iniciar um contentor utilizando a sua configuração de implantação. Como parte do processo de arranque do contentor, a `init()` função no seu script de pontuação é invocada pelo sistema. Se houver exceções não conseguindo na `init()` função, poderá ver o erro **do CrashLoopBackOff** na mensagem de erro.

Utilize a informação na secção [de registo do Docker](#dockerlog) para verificar os registos.

## <a name="function-fails-get_model_path"></a>Falha na função: get_model_path()

Frequentemente, `init()` na função no script de pontuação, a função [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) é chamada para localizar um ficheiro modelo ou uma pasta de ficheiros de modelos no recipiente. Se o ficheiro ou a pasta do modelo não puderem ser encontrados, a função falha. A maneira mais fácil de depurar este erro é executar o código Python abaixo na concha do recipiente:

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

As implementações do Serviço Azure Kubernetes suportam a autoscalagem, que permite adicionar réplicas para suportar carga adicional. No entanto, o autoescalador foi concebido para lidar com alterações **graduais** na carga. Se receber grandes picos de pedidos por segundo, os clientes poderão receber um código de estado HTTP 503.

Há duas coisas que podem ajudar a prevenir 503 códigos de estado:

* Alterar o nível de utilização em que a autoscalagem cria novas réplicas.
    
    Por padrão, a utilização do alvo de autoscalização está definida em 70%, o que significa que o serviço pode lidar com picos em pedidos por segundo (RPS) até 30%. Pode ajustar o alvo de utilização definindo o `autoscale_target_utilization` valor mais baixo.

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

Para obter mais informações sobre a definição `autoscale_target_utilization` `autoscale_max_replicas` , e `autoscale_min_replicas` para, consulte a referência do módulo [AksWebservice.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py)

## <a name="http-status-code-504"></a>Código de estado HTTP 504

Um código de estado 504 indica que o pedido está esgotado. O tempo limite de tempo padrão é de 1 minuto.

Pode aumentar o tempo limite ou tentar acelerar o serviço modificando o score.py para remover chamadas desnecessárias. Se estas ações não corrigirem o problema, utilize as informações deste artigo para depurar o ficheiro score.py. O código pode estar num estado suspenso ou num ciclo infinito.

## <a name="advanced-debugging"></a>Depuragem avançada

Em alguns casos, poderá ser necessário depurar interativamente o código Python contido na sua implementação de modelo. Por exemplo, se o script de entrada estiver a falhar e a razão não puder ser determinada por registo adicional. Ao utilizar o Código do Estúdio Visual e as Ferramentas Python para Estúdio Visual (PTVSD), pode anexar-se ao código que está a decorrer dentro do contentor Docker.

> [!IMPORTANT]
> Este método de depuração não funciona quando se utiliza `Model.deploy()` e implanta um modelo `LocalWebservice.deploy_configuration` localmente. Em vez disso, deve criar uma imagem utilizando o método [Modelo.pacote().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)

As implementações de serviços web locais requerem uma instalação de Docker funcionando no seu sistema local. Para obter mais informações sobre a utilização do Docker, consulte a Documentação do [Docker.](https://docs.docker.com/)

### <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Para instalar as Ferramentas Python para Estúdio Visual (PTVSD) no ambiente de desenvolvimento do Código VS local, utilize o seguinte comando:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Para obter mais informações sobre a utilização de PTVSD com código VS, consulte [a depuragem remota](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Para configurar o Código VS para comunicar com a imagem do Docker, crie uma nova configuração de depuração:

    1. A partir do Código VS, selecione o menu __Debug__ e, em seguida, selecione __configurações Open__. Um ficheiro chamado __launch.json__ abre.

    1. No ficheiro __launch.json,__ encontre a linha que `"configurations": [` contém, e insira o seguinte texto após o mesmo:

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Se já existirem outras entradas na secção de configurações, adicione uma vírgula (,) após o código que inseriu.

        Esta secção fixa-se ao contentor Docker utilizando a porta 5678.

    1. Guarde o ficheiro __launch.json.__

### <a name="create-an-image-that-includes-ptvsd"></a>Criar uma imagem que inclua PTVSD

1. Modifique o ambiente conda para a sua implantação de modo a incluir PTVSD. O exemplo a seguir demonstra a sua adição utilizando o `pip_packages` parâmetro:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.45', 'ptvsd'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Para iniciar o PTVSD e aguardar uma ligação quando o serviço começar, adicione o seguinte à parte superior do seu `score.py` ficheiro:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Crie uma imagem baseada na definição do ambiente e puxe a imagem para o registo local. Durante a depurada, pode querer fazer alterações nos ficheiros da imagem sem ter de recriá-lo. Para instalar um editor de texto (vim) na imagem do Docker, utilize o `Environment.docker.base_image` e `Environment.docker.base_dockerfile` propriedades:

    > [!NOTE]
    > Este exemplo pressupõe que `ws` aponta para o seu espaço de trabalho Azure Machine Learning, e este é o modelo que está a ser `model` implementado. O `myenv.yml` ficheiro contém as dependências da conda criadas no passo 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Uma vez criada e descarregada a imagem, o caminho da imagem (inclui repositório, nome e etiqueta, que neste caso também é a sua digestão) é exibido numa mensagem semelhante ao seguinte:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Para facilitar o trabalho com a imagem, utilize o seguinte comando para adicionar uma etiqueta. `myimagepath`Substitua-o pelo valor de localização do passo anterior.

    ```bash
    docker tag myimagepath debug:1
    ```

    Para o resto dos passos, você pode se referir à imagem local como `debug:1` em vez do valor completo do caminho da imagem.

### <a name="debug-the-service"></a>Depurar o serviço

> [!TIP]
> Se definir um tempo limite para a ligação PTVSD no `score.py` ficheiro, deve ligar o Código VS à sessão de depuração antes que o tempo limite expire. Iniciar o Código VS, abrir a cópia local de , definir um ponto de `score.py` rutura, e tê-lo pronto para ir antes de usar os passos nesta secção.
>
> Para obter mais informações sobre depuração e definição de pontos de rutura, consulte [Debugging](https://code.visualstudio.com/Docs/editor/debugging).

1. Para iniciar um recipiente Docker utilizando a imagem, utilize o seguinte comando:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Para ligar o Código VS ao PTVSD no interior do recipiente, abra o Código VS e utilize a tecla F5 ou selecione __Debug__. Quando solicitado, selecione a configuração __Azure Machine Learning: Docker Debug.__ Também pode selecionar o ícone de depuração da barra lateral, a entrada __Azure Machine Learning: Docker Debug__ do menu de dropdown Debug e, em seguida, usar a seta verde para fixar o depurador.

    ![O ícone de depuração, botão de depuração e seletor de configuração](./media/how-to-troubleshoot-deployment/start-debugging.png)

Neste ponto, o Código VS liga-se ao PTVSD dentro do recipiente Docker e para no ponto de rutura que definiu anteriormente. Agora pode passar pelo código à medida que corre, ver variáveis, etc.

Para obter mais informações sobre a utilização do Código VS para depurar python, consulte [Debug o seu código Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Modificar os ficheiros do contentor

Para escoar alterações nos ficheiros na imagem, pode ligar-se ao recipiente de funcionamento e executar uma casca de pancada. A partir daí, pode utilizar vim para editar ficheiros:

1. Para ligar ao recipiente de funcionamento e lançar uma casca de choque no recipiente, utilize o seguinte comando:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Para encontrar os ficheiros utilizados pelo serviço, utilize o seguinte comando a partir do reservatório de pancada no recipiente se o diretório predefinido for diferente `/var/azureml-app` de:

    ```bash
    cd /var/azureml-app
    ```

    A partir daqui, pode usar o vim para editar o `score.py` ficheiro. Para obter mais informações sobre a utilização do vim, consulte [utilizar o editor Vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. As alterações a um recipiente não são normalmente persistiu. Para guardar quaisquer alterações que escote, utilize o seguinte comando, antes de sair da concha iniciada no degrau acima (isto é, em outra concha):

    ```bash
    docker commit debug debug:2
    ```

    Este comando cria uma nova imagem chamada `debug:2` que contém as suas edições.

    > [!TIP]
    > Terá de parar o recipiente atual e começar a utilizar a nova versão antes que as alterações entrem em vigor.

1. Certifique-se de que mantém as alterações que faz aos ficheiros do recipiente em sintonia com os ficheiros locais que o Código VS utiliza. Caso contrário, a experiência de depurar não funcionará como esperado.

### <a name="stop-the-container"></a>Parar o contentor

Para parar o recipiente, utilize o seguinte comando:

```bash
docker stop debug
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a implementação:

* [Como implantar e onde](how-to-deploy-and-where.md)
* [Tutorial: Modelos de implantação de & de comboios](tutorial-train-models-with-aml.md)
