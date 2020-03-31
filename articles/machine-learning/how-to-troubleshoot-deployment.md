---
title: Guia de resolução de problemas de implementação
titleSuffix: Azure Machine Learning
description: Aprenda a trabalhar, resolver e resolver os erros comuns de implantação do Docker com o Serviço Azure Kubernetes e as instâncias de contentores Azure utilizando o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: fab46f7d7ae74ad643ce3f122b27b0dc767f5a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399678"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Resolução de problemas Azure Machine Learning Azure Kubernetes Service e implantação de instâncias de contentores Azure

Aprenda a contornar ou a resolver erros comuns de implementação do Docker com as Instâncias de Contentores Azure (ACI) e o Serviço Azure Kubernetes (AKS) utilizando o Azure Machine Learning.

Ao implementar um modelo em Azure Machine Learning, o sistema executa uma série de tarefas.

A abordagem recomendada e mais atualizada para a implementação do modelo é através da API [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) utilizando um objeto [Ambiente](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) como parâmetro de entrada. Neste caso, o nosso serviço criará uma imagem base de estivador para si durante a fase de implantação e montará os modelos necessários, todos numa única chamada. As tarefas básicas de implantação são:

1. Registe o modelo no registo do modelo workspace.

2. Definir Configuração de Inferência:
    1. Crie um objeto [Ambiente](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) baseado nas dependências que especifica no ficheiro iaml ambiente ou utilize um dos nossos ambientes adquiridos.
    2. Crie uma configuração de inferência (objeto InferenceConfig) com base no ambiente e no script de pontuação.

3. Desloque o modelo para o serviço Azure Container Instance (ACI) ou para o Serviço Azure Kubernetes (AKS).

Saiba mais sobre este processo na introdução da Gestão de [Modelos.](concept-model-management-and-deployment.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição Azure.** Se não tiver uma, experimente a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)
* O [SDK de Aprendizagem automática Azure.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* O [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* A [extensão CLI para Aprendizagem automática Azure](reference-azure-machine-learning-cli.md).
* Para depurar localmente, deve ter uma instalação de Docker em funcionamento no seu sistema local.

    Para verificar a sua instalação do Docker, utilize o comando `docker run hello-world` a partir de um terminal ou de um pedido de comando. Para obter informações sobre a instalação do Docker, ou problemas de resolução de erros do Docker, consulte a [Documentação do Docker](https://docs.docker.com/).

## <a name="before-you-begin"></a>Antes de começar

Se tiver algum problema, a primeira coisa a fazer é decompor a tarefa de implantação (descrita anteriormente) em passos individuais para isolar o problema.

Assumindo que está a utilizar o novo/recomendado método de implementação através do [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API com um objeto [Ambiente](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) como parâmetro de entrada, o seu código pode ser desfeito em três passos principais:

1. Registar o modelo. Aqui está um código de amostra:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Definir configuração de inferência para implementação:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Implementar o modelo utilizando a configuração de inferência criada na etapa anterior:

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

Uma vez que você tenha dividido o processo de implantação em tarefas individuais, podemos olhar para alguns dos erros mais comuns.

## <a name="debug-locally"></a>Depuração local

Se encontrar problemas em implementar um modelo para ACI ou AKS, tente implementá-lo como um serviço web local. A utilização de um serviço web local facilita a resolução de problemas. A imagem do Docker que contém o modelo é descarregada e iniciada no seu sistema local.

> [!WARNING]
> As implementações de serviços web locais não são suportadas para cenários de produção.

Para implementar localmente, modifique o seu código para usar `LocalWebservice.deploy_configuration()` para criar uma configuração de implementação. Em `Model.deploy()` seguida, use para implementar o serviço. O exemplo seguinte implementa um modelo (contido na variável modelo) como um serviço web local:

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

Por favor, note que se estiver a definir a sua própria especificação de conda YAML, deve listar os incumprimentos em azul com a versão >= 1.0,45 como dependência do pip. Este pacote contém a funcionalidade necessária para hospedar o modelo como um serviço web.

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

Durante os testes locais, `score.py` poderá ter de atualizar o ficheiro para adicionar registo ou tentar resolver quaisquer problemas que tenha descoberto. Para recarregar alterações `score.py` no `reload()`ficheiro, utilize . Por exemplo, o código seguinte recarrega o script para o serviço e, em seguida, envia dados para o mesmo. Os dados são obtidos utilizando o ficheiro atualizado: `score.py`

> [!IMPORTANT]
> O `reload` método só está disponível para destacamentos locais. Para obter informações sobre a atualização de uma implementação para outro alvo de cálculo, consulte a secção de atualização dos [modelos Deploy](how-to-deploy-and-where.md#update).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> O script é recarregado a partir `InferenceConfig` do local especificado pelo objeto utilizado pelo serviço.

Para alterar o modelo, dependências da Conda ou configuração de implementação, utilize [a atualização()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). O exemplo seguinte atualiza o modelo utilizado pelo serviço:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Eliminar o serviço

Para eliminar o serviço, utilize [a eliminação()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>Inspecione o registo do Docker

Pode imprimir mensagens detalhadas do motor Docker do objeto de serviço. Pode visualizar o registo para implementações ACI, AKS e Locais. O exemplo que se segue demonstra como imprimir os registos.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Lançamento de serviço falha

Depois de a imagem ser construída com sucesso, o sistema tenta iniciar um recipiente utilizando a sua configuração de implantação. Como parte do processo de arranque `init()` do recipiente, a função no seu script de pontuação é invocada pelo sistema. Se houver exceções não `init()` apanhadas na função, poderá ver o erro **crashLoopBackOff** na mensagem de erro.

Utilize a informação na secção [de registo Supor a secção de registo supor](#dockerlog) os registos.

## <a name="function-fails-get_model_path"></a>Falha na função: get_model_path()

Muitas vezes, na `init()` função no script de pontuação, a função [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) é chamada para localizar um ficheiro modelo ou uma pasta de ficheiros de modelo sintetizado no recipiente. Se o ficheiro ou pasta do modelo não puder ser encontrado, a função falha. A maneira mais fácil de depurar este erro é executar o código Python abaixo na concha do recipiente:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Este exemplo imprime o caminho `/var/azureml-app`local (em relação a) no recipiente onde o seu script de pontuação espera encontrar o ficheiro ou pasta do modelo. Em seguida, pode verificar se o ficheiro ou pasta está de facto onde se espera que esteja.

A definição do nível de registo para O DEBUG pode provocar o registo de informações adicionais, o que pode ser útil para identificar a falha.

## <a name="function-fails-runinput_data"></a>Falha na função: executar(input_data)

Se o serviço for implementado com sucesso, mas falhar quando publica dados no ponto final `run(input_data)` da pontuação, pode adicionar uma declaração de captura de erros na sua função de modo a que dereta uma mensagem de erro detalhada. Por exemplo:

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

**Nota**: A devolução das mensagens de erro da `run(input_data)` chamada deve ser feita apenas para fins de depuração. Por razões de segurança, não deve devolver mensagens de erro desta forma num ambiente de produção.

## <a name="http-status-code-502"></a>Código de estado HTTP 502

Um código de estado 502 indica que o serviço `run()` lançou uma exceção ou se despenhou no método do ficheiro score.py. Utilize as informações deste artigo para depurar o ficheiro.

## <a name="http-status-code-503"></a>Código de estado HTTP 503

As implementações do Serviço Azure Kubernetes suportam a autoscalcificação, o que permite adicionar réplicas para suportar cargaadicional. No entanto, o autoscaler foi concebido para lidar com alterações **graduais** na carga. Se receber grandes picos de pedidos por segundo, os clientes poderão receber um código de estado HTTP 503.

Há duas coisas que podem ajudar a prevenir 503 códigos de estado:

* Altere o nível de utilização ao qual a autoscalcificação cria novas réplicas.
    
    Por padrão, a utilização do alvo de autoscalcificação está fixada em 70%, o que significa que o serviço pode lidar com picos em pedidos por segundo (RPS) até 30%. Pode ajustar o alvo de `autoscale_target_utilization` utilização definindo o alvo para um valor mais baixo.

    > [!IMPORTANT]
    > Esta mudança não faz com que as réplicas sejam criadas *mais rapidamente*. Em vez disso, são criados num limiar de utilização mais baixo. Em vez de esperar até que o serviço seja 70% utilizado, mudar o valor para 30% faz com que as réplicas sejam criadas quando ocorre uma utilização de 30%.
    
    Se o serviço web já estiver a utilizar as réplicas max atuais `autoscale_max_replicas` e ainda estiver a ver 503 códigos de estado, aumente o valor para aumentar o número máximo de réplicas.

* Mude o número mínimo de réplicas. O aumento das réplicas mínimas proporciona uma piscina maior para lidar com os picos de entrada.

    Para aumentar o número mínimo `autoscale_min_replicas` de réplicas, definida para um valor mais elevado. Pode calcular as réplicas necessárias utilizando o seguinte código, substituindo valores por valores específicos do seu projeto:

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
    > Se receber picos de pedido maiores do que as novas réplicas mínimas podem suportar, poderá voltar a receber 503s. Por exemplo, à medida que o tráfego para o seu serviço aumenta, poderá ter de aumentar as réplicas mínimas.

Para obter mais `autoscale_target_utilization` `autoscale_max_replicas`informações `autoscale_min_replicas` sobre a definição , e para, consulte a referência do módulo [AksWebservice.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py)

## <a name="http-status-code-504"></a>Código de estado HTTP 504

Um código de estado 504 indica que o pedido tem prazo. O tempo de paragem padrão é de 1 minuto.

Pode aumentar o tempo limite ou tentar acelerar o serviço modificando o score.py para remover chamadas desnecessárias. Se estas ações não corrigirem o problema, utilize a informação neste artigo para depurar o ficheiro score.py. O código pode estar num estado suspenso ou num ciclo infinito.

## <a name="advanced-debugging"></a>Depuração avançada

Em alguns casos, poderá ser necessário depurar interativamente o código Python contido na implementação do seu modelo. Por exemplo, se o script de entrada estiver a falhar e a razão não puder ser determinada por registo saqueado adicional. Utilizando o Código do Estúdio Visual e as Ferramentas Python para Estúdio Visual (PTVSD), pode anexar-se ao código que funciona dentro do recipiente Docker.

> [!IMPORTANT]
> Este método de depuração não `Model.deploy()` `LocalWebservice.deploy_configuration` funciona quando se utiliza e implementa um modelo localmente. Em vez disso, deve criar uma imagem utilizando o método [Model.package().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)

As implementações locais do serviço web requerem uma instalação de Docker em funcionamento no seu sistema local. Para obter mais informações sobre a utilização do Docker, consulte a [Documentação do Docker](https://docs.docker.com/).

### <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Para instalar as Ferramentas Python para Estúdio Visual (PTVSD) no seu ambiente de desenvolvimento do Código VS local, utilize o seguinte comando:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Para obter mais informações sobre a utilização do PTVSD com código VS, consulte [A Depuração Remota](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Para configurar o Código VS para comunicar com a imagem do Docker, crie uma nova configuração de depuração:

    1. A partir do Código VS, selecione o menu __Debug__ e, em seguida, __selecione configurações abertas__. Um ficheiro chamado __Launch.json__ abre.

    1. No ficheiro __launch.json,__ encontre a `"configurations": [`linha que contém , e insira o seguinte texto após a sua:

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
        > Se já houver outras entradas na secção de configurações, adicione uma vírmula (,) após o código que inseriu.

        Esta secção prende-se ao recipiente Docker utilizando a porta 5678.

    1. Guarde o ficheiro __launch.json.__

### <a name="create-an-image-that-includes-ptvsd"></a>Criar uma imagem que inclua PTVSD

1. Modifique o ambiente de condomínio para a sua implementação de modo a que inclua PTVSD. O exemplo que se segue `pip_packages` demonstra adicioná-lo utilizando o parâmetro:

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

1. Para iniciar o PTVSD e esperar por uma ligação quando `score.py` o serviço começar, adicione o seguinte à parte superior do seu ficheiro:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Crie uma imagem baseada na definição ambiental e puxe a imagem para o registo local. Durante a depuração, é melhor efazer alterações nos ficheiros da imagem sem ter de o recriar. Para instalar um editor de texto (vim) `Environment.docker.base_image` `Environment.docker.base_dockerfile` na imagem do Docker, utilize as propriedades e propriedades:

    > [!NOTE]
    > Este exemplo pressupõe que `ws` aponta para o seu `model` espaço de trabalho azure Machine Learning, e que é o modelo que está a ser implementado. O `myenv.yml` ficheiro contém as dependências da conda criadas no passo 1.

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

    Uma vez que a imagem foi criada e descarregada, o caminho da imagem (inclui repositório, nome e etiqueta, que neste caso também é a sua digestão) é exibido numa mensagem semelhante à seguinte:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Para facilitar o funcionao com a imagem, utilize o seguinte comando para adicionar uma etiqueta. Substitua-a `myimagepath` pelo valor de localização do passo anterior.

    ```bash
    docker tag myimagepath debug:1
    ```

    Para o resto dos passos, pode consultar `debug:1` a imagem local como em vez do valor total do caminho da imagem.

### <a name="debug-the-service"></a>Depurar o serviço

> [!TIP]
> Se definir um prazo para a ligação `score.py` PTVSD no ficheiro, deve ligar o Código VS à sessão de depuração antes que o prazo expire. Inicie o Código VS, `score.py`abra a cópia local de , despolete um ponto de rutura e prepare-o antes de utilizar os passos nesta secção.
>
> Para obter mais informações sobre depuração e definição de pontos de rutura, consulte [Debugging](https://code.visualstudio.com/Docs/editor/debugging).

1. Para iniciar um recipiente Docker utilizando a imagem, utilize o seguinte comando:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Para fixar o Código VS ao PTVSD no interior do recipiente, abra o Código VS e utilize a tecla F5 ou selecione __Debug__. Quando solicitado, selecione a configuração __Azure Machine Learning: Docker Debug.__ Também pode selecionar o ícone de depuração da barra lateral, a entrada __de Máquinas Azure: Docker Debug__ a partir do menu de deserroceção e, em seguida, usar a seta verde para prender o debugger.

    ![O ícone de depuração, comece a depurar o botão e o seletor de configuração](./media/how-to-troubleshoot-deployment/start-debugging.png)

Neste ponto, o Código VS liga-se ao PTVSD dentro do recipiente Does e para no ponto de rutura que definiu anteriormente. Agora pode passar pelo código à medida que corre, ver variáveis, etc.

Para obter mais informações sobre a utilização do Código VS para depurar python, consulte [Debug your Python code](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Modificar os ficheiros do contentor

Para efazer alterações nos ficheiros na imagem, pode anexar-se ao recipiente de corrente e executar uma concha de batida. A partir daí, pode usar vim para editar ficheiros:

1. Para ligar ao recipiente de corrida e lançar uma concha no recipiente, utilize o seguinte comando:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Para encontrar os ficheiros utilizados pelo serviço, utilize o seguinte comando da concha `/var/azureml-app`de batida no recipiente se o diretório predefinido for diferente de:

    ```bash
    cd /var/azureml-app
    ```

    A partir daqui, pode usar `score.py` vim para editar o ficheiro. Para obter mais informações sobre o uso do vim, consulte [O editor da Vim.](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)

1. Normalmente, as alterações num recipiente não são persistidas. Para evitar quaisquer alterações que faça, utilize o seguinte comando, antes de sair da concha começou no degrau acima (isto é, em outra concha):

    ```bash
    docker commit debug debug:2
    ```

    Este comando cria uma `debug:2` nova imagem chamada que contém as suas edidas.

    > [!TIP]
    > Terá de parar o recipiente atual e começar a utilizar a nova versão antes de as alterações entrarem em vigor.

1. Certifique-se de manter as alterações que efaz em ficheiros no recipiente em sincronização com os ficheiros locais que o Código VS utiliza. Caso contrário, a experiência de desbugger não funcionará como esperado.

### <a name="stop-the-container"></a>Parar o contentor

Para parar o recipiente, utilize o seguinte comando:

```bash
docker stop debug
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a implementação:

* [Como implantar e onde](how-to-deploy-and-where.md)
* [Tutorial: & de treino implementar modelos](tutorial-train-models-with-aml.md)
