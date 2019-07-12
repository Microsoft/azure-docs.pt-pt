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
ms.openlocfilehash: a67ac07c26063b380bda2b8cb2b6a02677e7f816
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656190"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Iniciar, monitorizar e cancelar execuções de preparação em Python

O [do Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) e [da CLI do Machine Learning](reference-azure-machine-learning-cli.md) fornecem vários métodos para monitorizar, organizar e gerenciar suas execuções de formação e experimentação.

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

* O [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) e [extensão da CLI para o serviço Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="start-a-run-and-its-logging-process"></a>Iniciar uma execução e seu processo de registo

### <a name="using-the-sdk"></a>Utilizar o SDK

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

### <a name="using-the-cli"></a>Com a CLI

Para iniciar uma execução da sua experimentação, utilize os seguintes passos:

1. A partir de um shell ou a linha de comandos, utilize a CLI do Azure para autenticar a sua subscrição do Azure:

    ```azurecli-interactive
    az login
    ```

1. Anexe uma configuração de área de trabalho para a pasta que contém o script de treinamento. Substitua `myworkspace` com sua área de trabalho do serviço do Azure Machine Learning. Substitua `myresourcegroup` com o grupo de recursos do Azure que contém a sua área de trabalho:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Este comando cria um `.azureml` subdiretório que contém ficheiros de ambiente de runconfig e conda de exemplo. Também contém um `config.json` ficheiro que é utilizado para comunicar com a sua área de trabalho do Azure Machine Learning.

    Para obter mais informações, consulte [anexar a pasta de ml az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Para iniciar a execução, utilize o seguinte comando. Quando utilizar este comando, especifique o nome do ficheiro runconfig (o texto antes do \*.runconfig se estiver à procura no seu sistema de ficheiros) contra o parâmetro - c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > O `az ml folder attach` comando criado um `.azureml` subdiretório, que contém dois arquivos de runconfig de exemplo. 
    >
    > Se tiver um script de Python que cria um objeto de configuração de execução por meio de programação, pode usar [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) salvá-lo como um ficheiro de runconfig.
    >
    > Para mais arquivos de runconfig de exemplo, consulte [ https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml ](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml).

    Para obter mais informações, consulte [az ml executar script submeter](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

## <a name="monitor-the-status-of-a-run"></a>Monitorizar o estado de uma execução

### <a name="using-the-sdk"></a>Utilizar o SDK

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

### <a name="using-the-cli"></a>Com a CLI

1. Para ver uma lista de execuções para a experimentação, utilize o seguinte comando. Substitua `experiment` com o nome da sua experimentação:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    Este comando devolve um documento JSON que lista as informações sobre execuções para esta fase experimental.

    Para obter mais informações, consulte [lista de experimentação do az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Para ver informações sobre uma execução específica, utilize o seguinte comando. Substitua `runid` com o ID da execução:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    Este comando devolve um documento JSON que lista as informações sobre a execução.

    Para obter mais informações, consulte [az ml executar show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).

## <a name="cancel-or-fail-runs"></a>Cancelar ou efetuar a ativação de execuções

Se observar um erro ou se estiver a demorar demasiado tempo a concluir sua execução, pode cancelar a execução.

### <a name="using-the-sdk"></a>Utilizar o SDK

Para cancelar uma execução com o SDK, utilize o [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) método:

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Se a execução for concluída, mas contém um erro (por exemplo, o script de treinamento incorreta foi usado), pode utilizar o [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) método para marcá-la como falhada.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>Com a CLI

Para cancelar uma execução com a CLI, utilize o seguinte comando. Substitua `runid` com o ID da execução

```azurecli-interactive
az ml run cancel -r runid
```

Para obter mais informações, consulte [az ml executar Cancelar](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

## <a name="create-child-runs"></a>Criar subordinado é executado

Crie subordinado é executado para agrupar execuções relacionadas, como para iterações de otimização de hiper-parâmetros diferentes.

> [!NOTE]
> Execuções de subordinado só podem ser criadas com o SDK.

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

#### <a name="using-the-sdk"></a>Utilizar o SDK

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

#### <a name="using-the-cli"></a>Com a CLI

> [!NOTE]
> Com a CLI, só pode adicionar ou atualizar as etiquetas.

Para adicionar ou atualizar uma etiqueta, utilize o seguinte comando:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Para obter mais informações, consulte [executar o az ml update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).

### <a name="query-properties-and-tags"></a>Etiquetas e propriedades de consulta

Pode consultar é executado dentro de uma experimentação para devolver uma lista de execuções que correspondem a etiquetas e propriedades específicas.

#### <a name="using-the-sdk"></a>Utilizar o SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>Com a CLI

A CLI do Azure suporta [JMESPath](http://jmespath.org) consultas, que podem ser utilizadas para filtrar com base em propriedades e etiquetas de execuções. Para utilizar uma consulta JMESPath com a CLI do Azure, especifique-o com o `--query` parâmetro. Os exemplos seguintes mostram as consultas básicas com etiquetas e propriedades:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Para obter mais informações sobre como consultar resultados de CLI do Azure, consulte [saída do comando da CLI do Azure de consulta](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).

## <a name="example-notebooks"></a>Blocos de notas de exemplo

Os seguintes blocos de notas demonstram os conceitos deste artigo:

* Para saber mais sobre as APIs de log, consulte a [bloco de notas do registo API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Para obter mais informações sobre a gestão é executado com o SDK do Azure Machine Learning, consulte a [Gerir execuções de bloco de notas](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Passos Seguintes

* Para saber como iniciar sessão métricas para suas experimentações, veja [métricas de registo durante as execuções de preparações](how-to-track-experiments.md).
