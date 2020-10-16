---
title: Monitorar e visualizar registos de execução de ML & métricas
titleSuffix: Azure Machine Learning
description: Monitorize as suas experiências Azure ML e veja as métricas de execução para melhorar o processo de criação de modelos. Use widgets e o portal do estúdio para explorar o estado de execução e ver os registos de execução.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: aa4b55cb0700a47d9235a1d526ef1b1678d6db8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333822"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>Monitorar e visualizar ML executar registos e métricas



Neste artigo, aprende-se a monitorizar o Azure Machine Learning e a visualizar os seus registos. Antes de poder visualizar os registos, tem de os ativar primeiro. Para obter mais informações, consulte [Ativar o registo em treinos Azure ML](how-to-track-experiments.md).

Os registos podem ajudá-lo a diagnosticar erros e advertências, ou rastrear métricas de desempenho como parâmetros e precisão do modelo. Neste artigo, aprende a visualizar registos utilizando os seguintes métodos:

> [!div class="checklist"]
> * Monitor corre no estúdio
> * Monitor executa usando o widget Jupyter Notebook
> * Monitorize as aprendizagens automáticas de máquinas
> * Ver registos de saída após a conclusão
> * Ver registos de saída no estúdio

Para obter informações gerais sobre como gerir as suas experiências, consulte [Start, monitor e cancele as corridas de treino.](how-to-manage-runs.md)

## <a name="monitor-runs-in-the-studio"></a>Monitor corre no estúdio

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

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Monitor executa usando o widget de caderno Jupyter

Quando utilizar o método **ScriptRunConfig** para submeter execuções, pode observar o progresso da execução utilizando o [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true). Tal como a submissão da execução, o widget é assíncrono e disponibiliza atualizações dinâmicas a cada 10 a 15 segundos até à conclusão do trabalho.

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

Navegue no separador **Experimentos** e selecione a sua experiência. No painel de instrumentos de experimentação, pode ver métricas e registos rastreados para cada corrida. 

Desembre para uma corrida específica para ver as suas saídas ou registos, ou descarregue a imagem instantânea da experiência para que possa partilhar a pasta de experiências com outras.

Também pode editar a tabela de listas de execução para selecionar várias execuções e exibir o último, mínimo ou valor máximo registado para as suas execuções. Personalize os seus gráficos para comparar os valores e agregados de métricas registadas em várias corridas.

![Execute detalhes no estúdio Azure Machine Learning](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="format-charts-in-the-studio"></a>Gráficos de formato no estúdio

Utilize os seguintes métodos nas APIs de registo para influenciar o estúdio visualizando as suas métricas.

|Valor Registado|Código de exemplo| Formato no portal|
|----|----|----|
|Registar uma matriz de valores numéricos| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|gráfico de linha de uma única variável|
|Registar um único valor numérico com o mesmo nome métrico repetidamente utilizado (como de dentro de um loop)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Gráfico de linha de variável única|
|Faça um registo de linha com 2 colunas numéricas repetidamente|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Gráfico de linha de duas variáveis|
|Tabela de registo com 2 colunas numéricas|`run.log_table(name='Sine Wave', value=sines)`|Gráfico de linha de duas variáveis|


## <a name="next-steps"></a>Passos seguintes

Experimente estes próximos passos para aprender a usar a Azure Machine Learning:

* Aprenda a [rastrear experiências e ativer registos no designer de Aprendizagem automática Azure.](how-to-track-designer-experiments.md)

* Veja um exemplo sobre como registar o melhor modelo e implementá-lo no tutorial [Preparar um modelo de classificação de imagens com o Azure Machine Learning](tutorial-train-models-with-aml.md).

