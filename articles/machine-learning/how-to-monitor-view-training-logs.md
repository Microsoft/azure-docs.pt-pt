---
title: Monitorar e visualizar registos de execução de ML & métricas
titleSuffix: Azure Machine Learning
description: Monitorize as suas experiências ml e veja as métricas de execução com widgets Jupyter e o estúdio Azure Machine Learning.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: ea96e1056e6157cfddbdc2f0b6451ed55a74d1de
ms.sourcegitcommit: 90caa05809d85382c5a50a6804b9a4d8b39ee31e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97756063"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>Monitorar e visualizar ML executar registos e métricas

Saiba como monitorizar a aprendizagem automática do Azure e veja os seus registos. 

Quando fazes uma experiência, os registos e as métricas são transmitidos para ti.  Além disso, pode adicionar o seu próprio.  Para saber como, consulte [Enable iniciar sessão em treinos Azure ML](how-to-track-experiments.md).

Os registos podem ajudá-lo a diagnosticar erros e avisos para a sua execução. Métricas de desempenho como parâmetros e precisão do modelo ajudam-no a rastrear e monitorizar as suas corridas.

Neste artigo, aprende a visualizar registos utilizando os seguintes métodos:

> [!div class="checklist"]
> * Monitor corre no estúdio
> * Monitor executa usando o widget Jupyter Notebook
> * Monitorize as aprendizagens automáticas de máquinas
> * Ver registos de saída após a conclusão
> * Ver registos de saída no estúdio

Para obter informações gerais sobre como gerir as suas experiências, consulte [Start, monitor e cancele as corridas de treino.](how-to-manage-runs.md)

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Monitor executa usando o widget de caderno Jupyter

Quando utilizar o método **ScriptRunConfig** para submeter execuções, pode observar o progresso da execução utilizando o [widget Jupyter](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py). Tal como a submissão da execução, o widget é assíncrono e disponibiliza atualizações dinâmicas a cada 10 a 15 segundos até à conclusão do trabalho.

Veja o widget Jupyter enquanto espera que a corrida esteja completa.
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Screenshot do widget de caderno Jupyter](./media/how-to-track-experiments/run-details-widget.png)

Também pode obter um link para o mesmo ecrã no seu espaço de trabalho.

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>Monitorize as aprendizagens automáticas de máquinas

Para que o machine learning funciona automatizado, para aceder às tabelas de uma execução anterior, `<<experiment_name>>` substitua-os pelo nome de experiência apropriado:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Widget de caderno Jupyter para aprendizagem automática de máquinas](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>Mostrar saída após a conclusão

Quando utilizar **o ScriptRunConfig,** pode utilizar ```run.wait_for_completion(show_output = True)``` para mostrar quando o treino do modelo está completo. A ```show_output``` bandeira dá-lhe uma saída verbosa. Para obter mais informações, consulte a secção ScriptRunConfig de [Como permitir a sessão de registo](how-to-track-experiments.md#scriptrun-logs).

<a id="queryrunmetrics"></a>

## <a name="query-run-metrics"></a>Métricas de execução de consulta

Pode ver as métricas de um modelo treinado utilizando ```run.get_metrics()``` . Por exemplo, pode usar isto com o exemplo acima para determinar o melhor modelo procurando o modelo com o menor valor quadrado de erro quadrado médio (mse).

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-records-in-the-studio"></a>Ver discos de execução no estúdio

Pode navegar em registos completos, incluindo métricas registadas, no [estúdio Azure Machine Learning](https://ml.azure.com).

Navegue para o **separador Experimentos.** Para visualizar todas as suas corridas no seu espaço de trabalho através de Experiências, selecione o separador **Todas as execuções.** Pode perfurar em execuções para experiências específicas aplicando o filtro Experiment na barra de menu superior.

Para a vista experimento individual, selecione o separador **Todas as experiências.** No painel de instrumentos de experimentação, pode ver métricas e registos rastreados para cada corrida. 

Também pode editar a tabela de listas de execução para selecionar várias execuções e exibir o último, mínimo ou valor máximo registado para as suas execuções. Personalize os seus gráficos para comparar os valores e agregados de métricas registadas em várias corridas. 

![Execute detalhes no estúdio Azure Machine Learning](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="format-charts"></a>Gráficos de formato 

Utilize os seguintes métodos nas APIs de exploração madeireira para influenciar as visualizações de métricas.

|Valor Registado|Código de exemplo| Formato no portal|
|----|----|----|
|Registar uma matriz de valores numéricos| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|gráfico de linha de uma única variável|
|Registar um único valor numérico com o mesmo nome métrico repetidamente utilizado (como de dentro de um loop)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Gráfico de linha de variável única|
|Faça um registo de linha com 2 colunas numéricas repetidamente|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Gráfico de linha de duas variáveis|
|Tabela de registo com 2 colunas numéricas|`run.log_table(name='Sine Wave', value=sines)`|Gráfico de linha de duas variáveis|


### <a name="view-log-files-for-a-run"></a>Ver ficheiros de registo para uma execução 

Os ficheiros de registo são um recurso essencial para depurar as cargas de trabalho do Azure ML. Aprofundar até uma corrida específica para ver os seus registos e saídas:  

1. Navegue para o **separador Experimentos.**
1. Selecione o runID para uma execução específica.
1. Selecione **Saídas e registos** no topo da página.

:::image type="content" source="media/how-to-monitor-view-training-logs/view-logs.png" alt-text="Screenshot da secção de saída e registos de uma corrida":::

As tabelas abaixo mostram o conteúdo dos ficheiros de registo nas pastas que verá nesta secção.

> [!NOTE]
> Não verá necessariamente todos os ficheiros para cada execução. Por exemplo, o 20_image_build_log*.txt só aparece quando uma nova imagem é construída (por exemplo, quando muda de ambiente).

#### <a name="azureml-logs-folder"></a>`azureml-logs` pasta

|Ficheiro  |Description  |
|---------|---------|
|20_image_build_log.txt     | Registo de construção de imagem estivador para o ambiente de treino, opcional, um por corrida. Só é aplicável na atualização do seu Ambiente. Caso contrário, a AML reutilizará a imagem em cache. Se for bem sucedido, contém detalhes do registo de imagem para a imagem correspondente.         |
|55_azureml execução-<node_id>.txt     | stdout/stderr log da ferramenta hospedeira, um por nó. Puxa a imagem para calcular o alvo. Note que este registo só aparece depois de ter garantido recursos de computação.         |
|65_job_prep-<node_id>.txt     |   stdout/stderr log do script de preparação de trabalho, um por nó. Faça o download do seu código para calcular o alvo e as datastores (se solicitado).       |
|.txt 70_driver_log(_x)      |  registo stdout/stderr do script de controlo AML e script de treinamento do cliente, um por processo. **Esta é a saída padrão do seu script. É aqui que aparecem os registos do seu código (por exemplo, declarações de impressão).** Na maioria dos casos, irá monitorizar os registos aqui.       |
|70_mpi_log.txt     |   Registo-quadro mpi, opcional, um por corrida. Só para a execução de MPI.   |
|75_job_post-<node_id>.txt     |  stdout/stderr log do script de lançamento de trabalho, um por nó. Envie registos, liberte os recursos computacional de volta para Azure.        |
|process_info.jsem      |   mostrar que processo está em execução em que nó.  |
|process_status.jsem      | mostrar o estado do processo, isto é, se um processo não for iniciado, em execução ou concluído.         |

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
|prep_cmd.txt      |   Log for ContextManagers introduzidos quando `job_prep.py` é executado (alguns destes serão transmitidos `azureml-logs/65-job_prep` para)       |
|release_cmd.txt     |  Log for ComtextManagers saiu quando `job_release.py` é executado        |

#### <a name="other-folders"></a>Outras pastas

Para a formação de empregos em clusters multi-compute, os registos estão presentes para cada período de inquérito. A estrutura para cada nó é a mesma que os trabalhos de nó único. Há uma pasta de registos adicionais para execução geral, stderr e registos de sepido.

A Azure Machine Learning regista informações de várias fontes durante o treino, tais como AutoML ou o contentor Docker que gere o trabalho de formação. Muitos destes registos não estão documentados. Se encontrar problemas e contactar o suporte da Microsoft, poderão utilizar estes registos durante a resolução de problemas.

## <a name="monitor-a-compute-cluster"></a>Monitorize um cluster de cálculo

Para monitorizar as corres para um alvo de computação específico a partir do seu navegador, utilize os seguintes passos:

1. No [estúdio Azure Machine Learning,](https://ml.azure.com/)selecione o seu espaço de trabalho e, em seguida, selecione __Compute__ do lado esquerdo da página.

1. Selecione __Training Clusters__ para apresentar uma lista de alvos de computação utilizados para o treino. Em seguida, selecione o cluster.

    ![Selecione o cluster de treino](./media/how-to-track-experiments/select-training-compute.png)

1. Selecione __Runs__. É apresentada a lista de execuções que utilizam este cluster. Para ver detalhes para uma execução específica, utilize o link na coluna __Executar.__ Para ver detalhes para a experiência, use o link na coluna __Experiment.__

    ![Selecione corridas para cluster de treino](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Uma vez que os alvos de computação de treino são um recurso partilhado, eles podem ter múltiplas corridas em fila ou ativas num dado momento.
    > 
    > Uma corrida pode conter corridas de crianças, para que um trabalho de treino possa resultar em múltiplas entradas.

Uma vez concluída uma execução, deixará de ser exibida nesta página. Para ver informações sobre as execuções __completas,__ visite a secção Experiments do estúdio e selecione a experiência e corra. Para obter mais informações, consulte a secção [Ver métricas para execuções completas](#view-the-experiment-in-the-web-portal).


## <a name="next-steps"></a>Passos seguintes

Experimente estes próximos passos para aprender a usar a Azure Machine Learning:

* Aprenda a [rastrear experiências e ativer registos no designer de Aprendizagem automática Azure.](how-to-track-designer-experiments.md)

* Veja um exemplo sobre como registar o melhor modelo e implementá-lo no tutorial [Preparar um modelo de classificação de imagens com o Azure Machine Learning](tutorial-train-models-with-aml.md).
