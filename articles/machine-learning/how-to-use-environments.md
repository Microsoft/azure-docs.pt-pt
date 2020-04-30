---
title: Criar ambientes ML reutilizáveis
titleSuffix: Azure Machine Learning
description: Criar e gerir ambientes para formação e implantação de modelos. Gerencie os pacotes Python e outras configurações para o ambiente.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: dc30318027962247f7504b734385d7642550ec87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536357"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Reutilizar ambientes para formação e implantação utilizando o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprenda a criar e gerir [ambientes](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)de Aprendizagem automática Azure. Use os ambientes para rastrear e reproduzir as dependências de software dos seus projetos à medida que evoluem.

A gestão da dependência de software é uma tarefa comum para os desenvolvedores. Deve garantir que as construções são reprodutíveis sem uma configuração extensiva do software manual. A classe Azure Machine Learning `Environment` é responsável por soluções de desenvolvimento local como pip e Conda, e fornece uma solução para o desenvolvimento da nuvem local e distribuída.

Os exemplos deste artigo mostram como:

* Crie um ambiente e especifique as dependências dos pacotes.
* Recuperar e atualizar ambientes.
* Use um ambiente para treinar.
* Utilize um ambiente para a implementação do serviço web.

Para uma visão geral de alto nível de como os ambientes funcionam em Azure Machine Learning, veja [o que são os ambientes ML?](concept-environments.md)

## <a name="prerequisites"></a>Pré-requisitos

* O [SDK de Aprendizagem automática Azure para Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* Um [espaço de trabalho azure machine learning](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Criar um ambiente

As seguintes secções exploram as múltiplas formas de criar um ambiente para as suas experiências.

### <a name="use-a-curated-environment"></a>Use um ambiente curado

Pode selecionar um dos ambientes com curadoria para começar: 

* O ambiente _AzureML-Minimal_ contém um conjunto mínimo de pacotes para permitir o rastreio de execução e o upload de ativos. Pode usá-lo como ponto de partida para o seu próprio ambiente.

* O ambiente _AzureML-Tutorial_ contém pacotes comuns de ciência de dados. Estes pacotes incluem Scikit-Learn, Pandas, Matplotlib, e um conjunto maior de pacotes azureml-sdk.

Ambientes com curadoria são apoiados por imagens do Docker em cache. Este apoio reduz o custo de preparação da corrida.

Utilize `Environment.get` o método para selecionar um dos ambientes com curadoria:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Pode listar os ambientes com curadoria e os seus pacotes utilizando o seguinte código:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Não inicie o seu próprio nome de ambiente com o prefixo _AzureML._ Este prefixo é reservado para ambientes com curadoria.

### <a name="instantiate-an-environment-object"></a>Instantaneamente um objeto ambiental

Para criar manualmente um `Environment` ambiente, importe a classe a partir do SDK. Em seguida, utilize o seguinte código para instantaneamente um objeto ambiental.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Utilize ficheiros de especificações de Condomínio e pip

Também pode criar um ambiente a partir de uma especificação Conda ou de um ficheiro de requisitos de pip. Utilize [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) o método [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) ou o método. No argumento do método, inclua o seu nome de ambiente e o caminho de arquivo do ficheiro que deseja.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Utilize os ambientes conda existentes

Se tiver um ambiente Conda existente no seu computador local, então pode usar o serviço para criar um objeto ambiental. Ao utilizar esta estratégia, pode reutilizar o seu ambiente interativo local em execuções remotas.

O seguinte código cria um objeto ambiental `mycondaenv`a partir do ambiente conda existente. Usa o [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) método.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Criar ambientes automaticamente

Criar automaticamente um ambiente através da apresentação de um treino. Submeta a execução utilizando o `submit()` método. Quando se submete a um treino, a construção do novo ambiente pode demorar vários minutos. A duração da construção depende do tamanho das dependências necessárias. 

Se não especificar um ambiente na configuração do seu executante antes de submeter a execução, então um ambiente predefinido é criado para si.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

Da mesma forma, [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) se utilizar um objeto para treino, pode submeter diretamente a instância do estimador como uma corrida sem especificar um ambiente. O `Estimator` objeto já encapsula o ambiente e o alvo da computação.

## <a name="add-packages-to-an-environment"></a>Adicione pacotes a um ambiente

Adicione pacotes a um ambiente utilizando ficheiros de rodas Conda, pip ou privadas. Especifique cada dependência [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) do pacote utilizando a classe. Adicione-o ao `PythonSection`ambiente.

### <a name="conda-and-pip-packages"></a>Pacotes de condomínio e pip

Se um pacote estiver disponível num repositório de pacotes Conda, recomendamos que utilize a instalação Conda em vez da instalação do pip. Os pacotes de condomínio saem normalmente com binários pré-construídos que tornam a instalação mais fiável.

O exemplo seguinte acrescenta ao ambiente. Adiciona versão 1.17.0 `numpy`de . Também adiciona `pillow` o `myenv`pacote, . O exemplo [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) utiliza o [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) método e o método, respectivamente.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

>[!IMPORTANT]
> Se utilizar a mesma definição de ambiente para outra execução, o serviço de Aprendizagem automática Azure reutiliza a imagem em cache do seu ambiente. Se criar um ambiente com uma dependência de ```numpy```pacote sem ênfase, por exemplo, esse ambiente continuará a utilizar a versão do pacote instalada _no momento da criação do ambiente._ Além disso, qualquer ambiente futuro com definição correspondente continuará a usar a versão antiga. Para mais informações, consulte [ambiente de construção, cache e reutilização.](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)

### <a name="private-wheel-files"></a>Arquivos de rodas privadas

Pode utilizar ficheiros privados de rodas de pip enviando-os primeiro para o seu armazenamento no espaço de trabalho. Carrega-as utilizando um [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) método estático. Em seguida, captura o URL de `add_pip_package()` armazenamento e passa o URL para o método.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Gerir ambientes

Gerencie ambientes para que possa atualizar, rastrear e reutilizá-los através de alvos de computação e com outros utilizadores do espaço de trabalho.

### <a name="register-environments"></a>Registar ambientes

O ambiente é registado automaticamente com o seu espaço de trabalho quando submete uma execução ou implementa um serviço web. Também pode registar manualmente o [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) ambiente utilizando o método. Esta operação transforma o ambiente numa entidade que é rastreada e versonizada na nuvem. A entidade pode ser partilhada entre utilizadores do espaço de trabalho.

O seguinte código `myenv` regista o `ws` ambiente no espaço de trabalho.

```python
myenv.register(workspace=ws)
```

Quando se utiliza o ambiente pela primeira vez em treino ou implantação, está registado no espaço de trabalho. Depois é construído e implantado no alvo da computação. O serviço caches os ambientes. A reutilização de um ambiente em cache leva muito menos tempo do que a utilização de um novo serviço ou um que tenha sido atualizado.

### <a name="get-existing-environments"></a>Obtenha ambientes existentes

A `Environment` classe oferece métodos que lhe permitem recuperar ambientes existentes no seu espaço de trabalho. Você pode recuperar ambientes pelo nome, como uma lista, ou por um treino específico. Esta informação é útil para a resolução de problemas, auditoria e reprodutibilidade.

#### <a name="view-a-list-of-environments"></a>Ver uma lista de ambientes

Veja os ambientes no seu [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) espaço de trabalho utilizando a classe. Em seguida, selecione um ambiente para reutilizar.

#### <a name="get-an-environment-by-name"></a>Obter um ambiente pelo nome

Você também pode obter um ambiente específico por nome e versão. O seguinte código [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) utiliza o `1` método `myenv` para `ws` recuperar a versão do ambiente no espaço de trabalho.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Treine um ambiente específico de corrida

Para obter o ambiente que foi usado para uma corrida [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) específica `Run` após o acabamento do treino, use o método na aula.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Atualizar um ambiente existente

Digamos que se muda um ambiente existente, por exemplo, adicionando um pacote Python. Uma nova versão do ambiente é então criada quando submete uma execução, implementa um modelo ou regista manualmente o ambiente. A versão permite-lhe visualizar as mudanças do ambiente ao longo do tempo.

Para atualizar uma versão pacote Python num ambiente existente, especifique o número da versão para esse pacote. Se não utilizar o número exato da versão, então o Azure Machine Learning reutilizará o ambiente existente com as suas versões de pacote original.

### <a name="debug-the-image-build"></a>Desinem a construção da imagem

O exemplo que [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) se segue utiliza o método para criar manualmente um ambiente como imagem do Docker. Monitoriza os registos de saída a [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)partir da construção da imagem utilizando . A imagem construída aparece então na instância de registo de contentores azure do espaço de trabalho. Esta informação é útil para depurar.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Ativar Docker

 A [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) classe Azure `Environment` Machine Learning permite-lhe personalizar e controlar finamente o sistema operativo de hóspedes no qual executa o seu treino.

Quando ativas o Docker, o serviço constrói uma imagem do Docker. Também cria um ambiente Python que usa as suas especificações dentro do recipiente Docker. Esta funcionalidade proporciona isolamento e reprodutibilidade adicionais para os seus treinos.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Por padrão, a imagem recém-construída do Docker aparece no registo de contentores que está associado ao espaço de trabalho.  O nome do repositório tem a forma *azureml/azureml_\<uuid\>*. A parte única do identificador *(uuide)* do nome corresponde a um hash que é calculado a partir da configuração do ambiente. Esta correspondência permite ao serviço determinar se já existe uma imagem para o ambiente dado para reutilização.

Além disso, o serviço utiliza automaticamente uma das [imagens base](https://github.com/Azure/AzureML-Containers)baseadas em Ubuntu Linux . Instala os pacotes python especificados. A imagem base tem versões CPU e versões GPU. O Azure Machine Learning deteta automaticamente qual a versão a utilizar.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

Também pode especificar um Dockerfile personalizado. É mais simples começar a partir de uma das ```FROM``` imagens da base de Machine Learning Azure usando o comando do Docker, e depois adicionar os seus próprios passos personalizados. Utilize esta abordagem se precisar de instalar pacotes não Python como dependências.

```python
# Specify docker steps as a string. Alternatively, load the string from a file.
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
```

### <a name="use-user-managed-dependencies"></a>Utilize dependências geridas pelo utilizador

Em algumas situações, a sua imagem de base personalizada pode já conter um ambiente Python com pacotes que pretende utilizar.

Por padrão, o serviço de Machine Learning Azure construirá um ambiente Conda com dependências especificadas, e executará a execução nesse ambiente em vez de usar quaisquer bibliotecas Python que tenha instalado na imagem base. 

Para utilizar as suas próprias embalagens `Environment.python.user_managed_dependencies = True`instaladas, defina o parâmetro . Certifique-se de que a imagem base contém um intérprete Python, e tem os pacotes que o seu script de treino necessita.

Por exemplo, para funcionar num ambiente de Base Miniconda que tenha o pacote NumPy instalado, primeiro especifique um Dockerfile com um passo para instalar a embalagem. Em seguida, definir as dependências geridas pelo utilizador para `True`. 

Também pode especificar um caminho para um intérprete python `Environment.python.interpreter_path` específico dentro da imagem, definindo a variável.

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

## <a name="use-environments-for-training"></a>Utilizar ambientes para a formação

Para submeter um treino, precisa de combinar o seu ambiente, o seu alvo de [computação,](concept-compute-target.md)e o seu roteiro python de treino numa configuração de execução. Esta configuração é um objeto de invólucro que é usado para submeter execuções.

Quando se submete a um treino, a construção de um novo ambiente pode demorar vários minutos. A duração depende do tamanho das dependências necessárias. Os ambientes são cached pelo serviço. Assim, enquanto a definição de ambiente permanecer inalterada, incorre em todo o tempo de configuração apenas uma vez.

O exemplo de execução de [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) script local mostra onde você usaria como objeto de invólucro.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

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
> Para desativar o histórico de execução ou executar instantâneos, utilize a definição abaixo `ScriptRunConfig.run_config.history`.

Se não especificar o ambiente na sua configuração de execução, então o serviço cria um ambiente predefinido quando submete a sua execução.

### <a name="use-an-estimator-for-training"></a>Use um estimador para treinar

Se utilizar um [estimador](how-to-train-ml-models.md) para treino, então pode submeter a instância do estimador diretamente. Já encapsula o ambiente e o alvo da computação.

O código seguinte utiliza um estimador para uma execução de treino de um nó único. Funciona com uma computação `scikit-learn` remota para um modelo. Assume que criou anteriormente um objecto-alvo `compute_target`de cálculo, e `ds`um objeto de datastore, .

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

## <a name="use-environments-for-web-service-deployment"></a>Utilizar ambientes para implementação de serviços web

Pode utilizar ambientes quando implementa o seu modelo como serviço web. Esta capacidade permite um fluxo de trabalho reprodutível e conectado. Neste fluxo de trabalho, pode treinar, testar e implementar o seu modelo utilizando as mesmas bibliotecas tanto no seu computacional de treino como no seu cálculo de inferência.

Para implementar um serviço web, combine o ambiente, a computação de inferência, [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)o script de pontuação e o modelo registado no seu objeto de implementação, . Para mais informações, consulte [Como e onde implementar modelos.](how-to-deploy-and-where.md)

Neste exemplo, suponha que completou um treino. Agora quer implantar esse modelo para as instâncias de contentores Azure. Quando se constrói o serviço web, o modelo e os ficheiros de pontuação são montados na imagem, e a pilha de inferência de Machine Learning Azure é adicionada à imagem.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Exemplo de cadernos

Este [caderno de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) expande-se sobre conceitos e métodos demonstrados neste artigo.

[Implementar um modelo usando uma imagem de base personalizada do Docker](how-to-deploy-custom-docker-image.md) demonstra como implementar um modelo usando uma imagem de base personalizada do Docker.

Este [caderno de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) demonstra como implementar um modelo Spark como um serviço web.

## <a name="create-and-manage-environments-with-the-cli"></a>Criar e gerir ambientes com o CLI

O [AZURE Machine Learning CLI](reference-azure-machine-learning-cli.md) espelha a maior parte da funcionalidade do Python SDK. Pode usá-lo para criar e gerir ambientes. Os comandos que discutimos nesta secção demonstram a funcionalidade básica.

Os seguintes andaimes de comando os ficheiros para uma definição de ambiente predefinido no diretório especificado. Estes ficheiros são ficheiros JSON. Funcionam como a classe correspondente no SDK. Pode utilizar os ficheiros para criar novos ambientes que tenham configurações personalizadas. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Executar o seguinte comando para registar um ambiente a partir de um diretório especificado.

```azurecli-interactive
az ml environment register -d myenvdir
```

Executar o seguinte comando para listar todos os ambientes registados.

```azurecli-interactive
az ml environment list
```

Descarregue um ambiente registado utilizando o seguinte comando.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Passos seguintes

* Para utilizar um alvo de computação gerido para treinar um modelo, consulte [Tutorial: Treine um modelo](tutorial-train-models-with-aml.md).
* Depois de ter um modelo treinado, aprenda [como e onde implementar modelos.](how-to-deploy-and-where.md)
* Ver a [ `Environment` referência sdK classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Para obter mais informações sobre os conceitos e métodos descritos neste artigo, consulte o [caderno de exemplos.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)
