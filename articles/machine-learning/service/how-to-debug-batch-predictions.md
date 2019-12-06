---
title: Depurar e solucionar problemas de ParallelRunStep
titleSuffix: Azure Machine Learning
description: Depure e solucione problemas de pipelines do Machine Learning no SDK do Azure Machine Learning para Python. Aprenda armadilhas comuns para o desenvolvimento com pipelines e dicas para ajudá-lo a depurar seus scripts antes e durante a execução remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 11/21/2019
ms.openlocfilehash: db17dca1a859807f4d92b4953ec0e2785a3a4160
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852331"
---
# <a name="debug-and-troubleshoot-using-parallelrun"></a>Depurar e solucionar problemas usando o ParallelRun
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a depurar e solucionar problemas da classe [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) do [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Testando scripts localmente

Consulte a [seção testando scripts localmente](how-to-debug-pipelines.md#testing-scripts-locally) para pipelines do Machine Learning. Seu ParallelRunStep é executado como uma etapa em pipelines de ML para que a mesma resposta se aplique a ambos.

## <a name="debugging-scripts-from-remote-context"></a>Depurando scripts do contexto remoto

A transição da depuração de um script de Pontuação localmente para depurar um script de pontuação em um pipeline real pode ser um salto difícil. Para obter informações sobre como localizar seus logs no portal, a [seção pipelines do Machine Learning em depuração de scripts de um contexto remoto](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). As informações nessa seção também se aplicam a uma execução de inferência de lote.

Por exemplo, o arquivo de log `70_driver_log.txt` também contém: 

* Todas as instruções impressas durante a execução do script.
* O rastreamento de pilha do script.

Devido à natureza distribuída de trabalhos de inferência de lote, há logs de várias fontes diferentes. No entanto, são criados dois arquivos consolidados que fornecem informações de alto nível:

- `~/logs/overview.txt`: esse arquivo fornece informações de alto nível sobre o número de mini-lotes (também conhecidos como tarefas) criados até o momento e o número de mini-lotes processados até o momento. Nesse final, ele mostra o resultado do trabalho. Se o trabalho tiver falhado, ele mostrará a mensagem de erro e onde iniciar a solução de problemas.

- `~/logs/master.txt`: esse arquivo fornece a exibição do nó mestre (também conhecido como orquestrador) do trabalho em execução. Inclui a criação de tarefas, o monitoramento de progresso, o resultado da execução.

Quando você precisar de um entendimento completo de como cada nó executou o script de pontuação, examine os logs de processo individuais para cada nó. Os logs de processo podem ser encontrados na pasta `worker`, agrupados por nós de trabalho:

- `~/logs/worker/<ip_address>/Process-*.txt`: esse arquivo fornece informações detalhadas sobre cada mini-lote à medida que ele é selecionado ou concluído por um trabalho. Para cada mini-batch, esse arquivo inclui:

    - O endereço IP e o PID do processo de trabalho. 
    - O número total de itens e o número de itens processados com êxito. 
    - A hora de início e de término nas horas do relógio de parede (`start1` e `end1`). 
    - A hora de início e de término no tempo do processador gasto (`start2` e `end2`). 

Você também pode encontrar informações sobre o uso de recursos dos processos para cada trabalho. Essas informações estão no formato CSV e estão localizadas em `~/logs/performance/<ip_address>/`. Por exemplo, ao verificar a utilização de recursos, examine os seguintes arquivos:

- `process_resource_monitor_<ip>_<pid>.csv`: por uso de recursos de processo de trabalho. 
- `sys_resource_monitor_<ip>.csv`: log por nó.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Como fazer log do meu script de usuário a partir de um contexto remoto?
Você pode configurar um agente de log com as etapas a seguir para fazer os logs aparecerem na pasta **logs/usuários** no Portal:
1. Salve a primeira seção de código abaixo no arquivo entry_script_helper. py e coloque o arquivo na mesma pasta que o script de entrada. Essa classe obtém o caminho dentro de AmlCompute. Para o teste local, você pode alterar `get_working_dir()` para retornar uma pasta local.
2. Configurar um agente no seu método `init()` e, em seguida, usá-lo. A segunda seção de código abaixo é um exemplo. 

**entry_script_helper. py:**
```python
"""
This module provides helper features for entry script.
This file should be in Python search paths or in the same folder as the entry script.
"""
import os
import socket
import logging
import time
from multiprocessing import current_process
from azureml.core import Run


class EntryScriptHelper:
    """ A helper to provide common features for entry script."""

    LOG_CONFIGED = False

    def get_logger(self, name="EntryScript"):
        """ Return a logger.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        return logging.getLogger(name)

    def config(self, name="EntryScript", level="INFO"):
        """ Config a logger. This should be called in init() in score module.
            Config the logger one time if not configed.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        logger = logging.getLogger(name)

        formatter = logging.Formatter(
            "%(asctime)s|%(name)s|%(levelname)s|%(process)d|%(thread)d|%(funcName)s()|%(message)s"
        )
        formatter.converter = time.gmtime

        logger.setLevel(level)

        handler = logging.FileHandler(self.get_log_file_path())
        handler.setLevel(level)
        handler.setFormatter(formatter)
        logger.addHandler(handler)

        return logger

    def get_log_file_path(self):
        """ Get the log file path for users.
            Each process has its own log file, so there is not race issue among multiple processes.
        """
        ip_address = socket.gethostbyname(socket.gethostname())
        log_dir = os.path.join(self.get_log_dir(), "user", ip_address)
        os.makedirs(log_dir, exist_ok=True)
        return os.path.join(log_dir, f"{current_process().name}.txt")

    def get_log_dir(self):
        """ Return the folder for logs.
            Files and folders in it will be uploaded and show up in run detail page in the azure portal.
        """
        log_dir = os.path.join(self.get_working_dir(), "logs")
        os.makedirs(log_dir, exist_ok=True)
        return log_dir

    def get_working_dir(self):
        """ Return the working directory."""
        return os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), self.get_run().id)

    def get_temp_dir(self):
        """ Return local temp directory."""
        local_temp_dir = os.path.join(
            os.environ.get("AZ_BATCHAI_JOB_TEMP", ""), "azureml-bi", str(os.getpid())
        )
        os.makedirs(local_temp_dir, exist_ok=True)
        return local_temp_dir

    def get_run(self):
        """ Return the Run from the context."""
        return Run.get_context(allow_offline=False)

```

**Um script de entrada de exemplo usando o agente:**
```python
"""
This is a sample scoring module.

This module provides a sample which passes the input back without any change.
"""
import os
import logging
from entry_script_helper import EntryScriptHelper

LOG_NAME = "score_file_list"


def init():
    """ Init """
    EntryScriptHelper().config(LOG_NAME)
    logger = logging.getLogger(LOG_NAME)
    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    logger.info(f"{__file__}.output_folder:{output_folder}")
    logger.info("init()")
    os.makedirs(output_folder, exist_ok=True)


def run(mini_batch):
    """ Accept and return the list back."""
    logger = logging.getLogger(LOG_NAME)
    logger.info(f"{__file__}, run({mini_batch})")

    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    for file_name in mini_batch:
        with open(file_name, "r") as file:
            lines = file.readlines()
        base_name = os.path.basename(file_name)
        name = os.path.join(output_folder, base_name)
        logger.info(f"{__file__}: {name}")
        with open(name, "w") as file:
            file.write(f"ouput file {name} from {__file__}:\n")
            for line in lines:
                file.write(line)

    return mini_batch
```

## <a name="next-steps"></a>Passos seguintes

* Consulte a referência do SDK para obter ajuda com o pacote [azureml-contrib-pipeline-Step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) e a [documentação](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) da classe ParallelRunStep.

* Siga o [tutorial avançado](tutorial-pipeline-batch-scoring-classification.md) sobre como usar pipelines para pontuação em lote.
