---
title: Guia de resolução de problemas de implementação
titleSuffix: Azure Machine Learning
description: Saiba como solucionar problemas, resolver e solucionar os erros comuns de implantação do Docker com o serviço kubernetes do Azure e as instâncias de contêiner do Azure usando Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: 1645d2848c6d4b852a81042c4db8a0f6e90fd8fd
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945804"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Solução de problemas Azure Machine Learning implantação do serviço kubernetes do Azure e instâncias de contêiner do Azure

Saiba como solucionar ou resolver erros comuns de implantação do Docker com ACI (instâncias de contêiner do Azure) e AKS (serviço kubernetes do Azure) usando o Azure Machine Learning.

Ao implantar um modelo no Azure Machine Learning, o sistema executa várias tarefas.

A abordagem recomendada e mais atualizada para implantação de modelo é por meio da API [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) usando um objeto de [ambiente](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) como um parâmetro de entrada. Nesse caso, nosso serviço criará uma imagem base do Docker para você durante o estágio de implantação e montará os modelos necessários em uma única chamada. As tarefas básicas de implantação são:

1. Registe o modelo no registo do modelo de área de trabalho.

2. Definir configuração de inferência:
    1. Crie um objeto de [ambiente](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) com base nas dependências especificadas no arquivo YAML do ambiente ou use um dos nossos ambientes adquiridos.
    2. Crie uma configuração de inferência (objeto InferenceConfig) com base no ambiente e no script de pontuação.

3. Implante o modelo no serviço ACI (instância de contêiner do Azure) ou no AKS (serviço kubernetes do Azure).

Saiba mais sobre esse processo no [gestão de modelos](concept-model-management-and-deployment.md) introdução.

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição do Azure**. Se você não tiver uma, experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* O [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* A [extensão da CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Para depurar localmente, você deve ter uma instalação de Docker em funcionamento no sistema local.

    Para verificar a instalação do Docker, use o comando `docker run hello-world` de um terminal ou um prompt de comando. Para obter informações sobre como instalar o Docker ou solucionar erros do Docker, consulte a [documentação do Docker](https://docs.docker.com/).

## <a name="before-you-begin"></a>Antes de começar

Caso se depare com algum problema, a primeira coisa a fazer é dividir a tarefa de implementação (descrito anterior) em etapas individuais para isolar o problema.

Supondo que você esteja usando o método de implantação novo/recomendado por meio da API [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) com um objeto de [ambiente](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) como um parâmetro de entrada, seu código pode ser dividido em três etapas principais:

1. Registe o modelo. Aqui está um código de exemplo:

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

3. Implante o modelo usando a configuração de inferência criada na etapa anterior:

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

Assim que já dividiu o processo de implantação em tarefas individuais, podemos ver alguns dos erros mais comuns.

## <a name="debug-locally"></a>Depurar localmente

Se você encontrar problemas ao implantar um modelo para ACI ou AKS, tente implantá-lo como um serviço Web local. Usar um serviço Web local torna mais fácil solucionar problemas. A imagem do Docker que contém o modelo é baixada e iniciada no sistema local.

> [!WARNING]
> Não há suporte para implantações de serviço Web local para cenários de produção.

Para implantar localmente, modifique seu código para usar `LocalWebservice.deploy_configuration()` para criar uma configuração de implantação. Em seguida, use `Model.deploy()` para implantar o serviço. O exemplo a seguir implanta um modelo (contido na variável de modelo) como um serviço Web local:

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

Observe que, se você estiver definindo sua própria especificação de Conda YAML, deverá listar o azureml-padrões com a versão > = 1.0.45 como uma dependência Pip. Esse pacote contém a funcionalidade necessária para hospedar o modelo como um serviço Web.

Neste ponto, você pode trabalhar com o serviço normalmente. Por exemplo, o código a seguir demonstra o envio de dados para o serviço:

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

Para obter mais informações sobre como personalizar seu ambiente Python, consulte [criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md). 

### <a name="update-the-service"></a>Atualizar o serviço

Durante os testes locais, talvez seja necessário atualizar o arquivo de `score.py` para adicionar o log ou tentar resolver os problemas que você descobriu. Para recarregar as alterações no arquivo de `score.py`, use `reload()`. Por exemplo, o código a seguir recarrega o script para o serviço e, em seguida, envia dados a ele. Os dados são pontuados usando o arquivo de `score.py` atualizado:

> [!IMPORTANT]
> O método `reload` só está disponível para implantações locais. Para obter informações sobre como atualizar uma implantação para outro destino de computação, consulte a seção Atualizar de [implantar modelos](how-to-deploy-and-where.md#update).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> O script é recarregado a partir do local especificado pelo objeto `InferenceConfig` usado pelo serviço.

Para alterar o modelo, as dependências Conda ou a configuração de implantação, use [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). O exemplo a seguir atualiza o modelo usado pelo serviço:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Excluir o serviço

Para excluir o serviço, use [Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a>Inspecionar o log do Docker

Pode imprimir mensagens de registo do Docker motor detalhadas do objeto de serviço. Você pode exibir o log para implantações ACI, AKS e local. O exemplo a seguir demonstra como imprimir os logs.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Falha de inicialização de serviço

Depois que a imagem for criada com êxito, o sistema tentará iniciar um contêiner usando sua configuração de implantação. Como parte do processo de cópia de segurança a partir do contentor, o `init()` função no seu script de classificação é invocada pelo sistema. Se existirem exceções não identificadas no `init()` funcione, poderá ver **CrashLoopBackOff** erro na mensagem de erro.

Use as informações na seção [inspecionar o log do Docker](#dockerlog) para verificar os logs.

## <a name="function-fails-get_model_path"></a>Falha de função: get_model_path()

Geralmente, na função `init()` no script de pontuação, a função [Model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) é chamada para localizar um arquivo de modelo ou uma pasta de arquivos de modelo no contêiner. Se a pasta ou o arquivo de modelo não puder ser encontrado, a função falhará. A maneira mais fácil para depurar este erro é executar o abaixo o código de Python no shell do contentor:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Este exemplo imprime o caminho local (relativo a `/var/azureml-app`) no contêiner em que o script de Pontuação espera encontrar o arquivo ou a pasta do modelo. Em seguida, pode verificar se o ficheiro ou pasta está, de fato, onde é esperado que seja.

Definir o nível de log para depurar pode fazer com que informações adicionais sejam registradas, o que pode ser útil para identificar a falha.

## <a name="function-fails-runinput_data"></a>Falha de função: run(input_data)

Se o serviço é implementado com êxito, mas ele falha quando postar dados para o ponto final de classificação, pode adicionar erro capturando instrução em seu `run(input_data)` funcionar, para que ele retorne a mensagem de erro detalhada em vez disso. Por exemplo:

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

**Tenha em atenção**: a devolver mensagens de erro do `run(input_data)` chamada deve ser feita para apenas a fins de depuração. Por motivos de segurança, você não deve retornar mensagens de erro dessa maneira em um ambiente de produção.

## <a name="http-status-code-503"></a>Código de status HTTP 503

As implantações do serviço kubernetes do Azure dão suporte ao dimensionamento automático, que permite que as réplicas sejam adicionadas para dar suporte à carga adicional. No entanto, o dimensionador automático foi projetado para lidar com alterações **graduais** na carga. Se você receber grandes picos em solicitações por segundo, os clientes poderão receber um código de status HTTP 503.

Há duas coisas que podem ajudar a evitar códigos de status 503:

* Altere o nível de utilização no qual o dimensionamento automático cria novas réplicas.
    
    Por padrão, a utilização de destino de dimensionamento automático é definida como 70%, o que significa que o serviço pode lidar com picos em solicitações por segundo (RPS) de até 30%. Você pode ajustar o destino de utilização definindo o `autoscale_target_utilization` para um valor mais baixo.

    > [!IMPORTANT]
    > Essa alteração não faz com que as réplicas sejam criadas *mais rapidamente*. Em vez disso, eles são criados em um limite de utilização mais baixo. Em vez de aguardar até que o serviço seja 70% utilizado, alterar o valor para 30% faz com que as réplicas sejam criadas quando ocorre uma utilização de 30%.
    
    Se o serviço Web já estiver usando as réplicas máximas atuais e você ainda estiver vendo os códigos de status 503, aumente o valor `autoscale_max_replicas` para aumentar o número máximo de réplicas.

* Altere o número mínimo de réplicas. Aumentar as réplicas mínimas fornece um pool maior para lidar com os picos de entrada.

    Para aumentar o número mínimo de réplicas, defina `autoscale_min_replicas` como um valor mais alto. Você pode calcular as réplicas necessárias usando o código a seguir, substituindo valores por valores específicos do seu projeto:

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
    > Se você receber picos de solicitação maiores do que as novas réplicas mínimas podem lidar, você pode receber 503s novamente. Por exemplo, à medida que o tráfego para o serviço aumenta, talvez seja necessário aumentar as réplicas mínimas.

Para obter mais informações sobre como definir `autoscale_target_utilization`, `autoscale_max_replicas`e `autoscale_min_replicas` para, consulte a referência do módulo [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) .

## <a name="advanced-debugging"></a>Depuração avançada

Em alguns casos, talvez seja necessário depurar interativamente o código Python contido em sua implantação de modelo. Por exemplo, se o script de entrada estiver falhando e o motivo não puder ser determinado por log adicional. Usando Visual Studio Code e o Ferramentas Python para Visual Studio (PTVSD), você pode anexar ao código em execução dentro do contêiner do Docker.

> [!IMPORTANT]
> Esse método de depuração não funciona ao usar `Model.deploy()` e `LocalWebservice.deploy_configuration` para implantar um modelo localmente. Em vez disso, você deve criar uma imagem usando o método [Model. Package ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-) .

As implantações de serviço Web local exigem uma instalação de Docker em funcionamento no sistema local. Para obter mais informações sobre como usar o Docker, consulte a [documentação do Docker](https://docs.docker.com/).

### <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Para instalar o Ferramentas Python para Visual Studio (PTVSD) no ambiente de desenvolvimento de VS Code local, use o seguinte comando:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Para obter mais informações sobre como usar o PTVSD com VS Code, consulte [depuração remota](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Para configurar VS Code para se comunicar com a imagem do Docker, crie uma nova configuração de depuração:

    1. Em VS Code, selecione o menu __depurar__ e, em seguida, selecione __configurações abertas__. Um arquivo chamado __Launch. JSON__ é aberto.

    1. No arquivo __Launch. JSON__ , localize a linha que contém `"configurations": [`e insira o seguinte texto após:

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
        > Se já houver outras entradas na seção Configurações, adicione uma vírgula (,) após o código que você inseriu.

        Esta seção anexa o contêiner do Docker usando a porta 5678.

    1. Salve o arquivo __Launch. JSON__ .

### <a name="create-an-image-that-includes-ptvsd"></a>Criar uma imagem que inclui PTVSD

1. Modifique o ambiente Conda para sua implantação para que ele inclua o PTVSD. O exemplo a seguir demonstra como adicioná-lo usando o parâmetro `pip_packages`:

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

1. Para iniciar o PTVSD e aguardar uma conexão quando o serviço for iniciado, adicione o seguinte à parte superior do seu arquivo de `score.py`:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Crie uma imagem com base na definição de ambiente e extraia a imagem para o registro local. Durante a depuração, talvez você queira fazer alterações nos arquivos da imagem sem precisar recriá-los. Para instalar um vim (editor de texto) na imagem do Docker, use as propriedades `Environment.docker.base_image` e `Environment.docker.base_dockerfile`:

    > [!NOTE]
    > Este exemplo pressupõe que `ws` aponta para seu espaço de trabalho Azure Machine Learning e que `model` é o modelo que está sendo implantado. O arquivo de `myenv.yml` contém as dependências Conda criadas na etapa 1.

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

    Depois que a imagem tiver sido criada e baixada, o caminho da imagem (inclui repositório, nome e marca, que nesse caso também é seu resumo) é exibido em uma mensagem semelhante à seguinte:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Para facilitar o trabalho com a imagem, use o comando a seguir para adicionar uma marca. Substitua `myimagepath` pelo valor de local da etapa anterior.

    ```bash
    docker tag myimagepath debug:1
    ```

    Para o restante das etapas, você pode consultar a imagem local como `debug:1` em vez do valor do caminho de imagem completo.

### <a name="debug-the-service"></a>Depurar o serviço

> [!TIP]
> Se você definir um tempo limite para a conexão PTVSD no arquivo `score.py`, deverá conectar VS Code à sessão de depuração antes que o tempo limite expire. Inicie o VS Code, abra a cópia local de `score.py`, defina um ponto de interrupção e prepare-o antes de usar as etapas nesta seção.
>
> Para obter mais informações sobre depuração e definição de pontos de interrupção, consulte [Debugging](https://code.visualstudio.com/Docs/editor/debugging).

1. Para iniciar um contêiner do Docker usando a imagem, use o seguinte comando:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Para anexar VS Code ao PTVSD dentro do contêiner, abra VS Code e use a tecla F5 ou selecione __depurar__. Quando solicitado, selecione o __Azure Machine Learning: configuração de depuração do Docker__ . Você também pode selecionar o ícone de depuração na barra lateral, a __Azure Machine Learning: entrada de depuração do Docker__ no menu suspenso depurar e, em seguida, usar a seta verde para anexar o depurador.

    ![O ícone de depuração, o botão Iniciar Depuração e o seletor de configuração](./media/how-to-troubleshoot-deployment/start-debugging.png)

Neste ponto, VS Code se conecta ao PTVSD dentro do contêiner do Docker e para o ponto de interrupção definido anteriormente. Agora você pode percorrer o código conforme ele é executado, exibir variáveis, etc.

Para obter mais informações sobre como usar VS Code para depurar o Python, consulte [depurar seu código Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Modificar os arquivos de contêiner

Para fazer alterações nos arquivos da imagem, você pode anexar ao contêiner em execução e executar um shell bash. A partir daí, você pode usar o vim para editar arquivos:

1. Para se conectar ao contêiner em execução e iniciar um shell bash no contêiner, use o seguinte comando:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Para localizar os arquivos usados pelo serviço, use o seguinte comando do shell bash no contêiner se o diretório padrão for diferente do `/var/azureml-app`:

    ```bash
    cd /var/azureml-app
    ```

    A partir daqui, você pode usar o vim para editar o arquivo de `score.py`. Para obter mais informações sobre como usar o vim, consulte [usando o editor de vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. As alterações em um contêiner normalmente não são mantidas. Para salvar as alterações feitas, use o comando a seguir, antes de sair do Shell iniciado na etapa anterior (ou seja, em outro Shell):

    ```bash
    docker commit debug debug:2
    ```

    Este comando cria uma nova imagem chamada `debug:2` que contém as edições.

    > [!TIP]
    > Você precisará parar o contêiner atual e começar a usar a nova versão antes que as alterações entrem em vigor.

1. Certifique-se de manter as alterações feitas nos arquivos no contêiner em sincronia com os arquivos locais que o VS Code usa. Caso contrário, a experiência do depurador não funcionará conforme o esperado.

### <a name="stop-the-container"></a>Parar o contentor

Para parar o contêiner, use o seguinte comando:

```bash
docker stop debug
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a implementação:

* [Como implementar e, em que](how-to-deploy-and-where.md)
* [Tutorial: Preparar e implementar modelos](tutorial-train-models-with-aml.md)
