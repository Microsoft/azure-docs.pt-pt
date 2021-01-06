---
title: Configurar uma corrida de treino
titleSuffix: Azure Machine Learning
description: Treine o seu modelo de aprendizagem automática em vários ambientes de treino (metas de computação). Pode facilmente alternar entre ambientes de treino.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: a3427be85314f06b5408c4450e0415768122879f
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97913010"
---
# <a name="configure-and-submit-training-runs"></a>Configurar e enviar execuções de preparação

Neste artigo, aprende-se a configurar e submeter as corridas Azure Machine Learning para treinar os seus modelos. Os snippets de código explicam as partes-chave da configuração e submissão de um script de treino.  Em seguida, use um dos [cadernos](#notebooks) de exemplo para encontrar todos os exemplos de trabalho de ponta a ponta.

Ao treinar, é comum começar no computador local e, mais tarde, escalar para um cluster baseado em nuvem. Com o Azure Machine Learning, pode executar o seu script em vários alvos de computação sem ter de alterar o seu script de treino.

Tudo o que precisa de fazer é definir o ambiente para cada alvo de cálculo dentro de uma **configuração de execução de script**.  Em seguida, quando quiser executar a sua experiência de treino num alvo de computação diferente, especifique a configuração de execução para esse cálculo.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree)
* [O Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.13.0)
* [Um espaço de trabalho de aprendizagem automática Azure,](how-to-manage-workspace.md)`ws`
* Um alvo computacional, `my_compute_target` .  [Criar um alvo de computação](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>O que é uma configuração de execução de script?
Um [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) é usado para configurar as informações necessárias para submeter uma corrida de treino como parte de uma experiência.

Submetes a tua experiência de treino com um objeto ScriptRunConfig.  Este objeto inclui:

* **source_directory**: O diretório de origem que contém o seu roteiro de treino
* **script**: O roteiro de treino para executar
* **compute_target**: O alvo do cálculo a executar
* **ambiente**: O ambiente a utilizar ao executar o script
* e algumas opções configuráveis adicionais (ver [documentação de referência](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) para mais informações)

## <a name="train-your-model"></a><a id="submit"></a>Preparar o seu modelo

O código padrão para submeter uma corrida de treino é o mesmo para todos os tipos de metas de computação:

1. Criar uma experiência para correr
1. Criar um ambiente onde o script será executado
1. Crie um ScriptRunConfig, que especifica o alvo e o ambiente do cálculo
1. Submeter a corrida
1. Espere que a corrida termine.

Ou pode:

* Submeta uma execução HyperDrive para [sintonização hiperparítnica](how-to-tune-hyperparameters.md).
* Submeter uma experiência através da extensão do [Código VS.](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)

## <a name="create-an-experiment"></a>Criar uma experimentação

Crie uma experiência no seu espaço de trabalho.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>Selecione um alvo de cálculo

Selecione o alvo do cálculo onde o seu script de treino será executado. Se nenhum alvo de cálculo for especificado no ScriptRunConfig, ou se `compute_target='local'` , Azure ML executará o seu script localmente. 

O código de exemplo neste artigo pressupõe que já criou um alvo de computação `my_compute_target` a partir da secção "Pré-requisitos".

## <a name="create-an-environment"></a>Criar um ambiente
Os [ambientes](concept-environments.md) de aprendizagem automática Azure são uma encapsulação do ambiente onde o seu treino de aprendizagem automática acontece. Especificam os pacotes Python, a imagem do Docker, as variáveis ambientais e as definições de software em torno dos seus scripts de treino e pontuação. Também especificam os tempos de execução (Python, Spark ou Docker).

Você pode definir o seu próprio ambiente, ou usar um ambiente com curadoria Azure ML. [Ambientes curados](./how-to-use-environments.md#use-a-curated-environment) são ambientes predefinidos que estão disponíveis no seu espaço de trabalho por padrão. Estes ambientes são apoiados por imagens estivas em cache que reduzem o custo de preparação da execução. Consulte [ambientes curados para aprendizagem automática Azure](./resource-curated-environments.md) para a lista completa de ambientes com curadoria disponíveis.

Para um alvo de computação remota, você pode usar um desses ambientes populares curados para começar com:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Para obter mais informações e detalhes sobre ambientes, consulte [Criar & utilizar ambientes de software em Azure Machine Learning](how-to-use-environments.md).
  
### <a name="local-compute-target"></a><a name="local"></a>Alvo de computação local

Se o seu alvo de cálculo for a sua **máquina local,** é responsável por garantir que todos os pacotes necessários estão disponíveis no ambiente Python onde o script é executado.  Utilize `python.user_managed_dependencies` para utilizar o seu ambiente python atual (ou o Python no caminho que especificar).

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>Criar a configuração de execução do script

Agora que tem um alvo de cálculo `my_compute_target` () e ambiente `myenv` (), crie uma configuração de execução de script que executa o seu script de treino `train.py` () localizado no seu `project_folder` diretório:

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

Se não especificar um ambiente, será criado um ambiente padrão para si.

Se tiver argumentos de linha de comando que pretende passar para o seu script de treino, pode especificá-los através **`arguments`** do parâmetro do construtor ScriptRunConfig, por exemplo. `arguments=['--arg1', arg1_val, '--arg2', arg2_val]`

Se pretender anular o tempo máximo de execução por defeito, pode fazê-lo através do **`max_run_duration_seconds`** parâmetro. O sistema tentará cancelar automaticamente a execução se demorar mais tempo do que este valor.

### <a name="specify-a-distributed-job-configuration"></a>Especificar uma configuração de trabalho distribuída
Se quiser executar um trabalho de formação distribuído, forneça o config específico do trabalho distribuído ao **`distributed_job_config`** parâmetro. Os tipos de config suportados incluem [MpiConfiguration,](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py) [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py)e [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py). 

Para obter mais informações e exemplos sobre a execução de trabalhos de Horovod, TensorFlow e PyTorch, consulte:

* [Preparar modelos de TensorFlow](./how-to-train-tensorflow.md#distributed-training)
* [Preparar modelos de PyTorch](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>Submeter a experimentação

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Quando submete a formação, uma imagem do diretório que contém os seus scripts de treino é criada e enviada para o alvo do cálculo. Também é armazenado como parte da experiência no seu espaço de trabalho. Se alterar ficheiros e submeter a execução novamente, apenas os ficheiros alterados serão carregados.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Para obter mais informações sobre instantâneos, consulte [Snapshots](concept-azure-machine-learning-architecture.md#snapshots).

> [!IMPORTANT]
> **Pastas especiais** Duas *pastas, saídas* e *troncos,* recebem tratamento especial pela Azure Machine Learning. Durante o treino, quando escreve ficheiros para *pastas nomeadas saídas* e *registos relativos* ao diretório de raiz `./outputs` `./logs` (e, respectivamente), os ficheiros serão automaticamente carregados para o seu histórico de execução para que tenha acesso aos mesmos uma vez que a sua execução esteja terminada.
>
> Para criar artefactos durante o treino (como ficheiros de modelos, pontos de verificação, ficheiros de dados ou imagens traçadas) escreva-os para a `./outputs` pasta.
>
> Da mesma forma, pode escrever quaisquer registos da sua corrida de treino para a `./logs` pasta. Para utilizar a integração do [TensorBoard](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) da Azure Machine Learning certifique-se de que escreve os seus registos TensorBoard nesta pasta. Enquanto a sua execução estiver em andamento, poderá lançar o TensorBoard e transmitir estes registos.  Mais tarde, também poderá restaurar os registos de qualquer uma das suas execuções anteriores.
>
> Por exemplo, para descarregar um ficheiro escrito para a pasta *de saídas* para a sua máquina local após o seu treino remoto: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Rastreio e integração de Git

Quando se inicia uma corrida de treinamento onde o diretório de origem é um repositório local de Git, a informação sobre o repositório é armazenada na história da execução. Para obter mais informações, consulte [a integração do Git para a Azure Machine Learning.](concept-train-model-git-integration.md)

## <a name="notebook-examples"></a><a name="notebooks"></a>Exemplos de cadernos

Consulte estes cadernos como exemplos de configurações para vários cenários de formação:
* [Formação em vários alvos de computação](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Formação com quadros ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [tutoriais/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="troubleshooting"></a>Resolução de problemas

 * **MóduloErrors (Nenhum módulo nomeado)**: Se estiver a correr para o ModuleErrors enquanto submete experiências em Azure ML, o script de treino espera que seja instalado um pacote, mas não é adicionado. Assim que fornecer o nome do pacote, a Azure ML instala a embalagem no ambiente utilizado para o seu treino.

    Se estiver a utilizar os Estimadores para submeter experiências, pode especificar um nome de pacote através `pip_packages` ou `conda_packages` parâmetro no estimador com base na fonte que pretende instalar a embalagem. Também pode especificar um ficheiro yml com todas as suas dependências usando `conda_dependencies_file` ou listar todos os seus requisitos de pip num ficheiro TXT usando `pip_requirements_file` parâmetro. Se tiver o seu próprio objeto Azure ML Environment que pretende sobrepor a imagem padrão utilizada pelo estimador, pode especificar esse ambiente através `environment` do parâmetro do construtor estimador.
    
    A Azure ML manteve imagens de estivadores e o seu conteúdo pode ser visto em [Recipientes AzureML](https://github.com/Azure/AzureML-Containers).
    As dependências específicas do quadro constam da respetiva documentação-quadro:
    *  [Chainer](/python/api/azureml-train-core/azureml.train.dnn.chainer?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    * [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    * [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    *  [SKLearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    
    > [!Note]
    > Se você acha que um determinado pacote é comum o suficiente para ser adicionado em imagens e ambientes mantidos Azure ML por favor levante um problema gitHub em [recipientes AzureML](https://github.com/Azure/AzureML-Containers). 
 
* **NomeError (Nome não definido), AttributeError (Objeto não tem atributo)**: Esta exceção deve vir dos seus scripts de treino. Pode ver os ficheiros de registo do portal Azure para obter mais informações sobre o nome específico não definido ou o erro de atributo. A partir do SDK, pode usar `run.get_details()` para ver a mensagem de erro. Isto também listará todos os ficheiros de registo gerados para a sua execução. Por favor, certifique-se de dar uma olhada no seu script de treino e corrigir o erro antes de voltar a enviar a sua execução. 


* **Execução ou eliminação de experiências**: As experiências podem ser arquivadas utilizando o método [Experiment.archive](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truearchive--) ou a partir da vista do separador Experiment em Azure Machine Learning cliente através do botão "Archive experiment". Esta ação esconde a experiência de consultas e pontos de vista de listas, mas não a apaga.

    Atualmente, a eliminação permanente de experimentações ou execuções individuais não é suportada. Para obter mais informações sobre a eliminação dos ativos do Espaço de Trabalho, consulte [exportação ou elimine os dados do seu espaço de trabalho do serviço Machine Learning](how-to-export-delete-data.md).

* **Documento métrico é muito grande**: A Azure Machine Learning tem limites internos no tamanho de objetos métricos que podem ser registados de uma só vez a partir de uma corrida de treino. Caso se depare com um erro “O Documento de Métricas é demasiado grande” ao registar uma métrica com valores de lista, experimente dividir a lista em segmentos menores, por exemplo:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Internamente, o Azure ML concatena os blocos com o mesmo nome de métrica numa lista contígua.

* **A execução `jwt.exceptions.DecodeError` falha com**: Mensagem de erro exata: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` . 
    
    Considere atualizar para a versão mais recente do núcleo azureml: `pip install -U azureml-core` .
    
    Se estiver a deparar-se com este problema para as execuções locais, consulte a versão do PyJWT instalada no seu ambiente onde está a iniciar as suas corridas. As versões suportadas do PyJWT são < 2.0.0. Desinstalar pyJWT do ambiente se a versão for >= 2.0.0. Pode verificar a versão do PyJWT, desinstalar e instalar a versão certa da seguinte forma:
    1. Inicie uma concha de comando, ative o ambiente conda onde o núcleo azureml está instalado.
    2. `pip freeze`Insira e `PyJWT` procure, se encontrado, a versão listada deve ser < 2.0.0
    3. Se a versão listada não for uma versão suportada, `pip uninstall PyJWT` na concha de comando e insira y para confirmação.
    4. Instalar com `pip install 'PyJWT<2.0.0'`
    
    Se estiver a submeter um ambiente criado pelo utilizador com a sua execução, considere utilizar a versão mais recente do núcleo azureml nesse ambiente. Versões >= 1.18.0 de azureml-core já pin PyJWT < 2.0.0. Se precisar de utilizar uma versão do núcleo azureml < 1.18.0 no ambiente que submete, certifique-se de especificar PyJWT < 2.0.0 nas suas dependências de pip.

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md) usa um alvo de computação gerido para treinar um modelo.
* Veja como formar modelos com estruturas ML específicas, tais como [Scikit-learn,](how-to-train-scikit-learn.md) [TensorFlow](how-to-train-tensorflow.md)e [PyTorch](how-to-train-pytorch.md).
* Aprenda a [sintonizar eficientemente os hiperparmetros](how-to-tune-hyperparameters.md) para construir modelos melhores.
* Uma vez treinado, aprenda [como e onde implementar modelos.](how-to-deploy-and-where.md)
* Veja a referência SDK [da classe ScriptRunConfig.](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py)
* [Use a azure machine learning com redes virtuais Azure](./how-to-network-security-overview.md)
