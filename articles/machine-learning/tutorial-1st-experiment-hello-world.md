---
title: 'Tutorial: Executar roteiro python "Hello World" Python'
titleSuffix: Azure Machine Learning
description: A parte 2 da série Azure ML Get Started mostra como submeter um roteiro trivial de python "hello world" à nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 52a9932db4fc261b8f3d740a316af3e852559a32
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320494"
---
# <a name="tutorial-run-hello-world-python-script-part-2-of-4"></a>Tutorial: Executar roteiro python "Hello World" (Parte 2 de 4)

Neste tutorial aprende-se a usar o Azure Machine Learning Python SDK para submeter e executar um script Python "Hello World".

Este tutorial é **parte dois de uma série tutorial** de quatro partes na qual você aprende os fundamentos do Azure Machine Learning e completa tarefas de aprendizagem automática baseadas em empregos em Azure. Este tutorial baseia-se no trabalho que concluiu na [parte 1 do Tutorial: crie a sua máquina local para a Azure Machine Learning](
tutorial-1st-experiment-sdk-setup-local.md).

Neste tutorial você:

> [!div class="checklist"]
> * Crie e execute um roteiro python "Hello World" localmente
> * Crie um script de controlo Python para submeter "Hello world" ao Azure Machine Learning
> * Compreenda os conceitos de Aprendizagem automática Azure no script de controlo
> * Submeter e executar "Hello World"
> * Veja a sua saída de código na nuvem

## <a name="prerequisites"></a>Pré-requisitos

- Complete [a Parte 1 do tutorial na configuração da máquina local](tutorial-1st-experiment-sdk-setup-local.md) se ainda não tiver um espaço de trabalho de Aprendizagem automática Azure.
- Conhecimento introdutório da língua python e fluxos de trabalho de aprendizagem automática.
- Ambiente de desenvolvimento local. Isto inclui, mas não se limita ao Visual Studio Code, Jupyter ou PyCharm.
- Python (versão 3.5-3.7).

## <a name="create-and-run-a-python-script-locally"></a>Criar e executar um script Python localmente

Crie um novo subdiretório chamado `src` sob o `tutorial` diretório para armazenar código que pretende executar num cluster de cálculo Azure Machine Learning. No `src` subdiretório criar o `hello.py` script Python:

```python
# src/hello.py
print("Hello world!")
```

A sua estrutura de diretório de projetos passará a ser:

```Bash
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
└──01-create-workspace.py
└──02-create-compute.py
```

### <a name="test-your-script-locally"></a>Teste o seu script localmente

Pode executar o seu código localmente, que tem o benefício da depuragem interativa do código, utilizando o seu IDE favorito ou através de um terminal:

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

## <a name="create-a-control-script"></a>Criar um script de controlo

Um *script de controlo* permite-lhe executar o seu script na `hello.py` nuvem.  O script de controlo permite-lhe controlar como e onde o seu código de aprendizagem automática é executado.  

No seu diretório tutorial, crie um novo ficheiro python chamado `03-run-hello.py` e copie e cole o código abaixo nesse ficheiro:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>Compreender o código

Aqui está uma descrição de como o script de controlo funciona:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [O espaço de trabalho](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) conecta-se ao seu espaço de trabalho de aprendizagem de máquinas Azure, para que possa comunicar com os seus recursos de Aprendizagem automática Azure.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [A experiência](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true) fornece uma forma simples de organizar várias corridas com um único nome. Mais tarde, pode ver como as experiências facilitam a comparação de métricas entre dezenas de corridas.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) embrulha o seu `hello.py` código e passa-o para o seu espaço de trabalho. Como o nome sugere, pode usar esta aula para _configurar_ como pretende que o seu _script_ _seja executado_ em Azure Machine Learning. Também especifica qual o alvo do cálculo em que o script será executado.  Neste código, o alvo é o cluster de cálculo que criou no tutorial de [configuração.](tutorial-1st-experiment-sdk-setup-local.md)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Submete o teu guião. Esta submissão chama-se [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true).  Uma corrida encapsula uma única execução do seu código. Use uma execução para monitorizar o progresso do script, capturar a saída, analisar os resultados, visualizar métricas e muito mais.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        O `run` objeto fornece uma pega na execução do seu código. Monitorize o seu progresso a partir do Azure Machine Learning Studio com o URL que é impresso a partir do script python.  
   :::column-end:::
:::row-end:::

## <a name="submit-and-run-your-code-in-the-cloud"></a>Submeta e execute o seu código na nuvem

Executa o teu script de controlo, que por sua vez funciona `hello.py` no cluster de cálculo que criaste no tutorial de [configuração.](tutorial-1st-experiment-sdk-setup-local.md)

```bash
python 03-run-hello.py
```

## <a name="monitor-your-code-in-the-cloud-using-studio"></a>Monitorize o seu código na nuvem usando o estúdio

A saída conterá uma ligação ao estúdio Azure Machine Learning que se parece com isto: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>` .

Siga o link e navegue para o **separador Saídas + registos.** Lá pode ver um ficheiro `70_driver_log.txt` como este:

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

Na linha 8, vê-se o "Olá mundo!" saída.

O `70_driver_log.txt` ficheiro contém a saída padrão de uma execução. Este ficheiro pode ser útil ao depurar as operações remotas na nuvem.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, pegaste num simples guião de "Olá mundo" e passaste-o no Azure. Viu como se conectar ao seu espaço de trabalho Azure Machine Learning, criar uma Experiência e submeter o seu `hello.py` código à nuvem.

No próximo tutorial, baseia-se nestas aprendizagens, executando algo mais interessante do `print("Hello world!")` que.

> [!div class="nextstepaction"]
> [Tutorial: preparar um modelo](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Se quiser terminar a série tutorial aqui e não progredir para o próximo passo, lembre-se de [limpar os seus recursos](tutorial-1st-experiment-bring-data.md#clean-up-resources)
