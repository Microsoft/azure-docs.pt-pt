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
ms.date: 01/06/2020
ms.openlocfilehash: cb76c7d7804a7d39e8a18c7a4cf41e9b4e0a7593
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623642"
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

Utilize o método `Environment.get` para selecionar um dos ambientes com curadoria:

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

Para criar manualmente um ambiente, importe a classe `Environment` do SDK. Em seguida, utilize o seguinte código para instantaneamente um objeto ambiental.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Utilize ficheiros de especificações de Condomínio e pip

Também pode criar um ambiente a partir de uma especificação Conda ou de um ficheiro de requisitos de pip. Utilize o método [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) ou o método [`from_pip_requirements()`.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) No argumento do método, inclua o seu nome de ambiente e o caminho de arquivo do ficheiro que deseja.

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

O seguinte código cria um objeto ambiental a partir do ambiente conda existente `mycondaenv`. Usa o método [`from_existing_conda_environment()`.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-)

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Criar ambientes automaticamente

Criar automaticamente um ambiente através da apresentação de um treino. Submeta a execução utilizando o método `submit()`. Quando se submete a um treino, a construção do novo ambiente pode demorar vários minutos. A duração da construção depende do tamanho das dependências necessárias. 

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

Da mesma forma, se utilizar um objeto [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) para treino, pode submeter diretamente a instância estimada como uma corrida sem especificar um ambiente. O `Estimator` objeto já engloba o ambiente e o alvo da computação.

## <a name="add-packages-to-an-environment"></a>Adicione pacotes a um ambiente

Adicione pacotes a um ambiente utilizando ficheiros de rodas Conda, pip ou privadas. Especifique cada dependência do pacote utilizando a classe [`CondaDependency`.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) Adicione-o à `PythonSection`do ambiente.

### <a name="conda-and-pip-packages"></a>Pacotes de condomínio e pip

Se um pacote estiver disponível num repositório de pacotes Conda, recomendamos que utilize a instalação Conda em vez da instalação do pip. Os pacotes de condomínio saem normalmente com binários pré-construídos que tornam a instalação mais fiável.

O exemplo seguinte acrescenta ao ambiente. Adiciona versão 0.21.3 de `scikit-learn`. Acrescenta ainda o pacote `pillow`, `myenv`. O exemplo utiliza o método [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) e o método [`add_pip_package()`,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) respectivamente.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>Arquivos de rodas privadas

Pode utilizar ficheiros privados de rodas de pip enviando-os primeiro para o seu armazenamento no espaço de trabalho. Faça-os upload usando um método [estático`add_private_pip_wheel()`.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) Em seguida, captura o URL de armazenamento e passa o URL para o método `add_pip_package()`.

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

O ambiente é registado automaticamente com o seu espaço de trabalho quando submete uma execução ou implementa um serviço web. Também pode registar manualmente o ambiente utilizando o método [`register()`.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) Esta operação transforma o ambiente numa entidade que é rastreada e versonizada na nuvem. A entidade pode ser partilhada entre utilizadores do espaço de trabalho.

O código seguinte regista o ambiente `myenv` para o espaço de trabalho `ws`.

```python
myenv.register(workspace=ws)
```

Quando se utiliza o ambiente pela primeira vez em treino ou implantação, está registado no espaço de trabalho. Depois é construído e implantado no alvo da computação. O serviço caches os ambientes. A reutilização de um ambiente em cache leva muito menos tempo do que a utilização de um novo serviço ou um que tenha sido atualizado.

### <a name="get-existing-environments"></a>Obtenha ambientes existentes

A classe `Environment` oferece métodos que lhe permitem recuperar ambientes existentes no seu espaço de trabalho. Você pode recuperar ambientes pelo nome, como uma lista, ou por um treino específico. Esta informação é útil para a resolução de problemas, auditoria e reprodutibilidade.

#### <a name="view-a-list-of-environments"></a>Ver uma lista de ambientes

Veja os ambientes no seu espaço de trabalho utilizando a classe [`Environment.list(workspace="workspace_name")`.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) Em seguida, selecione um ambiente para reutilizar.

#### <a name="get-an-environment-by-name"></a>Obter um ambiente pelo nome

Você também pode obter um ambiente específico por nome e versão. O código que se segue utiliza o método [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) para recuperar a versão `1` do ambiente `myenv` no espaço de trabalho `ws`.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Treine um ambiente específico de corrida

Para obter o ambiente que foi usado para uma corrida específica após os acabamentos do treino, use o método [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) na classe `Run`.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Atualizar um ambiente existente

Digamos que se muda um ambiente existente, por exemplo, adicionando um pacote Python. Uma nova versão do ambiente é então criada quando submete uma execução, implementa um modelo ou regista manualmente o ambiente. A versão permite-lhe visualizar as mudanças do ambiente ao longo do tempo.

Para atualizar uma versão pacote Python num ambiente existente, especifique o número da versão para esse pacote. Se não utilizar o número exato da versão, então o Azure Machine Learning reutilizará o ambiente existente com as suas versões de pacote original.

### <a name="debug-the-image-build"></a>Desinem a construção da imagem

O exemplo que se segue utiliza o método [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) para criar manualmente um ambiente como imagem do Docker. Monitoriza os registos de saída a partir da construção da imagem utilizando [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-). A imagem construída aparece então na instância de registo de contentores azure do espaço de trabalho. Esta informação é útil para depurar.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Ativar Docker

 O [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) da aula de `Environment` de Machine Learning Azure permite-lhe personalizar e controlar finamente o sistema operativo de hóspedes em que executa o seu treino.

Quando ativas o Docker, o serviço constrói uma imagem do Docker. Também cria um ambiente Python que usa as suas especificações dentro do recipiente Docker. Esta funcionalidade proporciona isolamento e reprodutibilidade adicionais para os seus treinos.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Por padrão, a imagem recém-construída do Docker aparece no registo de contentores que está associado ao espaço de trabalho.  O nome do repositório tem a forma *azureml/azureml_\<\>uuide* . A parte única do identificador *(uuide)* do nome corresponde a um hash que é calculado a partir da configuração do ambiente. Esta correspondência permite ao serviço determinar se já existe uma imagem para o ambiente dado para reutilização.

Além disso, o serviço utiliza automaticamente uma das [imagens base](https://github.com/Azure/AzureML-Containers)baseadas em Ubuntu Linux . Instala os pacotes python especificados. A imagem base tem versões CPU e versões GPU. O Azure Machine Learning deteta automaticamente qual a versão a utilizar.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
# Alternatively, you can specify the contents of dockerfile of your base image
with open("docker_file_of_your_base_image", "r") as f:
    dockerfile_contents_of_your_base_image=f.read()
myenv.docker.base_dockerfile=dockerfile_contents_of_your_base_image 
```

> [!NOTE]
> Se especificar `environment.python.user_managed_dependencies=False` enquanto estiver a usar uma imagem personalizada do Docker, o serviço irá construir um ambiente Conda dentro da imagem. Executará a execução nesse ambiente em vez de utilizar quaisquer bibliotecas Python que tenha instalado na imagem base. Defina o parâmetro para `True` utilizar as suas próprias embalagens instaladas.

## <a name="use-environments-for-training"></a>Utilizar ambientes para a formação

Para submeter um treino, precisa de combinar o seu ambiente, o seu alvo de [computação,](concept-compute-target.md)e o seu roteiro python de treino numa configuração de execução. Esta configuração é um objeto de invólucro que é usado para submeter execuções.

Quando se submete a um treino, a construção de um novo ambiente pode demorar vários minutos. A duração depende do tamanho das dependências necessárias. Os ambientes são cached pelo serviço. Assim, enquanto a definição de ambiente permanecer inalterada, incorre em todo o tempo de configuração apenas uma vez.

O exemplo de execução de script local mostra onde usaria [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) como objeto de invólucro.

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
> Para desativar o histórico de execução ou executar instantâneos, utilize a definição em `ScriptRunConfig.run_config.history`.

Se não especificar o ambiente na sua configuração de execução, então o serviço cria um ambiente predefinido quando submete a sua execução.

### <a name="use-an-estimator-for-training"></a>Use um estimador para treinar

Se utilizar um [estimador](how-to-train-ml-models.md) para treino, então pode submeter a instância do estimador diretamente. Já encapsula o ambiente e o alvo da computação.

O código seguinte utiliza um estimador para uma execução de treino de um nó único. Funciona com uma computação remota para um modelo `scikit-learn`. Assume que criou anteriormente um objeto-alvo de cálculo, `compute_target`, e um objeto de datastore, `ds`.

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

Para implementar um serviço web, combine o ambiente, a computação de inferência, o script de pontuação e o modelo registado no seu objeto de implantação, [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). Para mais informações, consulte [Como e onde implementar modelos.](how-to-deploy-and-where.md)

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

## <a name="example-notebooks"></a>Blocos de notas de exemplo

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
* Ver a [referência sdK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)de classe`Environment` .
* Para obter mais informações sobre os conceitos e métodos descritos neste artigo, consulte o [caderno de exemplos.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)
