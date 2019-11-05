---
title: Criar ambientes ML reutilizáveis
titleSuffix: Azure Machine Learning
description: Crie e gerencie ambientes para treinamento e implantação de modelo. Gerencie pacotes do Python e outras configurações para o ambiente.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: f733e29fc5fbce764fef9a713747d6793d2ebd43
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489318"
---
# <a name="create-and-manage-reusable-environments-for-training-and-deployment-with-azure-machine-learning"></a>Crie e gerencie ambientes reutilizáveis para treinamento e implantação com o Azure Machine Learning.
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, saiba como criar e gerenciar [ambientes](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) de Azure Machine Learning para que você possa acompanhar e reproduzir as dependências de software de seus projetos à medida que eles evoluem.

O gerenciamento de dependências de software é uma tarefa comum para os desenvolvedores. Você deseja ser capaz de garantir que as compilações sejam reproduzíveis sem muita configuração manual de software. Com soluções para desenvolvimento local, como PIP e Conda em mente, a classe de ambientes Azure Machine Learning fornece uma solução para desenvolvimento de nuvem local e distribuída.

Os exemplos neste artigo mostram como:

* Criar um ambiente e especificar dependências de pacote
* Recuperar e atualizar ambientes
* Use o ambiente para treinamento
* Usar ambiente para implantação de serviço Web

## <a name="what-are-environments"></a>O que são ambientes

Ambientes especificam os pacotes python, as variáveis de ambiente e as configurações de software em relação aos seus scripts de treinamento e pontuação, e tempos de execução (Python, Spark ou Docker). Eles são entidades gerenciadas e com controle de versão em seu espaço de trabalho de Azure Machine Learning que permitem fluxos de trabalho de aprendizado de máquina reproduzíveis, auditáveis e portáteis entre diferentes destinos de computação.

Você pode usar um objeto de ambiente em sua computação local para desenvolver seu script de treinamento, reutilizar o mesmo ambiente em Azure Machine Learning computação para treinamento de modelo em escala e até mesmo implantar seu modelo com o mesmo ambiente.

O seguinte ilustra que o mesmo objeto de ambiente pode ser usado em sua configuração de execução para treinamento e em sua inferência e configuração de implantação para implantações de serviço Web.

![Diagrama de ambiente no fluxo de trabalho do Machine Learning](./media/how-to-use-environments/ml-environment.png)

### <a name="types-of-environments"></a>Tipos de ambientes

Os ambientes podem ser amplamente divididos em três categorias: **organizadas**, **gerenciadas pelo usuário** e **gerenciadas pelo sistema**.

Os ambientes organizados são fornecidos por Azure Machine Learning e estão disponíveis em seu espaço de trabalho por padrão. Eles contêm coleções de pacotes e configurações do Python para ajudá-lo a começar estruturas de aprendizado de máquina diferentes. 

Para um ambiente gerenciado pelo usuário, você é responsável por configurar seu ambiente e instalar todos os pacotes de que seu script de treinamento precisa no destino de computação. O Conda não verificará seu ambiente nem instalará nada para você. 

Ambientes gerenciados pelo sistema são usados quando você deseja que o [Conda](https://conda.io/docs/) gerencie o ambiente do Python e as dependências de script para você. O serviço assume esse tipo de ambiente por padrão, devido à sua utilidade em destinos de computação remota que não são configuráveis manualmente.

## <a name="prerequisites"></a>Pré-requisitos

* O SDK do Azure Machine Learning para Python [instalado](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).


## <a name="create-an-environment"></a>Criar um ambiente

Há várias maneiras de criar um ambiente para seus experimentos.

### <a name="use-curated-environment"></a>Usar ambiente organizado

Você pode selecionar um dos ambientes organizados para começar. 

* O ambiente do __AzureML-mínimo__ contém um conjunto mínimo de pacotes para habilitar o acompanhamento de execução e o carregamento de ativos. Você pode usá-lo como um ponto de partida para seu próprio ambiente.

* O ambiente do __AzureML-tutorial__ contém pacotes comuns de ciência de dados, como o Scikit-learn, o pandas e o matplotlib, além de um conjunto maior de pacotes do AzureML-SDK.

Os ambientes organizados são apoiados por imagens do Docker em cache, reduzindo o custo de preparação da execução.

Use o método __Environment. Get__ para selecionar um dos ambientes organizados:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Você pode listar os ambientes organizados e seus pacotes usando o seguinte código:
```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Não inicie seu próprio nome de ambiente com o prefixo _AzureML_ . Ele é reservado para ambientes organizados.

### <a name="instantiate-an-environment-object"></a>Criar uma instância de um objeto de ambiente

Para criar um ambiente manualmente, importe a classe de ambiente do SDK e crie uma instância de um objeto de ambiente com o código a seguir.

```python
from azureml.core import Environment
Environment(name="myenv")
```

### <a name="conda-and-pip-specification-files"></a>Arquivos de especificação Conda e Pip

Você também pode criar um ambiente de uma especificação Conda ou um arquivo de requisitos Pip.
Use o método [from_conda_specification ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) ou [from_pip_requirements ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) e inclua o nome do ambiente e o caminho do arquivo do arquivo desejado no argumento do método.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

#From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="existing-conda-environment"></a>Ambiente Conda existente

Se você tiver um ambiente Conda existente no computador local, o serviço oferecerá uma solução para a criação de um objeto de ambiente a partir dele. Dessa forma, você pode reutilizar seu ambiente interativo local em execuções remotas.

O código a seguir cria um objeto de ambiente fora do ambiente Conda existente `mycondaenv` com o método [from_existing_conda_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) .

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="automatically-create-environments"></a>Criar ambientes automaticamente

Crie um ambiente automaticamente enviando uma execução de treinamento com o método Submit (). Quando você envia uma execução de treinamento, a criação do novo ambiente pode levar vários minutos, dependendo do tamanho das dependências necessárias. 

Se você não especificar um ambiente em sua configuração de execução antes de enviar a execução, um ambiente padrão será criado para você.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attaches training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Shows each step of run 
run.wait_for_completion(show_output=True)
```

Da mesma forma, se você usar um objeto [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) para treinamento, poderá enviar a instância do estimador diretamente como uma execução sem precisar especificar um ambiente. O objeto `Estimator` já encapsula o ambiente e o destino de computação.


## <a name="add-packages-to-an-environment"></a>Adicionar pacotes a um ambiente

Adicione pacotes a um ambiente com arquivos de Conda, Pip ou de roda privada. Especifique cada dependência do pacote usando a [classe CondaDependency](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)e adicione-a ao PythonSection do ambiente.

### <a name="conda-and-pip-packages"></a>Pacotes Conda e Pip

Se um pacote estiver disponível em um repositório de pacotes do Conda, é recomendável usar a instalação do Conda over Pip. O motivo é que os pacotes Conda normalmente são fornecidos com binários pré-criados que tornam a instalação mais confiável.

O exemplo a seguir adiciona `scikit-learn`, especificamente a versão 0.21.3 e `pillow` pacote ao ambiente, `myenv` com os métodos [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) e [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) , respectivamente.

```python
from azureml.core import Environment
from azureml.core.environment import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>Arquivos de roda particulares

Você pode usar arquivos de roda Pip privados primeiro carregando-os para o armazenamento do espaço de trabalho usando o método estático [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) e, em seguida, capturando a URL de armazenamento e passando a URL para o método `add_pip_package()`

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Gerir ambientes

Gerencie ambientes para que você possa atualizá-los, rastreá-los e reutilizá-los em destinos de computação e com outros usuários do espaço de trabalho.

### <a name="register-environments"></a>Registrar ambientes

O ambiente é registrado automaticamente com seu espaço de trabalho quando você envia uma execução ou implanta um serviço Web. Você também pode registrar manualmente o ambiente usando o método [Register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) . Essa operação torna o ambiente em uma entidade controlada e com controle de versão na nuvem e pode ser compartilhado entre os usuários do espaço de trabalho.

O código a seguir registra o ambiente, `myenv`, no espaço de trabalho, `ws`.

```python
myenv.register(workspace=ws)
```

Quando usado pela primeira vez, em treinamento ou implantação, o ambiente é registrado com o espaço de trabalho, criado e implantado no destino de computação. Os ambientes são armazenados em cache pelo serviço. Reutilizar um ambiente armazenado em cache leva muito menos tempo do que usar um novo serviço ou um que tenha sido atualizado.

### <a name="get-existing-environments"></a>Obter ambientes existentes

A classe Environment oferece métodos que permitem que você recupere ambientes existentes em seu espaço de trabalho por nome, como uma lista ou por treinamentos específicos, executados para fins de solução de problemas ou auditoria, bem como reprodução.

#### <a name="view-list-of-environments"></a>Exibir lista de ambientes

Exiba os ambientes em seu espaço de trabalho com [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-)e selecione um para reutilizar.

#### <a name="get-environment-by-name"></a>Obter ambiente por nome

Você também pode obter um ambiente específico por nome e versão.
O código a seguir usa o método [Get ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) para recuperar a versão `1` do ambiente `myenv` no espaço de trabalho `ws`.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="training-run-specific-environment"></a>Ambiente específico de execução de treinamento

Para obter o ambiente usado para uma execução específica após a conclusão do treinamento, use o método [get_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) na classe Run.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Atualizar um ambiente existente

Se você fizer alterações em um ambiente existente, como adicionar um pacote do Python, uma nova versão de um ambiente será criada quando você enviar executar, implantar modelo ou registrar manualmente o ambiente. O controle de versão permite que você exiba alterações no ambiente ao longo do tempo.

Para atualizar uma versão de pacote Python de um ambiente existente, especifique o número de versão exato para esse pacote. Caso contrário, o Azure Machine Learning reutilizará o ambiente existente com versões de pacote de quando o ambiente foi criado.

### <a name="debug-the-image-build"></a>Depurar a compilação da imagem

Este exemplo usa o método [Build ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) para criar um ambiente manualmente como uma imagem do Docker e monitora os logs de saída da compilação da imagem usando [wait_for_completion ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-). A imagem interna é exibida sob o registro de contêiner do espaço de trabalho do Azure, que é útil para depuração.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="docker-and-environments"></a>Docker e ambientes

 O [DockerSection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) da classe `Environments` Azure Machine Learning, permite que você personalize e controle em detalhes o sistema operacional convidado no qual sua execução de treinamento é executada.

Quando você `enable` Docker, o serviço cria uma imagem do Docker e cria um ambiente do Python com suas especificações dentro desse contêiner do Docker. Isso fornece isolamento e reprodução adicionais para suas execuções de treinamento.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Uma vez criado, a imagem do Docker aparece no registro de contêiner do Azure associado ao espaço de trabalho, por padrão.  O nome do repositório tem o formato *azureml/azureml_\<uuid\>* . A parte do identificador exclusivo (*UUID*) corresponde a um hash computado da configuração do ambiente. Isso permite que o serviço determine se uma imagem correspondente ao ambiente fornecido já existe para reutilização.

Além disso, o serviço usa automaticamente uma das [imagens base](https://github.com/Azure/AzureML-Containers)baseadas em Ubuntu Linux e instala os pacotes do Python especificados. A imagem base tem versões de CPU e GPU. Azure Machine Learning detecta automaticamente qual versão usar.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

> [!NOTE]
> Se você especificar `environment.python.user_managed_dependencies=False` ao usar uma imagem personalizada do Docker, o serviço criará um ambiente Conda dentro da imagem e executará a execução nesse ambiente, em vez de usar bibliotecas Python que você pode ter instalado na imagem base. Defina o parâmetro como `True` para usar seus próprios pacotes instalados.

## <a name="using-environments-for-training"></a>Usando ambientes para treinamento

Para enviar uma execução de treinamento, você precisa combinar seu ambiente, o [destino de computação](concept-compute-target.md) e o script Python de treinamento em uma configuração de execução; um objeto wrapper usado para enviar execuções.

Quando você envia uma execução de treinamento, a criação de um novo ambiente pode levar vários minutos, dependendo do tamanho das dependências necessárias. Os ambientes são armazenados em cache pelo serviço, portanto, desde que a definição de ambiente permaneça inalterada, o tempo de configuração completo é incorrido apenas uma vez.

O exemplo de execução de script local a seguir mostra onde você usaria [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) como seu objeto wrapper.

```python
from azureml.core import Environment, ScriptRunConfig, Experiment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> Para desabilitar o histórico de execução ou executar instantâneos, use a configuração em `ScriptRunConfig.run_config.history`.

Se você não especificar o ambiente em sua configuração de execução, o serviço criará um ambiente padrão para você quando você enviar sua execução.

### <a name="train-with-an-estimator"></a>Treinar com um estimador

Se você estiver usando um [estimador](how-to-train-ml-models.md) para treinamento, poderá simplesmente enviar a instância do estimador diretamente, pois ela já encapsula o ambiente e o destino de computação.

O código a seguir usa um estimador para um treinamento de nó único executado em uma computação remota para um modelo scikit-Learn e assume um objeto de destino de computação criado anteriormente, `compute_target` e objeto datastore, `ds`.

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="using-environments-for-web-service-deployment"></a>Usando ambientes para implantação de serviço Web

Você pode usar ambientes ao implantar seu modelo como um serviço Web. Isso permite um fluxo de trabalho reproduzível e conectado, no qual você pode treinar, testar e implantar seu modelo usando exatamente as mesmas bibliotecas em suas computações de treinamento e inferência.

Para implantar um serviço Web, combine o ambiente, a computação de inferência, o script de Pontuação e o modelo registrado em seu objeto de implantação, [implante ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-). Saiba mais sobre a [implantação de serviços Web](how-to-deploy-and-where.md).

Neste exemplo, suponha que você concluiu uma execução de treinamento e deseja implantar esse modelo em uma instância de contêiner do Azure (ACI). Ao criar o serviço Web, os arquivos de modelo e de pontuação são montados na imagem e a pilha de inferência de Azure Machine Learning é adicionada à imagem.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Blocos de anotações de exemplo

Este [bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) expande sobre conceitos e métodos demonstrados neste artigo.

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: treinar um modelo](tutorial-train-models-with-aml.md) usa um destino de computação gerenciado para treinar um modelo.
* Depois de ter um modelo treinado, saiba [como e onde implantar modelos](how-to-deploy-and-where.md).
* Exiba a referência de SDK da [classe de ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) .
