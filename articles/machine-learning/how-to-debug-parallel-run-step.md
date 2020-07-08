---
title: Debug e resolução de problemas ParallelRunStep
titleSuffix: Azure Machine Learning
description: Depurar e resolução de problemas ParallelRunStep em oleodutos de aprendizagem automática no Azure Machine Learning SDK para Python. Aprenda armadilhas comuns para desenvolver com oleodutos e dicas para ajudá-lo a depurar os seus scripts antes e durante a execução remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.reviewer: trbye, jmartens, larryfr, vaidyas, laobri
ms.author: trmccorm
author: tmccrmck
ms.date: 07/06/2020
ms.openlocfilehash: 870563a1a27ee00c2f14935e5200f722136011a1
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027006"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Debug e resolução de problemas ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a depurar e a resolver problemas na aula [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) do [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Testar scripts localmente

Consulte a [secção de scripts de teste local](how-to-debug-pipelines.md#testing-scripts-locally) para os oleodutos de aprendizagem automática. O seu ParallelRunStep funciona como um passo nos oleodutos ML, pelo que a mesma resposta se aplica a ambos.

## <a name="debugging-scripts-from-remote-context"></a>Depurando scripts do contexto remoto

A transição de depurar um guião de pontuação localmente para depurar um script de pontuação em um pipeline real pode ser um salto difícil. Para obter informações sobre a descoberta dos seus registos no portal, a [secção de gasodutos de aprendizagem automática sobre a depuragem de scripts de um contexto remoto](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). A informação nessa secção também se aplica a um ParallelRunStep.

Por exemplo, o ficheiro de registo `70_driver_log.txt` contém informações do controlador que lança o código ParallelRunStep.

Devido à natureza distribuída dos trabalhos ParallelRunStep, existem registos de várias fontes diferentes. No entanto, são criados dois ficheiros consolidados que fornecem informações de alto nível:

- `~/logs/overview.txt`: Este ficheiro fornece uma informação de alto nível sobre o número de mini-lotes (também conhecidos como tarefas) criados até agora e o número de mini-lotes processados até agora. Neste fim, mostra o resultado do trabalho. Se o trabalho falhar, mostrará a mensagem de erro e onde iniciar a resolução de problemas.

- `~/logs/sys/master.txt`: Este ficheiro fornece a visão do nó mestre (também conhecido como orquestrador) do trabalho de execução. Inclui criação de tarefas, monitorização do progresso, resultado de execução.

Os registos gerados a partir do script de entrada utilizando o helper EntryScript e as declarações de impressão serão encontrados nos seguintes ficheiros:

- `~/logs/user/<ip_address>/<node_name>.log.txt`: Estes ficheiros são os registos escritos a partir de entry_script utilizando o ajudante de entrada. Também contém a declaração de impressão (stdout) de entry_script.

Para uma compreensão concisa dos erros no seu script existe:

- `~/logs/user/error.txt`: Este ficheiro tentará resumir os erros no seu script.

Para obter mais informações sobre erros no seu script, existe:

- `~/logs/user/error/`: Contém todos os erros lançados e vestígios de pilha completa organizados por nó.

Quando precisar de uma compreensão completa de como cada nó executou o script de pontuação, olhe para os registos de processo individuais para cada nó. Os registos de processo podem ser encontrados na `sys/node` pasta, agrupadas por nóns de trabalhador:

- `~/logs/sys/node/<node_name>.txt`: Este ficheiro fornece informações detalhadas sobre cada mini-lote à medida que é recolhido ou completado por um trabalhador. Para cada mini-lote, este ficheiro inclui:

    - O endereço IP e o PID do processo de trabalhador. 
    - O número total de itens, contagem de itens processados com sucesso e contagem de artigos falhados.
    - A hora de início, duração, tempo de processo e tempo de funcionamento do método.

Também pode encontrar informações sobre o uso de recursos dos processos para cada trabalhador. Esta informação está em formato CSV e está localizada em `~/logs/sys/perf/overview.csv` . Informações sobre cada processo estão disponíveis em `~logs/sys/processes.csv` .

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Como faço o registo do meu script de utilizador a partir de um contexto remoto?
Pode obter um madeireiro do EntryScript como mostrado no código de amostra abaixo para fazer com que os registos apareçam em **registos/pasta de utilizador** no portal.

**Um script de entrada de amostra usando o madeireiro:**
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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Como poderia passar uma entrada lateral, como um ficheiro ou ficheiros contendo uma mesa de procura, a todos os meus trabalhadores?

Construa um [Conjunto de Dados](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) contendo a entrada lateral e registe-o com o seu espaço de trabalho. Passe-o para o `side_input` parâmetro do seu `ParallelRunStep` . Além disso, pode adicionar o seu caminho na `arguments` secção para aceder facilmente ao seu caminho montado:

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

## <a name="next-steps"></a>Próximos passos

* Consulte a referência SDK para obter ajuda com o pacote [de passos de gasoduto azureml.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) Ver [documentação de](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?view=azure-ml-py) referência para a classe ParallelRunStep.

* Siga o [tutorial avançado](tutorial-pipeline-batch-scoring-classification.md) sobre a utilização de gasodutos com ParallelRunStep. O tutorial mostra como passar outro ficheiro como entrada lateral. 
