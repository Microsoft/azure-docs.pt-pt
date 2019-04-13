---
title: Iniciar, monitorizar e cancelar execuções de preparação em Python
titleSuffix: Azure Machine Learning service
description: Saiba como iniciar, definir o estado da marca e organizar suas experimentações do machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: c0c1c1353b12944fa913dfb0789192917b99f234
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543598"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Iniciar, monitorizar e cancelar execuções de preparação em Python

O [do Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) fornece vários métodos para monitorizar, organizar e gerenciar suas execuções de formação e experimentação.

Este artigo mostra exemplos das seguintes tarefas:

* Monitor de desempenho de execução.
* Cancelar ou efetuar a ativação é executada.
* Crie subordinado é executado.
* Marcar e encontrar execuções.

## <a name="prerequisites"></a>Pré-requisitos

Terá dos seguintes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Uma [área de trabalho do Azure Machine Learning serviço](setup-create-workspace.md).

* O Azure Machine Learning SDK para Python (versão 1.0.21 ou posterior). Para instalar ou atualizar para a versão mais recente do SDK, consulte [instalar ou atualizar o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Para verificar a sua versão do SDK do Azure Machine Learning, utilize o seguinte código:

    ```Python
    print(azureml.core.VERSION)
    ```

## <a name="start-a-run-and-its-logging-process"></a>Iniciar uma execução e seu processo de registo

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

## <a name="monitor-the-status-of-a-run"></a>Monitorizar o estado de uma execução

Obter o estado de uma execução com o [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) método.

```Python
print(notebook_run.get_status())
```

Para obter detalhes adicionais sobre a execução, utilize o [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) método.

```Python
notebook_run.get_details()
```

Quando a execução for concluída com êxito, utilize o [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) método para marcá-lo como concluído.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Se usar do Python `with...as` padrão, a execução será automaticamente marcar si própria como concluída quando a execução está fora do âmbito. Não tem de marcar manualmente a execução como concluído.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

## <a name="cancel-or-fail-runs"></a>Cancelar ou efetuar a ativação de execuções

 Se observar um erro ou se a sua execução está a demorar demasiado tempo a concluir, utilize o [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) método para parar a execução antes de concluir e marcá-la como canceladas.

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Se a execução for concluída, mas contém um erro (por exemplo, o script de treinamento incorreta foi usado), pode utilizar o [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) método para marcá-la como falhada.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

## <a name="create-child-runs"></a>Criar subordinado é executado

Crie subordinado é executado para agrupar execuções relacionadas, como para iterações de otimização de hiper-parâmetros diferentes.

Este exemplo de código utiliza a `hello_with_children.py` script para criar um lote de cinco execuções de subordinado de dentro de uma execução submetida ao utilizar o [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) método:

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
> Quando eles passam fora do escopo, as execuções de subordinados são automaticamente marcadas como concluídos.

Também pode iniciar filho execuções individualmente, mas uma vez que cada criação resulta numa chamada de rede, é menos eficiente do que enviando um lote de execuções.

Para consultar as execuções de subordinados de um pai específico, utilize o [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) método.

```Python
list(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Marcar e encontrar execuções

No serviço do Azure Machine Learning, pode utilizar etiquetas e propriedades para o ajudar a organizar e consultar suas execuções para obter informações importantes.

### <a name="add-properties-and-tags"></a>Adicionar etiquetas e propriedades

Para adicionar metadados pesquisáveis para suas execuções, utilize o [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) método. Por exemplo, o código a seguir adiciona o `"author"` propriedade para a execução:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Propriedades são imutáveis, para que eles criam um registro permanente para fins de auditoria. O seguinte código resultados de exemplo num erro, uma vez que já adicionamos `"azureml-user"` como o `"author"` valor da propriedade no código anterior:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Ao contrário das propriedades, as etiquetas são alteráveis. Para adicionar informações de pesquisáveis e significativas para os consumidores da sua experimentação, utilize o [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) método.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Também pode adicionar etiquetas de cadeia de caracteres simples. Quando essas marcas aparecem no dicionário de marca, eles têm um valor de `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>Etiquetas e propriedades de consulta

Pode consultar é executado dentro de uma experimentação para devolver uma lista de execuções que correspondem a etiquetas e propriedades específicas.

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Blocos de notas de exemplo

Os seguintes blocos de notas demonstram os conceitos deste artigo:

* Para saber mais sobre as APIs de log, consulte a [bloco de notas do registo API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Para obter mais informações sobre a gestão é executado com o SDK do Azure Machine Learning, consulte a [Gerir execuções de bloco de notas](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Passos Seguintes

* Para saber como iniciar sessão métricas para suas experimentações, veja [métricas de registo durante as execuções de preparações](how-to-track-experiments.md).