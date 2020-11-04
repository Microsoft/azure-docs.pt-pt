---
title: Depurar e resolver problemas do ParallelRunStep
titleSuffix: Azure Machine Learning
description: Depurar e resolução de problemas ParallelRunStep em oleodutos de aprendizagem automática no Azure Machine Learning SDK para Python. Aprenda armadilhas comuns para desenvolver com oleodutos e dicas para ajudá-lo a depurar os seus scripts antes e durante a execução remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: 93997629b6b30f87769a0154e344215ca52c8ec4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308387"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Depurar e resolver problemas do ParallelRunStep


Neste artigo, aprende-se a depurar e a resolver problemas na aula [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?preserve-view=true&view=azure-ml-py) do [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Testar scripts localmente

Consulte a [secção de scripts de teste local](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) para os oleodutos de aprendizagem automática. O seu ParallelRunStep funciona como um passo nos oleodutos ML, pelo que a mesma resposta se aplica a ambos.

## <a name="debugging-scripts-from-remote-context"></a>Depurando scripts do contexto remoto

A transição de depurar um guião de pontuação localmente para depurar um script de pontuação em um pipeline real pode ser um salto difícil. Para obter informações sobre a descoberta dos seus registos no portal, a [secção de gasodutos de aprendizagem automática sobre a depuragem de scripts de um contexto remoto](how-to-debug-pipelines.md). A informação nessa secção também se aplica a um ParallelRunStep.

Por exemplo, o ficheiro de registo `70_driver_log.txt` contém informações do controlador que lança o código ParallelRunStep.

Devido à natureza distribuída dos trabalhos ParallelRunStep, existem registos de várias fontes diferentes. No entanto, são criados dois ficheiros consolidados que fornecem informações de alto nível:

- `~/logs/job_progress_overview.txt`: Este ficheiro fornece uma informação de alto nível sobre o número de mini-lotes (também conhecidos como tarefas) criados até agora e o número de mini-lotes processados até agora. Neste fim, mostra o resultado do trabalho. Se o trabalho falhar, mostrará a mensagem de erro e onde iniciar a resolução de problemas.

- `~/logs/sys/master_role.txt`: Este ficheiro fornece a visão principal do nó (também conhecido como orquestrador) do trabalho de funcionamento. Inclui criação de tarefas, monitorização do progresso, resultado de execução.

Os registos gerados a partir do script de entrada utilizando o helper EntryScript e as declarações de impressão serão encontrados nos seguintes ficheiros:

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`: Estes ficheiros são os registos escritos a partir de entry_script utilizando o ajudante de entrada.

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`: Estes ficheiros são os registos da sestado (por exemplo, declaração de impressão) de entry_script.

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`: Estes ficheiros são os registos da stderr de entry_script.

Para uma compreensão concisa dos erros no seu script existe:

- `~/logs/user/error.txt`: Este ficheiro tentará resumir os erros no seu script.

Para obter mais informações sobre erros no seu script, existe:

- `~/logs/user/error/`: Contém vestígios completos de exceções lançadas durante o carregamento e execução do script de entrada.

Quando precisar de uma compreensão completa de como cada nó executou o script de pontuação, olhe para os registos de processo individuais para cada nó. Os registos de processo podem ser encontrados na `sys/node` pasta, agrupadas por nóns de trabalhador:

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: Este ficheiro fornece informações detalhadas sobre cada mini-lote à medida que é recolhido ou completado por um trabalhador. Para cada mini-lote, este ficheiro inclui:

    - O endereço IP e o PID do processo de trabalhador. 
    - O número total de itens, contagem de itens processados com sucesso e contagem de artigos falhados.
    - A hora de início, duração, tempo de processo e tempo de funcionamento do método.

Também pode encontrar informações sobre o uso de recursos dos processos para cada trabalhador. Esta informação está em formato CSV e está localizada em `~/logs/sys/perf/<ip_address>/node_resource_usage.csv` . Informações sobre cada processo estão disponíveis em `~logs/sys/perf/<ip_address>/processes_resource_usage.csv` .

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Como faço o registo do meu script de utilizador a partir de um contexto remoto?
ParallelRunStep pode executar vários processos em um nó com base em process_count_per_node. Para organizar registos de cada processo no nó e combinar a impressão e a declaração de registo, recomendamos a utilização do madeireiro ParallelRunStep, conforme mostrado abaixo. Obtém um madeireiro do EntryScript e faz com que os registos apareçam em **registos/pasta de utilizador** no portal.

**Um script de entrada de amostra usando o madeireiro:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Como poderia passar uma entrada lateral, como um ficheiro ou ficheiros contendo uma mesa de procura, a todos os meus trabalhadores?

O utilizador pode passar dados de referência para script usando side_inputs parâmetro do ParalleRunStep. Todos os conjuntos de dados fornecidos como side_inputs serão montados em cada nó de trabalhador. O utilizador pode obter a localização do monte através do argumento de passagem.

Construa um [Conjunto de Dados](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py) contendo os dados de referência e registe-os com o seu espaço de trabalho. Passe-o para o `side_inputs` parâmetro do seu `ParallelRunStep` . Além disso, pode adicionar o seu caminho na `arguments` secção para aceder facilmente ao seu caminho montado:

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

Depois disso, pode aceder-lhe no seu script de inferência (por exemplo, no seu método init() da seguinte forma:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>Como utilizar conjuntos de dados de entrada com autenticação principal do serviço?

O utilizador pode passar conjuntos de dados de entrada com a autenticação principal do serviço utilizada no espaço de trabalho. A utilização deste conjunto de dados em ParallelRunStep requer que o conjunto de dados seja registado para que possa construir a configuração ParallelRunStep.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="**_",
    service_principal_id="_*_",
    service_principal_password="_*_")
 
ws = Workspace(
    subscription_id="_*_",
    resource_group="_*_",
    workspace_name="_*_",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '_*_datastore-name_*_') 
ds = Dataset.File.from_files(default_blob_store, '_*path**_')
registered_ds = ds.register(ws, '_*_dataset-name_*_', create_new_version=True)
```

## <a name="next-steps"></a>Passos seguintes

_ Veja estes [cadernos jupyter demonstrando oleodutos Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)

* Consulte a referência SDK para obter ajuda com o pacote [de passos de gasoduto azureml.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) Ver [documentação de](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?preserve-view=true&view=azure-ml-py) referência para a classe ParallelRunStep.

* Siga o [tutorial avançado](tutorial-pipeline-batch-scoring-classification.md) sobre a utilização de gasodutos com ParallelRunStep. O tutorial mostra como passar outro ficheiro como entrada lateral.