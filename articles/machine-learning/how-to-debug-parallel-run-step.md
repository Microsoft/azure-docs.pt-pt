---
title: Resolver problemas de ParallelRunStep
titleSuffix: Azure Machine Learning
description: Dicas para como resolver problemas quando obtém erros usando o ParallelRunStep em oleodutos de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: pansav
author: psavdekar
ms.date: 09/23/2020
ms.openlocfilehash: 619123cc2723fcf8e4bd80410c6b098b113d61c6
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286322"
---
# <a name="troubleshooting-the-parallelrunstep"></a>Resolver problemas de ParallelRunStep

Neste artigo, aprende-se a resolver problemas quando obtém erros utilizando a aula [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) do [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro).

Para obter dicas gerais sobre a resolução de problemas de um oleoduto, consulte [os gasodutos de aprendizagem de máquinas de resolução de problemas](how-to-debug-pipelines.md).

## <a name="testing-scripts-locally"></a>Testar scripts localmente

 O seu ParallelRunStep funciona como um passo nos oleodutos ML. Pode querer [testar os seus scripts localmente](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) como um primeiro passo.

##  <a name="script-requirements"></a>Requisitos de script

O script para um `ParallelRunStep` *deve conter* duas funções:
- `init()`: Utilize esta função para qualquer preparação dispendiosa ou comum para posterior inferência. Por exemplo, use-o para carregar o modelo num objeto global. Esta função será chamada apenas uma vez no início do processo.
-  `run(mini_batch)`: A função funcionará para cada `mini_batch` instância.
    -  `mini_batch`: `ParallelRunStep` invocará o método de execução e passará uma lista ou pandas `DataFrame` como argumento para o método. Cada entrada em mini_batch será um caminho de arquivo se a entrada for um `FileDataset` ou um pandas `DataFrame` se a entrada for um `TabularDataset` .
    -  `response`: método de execução() deve devolver um pandas `DataFrame` ou uma matriz. Para append_row output_action, estes elementos devolvidos são anexados ao ficheiro de saída comum. Para summary_only, o conteúdo dos elementos é ignorado. Para todas as ações de saída, cada elemento de saída devolvido indica uma execução bem sucedida do elemento de entrada no mini-lote de entrada. Certifique-se de que os dados suficientes são incluídos no resultado de execução para mapear a entrada para executar o resultado da saída. A saída de execução será escrita no ficheiro de saída e não garantida para estar em ordem, deve utilizar alguma chave na saída para mapear a entrada.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Se tiver outro ficheiro ou pasta no mesmo diretório que o seu script de inferência, pode fazê-lo referenciar encontrando o diretório de trabalho atual.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>Parâmetros para ParallelRunConfig

`ParallelRunConfig` é a principal `ParallelRunStep` configuração, por exemplo, dentro do pipeline Azure Machine Learning. Usa-o para embrulhar o seu script e configurar os parâmetros necessários, incluindo todas as seguintes entradas:
- `entry_script`: Um script do utilizador como um caminho de arquivo local que será executado em paralelo em múltiplos nós. Se `source_directory` estiver presente, use um caminho relativo. Caso contrário, utilize qualquer caminho acessível na máquina.
- `mini_batch_size`: O tamanho do mini-lote passou para uma única `run()` chamada. (opcional; o valor predefinido são `10` ficheiros para `FileDataset` e para `1MB` `TabularDataset` .)
    - Para `FileDataset` , é o número de ficheiros com um valor mínimo de `1` . Pode combinar vários ficheiros num mini-lote.
    - Para, `TabularDataset` é o tamanho dos dados. Os valores de exemplo `1024` `1024KB` `10MB` são, `1GB` e. O valor recomendado é `1MB` . O mini-lote `TabularDataset` de nunca cruzará os limites do ficheiro. Por exemplo, se tiver ficheiros .csv com vários tamanhos, o ficheiro mais pequeno é de 100 KB e o maior é de 10 MB. Se `mini_batch_size = 1MB` definir, os ficheiros com um tamanho inferior a 1 MB serão tratados como um mini-lote. Os ficheiros com um tamanho superior a 1 MB serão divididos em vários mini-lotes.
        > [!NOTE]
        > Os Separadores Tabular apoiados por SQL não podem ser divididos. 

- `error_threshold`: O número de falhas de registo `TabularDataset` e falhas de ficheiros para tal deve `FileDataset` ser ignorado durante o processamento. Se a contagem de erros para toda a entrada for superior a este valor, o trabalho será abortado. O limiar de erro é para toda a entrada e não para mini-lote individual enviado para o `run()` método. O alcance `[-1, int.max]` é. A `-1` peça indica ignorar todas as falhas durante o processamento.
- `output_action`: Um dos seguintes valores indica como a saída será organizada:
    - `summary_only`: O script do utilizador armazena a saída. `ParallelRunStep` utilizará a saída apenas para o cálculo do limiar de erro.
    - `append_row`: Para todas as entradas, apenas um ficheiro será criado na pasta de saída para anexar todas as saídas separadas por linha.
- `append_row_file_name`: Para personalizar o nome do ficheiro de saída para append_row output_action (opcional; valor predefinido `parallel_run_step.txt` é ).
- `source_directory`: Caminhos para pastas que contenham todos os ficheiros a executar no alvo do cálculo (opcional).
- `compute_target`: Só `AmlCompute` é suportado.
- `node_count`: O número de nós computacional a utilizar para executar o script do utilizador.
- `process_count_per_node`: O número de processos por nó. A melhor prática é definir o número de GPU ou CPU que um nó tem (opcional; valor padrão é `1` ).
- `environment`: A definição do ambiente Python. Você pode configugá-lo para usar um ambiente Python existente ou para configurar um ambiente temporário. A definição é também responsável pela definição das dependências de aplicação necessárias (opcional).
- `logging_level`: Log verbosity. Os valores em cada vez maior verbosidade são: `WARNING` `INFO` e `DEBUG` . (opcional; o valor predefinido `INFO` é)
- `run_invocation_timeout`: O `run()` tempo de tempo de invocação do método em segundos. (opcional; valor predefinido `60` é)
- `run_max_try`: Contagem máxima de tentativa `run()` para um mini-lote. A `run()` é falhado se uma exceção for lançada, ou nada for devolvido quando for alcançado `run_invocation_timeout` (opcional; o valor padrão `3` é). 

Pode especificar `mini_batch_size` , , e como , para `node_count` `process_count_per_node` `logging_level` `run_invocation_timeout` `run_max_try` `PipelineParameter` que, quando reenviar uma corrida de gasoduto, possa afinar os valores dos parâmetros. Neste exemplo, `PipelineParameter` `mini_batch_size` usa-se e `Process_count_per_node` muda estes valores quando voltar a apresentar outra execução. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>Parâmetros para a criação do ParallelRunStep

Crie o ParallelRunStep utilizando o script, a configuração do ambiente e os parâmetros. Especifique o alvo do cálculo que já anexou ao seu espaço de trabalho como alvo de execução para o seu script de inferência. Utilize `ParallelRunStep` para criar o passo do gasoduto de inferência do lote, que toma todos os seguintes parâmetros:
- `name`: O nome do passo, com as seguintes restrições de nomeação: caracteres únicos, 3-32, e regex ^ \[ a-z \] ([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: Um `ParallelRunConfig` objeto, tal como definido anteriormente.
- `inputs`: Um ou mais conjuntos de dados de aprendizagem de máquinas Azure de tipo único a serem divididos para processamento paralelo.
- `side_inputs`: Um ou mais dados de referência ou conjuntos de dados utilizados como entradas laterais sem necessidade de serem divididos.
- `output`: Um `OutputFileDatasetConfig` objeto que represente o caminho do diretório no qual os dados de saída serão armazenados.
- `arguments`: Uma lista de argumentos passados para o script do utilizador. Utilize unknown_args para os recuperar no seu script de entrada (opcional).
- `allow_reuse`: Se o passo deve reutilizar os resultados anteriores quando executado com as mesmas definições/entradas. Se este parâmetro `False` for, será sempre gerado um novo ensaio para este passo durante a execução do gasoduto. (opcional; o valor predefinido é `True` .)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-scripts-from-remote-context"></a>Depurando scripts do contexto remoto

A transição de depurar um guião de pontuação localmente para depurar um script de pontuação em um pipeline real pode ser um salto difícil. Para obter informações sobre a descoberta dos seus registos no portal, consulte a  [secção de gasodutos de aprendizagem automática sobre scripts depurar de um contexto remoto](how-to-debug-pipelines.md). A informação nessa secção também se aplica a um ParallelRunStep.

Por exemplo, o ficheiro de registo `70_driver_log.txt` contém informações do controlador que lança o código ParallelRunStep.

Devido à natureza distribuída dos trabalhos ParallelRunStep, existem registos de várias fontes diferentes. No entanto, são criados dois ficheiros consolidados que fornecem informações de alto nível:

- `~/logs/job_progress_overview.txt`: Este ficheiro fornece uma informação de alto nível sobre o número de mini-lotes (também conhecidos como tarefas) criados até agora e o número de mini-lotes processados até agora. Neste fim, mostra o resultado do trabalho. Se o trabalho falhar, mostrará a mensagem de erro e onde iniciar a resolução de problemas.

- `~/logs/sys/master_role.txt`: Este ficheiro fornece a visão principal do nó (também conhecido como orquestrador) do trabalho de funcionamento. Inclui criação de tarefas, monitorização do progresso, resultado de execução.

Os registos gerados a partir do script de entrada utilizando o helper EntryScript e as declarações de impressão serão encontrados nos seguintes ficheiros:

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`: Estes ficheiros são os registos escritos a partir de entry_script utilizando o ajudante de entrada.

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`: Estes ficheiros são os registos da estatudo (por exemplo, declaração de impressão) de entry_script.

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

Também pode ver os resultados das verificações periódicas da utilização do recurso para cada nó. Os ficheiros de registo e os ficheiros de configuração estão nesta pasta:

- `~/logs/perf`: `--resource_monitor_interval` Desa mute o intervalo de verificação em segundos. O intervalo predefinido é `600` , que é de aproximadamente 10 minutos. Para parar a monitorização, desa um valor para `0` . Cada `<ip_address>` pasta inclui:

    - `os/`: Informação sobre todos os processos de execução no nó. Uma verificação executa um comando do sistema operativo e guarda o resultado para um ficheiro. Em Linux, o comando `ps` é. No Windows, utilize `tasklist` .
        - `%Y%m%d%H`: O nome da sub-pasta é de hora em hora.
            - `processes_%M`: O ficheiro termina com o minuto da hora de verificação.
    - `node_disk_usage.csv`: Utilização detalhada do disco do nó.
    - `node_resource_usage.csv`: Visão geral da utilização do uso de recursos do nó.
    - `processes_resource_usage.csv`: Visão geral da utilização de recursos de cada processo.

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

Construa um [Conjunto de Dados](/python/api/azureml-core/azureml.core.dataset.dataset) contendo os dados de referência, especifique uma trajetória de montagem local e registe-os com o seu espaço de trabalho. Passe-o para o `side_inputs` parâmetro do seu `ParallelRunStep` . Além disso, pode adicionar o seu caminho na `arguments` secção para aceder facilmente ao seu caminho montado:

```python
local_path = "/tmp/{}".format(str(uuid.uuid4()))
label_config = label_ds.as_named_input("labels_input").as_mount(local_path)
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
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")
 
ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***') 
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="next-steps"></a>Passos seguintes

* Veja estes [cadernos jupyter demonstrando oleodutos Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)

* Consulte a referência SDK para obter ajuda com o pacote [de passos de gasoduto azureml.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) Ver [documentação de](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep) referência para a classe ParallelRunStep.

* Siga o [tutorial avançado](tutorial-pipeline-batch-scoring-classification.md) sobre a utilização de gasodutos com ParallelRunStep. O tutorial mostra como passar outro ficheiro como entrada lateral.
