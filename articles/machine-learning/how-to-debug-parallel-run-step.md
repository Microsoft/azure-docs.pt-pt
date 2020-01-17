---
title: Depurar e solucionar problemas de ParallelRunStep
titleSuffix: Azure Machine Learning
description: Depure e solucione problemas do ParallelRunStep em pipelines do Machine Learning no SDK do Azure Machine Learning para Python. Aprenda armadilhas comuns para o desenvolvimento com pipelines e dicas para ajudá-lo a depurar seus scripts antes e durante a execução remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122967"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Depurar e solucionar problemas de ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a depurar e solucionar problemas da classe [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) do [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Testando scripts localmente

Consulte a [seção testando scripts localmente](how-to-debug-pipelines.md#testing-scripts-locally) para pipelines do Machine Learning. Seu ParallelRunStep é executado como uma etapa em pipelines de ML para que a mesma resposta se aplique a ambos.

## <a name="debugging-scripts-from-remote-context"></a>Depurando scripts do contexto remoto

A transição da depuração de um script de Pontuação localmente para depurar um script de pontuação em um pipeline real pode ser um salto difícil. Para obter informações sobre como localizar seus logs no portal, a [seção pipelines do Machine Learning em depuração de scripts de um contexto remoto](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). As informações nessa seção também se aplicam a uma etapa paralela executada.

Por exemplo, o arquivo de log `70_driver_log.txt` contém informações do controlador que inicia o código de etapa de execução paralela.

Devido à natureza distribuída de trabalhos de execução paralela, há logs de várias fontes diferentes. No entanto, são criados dois arquivos consolidados que fornecem informações de alto nível:

- `~/logs/overview.txt`: esse arquivo fornece informações de alto nível sobre o número de mini-lotes (também conhecidos como tarefas) criados até o momento e o número de mini-lotes processados até o momento. Nesse final, ele mostra o resultado do trabalho. Se o trabalho tiver falhado, ele mostrará a mensagem de erro e onde iniciar a solução de problemas.

- `~/logs/sys/master.txt`: esse arquivo fornece a exibição do nó mestre (também conhecido como orquestrador) do trabalho em execução. Inclui a criação de tarefas, o monitoramento de progresso, o resultado da execução.

Os logs gerados do script de entrada usando EntryScript. Logger e as instruções PRINT serão encontrados nos seguintes arquivos:

- `~/logs/user/<ip_address>/Process-*.txt`: esse arquivo contém logs gravados de entry_script usando EntryScript. Logger. Ele também contém a instrução print (stdout) da entry_script.

Quando você precisar de um entendimento completo de como cada nó executou o script de pontuação, examine os logs de processo individuais para cada nó. Os logs de processo podem ser encontrados na pasta `sys/worker`, agrupados por nós de trabalho:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: esse arquivo fornece informações detalhadas sobre cada mini-lote à medida que ele é selecionado ou concluído por um trabalho. Para cada mini-batch, esse arquivo inclui:

    - O endereço IP e o PID do processo de trabalho. 
    - O número total de itens, contagem de itens processados com êxito e contagem de itens com falha.
    - A hora de início, a duração, o tempo de processamento e o tempo do método de execução.

Você também pode encontrar informações sobre o uso de recursos dos processos para cada trabalho. Essas informações estão no formato CSV e estão localizadas em `~/logs/sys/perf/<ip_address>/`. Para um único nó, os arquivos de trabalho estarão disponíveis em `~logs/sys/perf`. Por exemplo, ao verificar a utilização de recursos, examine os seguintes arquivos:

- `Process-*.csv`: por uso de recursos de processo de trabalho. 
- `sys.csv`: log por nó.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Como fazer log do meu script de usuário a partir de um contexto remoto?
Você pode obter um agente de EntryScript conforme mostrado no código de exemplo abaixo para fazer os logs aparecerem na pasta **logs/usuário** no Portal.

**Um script de entrada de exemplo usando o agente:**
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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Como posso passar uma entrada no lado, como um arquivo ou arquivo (s) contendo uma tabela de pesquisa, para todos os meus trabalhos?

Construa um objeto de [conjunto](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) de dados contendo a entrada do lado e registre-se com seu espaço de trabalho. Depois disso, você pode acessá-lo em seu script de inferência (por exemplo, em seu método init ()) da seguinte maneira:

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Passos seguintes

* Consulte a referência do SDK para obter ajuda com o pacote [azureml-contrib-pipeline-Step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) e a [documentação](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) da classe ParallelRunStep.

* Siga o [tutorial avançado](tutorial-pipeline-batch-scoring-classification.md) sobre como usar pipelines com a etapa de execução paralela.
