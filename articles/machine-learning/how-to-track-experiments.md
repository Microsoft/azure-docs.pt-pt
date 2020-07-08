---
title: Experimente experiências de ML & métricas
titleSuffix: Azure Machine Learning
description: Monitorize as suas experiências Azure ML e monitorize as métricas de execução para melhorar o processo de criação de modelos. Adicione o registo no seu script de treino e veja os resultados registados de uma corrida.  Use run.log, Run.start_logging ou ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: how-to
ms.date: 03/12/2020
ms.custom: seodec18
ms.openlocfilehash: 426c79c19b599127e2235f61e8c917062ede3b79
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84675207"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Monitor Azure ML experimenta e métricas
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Melhore o processo de criação de modelos rastreando as suas experiências e monitorizando as métricas de execução. Neste artigo, aprenda a adicionar código de registo ao seu script de treino, submeta uma experiência, monitorize essa execução e inspecione os resultados em Azure Machine Learning.

> [!NOTE]
> A Azure Machine Learning também pode registar informações de outras fontes durante o treino, tais como corridas automáticas de machine learning, ou o contentor Docker que executa o trabalho de treino. Estes registos não estão documentados. Se encontrar problemas e contactar o suporte da Microsoft, poderão utilizar estes registos durante a resolução de problemas.

> [!TIP]
> A informação neste documento é principalmente para cientistas de dados e desenvolvedores que querem monitorizar o processo de formação de modelos. Se você é um administrador interessado em monitorizar a utilização de recursos e eventos da Azure Machine learning, tais como quotas, execuções de formação completas ou implementações de modelos concluídas, consulte [Monitoring Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Métricas disponíveis para acompanhar

As seguintes métricas podem ser adicionadas a uma corrida enquanto treina uma experiência. Para ver uma lista mais detalhada do que pode ser rastreado numa corrida, consulte a [documentação](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)de referência da classe Run .

|Tipo| Função python | Notas|
|----|:----|:----|
|Valores escalar |Função:<br>`run.log(name, value, description='')`<br><br>Exemplo:<br>run.log ("precisão", 0.95) |Faça um registo de valor numérico ou de corda para a execução com o nome próprio. Registar uma métrica para uma corrida faz com que a métrica seja armazenada no registo de execução na experiência.  Pode registar a mesma métrica várias vezes dentro de uma corrida, sendo o resultado considerado um vetor dessa métrica.|
|Listas|Função:<br>`run.log_list(name, value, description='')`<br><br>Exemplo:<br>run.log_list ("precisões", [0.6, 0.7, 0.87]) | Faça um registo de uma lista de valores para a execução com o nome próprio.|
|Linha|Função:<br>`run.log_row(name, description=None, **kwargs)`<br>Exemplo:<br>run.log_row("Y over X", x=1, y=0,4) | A utilização *de log_row* cria uma métrica com múltiplas colunas, conforme descrito em kwargs. Cada parâmetro nomeado gera uma coluna com o valor especificado.  *log_row* pode ser chamado uma vez para registar um tuple arbitrário, ou várias vezes em um loop para gerar uma mesa completa.|
|Tabela|Função:<br>`run.log_table(name, value, description='')`<br><br>Exemplo:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}} | Faça um login de um objeto dicionário para a execução com o nome próprio. |
|Imagens|Função:<br>`run.log_image(name, path=None, plot=None)`<br><br>Exemplo:<br>`run.log_image("ROC", plot=plt)` | Faça um registo de imagem no registo de execução. Utilize log_image para registar um . Ficheiro de imagem PNG ou um enredo de matplotlib para a corrida.  Estas imagens serão visíveis e comparáveis no registo de execução.|
|Marque uma corrida|Função:<br>`run.tag(key, value=None)`<br><br>Exemplo:<br>run.tag ("selecionado", "sim") | Marque a execução com uma chave de corda e valor de corda opcional.|
|Upload de arquivo ou diretório|Função:<br>`run.upload_file(name, path_or_stream)`<br> <br> Exemplo:<br>run.upload_file("best_model.pkl", "./model.pkl") | Faça o upload de um ficheiro para o registo de execução. Executa automaticamente o ficheiro de captura no diretório de saída especificado, que predefine em "./outputs" para a maioria dos tipos de execução.  Utilize upload_file apenas quando os ficheiros adicionais precisam de ser carregados ou não for especificado um diretório de saída. Sugerimos adicionar `outputs` ao nome para que seja enviado para o diretório de saídas. Pode listar todos os ficheiros que estão associados a este registo de execução chamado`run.get_file_names()`|

> [!NOTE]
> As métricas para escalões, listas, linhas e tabelas podem ter tipo: flutuar, inteiro ou corda.

## <a name="choose-a-logging-option"></a>Escolha uma opção de registo

Se quiser rastrear ou monitorizar a sua experiência, tem de adicionar código para começar a registar-se quando submeter a execução. Seguem-se formas de desencadear a submissão da execução:
* __Run.start_logging__ - Adicione funções de registo ao seu script de treino e inicie uma sessão de registo interativo na experiência especificada. **start_logging** cria uma corrida interativa para uso em cenários como cadernos. Quaisquer métricas registadas durante a sessão são adicionadas ao registo de execução na experiência.
* __ScriptRunConfig__ - Adicione funções de registo ao seu script de treino e carregue toda a pasta do script com a execução.  **ScriptRunConfig** é uma classe para configurar configurações para execuções de scripts. Com esta opção, pode adicionar código de monitorização para ser notificado da conclusão ou para obter um widget visual para monitorizar.
* __Registo de designers__ - Adicione funções de registo a um pipeline de design de drag-&-drop utilizando o módulo __executo python script.__ Adicione o código Python para registar experiências de designer. 

## <a name="set-up-the-workspace"></a>Configurar o espaço de trabalho
Antes de adicionar a madeira e submeter uma experiência, deve configurar o espaço de trabalho.

1. Carregue o espaço de trabalho. Para saber mais sobre a configuração do espaço de trabalho, consulte o [ficheiro de configuração do espaço de trabalho](how-to-configure-environment.md#workspace).

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_ws)]


## <a name="option-1-use-start_logging"></a>Opção 1: Utilizar start_logging

**start_logging** cria uma corrida interativa para uso em cenários como cadernos. Quaisquer métricas registadas durante a sessão são adicionadas ao registo de execução na experiência.

O exemplo seguinte treina um modelo simples de Sklearn Ridge localmente em um caderno jupyter local. Para saber mais sobre a submissão de experiências a diferentes ambientes, consulte [Configurar metas de computação para formação de modelos com Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets).

### <a name="load-the-data"></a>Carregar os dados

Este exemplo usa o conjunto de dados da diabetes, um conjunto de pequenos dados bem conhecido que vem com a aprendizagem de scikit. Esta célula carrega o conjunto de dados e divide-o em conjuntos de treino e teste aleatórios.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_data)]

### <a name="add-tracking"></a>Adicionar rastreio
Adicione o rastreio de experiências usando o Azure Machine Learning SDK e carreque um modelo persistido no registo de execução da experiência. O código seguinte adiciona tags, registos e envia um ficheiro modelo para a execução da experiência.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=create_experiment)]

O guião termina com ```run.complete()``` , que marca a execução como concluído.  Esta função é normalmente usada em cenários de cadernos interativos.

## <a name="option-2-use-scriptrunconfig"></a>Opção 2: Utilizar ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) é uma classe para configurar configurações para execuções de scripts. Com esta opção, pode adicionar código de monitorização para ser notificado da conclusão ou para obter um widget visual para monitorizar.

Este exemplo expande-se no modelo básico de Sklearn Ridge de cima. Faz uma simples varredura de parâmetros para varrer os valores alfa do modelo para capturar métricas e modelos treinados em corridas sob a experiência. O exemplo é dirigido localmente contra um ambiente gerido pelo utilizador. 

1. Crie um roteiro de `train.py` treino.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. As `train.py` referências de script `mylib.py` que permitem obter a lista de valores alfa a utilizar no modelo de cume.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. Configure um ambiente local gerido pelo utilizador.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=user_managed_env)]


4. Submeta o ```train.py``` script para ser executado no ambiente gerido pelo utilizador. Toda esta pasta de scripts é submetida para treino, incluindo o ```mylib.py``` ficheiro.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

## <a name="option-3-log-designer-experiments"></a>Opção 3: Experiências de log designer

Utilize o módulo __de script Execute Python__ para adicionar lógica de registo às suas experiências de designer. Pode registar qualquer valor usando este fluxo de trabalho, mas é especialmente útil para registar métricas a partir do módulo __De Modelo avaliar__ para rastrear o desempenho do modelo em diferentes execuções.

1. Ligue um módulo __de script de Python executado__ à saída do seu módulo de modelo de __avaliação.__

    ![Conecte o módulo de script python executado para avaliar o módulo do modelo](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. Cole o seguinte código no editor de código __execute Python__ para registar o erro absoluto médio para o seu modelo treinado:

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1 = None, dataframe2 = None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')

        from azureml.core import Run

        run = Run.get_context()

        # Log the mean absolute error to the current run to see the metric in the module detail pane.
        run.log(name='Mean_Absolute_Error', value=dataframe1['Mean_Absolute_Error'])

        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.
        run.parent.log(name='Mean_Absolute_Error', value=dataframe1['Mean_Absolute_Error'])
    
        return dataframe1,
    ```

## <a name="manage-a-run"></a>Gerir uma corrida

O [artigo start, monitor e cancele as corridas de treino](how-to-manage-runs.md) destaca fluxos de trabalho específicos de Aprendizagem automática do Azure para como gerir as suas experiências.

## <a name="view-run-details"></a>Ver detalhes de execução

### <a name="view-activequeued-runs-from-the-browser"></a>Ver execuções ativas/em fila a partir do navegador

Os alvos de cálculo usados para treinar modelos são um recurso partilhado. Como tal, podem ter várias corridas na fila ou ativas num dado momento. Para ver as corridas para um alvo de computação específico a partir do seu navegador, use os seguintes passos:

1. A partir do [estúdio Azure Machine Learning,](https://ml.azure.com/)selecione o seu espaço de trabalho e, em seguida, selecione __Compute__ do lado esquerdo da página.

1. Selecione __Training Clusters__ para apresentar uma lista de alvos de computação utilizados para o treino. Em seguida, selecione o cluster.

    ![Selecione o cluster de treino](./media/how-to-track-experiments/select-training-compute.png)

1. Selecione __Runs__. É apresentada a lista de execuções que utilizam este cluster. Para ver detalhes para uma execução específica, utilize o link na coluna __Executar.__ Para ver detalhes para a experiência, use o link na coluna __Experiment.__

    ![Selecione corridas para cluster de treino](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Uma corrida pode conter corridas de crianças, para que um trabalho de treino possa resultar em múltiplas entradas.

Uma vez concluída uma execução, deixará de ser exibida nesta página. Para ver informações sobre as execuções __completas,__ visite a secção Experiments do estúdio e selecione a experiência e corra. Para mais informações, consulte a secção [de métricas de execução](#queryrunmetrics) de consultas.

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Monitor executado com widget de caderno Jupyter
Quando utilizar o método **ScriptRunConfig** para submeter corridas, pode assistir ao progresso da corrida com um [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Tal como a submissão da execução, o widget é assíncrono e disponibiliza atualizações dinâmicas a cada 10 a 15 segundos até à conclusão do trabalho.

1. Veja o widget Jupyter enquanto espera que a corrida esteja completa.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Screenshot do widget de caderno Jupyter](./media/how-to-track-experiments/run-details-widget.png)

   Também pode obter um link para o mesmo ecrã no seu espaço de trabalho.

   ```python
   print(run.get_portal_url())
   ```

2. **[Para corridas automatizadas de aprendizagem automática de máquinas]** Para aceder às tabelas de uma execução anterior. `<<experiment_name>>`Substitua-a pelo nome de experiência adequado:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widget de caderno Jupyter para aprendizagem automática de máquinas](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Para ver mais detalhes de um pipeline clique no Pipeline que você gostaria de explorar na tabela, e as tabelas renderizarão em um pop-up do estúdio Azure Machine Learning.

### <a name="get-log-results-upon-completion"></a>Obter resultados do registo após a conclusão

O treino e monitorização do modelo ocorrem em segundo plano para que possa executar outras tarefas enquanto espera. Também pode esperar até que o modelo tenha terminado o treino antes de executar mais código. Quando utilizar **o ScriptRunConfig,** pode utilizar ```run.wait_for_completion(show_output = True)``` para mostrar quando o treino do modelo está completo. A ```show_output``` bandeira dá-lhe uma saída verbosa. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Métricas de execução de consulta

Pode ver as métricas de um modelo treinado utilizando ```run.get_metrics()``` . Agora pode obter todas as métricas que foram registadas no exemplo acima para determinar o melhor modelo.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>Veja a experiência no seu espaço de trabalho no [estúdio Azure Machine Learning](https://ml.azure.com)

Quando uma experiência terminar de correr, pode navegar para o registo de testes gravado. Você pode aceder à história a partir do [estúdio Azure Machine Learning](https://ml.azure.com).

Navegue no separador Experimentos e selecione a sua experiência. Você é trazido para o painel de corrida de experimentação, onde você pode ver métricas rastreadas e gráficos que são registados para cada corrida. 

Pode editar a tabela de listas de execução para mostrar o valor último, mínimo ou máximo registado para as suas execuções. Pode selecionar ou desmarcar várias corridas na lista de execuções e as execuções selecionadas irão preencher as tabelas com os seus dados. Também pode adicionar novos gráficos ou gráficos de edição para comparar as métricas registadas (valores mínimos, máximos, últimos ou todos) em vários períodos. Para explorar os seus dados de forma mais eficaz, também pode maximizar os seus gráficos.

:::image type="content" source="media/how-to-track-experiments/experimentation-tab.gif" alt-text="Execute detalhes no estúdio Azure Machine Learning":::

Pode perfurar para uma corrida específica para ver as suas saídas ou registos, ou transferir a imagem instantânea da experiência que submeteu para que possa partilhar a pasta da experiência com outras.

### <a name="viewing-charts-in-run-details"></a>Gráficos de visualização em detalhes de execução

Existem várias formas de usar as APIs de registo para registar diferentes tipos de métricas durante uma corrida e vê-las como gráficos no estúdio Azure Machine Learning.

|Valor Registado|Código de exemplo| Ver no portal|
|----|----|----|
|Registar uma matriz de valores numéricos| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|gráfico de linha de uma única variável|
|Registar um único valor numérico com o mesmo nome métrico repetidamente utilizado (como de dentro de um loop)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Gráfico de linha de variável única|
|Faça um registo de linha com 2 colunas numéricas repetidamente|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Gráfico de linha de duas variáveis|
|Tabela de registo com 2 colunas numéricas|`run.log_table(name='Sine Wave', value=sines)`|Gráfico de linha de duas variáveis|


## <a name="example-notebooks"></a>Blocos de notas de exemplo
Os seguintes cadernos demonstram conceitos neste artigo:
* [como usar-azureml/training/train-in-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [como usar-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [como usar-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximos passos

Experimente estes passos seguintes para aprender a utilizar o SDK do Azure Machine Learning para Python:

* Veja um exemplo de como registar o melhor modelo e implantá-lo no tutorial, Treine um modelo de [classificação de imagem com Azure Machine Learning](tutorial-train-models-with-aml.md).

* Saiba como treinar [modelos PyTorch com Azure Machine Learning](how-to-train-pytorch.md).
