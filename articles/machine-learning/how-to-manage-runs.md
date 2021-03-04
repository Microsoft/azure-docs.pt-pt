---
title: Iniciar, monitorizar e cancelar treinos em Python
titleSuffix: Azure Machine Learning
description: Aprenda a iniciar, status e gerir a sua experiência de aprendizagem automática funciona com o Azure Machine Learning Python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: fb877b208e6c9599347e2b45e140545e5815e695
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120815"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Iniciar, monitorizar e cancelar treinos em Python

O [Azure Machine Learning SDK para Python,](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) [Machine Learning CLI](reference-azure-machine-learning-cli.md)e a [Azure Machine Learning studio](https://ml.azure.com) fornecem vários métodos para monitorizar, organizar e gerir as suas corridas para treino e experimentação.

Este artigo mostra exemplos das seguintes tarefas:

* Monitorize o desempenho da execução.
* Cancelar ou falhar corre.
* Criar corridas de crianças.
* Marque e encontre corridas.

> [!TIP]
> Se procura informações sobre a monitorização do serviço de Aprendizagem automática Azure e dos serviços associados da Azure, consulte [Como monitorizar a Azure Machine Learning](monitor-azure-machine-learning.md).
> Se estiver à procura de informações sobre modelos de monitorização implementados como serviços web ou módulos IoT Edge, consulte [recolher dados do modelo](how-to-enable-data-collection.md) e [monitorar com insights de aplicações](how-to-enable-app-insights.md).

## <a name="prerequisites"></a>Pré-requisitos

Vai precisar dos seguintes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Um [espaço de trabalho de aprendizagem automática Azure.](how-to-manage-workspace.md)

* O Azure Machine Learning SDK para Python (versão 1.0.21 ou mais tarde). Para instalar ou atualizar para a versão mais recente do SDK, consulte [instalar ou atualizar o SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).

    Para verificar a sua versão do Azure Machine Learning SDK, utilize o seguinte código:

    ```python
    print(azureml.core.VERSION)
    ```

* A extensão [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest) e [CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="monitor-run-performance"></a>Monitor de execução

* Inicie uma corrida e o seu processo de registo

    # <a name="python"></a>[Python](#tab/python)
    
    1. Desconfie da sua experiência importando as classes [Workspace,](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) [Experiment,](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py) [Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py)e [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) a partir do pacote [azureml.core.](/python/api/azureml-core/azureml.core?preserve-view=true&view=azure-ml-py)
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. Inicie uma corrida e o seu processo de registo com o [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-) método.
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    Para iniciar uma série da sua experiência, utilize os seguintes passos:
    
    1. A partir de uma concha ou pedido de comando, utilize o CLI Azure para autenticar a sua subscrição Azure:
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. Fixe uma configuração de espaço de trabalho à pasta que contém o seu script de treino. `myworkspace`Substitua-o pelo seu espaço de trabalho de aprendizagem da máquina Azure. `myresourcegroup`Substitua-se pelo grupo de recursos Azure que contém o seu espaço de trabalho:
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        Este comando cria uma `.azureml` subdiretória que contém ficheiros de ambiente runconfig e conda. Também contém um `config.json` ficheiro que é usado para comunicar com o seu espaço de trabalho Azure Machine Learning.
    
        Para obter mais informações, consulte [o az ml pasta anexa .](/cli/azure/ext/azure-cli-ml/ml/folder?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)
    
    2. Para iniciar a execução, utilize o seguinte comando. Ao utilizar este comando, especifique o nome do ficheiro runconfig (o texto antes \* de .runconfig se estiver a olhar para o seu sistema de ficheiros) contra o parâmetro -c.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > O `az ml folder attach` comando criou uma `.azureml` subdiretório, que contém dois ficheiros runconfig de exemplo.
        >
        > Se tiver um script Python que crie um objeto de configuração de execução programáticamente, pode utilizar [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=truesave-path-none--name-none--separate-environment-yaml-false-) para o guardar como ficheiro runconfig.
        >
        > Para mais ficheiros runconfig, consulte [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) .
    
        Para obter mais informações, consulte [o roteiro de submissão az ml run](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

    # <a name="studio"></a>[Studio](#tab/azure-studio)

    Para um exemplo de formação de um modelo no designer Azure Machine Learning, consulte [Tutorial: Predict Automobile price with the designer](tutorial-designer-automobile-price-train-score.md).

    ---

* Monitorizar o estado de uma corrida

    # <a name="python"></a>[Python](#tab/python)
    
    * Obtenha o estado de uma corrida com o [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-status--) método.
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * Para obter o ID de execução, tempo de execução e detalhes adicionais sobre a execução, use o [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-details--) método.
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * Quando a sua execução terminar com sucesso, utilize o [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) método para o marcar como concluído.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Se utilizar o padrão de design da `with...as` Python, a execução marcar-se-á automaticamente como concluída quando a execução estiver fora de alcance. Não é preciso marcar manualmente a execução como concluído.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    * Para visualizar uma lista de execuções para a sua experiência, use o seguinte comando. `experiment`Substitua-o pelo nome da sua experiência:
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        Este comando devolve um documento JSON que lista informações sobre execuções para esta experiência.
    
        Para mais informações, consulte [a lista de experiências az ml](/cli/azure/ext/azure-cli-ml/ml/experiment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).
    
    * Para visualizar informações numa execução específica, utilize o seguinte comando. `runid`Substitua-a pelo ID da execução:
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        Este comando devolve um documento JSON que lista informações sobre a execução.
    
        Para mais informações, consulte [o az ml run show](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).
    
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Para ver as suas corridas no estúdio: 
    
    1. Navegue para o **separador Experimentos.**
    
    1. Selecione **todas as experiências** para visualizar todas as corridas numa experiência ou selecione **Todas as corre** para ver todas as correções submetidas no Espaço de Trabalho.
    
        Na página **'All runs',** pode filtrar a lista de execuções por tags, experiências, metas de computação e muito mais para organizar e estender melhor o seu trabalho.  
    
    1. Faça personalizações na página selecionando runs para comparar, adicionar gráficos ou aplicar filtros. Estas alterações podem ser guardadas como uma **Vista Personalizada** para que possa facilmente voltar ao seu trabalho. Os utilizadores com permissões de espaço de trabalho podem editar ou visualizar a vista personalizada. Além disso, partilhe a visão personalizada com os membros da equipa para uma colaboração reforçada selecionando a **vista Share**.   
    
        :::image type="content" source="media/how-to-manage-runs/custom-views.gif" alt-text="Screenshot: criar uma vista personalizada":::
    
    1. Para visualizar os registos de execução, selecione uma execução específica e no separador **Saídas + registos,** pode encontrar registos de diagnóstico e erro para a sua execução.
    
    ---

## <a name="run-description"></a>Executar descrição 

Uma descrição de execução pode ser adicionada a uma corrida para fornecer mais contexto e informações para a execução. Também pode pesquisar nestas descrições a partir da lista de execuções e adicionar a descrição da execução como uma coluna na lista de execuções. 

Navegue na página **'Detalhes de execução'** para a sua execução e selecione o ícone de edição ou lápis para adicionar, editar ou apagar descrições para a sua execução. Para persistir nas alterações à lista de execuções, guarde as alterações na sua Visualização Personalizada existente ou numa nova Vista Personalizada. O formato markdown é suportado para descrições de execução que permitem que as imagens sejam incorporadas e ligações profundas como mostrado abaixo.

:::image type="content" source="media/how-to-manage-runs/run-description.gif" alt-text="Screenshot: criar uma descrição de execução"::: 

## <a name="tag-and-find-runs"></a>Etiquetar e encontrar corridas

No Azure Machine Learning, pode utilizar propriedades e tags para ajudar a organizar e consultar as suas corridas para obter informações importantes.

* Adicionar propriedades e etiquetas

    # <a name="python"></a>[Python](#tab/python)
    
    Para adicionar metadados pesjáveis às suas execuções, utilize o [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueadd-properties-properties-) método. Por exemplo, o seguinte código adiciona a `"author"` propriedade à execução:
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    As propriedades são imutáveis, por isso criam um registo permanente para fins de auditoria. O exemplo de código a seguir resulta num erro, pois já adicionámos `"azureml-user"` como `"author"` valor patrimonial no código anterior:
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    Ao contrário das propriedades, as etiquetas são mutáveis. Para adicionar informações pes pes que pesjáveis e significativas para os consumidores da sua experiência, utilize o [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truetag-key--value-none-) método.
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    Também pode adicionar etiquetas de corda simples. Quando estas etiquetas aparecem no dicionário de etiquetas como teclas, têm um valor de `None` .
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    > [!NOTE]
    > Utilizando o CLI, só é possível adicionar ou atualizar tags.
    
    Para adicionar ou atualizar uma etiqueta, utilize o seguinte comando:
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    Para mais informações, consulte [a atualização de execução az ml](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Pode adicionar, editar ou eliminar tags de execução do estúdio. Navegue na página **'Detalhes de execução'** para a sua execução e selecione o ícone de edição ou lápis para adicionar, editar ou eliminar tags para as suas execuções. Também pode pesquisar e filtrar estas etiquetas na página da lista de execuções.
    
    :::image type="content" source="media/how-to-manage-runs/run-tags.gif" alt-text="Screenshot: Adicionar, editar ou eliminar tags de execução":::
    
    ---

* Propriedades e tags de consulta

    Você pode consultar corre dentro de uma experiência para devolver uma lista de runs que correspondem a propriedades e etiquetas específicas.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    O Azure CLI suporta consultas [JMESPath,](http://jmespath.org) que podem ser usadas para filtrar corridas com base em propriedades e etiquetas. Para utilizar uma consulta JMESPath com o CLI Azure, especifique-a com o `--query` parâmetro. Os exemplos a seguir mostram algumas consultas utilizando propriedades e etiquetas:
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Para obter mais informações sobre os resultados do Azure CLI, consulte [a saída do comando Do CLI da Consulta .](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest)
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    1. Navegue para a lista **de todas as corridas.**
    
    1. Utilize a barra de pesquisa para filtrar os metadados executados como as etiquetas, descrições, nomes de experiências e nome do apresentador. O filtro de etiquetas também pode ser utilizado para filtrar as etiquetas. 
    
    ---


## <a name="cancel-or-fail-runs"></a>Cancelar ou falhar corre

Se notar um erro ou se a sua corrida estiver a demorar muito tempo a terminar, pode cancelar a corrida.

# <a name="python"></a>[Python](#tab/python)

Para cancelar uma execução utilizando o SDK, utilize o [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecancel--) método:

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

Se a sua execução terminar, mas contiver um erro (por exemplo, o script de treino incorreto foi utilizado), pode utilizar o [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) método para o marcar como falhado.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para cancelar uma execução utilizando o CLI, utilize o seguinte comando. `runid`Substitua-se pelo ID da execução

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Para mais informações, consulte [a corrida az ml cancelar](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Para cancelar uma corrida no estúdio, utilizando os seguintes passos:

1. Vá para o gasoduto de funcionamento na secção **Experiments** ou **Pipelines.** 

1. Selecione o número de funcionação do gasoduto que pretende cancelar.

1. Na barra de ferramentas, selecione **Cancelar**

---

## <a name="create-child-runs"></a>Criar corridas de crianças

Criar corridas infantis para agrupar corridas relacionadas, tais como para diferentes iterações de afinação de hiperparímetros.

> [!NOTE]
> As corridas para crianças só podem ser criadas utilizando o SDK.

Este exemplo de código utiliza o `hello_with_children.py` script para criar um lote de cinco crianças executadas a partir de uma execução submetida utilizando o [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truechild-run-name-none--run-id-none--outputs-none-) método:

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> À medida que se afastam do alcance, as corridas para crianças são automaticamente marcadas como concluídas.

Para criar muitas crianças funciona de forma eficiente, use o [`create_children()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-children-count-none--tag-key-none--tag-values-none-) método. Como cada criação resulta numa chamada de rede, criar um lote de runs é mais eficiente do que criá-las uma a uma.

### <a name="submit-child-runs"></a>Submeter corridas de crianças

As corridas para crianças também podem ser submetidas a partir de uma corrida de pais. Isto permite-lhe criar hierarquias de pais e filhos. Não é possível criar uma corrida sem filhos: mesmo que a corrida dos pais não faça nada a não ser lançar a criança, ainda é necessário criar a hierarquia. O estatuto de todas as corridas é independente: um progenitor pode estar no `"Completed"` estado de sucesso, mesmo que uma ou mais crianças sejam canceladas ou falhadas.  

Pode desejar que o seu filho corra para utilizar uma configuração de execução diferente da execução dos pais. Por exemplo, pode utilizar uma configuração menos potente e baseada em CPU para o progenitor, enquanto utiliza configurações baseadas em GPU para os seus filhos. Outro desejo comum é passar a cada criança diferentes argumentos e dados. Para personalizar uma corrida de crianças, crie um `ScriptRunConfig` objeto para a corrida da criança. O código abaixo faz o seguinte:

- Recupere um recurso computacional nomeado `"gpu-cluster"` do espaço de trabalho `ws`
- Iterates sobre diferentes valores de argumento a serem passados para os objetos das `ScriptRunConfig` crianças
- Cria e submete uma nova corrida infantil, usando o recurso e argumento de computação personalizado
- Bloqueia até que toda a criança fique completa

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Para criar muitas corridas de crianças com configurações, argumentos e entradas idênticas de forma eficiente, utilize o [`create_children()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-children-count-none--tag-key-none--tag-values-none-) método. Como cada criação resulta numa chamada de rede, criar um lote de runs é mais eficiente do que criá-las uma a uma.

Dentro de uma corrida de crianças, você pode ver o iD do pai executar:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>A consulta da criança corre

Para consultar a criança de um progenitor específico, utilize o [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) método. O ``recursive = True`` argumento permite-lhe consultar uma árvore aninhada de filhos e netos.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>Faça login para pai ou raiz

Pode utilizar o `Run.parent` campo para aceder à corrida que lançou a atual corrida infantil. Um caso comum de utilização para este caso é quando deseja consolidar os resultados do registo num único local. Note que as corridas de crianças executam assíncronia e não há garantia de encomenda ou sincronização para além da capacidade do progenitor de esperar que o seu filho esteja completo.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="example-notebooks"></a>Blocos de notas de exemplo

Os seguintes cadernos demonstram os conceitos deste artigo:

* Para saber mais sobre as APIs de registo, consulte o [caderno API de registo.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)

* Para obter mais informações sobre a gestão de corridas com o Azure Machine Learning SDK, consulte o [caderno de gestão](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Passos seguintes

* Para aprender a registar métricas para as suas experiências, consulte [as métricas do Registo durante os treinos](how-to-track-experiments.md).
* Para aprender a monitorizar recursos e registos da Azure Machine Learning, consulte [monitorar a Azure Machine Learning](monitor-azure-machine-learning.md).
