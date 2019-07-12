---
title: Guia de resolução de problemas de implementação
titleSuffix: Azure Machine Learning service
description: Aprenda a solucionar e resolver e solucionar problemas de erros de implementação comuns do Docker com o serviço Kubernetes do Azure e o Azure Container Instances com o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 07/09/2018
ms.custom: seodec18
ms.openlocfilehash: e0f4b024d717c08df3514df057abf89d55be1dc9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707046"
---
# <a name="troubleshooting-azure-machine-learning-service-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Resolução de problemas de implementação de serviço Kubernetes do Azure e o Azure Container Instances de serviço do Azure Machine Learning

Aprenda a contornar ou resolver erros comuns de implementação de Docker com o Azure Container Instances (ACI) e o Azure Kubernetes Service (AKS) com o serviço Azure Machine Learning.

Ao implementar um modelo no serviço Azure Machine Learning, o sistema executa um número de tarefas. As tarefas de implementação são:

1. Registe o modelo no registo do modelo de área de trabalho.

2. Criar uma imagem do Docker, incluindo:
    1. Baixe o modelo registado no registo. 
    2. Crie um dockerfile, com um ambiente de Python com base nas dependências que especificar no arquivo de yaml de ambiente.
    3. Adicione seus arquivos de modelo e o script de classificação que fornece no dockerfile.
    4. Crie uma nova imagem de Docker com o dockerfile.
    5. Registre-se a imagem do Docker com o Azure Container Registry associadas com a área de trabalho.

    > [!IMPORTANT]
    > Dependendo do seu código, criação de imagens ocorre automaticamente sem sua entrada.

3. Implemente a imagem do Docker para o serviço de instância de contentor do Azure (ACI) ou para o Azure Kubernetes Service (AKS).

4. Inicie um novo contentor (ou contentores) no ACI ou AKS. 

Saiba mais sobre esse processo no [gestão de modelos](concept-model-management-and-deployment.md) introdução.

## <a name="before-you-begin"></a>Antes de começar

Caso se depare com algum problema, a primeira coisa a fazer é dividir a tarefa de implementação (descrito anterior) em etapas individuais para isolar o problema.

Separando a implementação de tarefas é útil se estiver a utilizar o [Webservice.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) API, ou [Webservice.deploy_from_model()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) API, como ambas as funções execute os passos mencionados anteriormente como um única ação. Normalmente, essas APIs são convenientes, mas ajuda a dividir os passos quando a resolução de problemas substituí-los com o abaixo chamadas de API.

1. Registe o modelo. Eis alguns exemplos de código:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Crie a imagem. Eis alguns exemplos de código:

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      entry_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. Implemente a imagem como serviço. Eis alguns exemplos de código:

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

Assim que já dividiu o processo de implantação em tarefas individuais, podemos ver alguns dos erros mais comuns.

## <a name="image-building-fails"></a>Falha de criação de imagem

Se não for possível criar a imagem do Docker, o [image.wait_for_creation()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) ou [service.wait_for_deployment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) chamada falha com algumas mensagens de erro que podem oferecer algumas dicas. Também pode encontrar mais detalhes sobre os erros de log da compilação de imagem. Abaixo é um código de exemplo que mostra como detetar o uri de log da compilação de imagem.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```

O uri de registo de imagem é um URL de SAS que aponta para um ficheiro de registo armazenado no armazenamento de Blobs do Azure. Basta copiar e colar o uri numa janela do browser e podem transferir e ver o ficheiro de registo.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Política de acesso do Cofre de chaves do Azure e os modelos Azure Resource Manager

A compilação de imagem também pode falhar devido a um problema com a política de acesso no Azure Key Vault. Esta situação pode ocorrer quando usar um modelo Azure Resource Manager para criar a área de trabalho e os recursos associados (incluindo o Azure Key Vault), várias vezes. Por exemplo, usando o modelo várias vezes com os mesmos parâmetros como parte de um pipeline de implementação e integração contínua.

A maioria das operações de criação de recursos através de modelos são idempotent, mas o Key Vault limpa as políticas de acesso sempre que o modelo é utilizado. Limpar o acesso políticas quebras o acesso ao Key Vault para qualquer área de trabalho existente que está a utilizar. Esta condição resulta em erros quando tenta criar novas imagens. Seguem-se exemplos dos erros que pode receber:

__Portal__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

Para evitar este problema, recomendamos uma das abordagens seguintes:

* Não implemente o modelo mais do que uma vez para os mesmos parâmetros. Ou eliminar os recursos existentes antes de utilizar o modelo de recriá-las.
* Examine as políticas de acesso do Cofre de chaves e, em seguida, utilizar estas políticas para definir o `accessPolicies` propriedade do modelo.
* Verifique se o recurso do Key Vault já existe. Se assim for, não recriá-lo através do modelo. Por exemplo, adicione um parâmetro que permite que desabilite a criação do recurso do Key Vault, se já existir.

## <a name="debug-locally"></a>Depurar localmente

Se tiver problemas ao implementar um modelo para o ACI ou AKS, tente implantá-lo como um serviço da local web. Utilização de um serviço da local web torna mais fácil resolver problemas. A imagem do Docker que contém o modelo é transferida e iniciada no seu sistema local.

> [!IMPORTANT]
> Implementações de serviços da local web exigem um instalação do Docker no seu sistema local de trabalho. Tem de executar o docker antes de implementar um serviço da local web. Para obter informações sobre como instalar e utilizar o Docker, consulte [ https://www.docker.com/ ](https://www.docker.com/).

> [!WARNING]
> Implementações de serviços da local web não são suportadas para cenários de produção.

Para implementar localmente, modifique o código para usar `LocalWebservice.deploy_configuration()` para criar uma configuração de implementação. Em seguida, utilizar `Model.deploy()` para implementar o serviço. O exemplo seguinte implementa um modelo (contidos no `model` variável) como um serviço da local web:

```python
from azureml.core.model import InferenceConfig,Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Neste ponto, pode trabalhar com o serviço como normal. Por exemplo, o código a seguir demonstra o envio de dados para o serviço:

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>Atualize o serviço

Durante a realização de testes locais, poderá ter de atualizar o `score.py` ficheiro para adicionar registos ou tentar resolver quaisquer problemas descobertos. Para recarregar as alterações para o `score.py` de ficheiros, utilize `reload()`. Por exemplo, o código a seguir Recarrega o script para o serviço e, em seguida, envia dados para ele. Os dados são classificados usando a atualização `score.py` ficheiro:

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> O script é recarregado da localização especificada pelo `InferenceConfig` objeto usado pelo serviço.

Para alterar o modelo, as dependências de Conda ou configuração de implementação, utilize [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). O exemplo seguinte atualiza o modelo usado pelo serviço:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Eliminar o serviço

Para eliminar o serviço, utilize [DELETE ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a> Inspecione o registo do Docker

Pode imprimir mensagens de registo do Docker motor detalhadas do objeto de serviço. Pode ver o registo para o ACI, AKS e implementações locais. O exemplo seguinte demonstra como os registos de impressão.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Falha de inicialização de serviço

Depois da imagem é criada com êxito, o sistema tenta iniciar um contentor utilizando a configuração da implementação. Como parte do processo de cópia de segurança a partir do contentor, o `init()` função no seu script de classificação é invocada pelo sistema. Se existirem exceções não identificadas no `init()` funcione, poderá ver **CrashLoopBackOff** erro na mensagem de erro.

Utilize as informações no [inspecionar o registo do Docker](#dockerlog) secção verificar os logs.

## <a name="function-fails-getmodelpath"></a>Falha de função: get_model_path()

Muitas vezes, o `init()` função no script de classificação, [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) função é chamada para localizar um ficheiro de modelo ou de uma pasta de ficheiros de modelo no contentor. Se não é possível localizar a pasta ou ficheiro de modelo, a função falhar. A maneira mais fácil para depurar este erro é executar o abaixo o código de Python no shell do contentor:

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Este exemplo imprime o caminho local (relação ao `/var/azureml-app`) no contentor onde o seu script de classificação está esperando para localizar o ficheiro de modelo ou a pasta. Em seguida, pode verificar se o ficheiro ou pasta está, de fato, onde é esperado que seja.

Definir o nível de registo para depuração pode fazer com que as informações adicionais de ter sessão iniciada, que podem ser úteis na identificação da falha.

## <a name="function-fails-runinputdata"></a>Falha de função: run(input_data)

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

**Nota**: Devolver mensagens de erro do `run(input_data)` chamada deve ser feita para apenas a fins de depuração. Por motivos de segurança, não deve devolver mensagens de erro desta forma num ambiente de produção.

## <a name="http-status-code-503"></a>Código de estado HTTP 503

Implementações de serviço Kubernetes do Azure suportam o dimensionamento automático, o que permite que as réplicas sejam adicionados para suportar a carga adicional. No entanto, o dimensionamento automático foi concebido para processar **gradual** alterações na carga. Se receber grandes picos de pedidos por segundo, os clientes podem receber um código de estado HTTP 503.

Existem duas coisas que podem ajudar a evitar 503 códigos de estado:

* Alterar o nível de utilização em qual dimensionamento automático cria réplicas de novo.
    
    Por predefinição, a utilização de destino de dimensionamento automático está definida para 70%, o que significa que o serviço pode processar picos de pedidos por segundo (RPS) de até 30%. Pode ajustar o destino de utilização ao definir o `autoscale_target_utilization` para um valor inferior.

    > [!IMPORTANT]
    > Esta alteração não faz com que as réplicas sejam criados *mais rapidamente*. Em vez disso, são criados com um limiar de utilização mais baixo. Em vez de aguardar até que o serviço é de 70% utilizados, alterar o valor para 30% faz com que as réplicas ser criadas quando ocorre a 30% de utilização.
    
    Se o serviço web já está a utilizar as réplicas máximas atuais e ainda estiver a ver os códigos de estado 503, aumentar a `autoscale_max_replicas` valor para aumentar o número máximo de réplicas.

* Altere o número mínimo de réplicas. Aumentar as réplicas mínimas fornece um conjunto maior de lidar com os picos de entrada.

    Para aumentar o número mínimo de réplicas, defina `autoscale_min_replicas` para um valor mais alto. Pode calcular as réplicas necessárias com o código seguinte, substituindo os valores com valores específicos ao seu projeto:

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
    > Se receber picos de pedidos maiores do que podem manipular as réplicas mínimo novo, poderá receber 503 s ao novamente. Por exemplo, como o tráfego para os aumentos de serviço, poderá ter de aumentar as réplicas mínimo.

Para obter mais informações sobre a definição `autoscale_target_utilization`, `autoscale_max_replicas`, e `autoscale_min_replicas` , consulte o [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) referência do módulo.


## <a name="advanced-debugging"></a>Depuração avançada

Em alguns casos, poderá ter de forma interativa depurar o código de Python contido na sua implementação do modelo. Por exemplo, se o script de entrada está a falhar e o motivo pelo qual não pode ser determinado pelo registo adicional. Ao utilizar o Visual Studio Code e as ferramentas Python para Visual Studio (PTVSD), pode anexar o código em execução no interior do contentor de Docker.

> [!IMPORTANT]
> Este método de depuração não funciona quando utilizar `Model.deploy()` e `LocalWebservice.deploy_configuration` para implementar um modelo localmente. Em vez disso, tem de criar uma imagem com o [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) classe. 
>
> Implementações de serviços da local web exigem um instalação do Docker no seu sistema local de trabalho. Tem de executar o docker antes de implementar um serviço da local web. Para obter informações sobre como instalar e utilizar o Docker, consulte [ https://www.docker.com/ ](https://www.docker.com/).

### <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Para instalar as ferramentas Python para Visual Studio (PTVSD) no seu ambiente de desenvolvimento local do VS Code, utilize o seguinte comando:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Para obter mais informações sobre como utilizar PTVSD com o VS Code, consulte [depuração remota](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Para configurar o VS Code para comunicar com a imagem do Docker, crie uma nova configuração de depuração:

    1. A partir do VS Code, selecione o __depurar__ menu e, em seguida, selecione __abrir configurações__. Um ficheiro denominado __Launch__ abre.

    1. Na __Launch__ ficheiro, encontre a linha que contém `"configurations": [`e inserir o seguinte texto depois dela:

        ```json
        {
            "name": "Azure Machine Learning service: Docker Debug",
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
        > Se já existirem outras entradas na seção configurações, adicione uma vírgula (,) depois do código que inseriu.

        Esta secção anexa o contentor de Docker através da porta 5678.

    1. Guardar a __Launch__ ficheiro.

### <a name="create-an-image-that-includes-ptvsd"></a>Criar uma imagem que inclua PTVSD

1. Modificar o ambiente de conda para a sua implementação para que ele inclua PTVSD. O exemplo seguinte demonstra a adicioná-lo usando o `pip_packages` parâmetro:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 
    
    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.17', 'ptvsd'])
    
    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Para começar a PTVSD e espera por uma ligação quando inicia o serviço, adicione o seguinte na parte superior do seu `score.py` ficheiro:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Durante a depuração, pode querer fazer alterações aos ficheiros na imagem sem ter de recriá-lo. Para instalar um editor de texto (vim) na imagem do Docker, crie um novo ficheiro de texto com o nome `Dockerfile.steps` e utilize o seguinte como conteúdo do ficheiro:

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    Um editor de texto permite-lhe modificar os arquivos da imagem do docker para testar as alterações sem criar uma nova imagem.

1. Criar uma imagem que utiliza a `Dockerfile.steps` de ficheiros, utilize o `docker_file` parâmetro ao criar uma imagem. O exemplo seguinte demonstra como fazer isso:

    > [!NOTE]
    > Este exemplo assume que `ws` aponta para a área de trabalho do Azure Machine Learning e que `model` é o modelo que está sendo implantado. O `myenv.yml` ficheiro contém as dependências de conda criadas no passo 1.

    ```python
    from azureml.core.image import Image, ContainerImage
    image_config = ContainerImage.image_configuration(runtime= "python",
                                 execution_script="score.py",
                                 conda_file="myenv.yml",
                                 docker_file="Dockerfile.steps")

    image = Image.create(name = "myimage",
                     models = [model],
                     image_config = image_config, 
                     workspace = ws)
    # Print the location of the image in the repository
    print(image.image_location)
    ```

Quando a imagem tiver sido criada, é apresentada a localização de imagem no registo. A localização é semelhante ao seguinte texto:

```text
myregistry.azurecr.io/myimage:1
```

Neste exemplo de texto, é o nome do registo `myregistry` e a imagem com o nome `myimage`. A versão da imagem é `1`.

### <a name="download-the-image"></a>Transferir a imagem

1. Abra uma linha de comandos, terminal ou outro shell e utilize o seguinte procedimento [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) comando para autenticar para a subscrição do Azure que contém a sua área de trabalho do Azure Machine Learning:

    ```azurecli
    az login
    ```

1. Para autenticar para o Azure Container Registry (ACR) que contém a imagem, utilize o seguinte comando. Substitua `myregistry` com um devolvido quando registou a imagem:

    ```azurecli
    az acr login --name myregistry
    ```

1. Para transferir a imagem para o Docker local, utilize o seguinte comando. Substitua `myimagepath` com a localização devolvida quando registou a imagem:

    ```bash
    docker pull myimagepath
    ```

    O caminho da imagem deve ser semelhante a `myregistry.azurecr.io/myimage:1`. Em que `myregistry` é o seu registo `myimage` é a sua imagem, e `1` é a versão da imagem.

    > [!TIP]
    > A autenticação do passo anterior não são eternos. Se esperar muito tempo suficiente entre o comando de autenticação e o comando de solicitação, receberá uma falha de autenticação. Se isto acontecer, autenticar.

    O tempo necessário para concluir a transferência, depende da velocidade da sua ligação à internet. Um Estado de transferência é apresentado durante o processo. Quando o download for concluído, pode usar o `docker images` comando para verificar que tenha transferido.

1. Para tornar mais fácil trabalhar com a imagem, utilize o seguinte comando para adicionar uma etiqueta. Substitua `myimagepath` com o valor de localização do passo 2.

    ```bash
    docker tag myimagepath debug:1
    ```

    Para o resto dos passos, pode ver a imagem local como `debug:1` em vez do valor de caminho completos da imagem.

### <a name="debug-the-service"></a>Depurar o serviço

> [!TIP]
> Se definir um tempo limite para a ligação de PTVSD no `score.py` ficheiro, tem de ligar VS Code para a sessão de depuração antes do tempo limite expira. Inicie o VS Code, abra a cópia local da `score.py`, defina um ponto de interrupção e deixá-lo pronto para ir antes de utilizar os passos nesta secção.
>
> Para obter mais informações sobre a depuração e definir pontos de interrupção, consulte [Debugging](https://code.visualstudio.com/Docs/editor/debugging).

1. Para iniciar um contentor de Docker com a imagem, utilize o seguinte comando:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Para anexar o VS Code para PTVSD dentro do contentor, abra o VS Code e utilize o F5 chave ou selecione __depurar__. Quando lhe for pedido, selecione o __serviço Azure Machine Learning: Depuração de docker__ configuração. Também pode selecionar o ícone de depuração da barra lateral, o __serviço Azure Machine Learning: Depuração de docker__ entrada no menu de lista pendente de depuração e, em seguida, utilize a seta verde para anexar o depurador.

    ![O ícone de depuração, o botão de depuração de início e o Seletor de configuração](media/how-to-troubleshoot-deployment/start-debugging.png)

Neste momento, o VS Code liga a PTVSD dentro do contentor de Docker e termina no ponto de interrupção que definiu anteriormente. Pode agora percorrer o código executado, ver variáveis, etc.

Para obter mais informações sobre como utilizar o VS Code para depurar o Python, veja [depurar seu código de Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Modificar os ficheiros de contentor

Para fazer alterações aos ficheiros na imagem, pode anexar ao contentor em execução e executar um shell de bash. A partir daí, pode usar vim para editar ficheiros:

1. Para ligar para o contentor em execução e iniciar um shell de bash no contentor, utilize o seguinte comando:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Para localizar os ficheiros utilizados pelo serviço, utilize o seguinte comando a partir da shell de bash no contentor:

    ```bash
    cd /var/azureml-app
    ```

    A partir daqui, pode utilizar vim para editar o `score.py` ficheiro. Para obter mais informações sobre como utilizar vim, consulte [usando o editor de Vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Normalmente, as alterações a um contentor não são mantidas. Para guardar quaisquer alterações efetuadas, utilize o seguinte comando, antes de sair da shell iniciado no passo acima (ou seja, na shell de outro):

    ```bash
    docker commit debug debug:2
    ```

    Este comando cria uma nova imagem com o nome `debug:2` que contém as edições.

    > [!TIP]
    > Terá de parar o contentor atual e começar a utilizar a nova versão, antes das alterações entrem em vigor.

1. Certifique-se manter as alterações que fizer aos ficheiros no contentor em sincronia com os ficheiros locais que utiliza o VS Code. Caso contrário, a experiência de depurador não irá funcionar conforme esperado.

### <a name="stop-the-container"></a>Parar o contentor

Para parar o contentor, utilize o seguinte comando:

```bash
docker stop debug
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a implementação:

* [Como implementar e, em que](how-to-deploy-and-where.md)
* [Tutorial: Preparar e implementar modelos](tutorial-train-models-with-aml.md)
