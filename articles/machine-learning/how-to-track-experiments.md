---
title: Log ML experimenta & métricas
titleSuffix: Azure Machine Learning
description: Monitorize as suas experiências Azure ML e monitorize métricas de execução para melhorar o processo de criação do modelo. Adicione o loglogging ao seu script de treino e veja os resultados registados de uma corrida.  Utilize run.log, Run.start_logging ou ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/12/2020
ms.custom: seodec18
ms.openlocfilehash: dcd5668fa2c6e1840eed13a9ee0cbd30d8d8a25a
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983249"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Monitor Azure ML executa e métricas
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Melhore o processo de criação do modelo, rastreando as suas experiências e monitorizando as métricas de execução. Neste artigo, aprenda a adicionar código de registo ao seu script de treino, envie uma experiência, monitorize e inspecione os resultados em Azure Machine Learning.

> [!NOTE]
> O Azure Machine Learning também pode registar informações de outras fontes durante o treino, tais como corridas automatizadas de aprendizagem automática de máquinas, ou o recipiente Docker que executa o trabalho de formação. Estes registos não estão documentados. Se encontrar problemas e contactar o suporte da Microsoft, poderão utilizar estes registos durante a resolução de problemas.

> [!TIP]
> A informação neste documento é principalmente para cientistas de dados e desenvolvedores que querem monitorizar o processo de formação de modelos. Se for um administrador interessado em monitorizar a utilização de recursos e eventos da Aprendizagem de Máquinas Azure, tais como quotas, execuções de formação concluídas ou implementações de modelos concluídas, consulte [Monitorização da Aprendizagem automática azure](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Métricas disponíveis para rastrear

As seguintes métricas podem ser adicionadas a uma corrida enquanto treinam uma experiência. Para ver uma lista mais detalhada do que pode ser rastreado em uma corrida, consulte a [documentação](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)de referência da classe Run .

|Tipo| Função Python | Notas|
|----|:----|:----|
|Valores escalar |Função:<br>`run.log(name, value, description='')`<br><br>Exemplo:<br>run.log ("precisão", 0,95) |Faça loga numérico ou valor de cadeia na execução com o nome dado. Registar uma métrica a uma corrida faz com que essa métrica seja armazenada no registo de execução da experiência.  Você pode registar a mesma métrica várias vezes dentro de uma corrida, sendo o resultado considerado um vetor dessa métrica.|
|Listas|Função:<br>`run.log_list(name, value, description='')`<br><br>Exemplo:<br>run.log_list ("exatidão", [0,6, 0,7, 0,87]) | Faça loga numa lista de valores para a execução com o nome dado.|
|Linha|Função:<br>`run.log_row(name, description=None, **kwargs)`<br>Exemplo:<br>run.log_row ("Y over X", x=1, y=0.4) | Usar *log_row* cria uma métrica com múltiplas colunas como descrito em kwargs. Cada parâmetro nomeado gera uma coluna com o valor especificado.  *log_row* pode ser chamado uma vez para registar uma tuple arbitrária, ou várias vezes em um loop para gerar uma tabela completa.|
|Tabela|Função:<br>`run.log_table(name, value, description='')`<br><br>Exemplo:<br>run.log_table ("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]} | Faça loga num objeto de dicionário para a execução com o nome dado. |
|Imagens|Função:<br>`run.log_image(name, path=None, plot=None)`<br><br>Exemplo:<br>`run.log_image("ROC", plot=plt)` | Faça log a imagem no registo de execução. Utilize log_image para registar um . Ficheiro de imagem PNG ou um enredo matplotlib para a execução.  Estas imagens serão visíveis e comparáveis no registo de execução.|
|Marque uma corrida|Função:<br>`run.tag(key, value=None)`<br><br>Exemplo:<br>run.tag("selecionado", "sim") | Marque a corrida com uma chave de cordas e valor de corda opcional.|
|Upload de ficheiro ou diretório|Função:<br>`run.upload_file(name, path_or_stream)`<br> <br> Exemplo:<br>run.upload_file ("best_model.pkl", "./model.pkl") | Faça o upload de um ficheiro para o registo de execução. Executa a captura automática do ficheiro no diretório de saída especificado, que se incorre em "./saídas" para a maioria dos tipos de execução.  Utilize upload_file apenas quando for necessário carregar ficheiros adicionais ou não for especificado um diretório de saída. Sugerimos `outputs` adicionar ao nome para que seja enviado para o diretório de saídas. Pode listar todos os ficheiros associados a este registo de execução chamado`run.get_file_names()`|

> [!NOTE]
> As métricas para escalars, listas, linhas e mesas podem ter tipo: flutuador, inteiro ou corda.

## <a name="choose-a-logging-option"></a>Escolha uma opção de exploração madeireira

Se pretender acompanhar ou monitorizar a sua experiência, tem de adicionar código para iniciar a exploração de sessão quando submeter a execução. Seguem-se formas de desencadear a submissão da execução:
* __Run.start_logging__ - Adicione as funções de registo ao seu script de treino e inicie uma sessão de registo interativo na experiência especificada. **start_logging** cria uma corrida interativa para uso em cenários como cadernos. Quaisquer métricas que sejam registadas durante a sessão são adicionadas ao recorde de execução na experiência.
* __ScriptRunConfig__ - Adicione funções de registo ao seu script de treino e carregue toda a pasta de script com a execução.  **ScriptRunConfig** é uma classe para configurar configurações para scripts. Com esta opção, pode adicionar código de monitorização a ser notificado da conclusão ou obter um widget visual para monitorizar.

## <a name="set-up-the-workspace"></a>Configurar o espaço de trabalho
Antes de adicionar a exploração madeireira e submeter uma experiência, deve configurar o espaço de trabalho.

1. Carregue o espaço de trabalho. Para saber mais sobre a definição da configuração do espaço de trabalho, consulte o ficheiro de [configuração](how-to-configure-environment.md#workspace)do espaço de trabalho .

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_ws)]


## <a name="option-1-use-start_logging"></a>Opção 1: Utilizar start_logging

**start_logging** cria uma corrida interativa para uso em cenários como cadernos. Quaisquer métricas que sejam registadas durante a sessão são adicionadas ao recorde de execução na experiência.

O exemplo seguinte treina um modelo simples de Ridge localmente em um caderno jupyter local. Para saber mais sobre a submissão de experiências para diferentes ambientes, consulte Definir metas de [computação para formação de modelos com o Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets)

### <a name="load-the-data"></a>Carregar os dados

Este exemplo usa o conjunto de dados da diabetes, um conjunto de dados bem conhecido que vem com scikit-learn. Esta célula carrega o conjunto de dados e divide-o em conjuntos de treino e teste aleatórios.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_data)]

### <a name="add-tracking"></a>Adicionar rastreio
Adicione o rastreio de experiências utilizando o Azure Machine Learning SDK e carregue um modelo persistente no registo de execução da experiência. O código seguinte adiciona tags, logs e envia um ficheiro modelo para a execução da experiência.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=create_experiment)]

O guião ```run.complete()```termina com , que marca a execução como concluída.  Esta função é normalmente usada em cenários de cadernos interativos.

## <a name="option-2-use-scriptrunconfig"></a>Opção 2: Utilizar scriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) é uma classe para configurar configurações para scripts. Com esta opção, pode adicionar código de monitorização a ser notificado da conclusão ou obter um widget visual para monitorizar.

Este exemplo expande-se no modelo básico de Ridge sklearn de cima. Faz uma simples varredura de parâmetros para varrer os valores alfa do modelo para capturar métricas e modelos treinados em corridas sob a experiência. O exemplo é dirigido localmente contra um ambiente gerido pelo utilizador. 

1. Crie um `train.py`roteiro de treino.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. As `train.py` referências `mylib.py` do script que lhe permitem obter a lista de valores alfa para usar no modelo ridge.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. Configure um ambiente local gerido pelo utilizador.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=user_managed_env)]


4. Envie ```train.py``` o script para executar no ambiente gerido pelo utilizador. Toda esta pasta de script é ```mylib.py``` submetida para treino, incluindo o ficheiro.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]




## <a name="manage-a-run"></a>Gerir uma corrida

O artigo de [início, monitor e cancelamento](how-to-manage-runs.md) de executões de treino destaca fluxos de trabalho específicos de Aprendizagem automática azure para como gerir as suas experiências.

## <a name="view-run-details"></a>Ver detalhes da execução

### <a name="view-activequeued-runs-from-the-browser"></a>Ver funciona ativo/fila do navegador

Os alvos computacionais usados para treinar modelos são um recurso partilhado. Como tal, podem ter várias corridas em fila ou ativas num dado momento. Para ver as correções para um alvo de cálculo específico do seu navegador, use os seguintes passos:

1. A partir do [estúdio Azure Machine Learning,](https://ml.azure.com/)selecione o seu espaço de trabalho e, em seguida, selecione __Compute__ do lado esquerdo da página.

1. Selecione __Clusters de Treino__ para apresentar uma lista de alvos de computação utilizados para o treino. Em seguida, selecione o cluster.

    ![Selecione o cluster de treino](./media/how-to-track-experiments/select-training-compute.png)

1. Selecione __Runs__. A lista de execuções que utilizam este cluster é apresentada. Para visualizar detalhes para uma execução específica, utilize o link na coluna __Executar.__ Para visualizar detalhes para a experiência, utilize o link na coluna __Experiment.__

    ![Selecione correções para o cluster de treino](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Uma corrida pode conter corridas de crianças, por isso um trabalho de treino pode resultar em múltiplas entradas.

Uma vez concluída uma execução, já não é exibida nesta página. Para ver informações sobre as execuções __concluídas,__ visite a secção Experiments do estúdio e selecione a experiência e executar. Para mais informações, consulte a secção de métricas de execução da [Consulta.](#queryrunmetrics)

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Monitor executar com widget de caderno Jupyter
Quando utilizar o método **ScriptRunConfig** para submeter execuções, pode assistir ao progresso da execução com um [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Tal como a submissão da execução, o widget é assíncrono e disponibiliza atualizações dinâmicas a cada 10 a 15 segundos até à conclusão do trabalho.

1. Veja o widget Jupyter enquanto espera que a corrida esteja concluída.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Screenshot do widget do caderno Jupyter](./media/how-to-track-experiments/run-details-widget.png)

   Também pode obter uma ligação com o mesmo ecrã no seu espaço de trabalho.

   ```python
   print(run.get_portal_url())
   ```

2. **[Para corridas automatizadas de aprendizagem automática de máquinas]** Para aceder às tabelas de uma corrida anterior. Substitua `<<experiment_name>>` pelo nome da experiência apropriada:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widget de caderno jupyter para aprendizagem automática de máquinas](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Para ver mais detalhes de um pipeline clique no Pipeline que você gostaria de explorar na tabela, e as tabelas renderizarão em um pop-up do estúdio Azure Machine Learning.

### <a name="get-log-results-upon-completion"></a>Obter resultados do registo após a conclusão

O treino e monitorização do modelo ocorrem em segundo plano para que possa executar outras tarefas enquanto espera. Também pode esperar até que o modelo termine o treino antes de executar mais código. Quando utilizar o **ScriptRunConfig,** pode utilizar ```run.wait_for_completion(show_output = True)``` para mostrar quando o treino do modelo está completo. A ```show_output``` bandeira dá-lhe saída verbosa. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Métricas de execução de consulta

Pode ver as métricas de ```run.get_metrics()```um modelo treinado usando . Agora pode obter todas as métricas que foram registadas no exemplo acima para determinar o melhor modelo.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>Veja a experiência no seu espaço de trabalho no [estúdio Azure Machine Learning](https://ml.azure.com)

Quando uma experiência terminar de correr, pode navegar até ao registo de experiências gravadas. Você pode aceder à história do [estúdio Azure Machine Learning.](https://ml.azure.com)

Navegue para o separador Experimentos e selecione a sua experiência. Você é trazido para o painel de experimentação executar, onde você pode ver métricas e gráficos rastreados que são registrados para cada corrida. Neste caso, registamos a MSE e os valores alfa.

  ![Executar detalhes no estúdio Azure Machine Learning](./media/how-to-track-experiments/experiment-dashboard.png)

Pode perfurar até uma execução específica para visualizar as suas saídas ou registos, ou descarregar a imagem instantânea da experiência que submeteu para que possa partilhar a pasta da experiência com outros.

### <a name="viewing-charts-in-run-details"></a>Gráficos de visualização em detalhes de execução

Existem várias formas de usar as APIs de exploração para gravar diferentes tipos de métricas durante uma corrida e vê-las como gráficos no estúdio Azure Machine Learning.

|Valor Registado|Código de exemplo| Ver no portal|
|----|----|----|
|Registar uma série de valores numéricos| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|gráfico de linha de única variável|
|Registar um único valor numérico com o mesmo nome métrico repetidamente utilizado (como dentro de um para loop)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Gráfico de linha de variável única|
|Registar uma linha com 2 colunas numéricas repetidamente|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Gráfico de linha de duas variáveis|
|Mesa de registo com 2 colunas numéricas|`run.log_table(name='Sine Wave', value=sines)`|Gráfico de linha de duas variáveis|


## <a name="example-notebooks"></a>Exemplo de cadernos
Os seguintes cadernos demonstram conceitos neste artigo:
* [como usar-azureml/formação/comboio dentro do caderno](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [como-usar-azureml/formação/comboio-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [como-usar-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

Experimente estes passos seguintes para aprender a utilizar o SDK do Azure Machine Learning para Python:

* Veja um exemplo de como registar o melhor modelo e implantá-lo no tutorial, [Treine um modelo](tutorial-train-models-with-aml.md)de classificação de imagem com Azure Machine Learning .

* Saiba treinar [modelos pyTorch com aprendizagem automática azure](how-to-train-pytorch.md).
