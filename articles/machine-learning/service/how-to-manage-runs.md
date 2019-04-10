---
title: Iniciar, monitorizar e cancelar execuções de preparação em Python
titleSuffix: Azure Machine Learning service
description: Saiba como iniciar, estado, etiqueta e organizar suas experimentações de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 4/5/2019
ms.openlocfilehash: 726273024a2da0cea5207c86140f3c31263a208f
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426746"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Iniciar, monitorizar e cancelar execuções de preparação em Python

O [do Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) fornece vários métodos para monitorizar, organizar e gerenciar suas execuções de formação e experimentação.

Nesta explicação de procedimento mostra exemplos das seguintes tarefas:

* [Monitor de desempenho de execução](#monitor)
* [Cancelar ou efetuar a ativação de execuções](#cancel)
* [Criar subordinado é executado](#children)
* [Marcar e encontrar execuções](#tag)

## <a name="prerequisites"></a>Pré-requisitos

Precisará do seguinte:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Uma área de trabalho de serviço do Azure Machine Learning. Ver [criar uma área de trabalho do serviço do Azure Machine Learning](setup-create-workspace.md).

* O Azure Machine Learning SDK para Python instalada (versão 1.0.21 ou posterior). Para instalar ou atualizar para a versão mais recente do SDK, vá para o [instalar/atualizar o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) página.

    Para verificar a sua versão do SDK do Azure Machine Learning, utilize o seguinte código.

    ```Python
    print(azureml.core.VERSION)
    ```

<a name="monitor"></a>

## <a name="start-and-status-a-run"></a>Início e de estado de uma execução

Configurar a sua experimentação ao importar o [área de trabalho](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [experimentar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [executar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py), e [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) classes a partir do [azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) pacote.

```Python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Iniciar uma execução e seu processo de registo com o [ `start_logging()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) método.

```Python
notebook_run = exp.start_logging()

notebook_run.log(name="message", value="Hello from run!")
```

Obter o estado da execução com [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--).

```Python
print(notebook_run.get_status())
```

Para obter detalhes adicionais sobre a utilização de execução [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--).

```Python
notebook_run.get_details()
```

Quando a execução for concluída com êxito, utilize o [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) método para marcá-lo como concluído.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Também pode utilizar de Python `with...as` padrão. Neste contexto, a execução será automaticamente marcar em si como concluída quando a execução está fora do âmbito. Dessa forma não tem de marcar manualmente a execução como concluído.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

<a name="cancel"></a>

## <a name="cancel-or-fail-runs"></a>Cancelar ou efetuar a ativação de execuções

 Se observar um erro ou a execução parece estar a demorar tempo para concluir, utilize o [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) método para parar a execução antes de concluir e marcá-la como canceladas.

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Se a execução for concluída, mas contém um erro, como, utilizou o script de treinamento incorreto, pode utilizar o [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) método para marcá-la como falhada.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

<a name="children"></a>

## <a name="create-child-runs"></a>Criar subordinado é executado

Crie subordinado é executado para agrupar execuções relacionadas, como para diferentes hiper-parâmetros iterações de otimização.

Este exemplo de código utiliza o script de hello_with_children.py para criar um lote de cinco execuções de subordinados no uso execução submetido a [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) método.

```Python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Subordinado é executado concluído automaticamente quando eles passam fora do escopo.

Também pode iniciar filho execuções individualmente, mas uma vez que cada criação resulta numa chamada de rede, é menos eficiente do que enviando um lote de execuções.

Utilize o [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) executa o método para consultar o filho de um pai específico.

```Python
list(parent_run.get_children())
```

<a name="tag"></a>

## <a name="tag-and-find-runs"></a>Marcar e encontrar execuções

No serviço do Azure Machine Learning, pode utilizar etiquetas e propriedades para o ajudar a organizar e consultar suas execuções para obter informações importantes.

### <a name="add-properties-and-tags"></a>Adicionar etiquetas e propriedades

Utilize o [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) adicionar metadados pesquisáveis para suas execuções. Por exemplo, o código a seguir adiciona a propriedade de "autor" para a execução.

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Propriedades são imutáveis, o que é útil como um registro permanente para fins de auditoria. O exemplo de código seguinte irá resultar num erro, uma vez que estamos já adicionada "azureml utilizador" como a propriedade de "autor" no código anterior.

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

No entanto etiquetas, são alteráveis. Uso [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) para adicionar informações de pesquisáveis e significativas para os consumidores da sua experimentação.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Também pode adicionar uma etiqueta de cadeia de caracteres simples. Ele aparece no dicionário com o valor de etiqueta `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>Etiquetas e propriedades de consulta

Pode consultar é executado dentro de uma experimentação que correspondem a etiquetas e propriedades específicas.

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Blocos de notas de exemplo

Os seguintes blocos de notas demonstram os conceitos deste artigo:

* Para saber mais sobre APIs de registro, consulte a [bloco de notas do registo API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Para obter mais informações sobre a gestão é executado com o SDK do Azure Machine Learning, consulte a [Gerir execuções de bloco de notas](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Passos Seguintes

* Para saber como iniciar sessão métricas para suas experimentações, veja a [métricas de registo durante as execuções de preparações](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiment) artigo.