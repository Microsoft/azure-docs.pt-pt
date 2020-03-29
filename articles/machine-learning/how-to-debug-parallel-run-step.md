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
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122967"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Depuração e resolução de problemas ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a depurar e resolver problemas com a classe [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) do [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Testar scripts localmente

Consulte a [secção de testes localmente](how-to-debug-pipelines.md#testing-scripts-locally) para os gasodutos de aprendizagem automática. O seu ParallelRunStep funciona como um passo nos gasodutos ML, pelo que a mesma resposta se aplica a ambos.

## <a name="debugging-scripts-from-remote-context"></a>Depurando scripts do contexto remoto

A transição de depurar um roteiro de pontuação localmente para depurar um roteiro de pontuação num oleoduto real pode ser um salto difícil. Para obter informações sobre a descoberta dos seus registos no portal, a secção de pipelines de [aprendizagem automática sobre depuração de scripts a partir de um contexto remoto](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). As informações nessa secção aplicam-se igualmente a um passo paralelo.

Por exemplo, o `70_driver_log.txt` ficheiro de registo contém informações do controlador que lança um código de passo paralelo.

Devido à natureza distribuída dos trabalhos paralelos, existem registos de várias fontes diferentes. No entanto, são criados dois ficheiros consolidados que fornecem informações de alto nível:

- `~/logs/overview.txt`: Este ficheiro fornece uma informação de alto nível sobre o número de mini-lotes (também conhecidos como tarefas) criados até agora e o número de mini-lotes processados até agora. Neste final, mostra o resultado do trabalho. Se o trabalho falhar, mostrará a mensagem de erro e onde iniciar a resolução de problemas.

- `~/logs/sys/master.txt`: Este ficheiro fornece o nó principal (também conhecido como orquestrador) da visão do trabalho em execução. Inclui a criação de tarefas, a monitorização do progresso, o resultado da execução.

Os registos gerados a partir do script de entrada utilizando o EntryScript.logger e as declarações de impressão serão encontrados nos seguintes ficheiros:

- `~/logs/user/<ip_address>/Process-*.txt`: Este ficheiro contém registos escritos a partir de entry_script utilizando o EntryScript.logger. Também contém uma declaração de impressão (stdout) de entry_script.

Quando precisar de uma compreensão completa de como cada nó executou o script de pontuação, olhe para os registos de processo individual para cada nó. Os registos de processo `sys/worker` podem ser encontrados na pasta, agrupados por nós dos trabalhadores:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: Este ficheiro fornece informações detalhadas sobre cada mini-lote, uma vez que é recolhido ou concluído por um trabalhador. Para cada mini-lote, este ficheiro inclui:

    - O endereço IP e o PID do processo do trabalhador. 
    - O número total de itens, itens processados com sucesso contam e não contam itens.
    - A hora de início, duração, tempo de processo e tempo de execução do método.

Também pode encontrar informações sobre o uso de recursos dos processos para cada trabalhador. Esta informação encontra-se em formato CSV e está localizada em `~/logs/sys/perf/<ip_address>/`. Para um único nó, os ficheiros de trabalho estarão disponíveis em . `~logs/sys/perf` Por exemplo, ao verificar a utilização de recursos, consulte os seguintes ficheiros:

- `Process-*.csv`: Por utilização de recursos de processo de trabalho. 
- `sys.csv`: Por registo de nó.

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

Construa um objeto [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) contendo a entrada lateral e registe-se com o seu espaço de trabalho. Depois disso, pode aceder-lhe no seu script de inferência (por exemplo, no seu método init() da seguinte forma:

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Passos seguintes

* Consulte a referência SDK para obter ajuda com o pacote de [passo sinuoso-de-gasoduto-diálgeno e](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) a [documentação](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) para a classe ParallelRunStep.

* Siga o [tutorial avançado](tutorial-pipeline-batch-scoring-classification.md) sobre a utilização de gasodutos com passo de execução paralelo.
