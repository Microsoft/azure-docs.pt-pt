---
title: 'Tutorial: Executar um "Olá mundo!" Script de Python'
titleSuffix: Azure Machine Learning
description: A 2ª parte da série Azure Machine Learning mostra como submeter um trivial "Olá mundo!" Roteiro python para a nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 43a483f49a9e9004a4f487e82195198f2600a919
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071158"
---
# <a name="tutorial-run-a-hello-world-python-script-part-2-of-4"></a>Tutorial: Executar um "Olá mundo!" Escrita python (parte 2 de 4)

Neste tutorial, aprende-se a usar o Azure Machine Learning SDK para python para submeter e executar um Python "Olá mundo!" roteiro.

Este tutorial é *parte 2 de uma série tutorial* de quatro partes na qual você aprende os fundamentos do Azure Machine Learning e completa tarefas de aprendizagem automática baseadas em empregos em Azure. Este tutorial baseia-se no trabalho que concluiu na [Parte 1: Crie a sua máquina local para a Azure Machine Learning](tutorial-1st-experiment-sdk-setup-local.md).

Neste tutorial, vai:

> [!div class="checklist"]
> * Criar e executar um "Olá mundo!" Python roteiro localmente.
> * Crie um script de controlo Python para submeter "Olá mundo!" para Azure Machine Learning.
> * Compreenda os conceitos de Aprendizagem automática Azure no script de controlo.
> * Submeta-se e corra o "Olá mundo!" roteiro.
> * Veja a saída do seu código na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

- Conclusão da [parte 1](tutorial-1st-experiment-sdk-setup-local.md) se ainda não tiver um espaço de trabalho de Aprendizagem automática Azure.

## <a name="create-and-run-a-python-script-locally"></a>Criar e executar um script Python localmente

Crie um novo subdiretório chamado `src` sob o `tutorial` diretório para armazenar código que pretende executar num cluster de cálculo Azure Machine Learning. Na `src` subdiretória, crie o `hello.py` script Python:

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


### <a name="test-your-script-locally"></a><a name="test"></a>Teste o seu script localmente

Pode executar o seu código localmente, utilizando o seu IDE favorito ou um terminal. O código de execução local tem o benefício da depuragem interativa do código.  Na janela que tem o ambiente *de conda tutorial1* ativado, execute o ficheiro Python:

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

> [!div class="nextstepaction"]
> [Corri o guião localmente](?success=run-local#control-script) [encontrei um problema](https://www.research.net/r/7C2NTH7?issue=run-local)

## <a name="create-a-control-script"></a><a name="control-script"></a> Criar um script de controlo

Um *script de controlo* permite-lhe executar o seu script na `hello.py` nuvem. Usa o script de controlo para controlar como e onde o seu código de aprendizagem automática é executado.  

No seu diretório tutorial, crie um novo ficheiro Python chamado `03-run-hello.py` e copie/cole o seguinte código nesse ficheiro:

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
      [O espaço de trabalho](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) conecta-se ao seu espaço de trabalho de aprendizagem de máquinas Azure, para que possa comunicar com os seus recursos de Aprendizagem automática Azure.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [A experiência](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py) fornece uma forma simples de organizar várias corridas com um único nome. Mais tarde, pode ver como as experiências facilitam a comparação de métricas entre dezenas de corridas.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) embrulha o seu `hello.py` código e passa-o para o seu espaço de trabalho. Como o nome sugere, pode usar esta aula para _configurar_ como pretende que o seu _script_ _seja executado_ em Azure Machine Learning. Também especifica qual o alvo do cálculo em que o script será executado. Neste código, o alvo é o cluster de cálculo que criou no tutorial de [configuração.](tutorial-1st-experiment-sdk-setup-local.md)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Submete o teu guião. Esta submissão chama-se [corrida.](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) Uma corrida encapsula uma única execução do seu código. Use uma corrida para monitorizar o progresso do script, capturar a saída, analisar os resultados, visualizar métricas e muito mais.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        O `run` objeto fornece uma pega na execução do seu código. Monitorize o seu progresso a partir do estúdio Azure Machine Learning com o URL impresso a partir do script Python.  
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Criei o guião de controlo](?success=create-control-script#submit) [que encontrei num problema.](https://www.research.net/r/7C2NTH7?issue=create-control-script)

## <a name="submit-and-run-your-code-in-the-cloud"></a><a name="submit"></a> Submeta e execute o seu código na nuvem

Executa o teu script de controlo, que por sua vez funciona `hello.py` no cluster de cálculo que criaste no tutorial de [configuração](tutorial-1st-experiment-sdk-setup-local.md).


```bash
python 03-run-hello.py
```

> [!TIP]
> Se executar este código lhe dá um erro no qual não tem acesso à subscrição, consulte [Ligar a um espaço de trabalho](how-to-manage-workspace.md?tab=python#connect-multi-tenant) para obter informações sobre opções de autenticação.

> [!div class="nextstepaction"]
> [Apresentei código na nuvem](?success=submit-to-cloud#monitor) [e dei conta de um problema.](https://www.research.net/r/7C2NTH7?issue=submit-to-cloud)

## <a name="monitor-your-code-in-the-cloud-by-using-the-studio"></a><a name="monitor"></a>Monitorize o seu código na nuvem usando o estúdio

A saída do seu script conterá um link para o estúdio que se parece com isto: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>` .

Siga o link.  No início, verá um estado de **preparação.**  A primeira corrida levará 5 a 10 minutos para ser concluída. Isto porque ocorre o seguinte:

* Uma imagem de estiva é construída na nuvem
* O cluster computacional é redimensionado de 0 a 1 nó
* A imagem do estivador é transferida para o cálculo. 

As execuções subsequentes são muito mais rápidas (~15 segundos) uma vez que a imagem do estivador é em cache no cálculo. Pode testá-lo reenviando o código abaixo depois de concluído o primeiro ensaio.

Assim que o trabalho estiver concluído, vá ao **separador Saídas + registos.** Lá pode ver um `70_driver_log.txt` ficheiro que se parece com este:

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

O `70_driver_log.txt` ficheiro contém a saída padrão de uma execução. Este ficheiro pode ser útil quando está a depurar as remotas corridas na nuvem.

> [!div class="nextstepaction"]
> [Vi o tronco no estúdio.](?success=monitor-in-studio#next-steps) [](https://www.research.net/r/7C2NTH7?issue=monitor-in-studio)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fizeste um simples "Olá mundo!" guião e correu no Azure. Viu como se conectar ao seu espaço de trabalho Azure Machine Learning, criar uma experiência e submeter o seu `hello.py` código à nuvem.

No próximo tutorial, baseia-se nestas aprendizagens, executando algo mais interessante do `print("Hello world!")` que.

> [!div class="nextstepaction"]
> [Tutorial: preparar um modelo](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Se quiser terminar a série tutorial aqui e não avançar para o próximo passo, lembre-se de [limpar os seus recursos.](tutorial-1st-experiment-bring-data.md#clean-up-resources)