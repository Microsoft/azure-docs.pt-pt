---
title: Registar & visualizar métricas e ficheiros de registo
titleSuffix: Azure Machine Learning
description: Ativar o registo no seu treino de ML para monitorizar as métricas de execução em tempo real e para ajudar a diagnosticar erros e avisos.
services: machine-learning
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 04/19/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bc7564a11f570833bd6cb10c14267e08c3b5753
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820557"
---
# <a name="log--view-metrics-and-log-files"></a>Registar & visualizar métricas e ficheiros de registo

Inicie informações em tempo real utilizando o pacote de registo padrão python e a funcionalidade específica de Azure Machine Learning Python SDK. Pode registar localmente e enviar registos para a sua área de trabalho no portal.

Os registos podem ajudá-lo a diagnosticar erros e avisos ou a acompanhar métricas de desempenho, como parâmetros e desempenho do modelo. Neste artigo, vai aprender a ativar o registo nos seguintes cenários:

> [!div class="checklist"]
> * Métricas de execução de registo
> * Sessões de preparação interativas
> * Enviar tarefas de preparação com ScriptRunConfig
> * Definições de `logging` nativas do Python
> * Registo de origens adicionais


> [!TIP]
> Este artigo mostra-lhe como monitorizar o processo de preparação do modelo. Se estiver interessado em monitorizar a utilização de recursos e os eventos do Azure Machine Learning, como quotas, execuções de preparação concluídas ou implementações de modelos concluídas, veja [Monitorizar o Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Tipos de dados

Pode registar vários tipos de dados, incluindo valores escalares, listas, tabelas, imagens e diretórios, entre outros. Para obter mais informações e exemplos de código do Python para diferentes tipos de dados, veja a [página de referência da classe Run](/python/api/azureml-core/azureml.core.run%28class%29).

### <a name="logging-run-metrics"></a>Métricas de execução de registo 

Utilize os seguintes métodos nas APIs de exploração madeireira para influenciar as visualizações de métricas. Note os [limites de serviço](./resource-limits-quotas-capacity.md#metrics) para estas métricas registadas. 

|Valor Registado|Código de exemplo| Formato no portal|
|----|----|----|
|Registar uma matriz de valores numéricos| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|gráfico de linha de uma única variável|
|Registar um único valor numérico com o mesmo nome métrico repetidamente utilizado (como de dentro de um loop)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Gráfico de linha de variável única|
|Faça um registo de linha com 2 colunas numéricas repetidamente|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Gráfico de linha de duas variáveis|
|Tabela de registo com 2 colunas numéricas|`run.log_table(name='Sine Wave', value=sines)`|Gráfico de linha de duas variáveis|
|Imagem de registo|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Utilize este método para registar um ficheiro de imagem ou um enredo de matplotlib para a execução. Estas imagens serão visíveis e comparáveis no registo de corridas|

### <a name="logging-with-mlflow"></a>Registo com MLflow
Utilize o MLFlowLogger para registar métricas.

```python
from azureml.core import Run
# connect to the workspace from within your running code
run = Run.get_context()
ws = run.experiment.workspace

# workspace has associated ml-flow-tracking-uri
mlflow_url = ws.get_mlflow_tracking_uri()

#Example: PyTorch Lightning
from pytorch_lightning.loggers import MLFlowLogger

mlf_logger = MLFlowLogger(experiment_name=run.experiment.name, tracking_uri=mlflow_url)
mlf_logger._run_id = run.id
```

## <a name="view-run-metrics"></a>Ver métricas de execução

## <a name="via-the-sdk"></a>Através do SDK
Pode ver as métricas de um modelo treinado utilizando ```run.get_metrics()``` . Veja o exemplo abaixo. 

```python
from azureml.core import Run
run = Run.get_context()
run.log('metric-name', metric_value)

metrics = run.get_metrics()
# metrics is of type Dict[str, List[float]] mapping mertic names
# to a list of the values for that metric in the given run.

metrics.get('metric-name')
# list of metrics in the order they were recorded
```

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-metrics-in-aml-studio-ui"></a>Ver métricas de execução no estúdio AML UI

Pode navegar em registos completos, incluindo métricas registadas, no [estúdio Azure Machine Learning](https://ml.azure.com).

Navegue para o **separador Experimentos.** Para visualizar todas as suas corridas no seu espaço de trabalho através de Experiências, selecione o separador **Todas as execuções.** Pode perfurar em execuções para experiências específicas aplicando o filtro Experiment na barra de menu superior.

Para a vista experimento individual, selecione o separador **Todas as experiências.** No painel de instrumentos de experimentação, pode ver métricas e registos rastreados para cada corrida. 

Também pode editar a tabela de listas de execução para selecionar várias execuções e exibir o último, mínimo ou valor máximo registado para as suas execuções. Personalize os seus gráficos para comparar os valores e agregados de métricas registadas em várias corridas. Pode traçar várias métricas no eixo y da sua carta e personalizar o seu eixo x para traçar as suas métricas registadas. 


### <a name="view-and-download-log-files-for-a-run"></a>Ver e descarregar ficheiros de registo para uma execução 

Os ficheiros de registo são um recurso essencial para depurar as cargas de trabalho do Azure ML. Depois de submeter um trabalho de formação, faça um ensaio específico para ver os seus registos e saídas:  

1. Navegue para o **separador Experimentos.**
1. Selecione o runID para uma execução específica.
1. Selecione **Saídas e registos** no topo da página.
2. Selecione **Baixar tudo** para descarregar todos os seus registos numa pasta zip.
3. Também pode descarregar ficheiros de registo individuais escolhendo o ficheiro de registo e selecionando **Download**

:::image type="content" source="media/how-to-log-view-metrics/download-logs.png" alt-text="Screenshot da secção de saída e registos de uma corrida.":::

As tabelas abaixo mostram o conteúdo dos ficheiros de registo nas pastas que verá nesta secção.

> [!NOTE]
> Não verá necessariamente todos os ficheiros para cada execução. Por exemplo, o 20_image_build_log*.txt só aparece quando uma nova imagem é construída (por exemplo, quando muda de ambiente).

#### <a name="azureml-logs-folder"></a>`azureml-logs` pasta

|Ficheiro  |Description  |
|---------|---------|
|20_image_build_log.txt     | Registo de construção de imagem estivador para o ambiente de treino, opcional, um por corrida. Só é aplicável na atualização do seu Ambiente. Caso contrário, a AML reutilizará a imagem em cache. Se for bem sucedido, contém detalhes do registo de imagem para a imagem correspondente.         |
|55_azureml execução-<node_id>.txt     | stdout/stderr log da ferramenta hospedeira, um por nó. Puxa a imagem para calcular o alvo. Note que este registo só aparece depois de ter garantido recursos de computação.         |
|65_job_prep-<node_id>.txt     |   stdout/stderr log do script de preparação de trabalho, um por nó. Faça o download do seu código para calcular o alvo e as datastores (se solicitado).       |
|.txt 70_driver_log(_x)      |  registo stdout/stderr do script de controlo AML e script de treinamento do cliente, um por processo. **Saída padrão do seu script. Este ficheiro é onde os registos do seu código (por exemplo, declarações de impressão) aparecem.** Na maioria dos casos, irá monitorizar os registos aqui.       |
|70_mpi_log.txt     |   Registo-quadro mpi, opcional, um por corrida. Só para a execução de MPI.   |
|75_job_post-<node_id>.txt     |  stdout/stderr log do script de lançamento de trabalho, um por nó. Envie registos, liberte os recursos computacional de volta para Azure.        |
|process_info.jsem      |   mostrar que processo está em execução em que nó.  |
|process_status.jsem      | mostrar o estado do processo, como se um processo não for iniciado, em execução ou concluído.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` pasta

|Ficheiro  |Description  |
|---------|---------|
|110_azureml.log      |         |
|job_prep_azureml.log     |   registo do sistema para a preparação do trabalho        |
|job_release_azureml.log     | registo do sistema para a libertação de emprego        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` pasta

Quando o sidecar estiver ativado, os scripts de preparação de emprego e de lançamento de emprego serão executados dentro do contentor sidecar.  Há uma pasta para cada nó. 

|Ficheiro  |Description  |
|---------|---------|
|start_cms.txt     |  Registo de processo que começa quando o Contentor Sidecar começa       |
|prep_cmd.txt      |   Log for ContextManagers introduzidos quando `job_prep.py` é executado (alguns destes conteúdos serão transmitidos `azureml-logs/65-job_prep` para)       |
|release_cmd.txt     |  Log for ComtextManagers saiu quando `job_release.py` é executado        |

#### <a name="other-folders"></a>Outras pastas

Para a formação de empregos em clusters multi-compute, os registos estão presentes para cada período de inquérito. A estrutura para cada nó é a mesma que os trabalhos de nó único. Há mais uma pasta de registos para execução geral, stderr e registos de stdout.

A Azure Machine Learning regista informações de várias fontes durante o treino, como o AutoML ou o contentor Docker que gere o trabalho de formação. Muitos destes registos não estão documentados. Se encontrar problemas e contactar o suporte da Microsoft, poderão utilizar estes registos durante a resolução de problemas.


## <a name="interactive-logging-session"></a>Sessão de registo interativa

Normalmente, as sessões de registo interativas são utilizadas em ambientes de blocos de notas. O método [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) inicia uma sessão de registo interativa. Todas as métricas registadas durante a sessão são adicionadas ao registo de execução na experimentação. O método [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) encerra as sessões e marca a execução como concluída.

## <a name="scriptrun-logs"></a>Registos ScriptRun

Nesta secção, irá aprender a adicionar código de registo dentro de execuções criadas quando configuradas com ScriptRunConfig. Pode utilizar a classe [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig) para encapsular os scripts e os ambientes para execuções repetíveis. Também pode utilizar esta opção para mostrar um widget de elemento visual de Jupyter Notebooks para monitorização.

Este exemplo realiza um varrimento de parâmetros em valores alfa e captura os resultados com o método [run.log()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----).

1. Crie um script de preparação que inclua a lógica de registo, `train.py`.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Envie o script ```train.py``` a ser executado num ambiente gerido pelo utilizador. A pasta completa do script é enviada para preparação.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)]


   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    O parâmetro `show_output` ativa o registo verboso, o que lhe permite ver detalhes do processo de preparação, bem como informações sobre quaisquer recursos remotos ou destinos de computação. Utilize o código seguinte para ativar o registo verboso ao enviar a experimentação.

```python
run = exp.submit(src, show_output=True)
```

Também pode utilizar o mesmo parâmetro na função `wait_for_completion` na execução resultante.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Registo do Python nativo

Alguns registos no SDK podem conter um erro que instrui o utilizador a definir o nível de registo como DEPURAÇÃO. Para definir o nível de registo, adicione o código seguinte ao seu script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="other-logging-sources"></a>Outras fontes de exploração madeireira

O Azure Machine Learning também pode registar informações de outras origens durante a preparação, como execuções de machine learning automatizado ou contentores do Docker que executam as tarefas. Estes registos não estão documentados, mas se encontrar problemas e entrar em contacto com o suporte da Microsoft, este pode utilizá-los durante a resolução de problemas.

Para obter informações sobre as métricas de registo no estruturador do Azure Machine Learning, veja [Como registar métricas no estruturador](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Blocos de notas de exemplo

Os blocos de notas seguintes demonstram conceitos neste artigo:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

Veja estes artigos para saber mais sobre como utilizar o Azure Machine Learning:

* Veja um exemplo sobre como registar o melhor modelo e implementá-lo no tutorial [Preparar um modelo de classificação de imagens com o Azure Machine Learning](tutorial-train-models-with-aml.md).