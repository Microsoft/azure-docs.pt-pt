---
title: Depuração e resolução de problemas ParallelRunStep
titleSuffix: Azure Machine Learning
description: Depuração e resolução de problemas ParallelRunStep em pipelines de aprendizagem automática no Azure Machine Learning SDK para Python. Aprenda armadilhas comuns para desenvolver com oleodutos e dicas para ajudá-lo a depurar os seus scripts antes e durante a execução remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: b5431ae574f40c29368848808004a53abe43c3a8
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680965"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Depuração e resolução de problemas ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a depurar e resolver problemas com a classe [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) do [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Testar scripts localmente

Consulte a [secção de testes localmente](how-to-debug-pipelines.md#testing-scripts-locally) para os gasodutos de aprendizagem automática. O seu ParallelRunStep funciona como um passo nos gasodutos ML, pelo que a mesma resposta se aplica a ambos.

## <a name="debugging-scripts-from-remote-context"></a>Depurando scripts do contexto remoto

A transição de depurar um roteiro de pontuação localmente para depurar um roteiro de pontuação num oleoduto real pode ser um salto difícil. Para obter informações sobre a descoberta dos seus registos no portal, a secção de pipelines de [aprendizagem automática sobre depuração de scripts a partir de um contexto remoto](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). As informações nessa secção também se aplicam a um ParallelRunStep.

Por exemplo, o ficheiro de registo `70_driver_log.txt` contém informações do controlador que lança o código ParallelRunStep.

Devido à natureza distribuída dos trabalhos ParaleloRunStep, existem registos de várias fontes diferentes. No entanto, são criados dois ficheiros consolidados que fornecem informações de alto nível:

- `~/logs/overview.txt`: Este ficheiro fornece uma informação de alto nível sobre o número de mini-lotes (também conhecidos como tarefas) criados até agora e o número de mini-lotes processados até agora. Neste final, mostra o resultado do trabalho. Se o trabalho falhar, mostrará a mensagem de erro e onde iniciar a resolução de problemas.

- `~/logs/sys/master.txt`: Este ficheiro fornece o nó principal (também conhecido como orquestrador) da visão do trabalho em execução. Inclui a criação de tarefas, a monitorização do progresso, o resultado da execução.

Os registos gerados a partir do script de entrada utilizando o ajudante do EntryScript e as declarações de impressão serão encontrados nos seguintes ficheiros:

- `~/logs/user/<node_name>.log.txt`: Estes são os registos escritos a partir de entry_script utilizando o ajudante EntryScript. Também contém uma declaração de impressão (stdout) de entry_script.

Para uma compreensão concisa dos erros no seu script, existe:

- `~/logs/user/error.txt`: Este ficheiro tentará resumir os erros do seu script.

Para mais informações sobre erros no seu script, existe:

- `~/logs/user/error/`: Contém todos os erros lançados e vestígios de pilhas completos organizados pelo nó.

Quando precisar de uma compreensão completa de como cada nó executou o script de pontuação, olhe para os registos de processo individual para cada nó. Os registos de processo podem ser encontrados na `sys/node` pasta, agrupados por nós dos trabalhadores:

- `~/logs/sys/node/<node_name>.txt`: Este ficheiro fornece informações detalhadas sobre cada mini-lote, uma vez que é recolhido ou concluído por um trabalhador. Para cada mini-lote, este ficheiro inclui:

    - O endereço IP e o PID do processo do trabalhador. 
    - O número total de itens, itens processados com sucesso contam e não contam itens.
    - A hora de início, duração, tempo de processo e tempo de execução do método.

Também pode encontrar informações sobre o uso de recursos dos processos para cada trabalhador. Esta informação encontra-se em formato CSV e está localizada em `~/logs/sys/perf/overview.csv` . Para obter informações sobre cada processo, está disponível em `~logs/sys/processes.csv` .

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Como faço login no meu script de utilizador a partir de um contexto remoto?
Pode obter um logger do EntryScript, como mostrado no código de amostra abaixo, para que os registos apareçam nos **registos/pasta** do utilizador no portal.

**Um script de entrada de amostra utilizando o madeireiro:**
```python
from entry_script import EntryScript

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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Como poderia passar uma entrada lateral como, um ficheiro ou arquivo(s) contendo uma mesa de vigia, a todos os meus trabalhadores?

Construa um Conjunto de [Dados](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) contendo a entrada lateral e registe-o com o seu espaço de trabalho. Passe-o para o `side_input` parâmetro da sua `ParallelRunStep` . Além disso, pode adicionar o seu caminho na `arguments` secção para aceder facilmente ao seu caminho montado:

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

## <a name="next-steps"></a>Passos seguintes

* Consulte a referência SDK para obter ajuda com o pacote de [passo sinuoso-de-gasoduto-diálgeno e](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) a [documentação](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) para a classe ParallelRunStep.

* Siga o [tutorial avançado](tutorial-pipeline-batch-scoring-classification.md) sobre a utilização de gasodutos com ParallelRunStep e, por exemplo, passe outro ficheiro como entrada lateral. 
