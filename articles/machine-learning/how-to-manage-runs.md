---
title: Iniciar, monitorizar e cancelar treinos em Python
titleSuffix: Azure Machine Learning
description: Aprenda a começar, definir o estado de, etiquetar e organizar as suas experiências de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.openlocfilehash: 8c261a010a1e8f4d1be9b3883510eb38c37a15ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296882"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Iniciar, monitorizar e cancelar treinos em Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

O Estúdio [Azure Machine Learning SDK para Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) [Machine Learning CLI](reference-azure-machine-learning-cli.md)e [Azure Machine Learning](https://ml.azure.com) fornecem vários métodos para monitorizar, organizar e gerir as suas corridas para treino e experimentação.

Este artigo apresenta exemplos das seguintes tarefas:

* Monitor desempenho de execução.
* Cancelar ou falhar corridas.
* Criar corridas infantis.
* Etiqueta e encontrar corridas.

## <a name="prerequisites"></a>Pré-requisitos

Vai precisar dos seguintes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Um [espaço de trabalho azure machine learning.](how-to-manage-workspace.md)

* O Azure Machine Learning SDK para Python (versão 1.0.21 ou posterior). Para instalar ou atualizar para a versão mais recente do SDK, consulte [Instalar ou atualizar o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Para verificar a sua versão do Azure Machine Learning SDK, utilize o seguinte código:

    ```python
    print(azureml.core.VERSION)
    ```

* A extensão [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) e [CLI para Aprendizagem automática Azure](reference-azure-machine-learning-cli.md).

## <a name="start-a-run-and-its-logging-process"></a>Inicie uma corrida e o seu processo de exploração madeireira

### <a name="using-the-sdk"></a>Utilizar o SDK

Crie a sua experiência importando as classes [Workspace,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) [Experiment,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)e [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) do pacote [azureml.core.](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py)

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Inicie uma corrida e o [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) seu processo de exploração madeireira com o método.

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>Usando o CLI

Para iniciar uma série da sua experiência, use os seguintes passos:

1. A partir de um pedido de cartão ou comando, utilize o Azure CLI para autenticar a sua assinatura Azure:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

1. Fixe uma configuração de espaço de trabalho à pasta que contém o seu script de treino. Substitua-o `myworkspace` pelo seu espaço de trabalho Azure Machine Learning. Substitua-o `myresourcegroup` pelo grupo de recursos Azure que contém o seu espaço de trabalho:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Este comando `.azureml` cria um subdiretório que contém ficheiros de ambiente runconfig e conda exemplo. Também contém `config.json` um ficheiro que é usado para comunicar com o seu espaço de trabalho Azure Machine Learning.

    Para mais informações, consulte a anexação da [pasta Az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Para iniciar a corrida, utilize o seguinte comando. Ao utilizar este comando, especifique o nome \*do ficheiro runconfig (o texto antes de .runconfig se estiver a olhar para o seu sistema de ficheiros) contra o parâmetro -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > O `az ml folder attach` comando `.azureml` criou um subdiretório, que contém dois exemplos de ficheiros runconfig.
    >
    > Se tiver um script Python que cria um objeto de configuração de execução programáticamente, pode utilizar [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) para guardá-lo como ficheiro runconfig.
    >
    > Para mais exemplo, ficheiros [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)runconfig, consulte .

    Para mais informações, consulte [az ml executar envie um sub-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

### <a name="using-azure-machine-learning-studio"></a>Usando o estúdio Azure Machine Learning

Para iniciar uma execução de pipeline no designer (pré-visualização), utilize os seguintes passos:

1. Detete um alvo de computação padrão para o seu oleoduto.

1. Selecione **Executar** na parte superior da tela do gasoduto.

1. Selecione uma Experiência para agrupar as suas corridas de gasodutos.

## <a name="monitor-the-status-of-a-run"></a>Monitorizar o estado de uma corrida

### <a name="using-the-sdk"></a>Utilizar o SDK

Obtenha o estado de [`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) uma corrida com o método.

```python
print(notebook_run.get_status())
```

Para obter o ID de execução, o tempo [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) de execução e detalhes adicionais sobre a execução, use o método.

```python
print(notebook_run.get_details())
```

Quando a sua corrida terminar [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) com sucesso, utilize o método para o marcar como concluído.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Se utilizar o `with...as` padrão de design da Python, o percurso marcar-se-á automaticamente como concluído quando a execução estiver fora de alcance. Não precisa de marcar manualmente a corrida como concluída.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>Usando o CLI

1. Para ver uma lista de corridas para a sua experiência, use o seguinte comando. Substitua-o `experiment` pelo nome da sua experiência:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    Este comando devolve um documento JSON que lista informações sobre execuções para esta experiência.

    Para mais informações, consulte a lista de [experiências az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Para visualizar informações sobre uma execução específica, utilize o seguinte comando. Substitua-a `runid` pela identificação da execução:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    Este comando devolve um documento JSON que lista informações sobre a execução.

    Para mais informações, consulte [az ml run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).


### <a name="using-azure-machine-learning-studio"></a>Usando o estúdio Azure Machine Learning

Para ver o número de corridas ativas para a sua experiência no estúdio.

1. Navegue para a secção **de Experiências.** 

1. Selecione uma experiência.

    Na página da experiência, pode ver o número de alvos de computação ativa e a duração de cada execução. 

1. Selecione um número de execução específico.

1. No separador **Logs,** pode encontrar registos de diagnóstico e erro para o seu pipeline.


## <a name="cancel-or-fail-runs"></a>Cancelar ou falhar corridas

Se notar um erro ou se a sua corrida estiver a demorar muito tempo a terminar, pode cancelar a corrida.

### <a name="using-the-sdk"></a>Utilizar o SDK

Para cancelar uma execução utilizando o [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) SDK, utilize o método:

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

Se a sua execução terminar, mas contém um erro (por exemplo, [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) o script de treino incorreto foi usado), pode usar o método para o marcar como falhado.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>Usando o CLI

Para cancelar uma corrida utilizando o CLI, utilize o seguinte comando. Substitua-a `runid` com a identificação da corrida

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Para mais informações, consulte [az ml de cancelamento](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

### <a name="using-azure-machine-learning-studio"></a>Usando o estúdio Azure Machine Learning

Para cancelar uma corrida no estúdio, utilizando os seguintes passos:

1. Vá para o gasoduto de corrida na secção **Experiências** ou **Gasodutos.** 

1. Selecione o número de execução do gasoduto que pretende cancelar.

1. Na barra de ferramentas, selecione **Cancelar**


## <a name="create-child-runs"></a>Criar corridas infantis

Criar corridas para agrupar as corridas relacionadas com a criança, tais como para diferentes iterações de afinação de hiperparâmetros.

> [!NOTE]
> As corridas infantis só podem ser criadas usando o SDK.

Este exemplo de `hello_with_children.py` código usa o script para criar um lote de [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) cinco crianças que corre dentro de uma execução submetida usando o método:

```python
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
> À medida que se movem fora de alcance, as corridas de crianças são automaticamente marcadas como concluídas.

Para criar muitas crianças [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) corre eficientemente, use o método. Porque cada criação resulta numa chamada de rede, criar um lote de execuções é mais eficiente do que criá-los um a um.

### <a name="submit-child-runs"></a>Submeter corridas de crianças

As corridas de crianças também podem ser submetidas a partir de uma execução dos pais. Isto permite criar hierarquias de corridas de pais e filhos, cada uma correndo em diferentes alvos de computação, conectados por id de execução comum dos pais.

Utilize o método ['submit_child()'](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#submit-child-config--tags-none----kwargs-) para submeter uma criança a partir de uma execução dos pais. Para isso no roteiro de execução dos pais, obtenha ``submit_child`` o contexto de execução e submeta a criança executar usando o método da instância de contexto.

```python
## In parent run script
parent_run = Run.get_context()
child_run_config = ScriptRunConfig(source_directory='.', script='child_script.py')
parent_run.submit_child(child_run_config)
```

Dentro de uma corrida de crianças, você pode ver o ID executar dos pais:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Pergunta criança corre

Para consultar a criança corre de um [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) progenitor específico, use o método. O ``recursive = True`` argumento permite-lhe consultar uma árvore aninhada de crianças e netos.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Marque e encontre corridas

No Azure Machine Learning, pode utilizar propriedades e tags para ajudar a organizar e consultar as suas corridas para obter informações importantes.

### <a name="add-properties-and-tags"></a>Adicione propriedades e tags

#### <a name="using-the-sdk"></a>Utilizar o SDK

Para adicionar metadados pesquisáveis [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) às suas execuções, utilize o método. Por exemplo, o seguinte `"author"` código adiciona a propriedade à execução:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

As propriedades são imutáveis, por isso criam um registo permanente para fins de auditoria. O seguinte exemplo de código resulta `"azureml-user"` num `"author"` erro, porque já adicionámos como valor de propriedade no código anterior:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Ao contrário das propriedades, as etiquetas são mutáveis. Para adicionar informações pesquisáveis e significativas para os consumidores da sua experiência, utilize o [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) método.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Também pode adicionar etiquetas de corda simples. Quando estas etiquetas aparecem no dicionário de etiquetas `None`como chaves, têm um valor de .

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>Usando o CLI

> [!NOTE]
> Utilizando o CLI, só pode adicionar ou atualizar etiquetas.

Para adicionar ou atualizar uma etiqueta, utilize o seguinte comando:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Para mais informações, consulte [a az ml de atualização](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)de execução .

### <a name="query-properties-and-tags"></a>Propriedades e etiquetas de consulta

Você pode consultar executa dentro de uma experiência para devolver uma lista de execuções que correspondem a propriedades e etiquetas específicas.

#### <a name="using-the-sdk"></a>Utilizar o SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>Usando o CLI

O Azure CLI suporta consultas [JMESPath,](http://jmespath.org) que podem ser usadas para filtrar execuções com base em propriedades e etiquetas. Para utilizar uma consulta JMESPath com o CLI `--query` Azure, especifique-a com o parâmetro. Os seguintes exemplos mostram consultas básicas utilizando propriedades e etiquetas:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Para obter mais informações sobre a consulta dos resultados do Azure CLI, consulte a saída de [comando DoCe Azure CLI](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).

### <a name="using-azure-machine-learning-studio"></a>Usando o estúdio Azure Machine Learning

1. Navegue para a secção **Pipelines.**

1. Utilize a barra de pesquisa para filtrar os gasodutos utilizando etiquetas, descrições, nomes de experiências e nomes de apresentadores.

## <a name="example-notebooks"></a>Exemplo de cadernos

Os seguintes cadernos demonstram os conceitos deste artigo:

* Para saber mais sobre as APIs de exploração, consulte o [caderno API de registo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Para obter mais informações sobre gestão de corridas com o Azure Machine Learning SDK, consulte o [caderno de gestão executa](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Passos seguintes

* Para aprender a registar métricas para as suas experiências, consulte métricas de [log durante os treinos](how-to-track-experiments.md).
* Para aprender a monitorizar recursos e registos da Azure Machine Learning, consulte a Monitorização de [Machine Learning Azure](monitor-azure-machine-learning.md).