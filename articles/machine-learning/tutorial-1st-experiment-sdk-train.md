---
title: 'Tutorial: Treine o seu primeiro modelo de aprendizagem automática - Python'
titleSuffix: Azure Machine Learning
description: A parte 3 da série Azure Machine Learning mostra como treinar um modelo de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: df511e79b73256833ec54c5906bb6acbc852bc46
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739625"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>Tutorial: Treine o seu primeiro modelo de aprendizagem automática (parte 3 de 4)

Este tutorial mostra-lhe como treinar um modelo de aprendizagem automática em Azure Machine Learning.

Este tutorial é *parte 3 de uma série tutorial* de quatro partes na qual você aprende os fundamentos do Azure Machine Learning e completa tarefas de aprendizagem automática baseadas em empregos em Azure. Este tutorial baseia-se no trabalho que concluiu na [Parte 1: set up](tutorial-1st-experiment-sdk-setup-local.md) e Parte [2: Run "Hello world!"](tutorial-1st-experiment-hello-world.md) da série.

Neste tutorial, você dá o próximo passo submetendo um script que treina um modelo de machine learning. Este exemplo irá ajudá-lo a entender como a Azure Machine Learning facilita o comportamento consistente entre depuração local e execuções remotas.

Neste tutorial:

> [!div class="checklist"]
> * Criar um roteiro de treino.
> * Utilize a Conda para definir um ambiente de aprendizagem automática Azure.
> * Criar um script de controlo.
> * Compreenda as aulas de Aprendizagem automática do Azure `Environment` (, `Run` , `Metrics` .
> * Submeta e execute o seu roteiro de treino.
> * Veja a saída do seu código na nuvem.
> * Registar métricas para Azure Machine Learning.
> * Veja as suas métricas na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão da [parte 2](tutorial-1st-experiment-hello-world.md) da série.
* Conhecimento introdutório da língua python e fluxos de trabalho de aprendizagem automática.
* Ambiente de desenvolvimento local, como Visual Studio Code, Jupyter ou PyCharm.
* Python (versão 3.5 a 3.7).

## <a name="create-training-scripts"></a>Criar scripts de formação

Primeiro, define-se a arquitetura da rede neural num `model.py` ficheiro. Todo o seu código de treino irá para a `src` subdiretória, `model.py` incluindo.

O seguinte código é retirado [deste exemplo introdutório](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) de PyTorch. Note que os conceitos de Aprendizagem automática Azure se aplicam a qualquer código de aprendizagem automática, e não apenas pyTorch.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/model.py":::

Em seguida, define o roteiro do treino. Este script descarrega o conjunto de dados CIFAR10 utilizando APIs PyTorch, `torchvision.dataset` configura a rede definida em , e `model.py` treina-a para duas épocas usando a perda padrão de SGD e cross-entropy.

Criar um `train.py` script na `src` subdiretória:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/train.py":::

Tem agora a seguinte estrutura de diretório:

```txt
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
```

> [!div class="nextstepaction"]
> [Criei os guiões de treino](?success=create-scripts#environment) [que encontrei num problema.](https://www.research.net/r/7CTJQQN?issue=create-scripts)

## <a name="create-a-python-environment"></a><a name="environment"></a> Criar um ambiente Python

Para fins de demonstração, vamos usar um ambiente Conda. (Os passos para um ambiente virtual pip são quase idênticos.)

Criar um ficheiro chamado `pytorch-env.yml` `.azureml` diretório oculto:

:::code language="yml" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/environments/pytorch-env.yml":::

Este ambiente tem todas as dependências que o seu modelo e script de treino requerem. Note que não há dependência do Azure Machine Learning SDK para Python.

> [!div class="nextstepaction"]
> [Criei o ficheiro ambiental](?success=create-env-file#test-local) [que encontrei num problema.](https://www.research.net/r/7CTJQQN?issue=create-env-file)

## <a name="test-locally"></a><a name="test-local"></a> Teste local

Utilize o seguinte código para testar o seu script que funciona localmente neste ambiente:

```bash
conda env create -f .azureml/pytorch-env.yml    # create conda environment
conda activate pytorch-env                      # activate conda environment
python src/train.py                             # train model
```

Depois de executar este script, verá os dados descarregados num diretório chamado `tutorial/data` .

> [!div class="nextstepaction"]
> [Criei o ficheiro ambiental](?success=test-local#create-local) [que encontrei num problema.](https://www.research.net/r/7CTJQQN?issue=test-local)

## <a name="create-the-control-script"></a><a name="create-local"></a> Criar o script de controlo

A diferença entre o seguinte guião de controlo e o que usou para submeter "Olá mundo!" é que adicione um par de linhas extra para definir o ambiente.

Crie um novo ficheiro Python no `tutorial` diretório chamado `04-run-pytorch.py` :

```python
# 04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='./src',
                             script='train.py',
                             compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```    
    
### <a name="understand-the-code-changes"></a>Compreender as alterações de código

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      A Azure Machine Learning fornece o conceito de um [ambiente](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) para representar um ambiente python reprodutível e versado para realizar experiências. É fácil criar um ambiente a partir de um ambiente local de Conda ou pip.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Adiciona o ambiente ao [ScriptRunConfig.](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py)
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Criei o guião de controlo](?success=control-script#submit) [que encontrei num problema.](https://www.research.net/r/7CTJQQN?issue=control-script)


## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit"></a> Submeta a corrida para Azure Machine Learning

Se alterou os ambientes locais, não se esqueça de voltar a mudar para um ambiente que tenha o Azure Machine Learning SDK para Python instalado.

Em seguida, execute:

```bash
python 04-run-pytorch.py
```

>[!NOTE] 
> A primeira vez que executar este script, a Azure Machine Learning construirá uma nova imagem docker a partir do seu ambiente PyTorch. Toda a corrida pode levar 5 a 10 minutos para ser concluída. 
>
> Pode ver o Docker a construir registos no estúdio Azure Machine Learning. Siga o link para o estúdio, selecione o **separador Saídas + registos** e, em seguida, selecione `20_image_build_log.txt` .
>
> Esta imagem será reutilizada em futuras corridas para que corram muito mais rápido.

Depois de construir a sua imagem, selecione `70_driver_log.txt` para ver a saída do seu script de treino.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> Se vir um `Your total snapshot size exceeds the limit` erro, o `data` diretório encontra-se no `source_directory` valor utilizado em `ScriptRunConfig` .
>
> Mova-se `data` para `src` fora.

Ambientes podem ser registados num espaço de trabalho com `env.register(ws)` . Podem então ser facilmente partilhados, reutilizados e versados. Os ambientes facilitam a reprodução de resultados anteriores e a colaboração com a sua equipa.

A Azure Machine Learning também mantém uma coleção de ambientes curados. Estes ambientes cobrem cenários comuns de aprendizagem automática e são apoiados por imagens de Docker em cache. As imagens do Docker em cache tornam o primeiro remoto a correr mais depressa.

Em suma, a utilização de ambientes registados pode poupar-lhe tempo! Leia [Como utilizar ambientes](./how-to-use-environments.md) para obter mais informações.

> [!div class="nextstepaction"]
> [Apresentei a corrida](?success=test-w-environment#log) [que encontrei um problema](https://www.research.net/r/7CTJQQN?issue=test-w-environment)

## <a name="log-training-metrics"></a><a name="log"></a> Métricas de treinamento de registo

Agora que tem um modelo de formação em Azure Machine Learning, comece a rastrear algumas métricas de desempenho.

O roteiro de treino atual imprime métricas ao terminal. O Azure Machine Learning fornece um mecanismo para registar métricas com mais funcionalidade. Ao adicionar algumas linhas de código, ganha-se a capacidade de visualizar métricas no estúdio e de comparar métricas entre várias corridas.

### <a name="modify-trainpy-to-include-logging"></a>Modificar `train.py` para incluir a exploração madeireira

Modifique o seu `train.py` script para incluir mais duas linhas de código:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-train-with-logging/train.py":::


#### <a name="understand-the-additional-two-lines-of-code"></a>Compreenda as duas linhas de código adicionais

Em `train.py` , aceda ao  objeto de execução a partir do próprio script de treino, utilizando o método `Run.get_context()` e usá-lo para registar métricas:

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

As métricas em Azure Machine Learning são:

- Organizado por experiência e corrida, por isso é fácil acompanhar e comparar métricas.
- Equipado com uma UI para que possa visualizar o desempenho de treino no estúdio.
- Projetado para escalar, então você mantém estes benefícios mesmo que você executar centenas de experiências.

> [!div class="nextstepaction"]
> [Modifiquei train.py ](?success=modify-train#log) [dei uma questão.](https://www.research.net/r/7CTJQQN?issue=modify-train)

### <a name="update-the-conda-environment-file"></a>Atualizar o ficheiro ambiente Conda

O `train.py` guião acabou de assumir uma nova `azureml.core` dependência. Atualização `pytorch-env.yml` para refletir esta alteração:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/configuration/pytorch-aml-env.yml":::

> [!div class="nextstepaction"]
> [Atualizei o ficheiro ambiental](?success=update-environment#submit-again) [que desvendou um problema.](https://www.research.net/r/7CTJQQN?issue=update-environment)

### <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-again"></a> Submeta a corrida para Azure Machine Learning
Submeta este script mais uma vez:

```bash
python 04-run-pytorch.py
```

Desta vez, quando visitar o estúdio, vá ao separador **Métricas** onde agora pode ver atualizações ao vivo sobre a perda de treino do modelo!

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Gráfico de perda de treino no separador Métricas.":::

> [!div class="nextstepaction"]
> [Resubmeti-me a correr](?success=resubmit-with-logging#next-steps) [e encontrei um problema.](https://www.research.net/r/7CTJQQN?issue=resubmit-with-logging)

## <a name="next-steps"></a>Passos seguintes

Nesta sessão, atualizaste-te de um "Olá mundo" básico! script para um script de treino mais realista que exigia um ambiente python específico para executar. Você viu como levar um ambiente conda local para a nuvem com ambientes de aprendizagem de máquinas Azure. Finalmente, viu como em algumas linhas de código pode registar métricas para Azure Machine Learning.

Existem outras formas de criar ambientes de aprendizagem automática Azure, incluindo [a partir de um ficheiro pip requirements.txt](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-pip-requirements-name--file-path-) ou de um ambiente [conda local existente.](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-)

Na próxima sessão, você verá como trabalhar com os dados em Azure Machine Learning, enviando o conjunto de dados CIFAR10 para Azure.

> [!div class="nextstepaction"]
> [Tutorial: Traga os seus próprios dados](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Se quiser terminar a série tutorial aqui e não avançar para o próximo passo, lembre-se de [limpar os seus recursos.](tutorial-1st-experiment-bring-data.md#clean-up-resources)
