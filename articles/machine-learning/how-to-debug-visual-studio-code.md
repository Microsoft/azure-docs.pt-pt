---
title: Depuragem interativa com código de estúdio visual
titleSuffix: Azure Machine Learning
description: Código de aprendizagem automática Azure, oleodutos e implementações de depuração interativamente depuração
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
author: luisquintanilla
ms.author: luquinta
ms.date: 08/06/2020
ms.openlocfilehash: 73cb8396876a5baad74190ec9a86237362037c36
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87908905"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Depuragem interativa com código de estúdio visual

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a depurar interativamente os oleodutos e implementações de Machine Learning Azure usando o Código do Estúdio Visual (Código VS) e [o depugpy](https://github.com/microsoft/debugpy/).

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Depurar e resolver problemas de pipelines de machine learning

Em alguns casos, poderá ser necessário depurar interativamente o código Python utilizado no seu gasoduto ML. Ao utilizar o Código VS e o depuribus, pode anexar-se ao código à medida que funciona no ambiente de treino.

### <a name="prerequisites"></a>Pré-requisitos

* Um __espaço de trabalho de aprendizagem automática Azure__ que está configurado para utilizar uma Rede Virtual __Azure.__
* Um __oleoduto Azure Machine Learning__ que usa scripts Python como parte dos passos do oleoduto. Por exemplo, um PythonScriptStep.
* Um cluster Azure Machine Learning Compute, que está __na rede virtual__ e é utilizado pelo pipeline para __treino.__
* Um __ambiente de desenvolvimento__ que está na rede __virtual.__ O ambiente de desenvolvimento pode ser um dos seguintes:

  * Uma máquina virtual Azure na rede virtual
  * Uma instância computacional de VM de caderno na rede virtual
  * Uma máquina cliente que tem conectividade de rede privada com a rede virtual, seja por VPN ou via ExpressRoute.

Para obter mais informações sobre a utilização de uma Rede Virtual Azure com Azure Machine Learning, consulte [trabalhos de experimentação e inferência Secure Azure ML dentro de uma Rede Virtual Azure](how-to-enable-virtual-network.md).

> [!TIP]
> Embora possa trabalhar com recursos de Aprendizagem automática Azure que não estão por trás de uma rede virtual, é recomendado usar uma rede virtual.

### <a name="how-it-works"></a>Como funciona

Os seus passos de gasoduto ML executam scripts Python. Estes scripts são modificados para executar as seguintes ações:

1. Faça o registo do endereço IP do anfitrião em que estão a funcionar. Utiliza o endereço IP para ligar o depurador ao script.

2. Inicie o componente de depurg depura e aguarde que um depurrão se conecte.

3. A partir do seu ambiente de desenvolvimento, monitoriza os registos criados pelo processo de treino para encontrar o endereço IP onde o script está a decorrer.

4. Diga ao CÓDIGO VS o endereço IP para ligar o depurador através de um `launch.json` ficheiro.

5. Prende-se o depurador e interativamente passa-se pelo guião.

### <a name="configure-python-scripts"></a>Configurar scripts Python

Para permitir a depuragem, faça as seguintes alterações ao(s) script(s) Python utilizado por passos no seu pipeline ML:

1. Adicione as seguintes declarações de importação:

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Adicione os seguintes argumentos. Estes argumentos permitem-lhe ativar o depurante conforme necessário, e definir o tempo limite para anexar o depurante:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. Adicione as seguintes declarações. Estas declarações carregam o contexto de execução atual para que possa registar o endereço IP do nó em que o código está em execução:

    ```python
    global run
    run = Run.get_context()
    ```

1. Adicione uma `if` declaração que começa a depurar e espera que um depurrão se prenda. Se nenhum depurar se ligar antes do intervalo, o script continua normal. Certifique-se de que substitui os `HOST` valores e `PORT` é a `listen` função por si próprio.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

O exemplo python a seguir mostra um ficheiro básico `train.py` que permite depurar:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Configure o gasoduto ML

Para fornecer os pacotes Python necessários para iniciar a depuria e obter o contexto de execução, criar um ambiente e definir `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` . Altere a versão SDK para corresponder à que está a utilizar. O seguinte corte de código demonstra como criar um ambiente:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

Na secção [de scripts Configure Python,](#configure-python-scripts) foram adicionados novos argumentos aos scripts utilizados pelos seus passos de gasoduto ML. O seguinte corte de código demonstra como utilizar estes argumentos para permitir a depuração para o componente e definir um tempo limite. Demonstra também como utilizar o ambiente criado anteriormente pela `runconfig=run_config` definição:

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
                         compute_target=aml_compute,
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

Quando o gasoduto funciona, cada passo cria uma criança. Se a depuragem estiver ativada, o script modificado regista informações semelhantes ao seguinte texto na `70_driver_log.txt` execução da criança:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Poupe o `ip_address` valor. É usado na secção seguinte.

> [!TIP]
> Também pode encontrar o endereço IP a partir dos registos de execução para a criança correr para este passo de pipeline. Para obter mais informações sobre a visualização desta informação, consulte [as métricas e métricas da experiência Monitor Azure ML](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Para instalar depuração no seu ambiente de desenvolvimento do Código VS, utilize o seguinte comando:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Para obter mais informações sobre a utilização de depurado com código VS, consulte [Depuragem Remota](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Para configurar o Código VS para comunicar com o computamento Azure Machine Learning que está a executar o depurador, crie uma nova configuração de depuração:

    1. A partir do Código VS, selecione o menu __Debug__ e, em seguida, selecione __configurações Open__. Um ficheiro chamado __launch.jsem__ aberturas.

    1. No __launch.jsficheiro,__ encontre a linha que contém `"configurations": [` , e insira o seguinte texto após o mesmo. Altere a `"host": "<IP-ADDRESS>"` entrada no endereço IP devolvido nos seus registos a partir da secção anterior. Altere `"localRoot": "${workspaceFolder}/code/step"` a entrada para um diretório local que contenha uma cópia do guião que está a ser depurado:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Se já existirem outras entradas na secção de configurações, adicione uma vírgula (,) após o código que inseriu.

        > [!TIP]
        > A melhor prática, especialmente para os oleodutos, é manter os recursos para scripts em diretórios separados para que o código seja relevante apenas para cada uma das etapas. Neste exemplo, o `localRoot` exemplo refere-se. `/code/step1`
        >
        > Se estiver a depurar vários scripts, em diferentes diretórios, crie uma secção de configuração separada para cada script.

    1. Guarde o __launch.jsarquivado.__

### <a name="connect-the-debugger"></a>Ligue o depurar

1. Abra o Código VS e abra uma cópia local do script.
2. Desaponte os pontos de rutura onde pretende que o script pare depois de anexado.
3. Enquanto o processo da criança estiver a executar o script, e o `Timeout for debug connection` ser apresentado nos registos, utilize a tecla F5 ou selecione __Debug__. Quando solicitado, selecione o __Azure Machine Learning Compute: configuração de depuração remota.__ Também pode selecionar o ícone de depuração da barra lateral, o __Azure Machine Learning: entrada remota de depuração__ do menu de depuração Debug e, em seguida, usar a seta verde para fixar o depurador.

    Neste ponto, o Código VS liga-se à depuração no nó de computação e para no ponto de rutura que definiu anteriormente. Agora pode passar pelo código à medida que corre, ver variáveis, etc.

    > [!NOTE]
    > Se o registo apresentar uma entrada indicando `Debugger attached = False` , então o tempo limite expirou e o script continuou sem o depurar. Submeta novamente o gasoduto e ligue o depurar após a `Timeout for debug connection` mensagem e antes que o intervalo expire.

## <a name="debug-and-troubleshoot-deployments"></a>Depuração e resolução de problemas

Em alguns casos, poderá ser necessário depurar interativamente o código Python contido na sua implementação de modelo. Por exemplo, se o script de entrada estiver a falhar e a razão não puder ser determinada por registo adicional. Utilizando o Código VS e o depuratório, pode anexar-se ao código que está a funcionar dentro do contentor Docker.

> [!IMPORTANT]
> Este método de depuração não funciona quando se utiliza `Model.deploy()` e implanta um modelo `LocalWebservice.deploy_configuration` localmente. Em vez disso, deve criar uma imagem utilizando o método [Modelo.pacote().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)

As implementações de serviços web locais requerem uma instalação de Docker funcionando no seu sistema local. Para obter mais informações sobre a utilização do Docker, consulte a Documentação do [Docker.](https://docs.docker.com/) Note que ao trabalhar com casos de computação, Docker já está instalado.

### <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Para instalar o depurar no ambiente de desenvolvimento do Código VS local, utilize o seguinte comando:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Para obter mais informações sobre a utilização de depurado com código VS, consulte [Depuragem Remota](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Para configurar o Código VS para comunicar com a imagem do Docker, crie uma nova configuração de depuração:

    1. A partir do Código VS, selecione o menu __Debug__ e, em seguida, selecione __configurações Open__. Um ficheiro chamado __launch.jsem__ aberturas.

    1. No __launch.jsficheiro,__ encontre a linha que contém `"configurations": [` , e insira o seguinte texto após o mesmo:

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
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

    1. Guarde o __launch.jsarquivado.__

### <a name="create-an-image-that-includes-debugpy"></a>Criar uma imagem que inclua depurar

1. Modifique o ambiente conda para a sua implantação de modo a que inclua depuração. O exemplo a seguir demonstra a sua adição utilizando o `pip_packages` parâmetro:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Para começar a depurar e esperar por uma ligação quando o serviço começar, adicione o seguinte à parte superior do seu `score.py` ficheiro:

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Crie uma imagem baseada na definição do ambiente e puxe a imagem para o registo local. 

    > [!NOTE]
    > Este exemplo pressupõe que `ws` aponta para o seu espaço de trabalho Azure Machine Learning, e este é o modelo que está a ser `model` implementado. O `myenv.yml` ficheiro contém as dependências da conda criadas no passo 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
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
> Se definir um tempo limite para a ligação de depuração no `score.py` ficheiro, deve ligar o Código VS à sessão de depuração antes que o tempo limite expire. Iniciar o Código VS, abrir a cópia local de , definir um ponto de `score.py` rutura, e tê-lo pronto para ir antes de usar os passos nesta secção.
>
> Para obter mais informações sobre depuração e definição de pontos de rutura, consulte [Debugging](https://code.visualstudio.com/Docs/editor/debugging).

1. Para iniciar um recipiente Docker utilizando a imagem, utilize o seguinte comando:

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_path_to_score.py>:/var/azureml-apps/score.py debug:1 /bin/bash
    ```

    Isto liga o seu `score.py` local ao do recipiente. Portanto, quaisquer alterações efetuadas no editor são automaticamente refletidas no recipiente.

1. Dentro do recipiente, executar o seguinte comando na concha

    ```bash
    runsvdir /var/runit
    ```

1. Para anexar o Código VS à depuria dentro do recipiente, abra o Código VS e utilize a tecla F5 ou selecione __Debug__. Quando solicitado, selecione a Implementação de __Aprendizagem de Máquinas Azure: Configuração Docker Debug.__ Também pode selecionar o ícone de depuração da barra lateral, a Implementação da Aprendizagem da __Máquina Azure: Entrada Docker Debug__ a partir do menu de depuração de Debug e, em seguida, usar a seta verde para fixar o depurador.

    ![O ícone de depuração, botão de depuração e seletor de configuração](./media/how-to-troubleshoot-deployment/start-debugging.png)

Neste ponto, o Código VS liga-se à depuração dentro do recipiente Docker e para no ponto de rutura que definiu anteriormente. Agora pode passar pelo código à medida que corre, ver variáveis, etc.

Para obter mais informações sobre a utilização do Código VS para depurar python, consulte [Debug o seu código Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

### <a name="stop-the-container"></a>Parar o contentor

Para parar o recipiente, utilize o seguinte comando:

```bash
docker stop debug
```

## <a name="next-steps"></a>Passos seguintes

Agora que configuraste o Visual Studio Code Remote, podes usar uma instância de computação como computação remota do Código do Estúdio Visual para depurar interativamente o teu código. 

[Tutorial: Treine o seu primeiro modelo ML](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância computacional com um caderno integrado.
