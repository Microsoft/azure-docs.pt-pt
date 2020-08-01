---
title: Use ambientes de software
titleSuffix: Azure Machine Learning
description: Criar e gerir ambientes para formação e implantação de modelos. Gerir pacotes Python e outras configurações para o ambiente.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: d39b5f414b1998a4c0eacbbf8fd792d27c70309c
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87474585"
---
# <a name="create--use-software-environments-in-azure-machine-learning"></a>Crie & utilize ambientes de software em Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprenda a criar e gerir [ambientes](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)de Aprendizagem automática Azure. Use os ambientes para rastrear e reproduzir as dependências de software dos seus projetos à medida que evoluem.

A gestão da dependência de software é uma tarefa comum para os desenvolvedores. Pretende certificar-se de que as construções são reprodutíveis sem uma configuração manual extensiva do software manual. A classe Azure Machine Learning `Environment` é responsável por soluções de desenvolvimento local como pip e Conda e distribuiu desenvolvimento em nuvem através das capacidades do Docker.

Os exemplos deste artigo mostram como:

* Crie um ambiente e especifique as dependências dos pacotes.
* Recupere e atualize ambientes.
* Use um ambiente para treinar.
* Utilize um ambiente para implantação de serviço web.

Para uma visão geral de alto nível de como os ambientes funcionam em Azure Machine Learning, veja [o que são ambientes ML?](concept-environments.md) Para obter informações sobre a configuração de ambientes de desenvolvimento, consulte [aqui.](how-to-configure-environment.md)

## <a name="prerequisites"></a>Pré-requisitos

* [O Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Criar um ambiente

As secções seguintes exploram as múltiplas formas de criar um ambiente para as suas experiências.

### <a name="use-a-curated-environment"></a>Use um ambiente curado

Ambientes com curadoria contêm coleções de pacotes Python e estão disponíveis no seu espaço de trabalho por padrão. Estes ambientes são apoiados por imagens estivas em cache que reduzem o custo de preparação da execução. Você pode selecionar um destes ambientes populares com curadoria para começar com: 

* O ambiente _AzureML-Minimal_ contém um conjunto mínimo de pacotes para permitir o rastreio de execução e o upload de ativos. Pode usá-lo como ponto de partida para o seu próprio ambiente.

* O ambiente _AzureML-Tutorial_ contém pacotes comuns de ciência de dados. Estes pacotes incluem Scikit-Learn, Pandas, Matplotlib, e um conjunto maior de pacotes azureml-sdk.

Para obter uma lista de ambientes curados, consulte o [artigo ambientes curados.](resource-curated-environments.md)

Utilize o `Environment.get` método para selecionar um dos ambientes curados:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Para modificar um ambiente curado, deve ser copiado:

```python
env = Environment.get(workspace=ws, name="AzureML-Tutorial").clone("new_env")
```
Pode listar os ambientes curados e os seus pacotes utilizando o seguinte código:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Não comece o seu próprio nome de ambiente com o prefixo _AzureML._ Este prefixo é reservado para ambientes com curadoria.


### <a name="instantiate-an-environment-object"></a>Instantiizar um objeto ambiental

Para criar manualmente um ambiente, importe a `Environment` classe do SDK. Em seguida, utilize o seguinte código para instantaneaizar um objeto ambiente.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

Se estiver a definir o seu próprio ambiente, deve listar `azureml-defaults` com a versão >= 1.0.45 como uma dependência de pip. Este pacote contém a funcionalidade necessária para hospedar o modelo como um serviço web.

### <a name="use-conda-and-pip-specification-files"></a>Utilize ficheiros de especificações Conda e pip

Pode criar um ambiente a partir de uma especificação Conda ou de um ficheiro de requisitos de pip. Utilize o [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) método ou o [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) método. No argumento do método, inclua o nome do ambiente e o caminho do ficheiro que pretende. 

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")                                          
```

### <a name="use-existing-environments"></a>Utilizar ambientes existentes

Se tiver um ambiente Conda existente no seu computador local, então pode usar o serviço para criar um objeto ambiental. Ao utilizar esta estratégia, pode reutilizar o seu ambiente interativo local em execuções remotas.

O seguinte código cria um objeto ambiente a partir do ambiente Conda existente `mycondaenv` . Usa o [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) método.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

Uma definição de ambiente pode ser guardada para um diretório num formato facilmente editável com o [`save_to_directory()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#save-to-directory-path--overwrite-false-) método. Uma vez modificado, um novo ambiente pode ser instantâneo carregando ficheiros do diretório.

```python
myenv = Environment.save_to_directory(path = "path-to-destination-directory", overwrite = False)
# modify the environment definition
newenv = Environment.load_from_directory(path = "path-to-source-directory")
```

### <a name="create-environments-automatically"></a>Criar ambientes automaticamente

Criar automaticamente um ambiente através da apresentação de uma formação. Submeta a execução utilizando o `submit()` método. Quando se submete uma corrida de treino, a construção do novo ambiente pode demorar vários minutos. A duração da construção depende do tamanho das dependências exigidas. 

Se não especificar um ambiente na configuração de execução antes de submeter a execução, então é criado um ambiente padrão para si.

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

Da mesma forma, se utilizar um [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) objeto para treinar, pode submeter diretamente a instância do estimador como uma corrida sem especificar um ambiente. O `Estimator` objeto já encapsula o ambiente e o alvo do cálculo.

## <a name="add-packages-to-an-environment"></a>Adicionar pacotes a um ambiente

Adicione pacotes a um ambiente utilizando ficheiros Conda, pip ou rodas privadas. Especifique a dependência de cada pacote utilizando a [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) classe. Adicione-o ao `PythonSection` ambiente.

### <a name="conda-and-pip-packages"></a>Pacotes Conda e pip

Se um pacote estiver disponível num repositório de pacotes Conda, recomendamos que utilize a instalação Conda em vez da instalação do pip. Os pacotes Conda normalmente vêm com binários pré-construídos que tornam a instalação mais fiável.

O exemplo a seguir aumenta o ambiente. Adiciona a versão 1.17.0 de `numpy` . Também adiciona o `pillow` pacote, `myenv` . O exemplo utiliza o [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) método e o [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) método, respectivamente.

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

Também pode adicionar variáveis ambientais ao seu ambiente. Estes ficam então disponíveis usando os.environ.obter no seu roteiro de treino.

```python
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

>[!IMPORTANT]
> Se utilizar a mesma definição de ambiente para outra execução, o serviço Azure Machine Learning reutiliza a imagem em cache do seu ambiente. Se criar um ambiente com uma dependência de pacotes não pinados, por ```numpy``` exemplo, esse ambiente continuará a utilizar a versão pacote instalada _no momento da criação do ambiente_. Além disso, qualquer ambiente futuro com definição correspondente continuará a usar a versão antiga. Para mais informações, consulte [a construção do Ambiente, caching e reutilização.](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)

### <a name="private-python-packages"></a>Pacotes privados python

Para utilizar pacotes Python de forma privada e segura sem os expor à internet pública, consulte o artigo [Como utilizar pacotes python privados.](how-to-use-private-python-packages.md)

## <a name="manage-environments"></a>Gerir ambientes

Gerencie ambientes para que possa atualizar, rastrear e reutilizar os mesmos através de alvos de computação e com outros utilizadores do espaço de trabalho.

### <a name="register-environments"></a>Registar ambientes

O ambiente é automaticamente registado no seu espaço de trabalho quando submete uma execução ou implementa um serviço web. Também pode registar manualmente o ambiente utilizando o [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) método. Esta operação torna o ambiente numa entidade rastreada e versada na nuvem. A entidade pode ser partilhada entre utilizadores do espaço de trabalho.

O código seguinte regista o `myenv` ambiente no espaço de `ws` trabalho.

```python
myenv.register(workspace=ws)
```

Quando se utiliza o ambiente pela primeira vez em treinos ou implantações, está registado no espaço de trabalho. Depois é construído e implantado no alvo do cálculo. O serviço caches os ambientes. Reutilizar um ambiente em cache leva muito menos tempo do que usar um novo serviço ou um que tenha sido atualizado.

### <a name="get-existing-environments"></a>Obtenha ambientes existentes

A `Environment` classe oferece métodos que lhe permitem recuperar ambientes existentes no seu espaço de trabalho. Você pode recuperar ambientes pelo nome, como uma lista, ou por uma corrida de treino específica. Estas informações são úteis para a resolução de problemas, auditoria e reprodutibilidade.

#### <a name="view-a-list-of-environments"></a>Ver uma lista de ambientes

Veja os ambientes no seu espaço de trabalho utilizando a [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) classe. Em seguida, selecione um ambiente para reutilizar.

#### <a name="get-an-environment-by-name"></a>Obtenha um ambiente pelo nome

Também pode obter um ambiente específico pelo nome e versão. O seguinte código utiliza o [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) método para recuperar a versão do ambiente no espaço de `1` `myenv` `ws` trabalho.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Treine um ambiente específico de corrida

Para obter o ambiente que foi usado para uma corrida específica após os acabamentos do treino, use o [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) método na `Run` classe.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Atualizar um ambiente existente

Digamos que se muda um ambiente existente, por exemplo, adicionando um pacote Python. Isto levará tempo a construir à medida que uma nova versão do ambiente é então criada quando você submete uma corrida, implanta um modelo ou registra manualmente o ambiente. A versão permite-lhe ver as mudanças do ambiente ao longo do tempo. 

Para atualizar uma versão do pacote Python num ambiente existente, especifique o número da versão para esse pacote. Se não utilizar o número exato da versão, então o Azure Machine Learning reutilizará o ambiente existente com as suas versões de pacote originais.

### <a name="debug-the-image-build"></a>Depurar a construção de imagem

O exemplo a seguir utiliza o [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) método para criar manualmente um ambiente como uma imagem do Docker. Monitoriza os registos de saída da construção de imagens utilizando [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) . A imagem construída aparece então na instância do registo do contentor Azure do espaço de trabalho. Esta informação é útil para depurar.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

É útil primeiro construir imagens localmente usando o [`build_local()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-local-workspace--platform-none----kwargs-) método. E definir o parâmetro opcional `pushImageToWorkspaceAcr = True` irá empurrar a imagem resultante para o registo do espaço de trabalho Azure ML. 

## <a name="enable-docker"></a>Ativar Docker

O contentor docker fornece uma maneira eficiente de encapsular as dependências. Quando ativa o Docker, o Azure ML constrói uma imagem docker e cria um ambiente Python dentro desse contentor, dadas as suas especificações. As imagens Docker são em cache e reutilizadas: a primeira corrida num novo ambiente normalmente demora mais tempo à medida que a imagem é construída.

A [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) aula de Aprendizagem automática Azure `Environment` permite-lhe personalizar e controlar finamente o sistema operativo de hóspedes no qual executa o seu treino. A `arguments` variável pode ser usada para especificar argumentos extras para passar para o comando de execução do Docker.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Por predefinição, a imagem do Docker recém-construída aparece no registo do contentor que está associado ao espaço de trabalho.  O nome do repositório tem a forma *azureml/azureml_ \<uuid\> *. A parte do identificador único *(uuid)* do nome corresponde a um haxixe que é calculado a partir da configuração do ambiente. Esta correspondência permite que o serviço determine se já existe uma imagem para o ambiente dado para reutilização.

### <a name="use-a-custom-docker-image-or-dockerfile"></a>Use uma imagem personalizada do Docker ou Dockerfile 
O serviço utiliza automaticamente uma das imagens base [baseadas](https://github.com/Azure/AzureML-Containers)em Ubuntu Linux. Instala os pacotes Python especificados. A imagem base tem versões CPU e versões GPU. O Azure Machine Learning deteta automaticamente qual a versão a utilizar. Também é possível usar uma [imagem base personalizada do Docker.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image#create-a-custom-base-image)

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

>[!IMPORTANT]
> A Azure Machine Learning suporta apenas imagens docker que fornecem o seguinte software:
> * Ubuntu 16.04 ou maior.
> * Conda 4.5.# ou maior.
> * Python 3.5.#, 3.6.# ou 3.7.#.

Também pode especificar um Dockerfile personalizado. É mais simples começar a partir de uma das imagens base de Azure Machine Learning usando ```FROM``` o comando Docker, e depois adicionar os seus próprios passos personalizados. Utilize esta abordagem se precisar de instalar pacotes não Python como dependências. Lembre-se de definir a imagem base para Nenhum.

```python
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = "./Dockerfile"
```

### <a name="specify-your-own-python-interpreter"></a>Especifique o seu próprio intérprete Python

Em algumas situações, a sua imagem de base personalizada pode já conter um ambiente Python com pacotes que pretende utilizar.

Para utilizar os seus próprios pacotes instalados e desativar a Conda, desative o parâmetro `Environment.python.user_managed_dependencies = True` . Certifique-se de que a imagem base contém um intérprete Python e tem os pacotes de que o seu script de treino precisa.

Por exemplo, para funcionar num ambiente de Miniconda base que tenha o pacote NumPy instalado, primeiro especifique um Dockerfile com um passo para instalar o pacote. Em seguida, desa estale as dependências geridas pelo utilizador para `True` . 

Também pode especificar um caminho para um intérprete python específico dentro da imagem, definindo a `Environment.python.interpreter_path` variável.

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

> [!WARNING]
> Se instalar algumas dependências python na sua imagem Docker e se esquecer de definir user_managed_dependencies = Verdade, esses pacotes não existirão no ambiente de execução, causando assim falhas no tempo de execução. Por padrão, o Azure ML construirá um ambiente Conda com dependências especificadas, e executará a execução nesse ambiente em vez de usar quaisquer bibliotecas Python que instalou na imagem base.

## <a name="use-environments-for-training"></a>Utilizar ambientes para formação

Para submeter uma corrida de treino, precisa combinar o seu ambiente, [o seu alvo de computação](concept-compute-target.md)e o seu script python de treino numa configuração de execução. Esta configuração é um objeto de invólucro que é usado para submeter corridas.

Quando se submete uma corrida de treino, a construção de um novo ambiente pode demorar vários minutos. A duração depende do tamanho das dependências exigidas. Os ambientes são cached pelo serviço. Assim, enquanto a definição de ambiente permanecer inalterada, incorrerá o tempo de configuração completo apenas uma vez.

O exemplo de execução do script local mostra onde usaria [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) como seu objeto de invólucro.

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
> Para desativar o histórico de execução ou executar instantâneos, utilize a definição em `ScriptRunConfig.run_config.history` .

Se não especificar o ambiente na configuração de execução, o serviço cria um ambiente predefinido quando submete a sua execução.

### <a name="use-an-estimator-for-training"></a>Use um estimador para o treino

Se utilizar um [estimador](how-to-train-ml-models.md) para o treino, então pode submeter a instância do estimador diretamente. Já encapsula o ambiente e o alvo do cálculo.

O seguinte código utiliza um estimador para uma corrida de treino de nó único. Funciona num cálculo remoto para um `scikit-learn` modelo. Assume que criou anteriormente um objeto-alvo de `compute_target` computação, e um objeto de datastore, `ds` .

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

## <a name="use-environments-for-web-service-deployment"></a>Use ambientes para implantação de serviços web

Pode utilizar ambientes quando implementar o seu modelo como serviço web. Esta capacidade permite um fluxo de trabalho reprodutível e conectado. Neste fluxo de trabalho, pode treinar, testar e implementar o seu modelo utilizando as mesmas bibliotecas tanto no seu compute de treino como no seu computo de inferência.

Para implementar um serviço web, combine o ambiente, inferência computacional, script de pontuação e modelo registado no seu objeto de implantação, [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) . Para mais informações, consulte [como e onde implementar modelos.](how-to-deploy-and-where.md)

Neste exemplo, assuma que completou um treino. Agora quer colocar este modelo nas instâncias do Azure Container. Quando constrói o serviço web, o modelo e os ficheiros de pontuação são montados na imagem e a pilha de inferência de Aprendizagem automática Azure é adicionada à imagem.

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

## <a name="notebooks"></a>Notebooks

Este [artigo](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#add-new-kernels) fornece informações sobre como instalar um ambiente Conda como um núcleo num caderno.

Este [caderno de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) expande-se sobre conceitos e métodos demonstrados neste artigo.

Este [caderno de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local/train-on-local.ipynb) demonstra como treinar um modelo localmente com diferentes tipos de ambientes.

[Implementar um modelo utilizando uma imagem base personalizada do Docker](how-to-deploy-custom-docker-image.md) demonstra como implementar um modelo utilizando uma imagem base personalizada do Docker.

Este [caderno de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) demonstra como implementar um modelo Spark como um serviço web.

## <a name="create-and-manage-environments-with-the-cli"></a>Criar e gerir ambientes com o CLI

O [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) espelha a maior parte da funcionalidade do Python SDK. Pode usá-lo para criar e gerir ambientes. Os comandos que discutimos nesta secção demonstram funcionalidade básica.

Os andaimes de comando a seguir são os ficheiros para uma definição de ambiente predefinido no diretório especificado. Estes ficheiros são ficheiros JSON. Funcionam como a classe correspondente no SDK. Pode utilizar os ficheiros para criar novos ambientes com configurações personalizadas. 

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

Faça o download de um ambiente registado utilizando o seguinte comando.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Passos seguintes

* Para utilizar um alvo de computação gerido para treinar um modelo, consulte [Tutorial: Treine um modelo.](tutorial-train-models-with-aml.md)
* Depois de ter um modelo treinado, aprenda [como e onde implementar modelos.](how-to-deploy-and-where.md)
* Ver a [ `Environment` referência SDK de classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
