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
ms.openlocfilehash: f3ba5751e7a0c2369d505535896bbb4ff7523c02
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314570"
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

```python
# tutorial/src/model.py
import torch.nn as nn
import torch.nn.functional as F


class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x
```

Em seguida, define o roteiro do treino. Este script descarrega o conjunto de dados CIFAR10 utilizando APIs PyTorch, `torchvision.dataset` configura a rede definida em , e `model.py` treina-a para duas épocas usando a perda padrão de SGD e cross-entropy.

Criar um `train.py` script na `src` subdiretória:

```python
# tutorial/src/train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net

# download CIFAR10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                print(f"epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}")
                running_loss = 0.0

    print("Finished Training")

```

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

## <a name="create-a-python-environment"></a>Criar um ambiente Python

Para fins de demonstração, vamos usar um ambiente Conda. (Os passos para um ambiente virtual pip são quase idênticos.)

Criar um ficheiro chamado `pytorch-env.yml` `.azureml` diretório oculto:

```yml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
```

Este ambiente tem todas as dependências que o seu modelo e script de treino requerem. Note que não há dependência do Azure Machine Learning SDK para Python.

## <a name="test-locally"></a>Teste local

Utilize o seguinte código para testar o seu script que funciona localmente neste ambiente:

```bash
conda env create -f .azureml/pytorch-env.yml    # create conda environment
conda activate pytorch-env                      # activate conda environment
python src/train.py                             # train model
```

Depois de executar este script, verá os dados descarregados num diretório chamado `tutorial/data` .

## <a name="create-the-control-script"></a>Criar o script de controlo

A diferença entre o seguinte guião de controlo e o que usou para submeter "Olá mundo!" é que adicione um par de linhas extra para definir o ambiente.

Crie um novo ficheiro Python no `tutorial` diretório chamado `04-run-pytorch.py` :

```python
# tutorial/04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='src', script='train.py', compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env', file_path='.azureml/pytorch-env.yml')
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

## <a name="submit-the-run-to-azure-machine-learning"></a>Submeta a corrida para Azure Machine Learning

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

## <a name="log-training-metrics"></a>Métricas de treinamento de registo

Agora que tem um modelo de formação em Azure Machine Learning, comece a rastrear algumas métricas de desempenho.

O roteiro de treino atual imprime métricas ao terminal. O Azure Machine Learning fornece um mecanismo para registar métricas com mais funcionalidade. Ao adicionar algumas linhas de código, ganha-se a capacidade de visualizar métricas no estúdio e de comparar métricas entre várias corridas.

### <a name="modify-trainpy-to-include-logging"></a>Modificar `train.py` para incluir a exploração madeireira

Modifique o seu `train.py` script para incluir mais duas linhas de código:

```python
# train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run


# ADDITIONAL CODE: get Azure Machine Learning run from the current context
run = Run.get_context()

# download CIFAR10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True, num_workers=2
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                run.log('loss', loss) # ADDITIONAL CODE: log loss metric to Azure Machine Learning
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

#### <a name="understand-the-additional-two-lines-of-code"></a>Compreenda as duas linhas de código adicionais

Em `train.py` , aceda ao _within_ objeto de execução a partir do próprio script de treino, utilizando o método `Run.get_context()` e usá-lo para registar métricas:

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

### <a name="update-the-conda-environment-file"></a>Atualizar o ficheiro ambiente Conda

O `train.py` guião acabou de assumir uma nova `azureml.core` dependência. Atualização `pytorch-env.yml` para refletir esta alteração:

```yaml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
    - pip
    - pip:
        - azureml-sdk
```

### <a name="submit-the-run-to-azure-machine-learning"></a>Submeta a corrida para Azure Machine Learning
Submeta este script mais uma vez:

```bash
python 04-run-pytorch.py
```

Desta vez, quando visitar o estúdio, vá ao separador **Métricas** onde agora pode ver atualizações ao vivo sobre a perda de treino do modelo!

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Gráfico de perda de treino no separador Métricas.":::

## <a name="next-steps"></a>Passos seguintes

Nesta sessão, atualizaste-te de um "Olá mundo" básico! script para um script de treino mais realista que exigia um ambiente python específico para executar. Você viu como levar um ambiente conda local para a nuvem com ambientes de aprendizagem de máquinas Azure. Finalmente, viu como em algumas linhas de código pode registar métricas para Azure Machine Learning.

Existem outras formas de criar ambientes de aprendizagem automática Azure, incluindo [a partir de um ficheiro pip requirements.txt](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-pip-requirements-name--file-path-) ou de um ambiente [conda local existente.](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-)

Na próxima sessão, você verá como trabalhar com os dados em Azure Machine Learning, enviando o conjunto de dados CIFAR10 para Azure.

> [!div class="nextstepaction"]
> [Tutorial: Traga os seus próprios dados](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Se quiser terminar a série tutorial aqui e não avançar para o próximo passo, lembre-se de [limpar os seus recursos.](tutorial-1st-experiment-bring-data.md#clean-up-resources)