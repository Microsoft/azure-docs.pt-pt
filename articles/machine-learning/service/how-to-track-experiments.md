---
title: Log ML experimentos & métricas
titleSuffix: Azure Machine Learning
description: Monitore seus experimentos do Azure ML e monitore as métricas de execução para aprimorar o processo de criação de modelo. Adicione o registro em log ao script de treinamento e exiba os resultados registrados de uma execução.  Use execute. log, Run. start_logging ou ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: a60691222c6f5f31a5b5c97df029790c1fd690ed
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873891"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Monitorar execuções e métricas de experimento do Azure ML
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprimore o processo de criação de modelo rastreando suas experiências e monitorando métricas de execução. Neste artigo, saiba como adicionar o código de registro em log ao script de treinamento, enviar uma execução de experimento, monitorar a execução e inspecionar os resultados em Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning também pode registrar informações de outras fontes durante o treinamento, como execuções automatizadas do Machine Learning ou o contêiner do Docker que executa o trabalho de treinamento. Esses logs não estão documentados. Se você encontrar problemas e entrar em contato com o suporte da Microsoft, eles podem ser capazes de usar esses logs durante a solução de problemas.

> [!TIP]
> As informações neste documento são basicamente para cientistas de dados e desenvolvedores que desejam monitorar o processo de treinamento de modelo. Se você for um administrador interessado em monitorar o uso de recursos e eventos do Azure Machine Learning, como cotas, execuções de treinamento concluídas ou implantações de modelo concluídas, consulte [monitoramento Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Métricas disponíveis para acompanhar

As métricas seguintes podem ser adicionadas a uma execução enquanto uma experimentação de preparação. Para ver uma lista mais detalhada das quais podem ser acompanhadas numa execução, consulte a [execute a documentação de referência de classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Tipo| Função de Python | Notas|
|----|:----|:----|
|Valores escalares |Função:<br>`run.log(name, value, description='')`<br><br>Exemplo:<br>Run.log ("precisão", 0.95) |Iniciar um numéricos ou de cadeia de valor para a execução com o nome fornecido. Registo de uma métrica para uma execução faz com que essa métrica sejam armazenadas no registo de execução na experimentação.  Pode se conectar a mesma métrica várias vezes dentro de uma execução, o resultado a ser considerado um vetor dessa métrica.|
|Apresenta uma lista|Função:<br>`run.log_list(name, value, description='')`<br><br>Exemplo:<br>Run.log_list ("precisões", [0,6, 0,7, 0,87]) | Inicie a sessão de uma lista de valores para a execução com o nome fornecido.|
|linha|Função:<br>`run.log_row(name, description=None, **kwargs)`<br>Exemplo:<br>Run.log_row ("Y ao longo de X", x = 1, y = 0.4) | Usando *log_row* cria uma métrica com várias colunas, conforme descrito em kwargs. Cada parâmetro nomeado gera uma coluna com o valor especificado.  *log_row* pode ser chamado uma vez para iniciar sessão uma tupla arbitrária, ou várias vezes num loop para gerar uma tabela completa.|
|Tabelas|Função:<br>`run.log_table(name, value, description='')`<br><br>Exemplo:<br>Run.log_table ("Y ao longo de X", {"x": [1, 2, 3], "y": [0,6, 0,7, 0.89]}) | Inicie a sessão de um objeto de dicionário para a execução com o nome fornecido. |
|Imagens|Função:<br>`run.log_image(name, path=None, plot=None)`<br><br>Exemplo:<br>`run.log_image("ROC", plt)` | Inicie a sessão de uma imagem para o registo de execução. Utilizar log_image para registar um ficheiro de imagem ou um matplotlib gráfico para a execução.  Estas imagens serão visíveis e comparável no registo de execução.|
|Marcar uma execução|Função:<br>`run.tag(key, value=None)`<br><br>Exemplo:<br>Run.tag ("selecionado", "Sim") | Marca a execução com uma chave de cadeia de caracteres e o valor de cadeia de caracteres opcional.|
|Carregar o ficheiro ou diretório|Função:<br>`run.upload_file(name, path_or_stream)`<br> <br> Exemplo:<br>Run.upload_file ("best_model.pkl", ". / pkl") | Carregar um ficheiro para o registo de execução. Execuções de recolher automaticamente o ficheiro no diretório de saída especificado, o que está predefinida para ". / saídas" para a mais tipos de execução.  Utilize upload_file apenas quando os ficheiros adicionais devem ser carregados ou não for especificado um diretório de saída. Sugerimos que adicionar `outputs` para o nome, de modo que ele é carregado para o diretório de saídas. Pode listar todos os ficheiros que estão associados com esta opção executar o registo por chamada `run.get_file_names()`|

> [!NOTE]
> As métricas de escalares, listas, linhas e tabelas podem ter o tipo: número de vírgula flutuante, número inteiro ou uma cadeia.

## <a name="choose-a-logging-option"></a>Escolha uma opção de log

Se pretender controlar ou monitorizar a sua experimentação, tem de adicionar o código para iniciar o log ao submeter a execução. Seguem-se formas para acionar o envio de execução:
* __Run.start_logging__ - adicionar funções de registo para o script de treinamento e iniciar uma sessão de registo interativo na experimentação do especificado. **start_logging** cria uma execução interativa para utilização em cenários como blocos de notas. Qualquer métricas que são registadas durante a sessão são adicionadas ao registo de execução na experimentação.
* __ScriptRunConfig__ - adicionar funções de registo para o script de formação e a pasta de script inteiro com a execução de carga.  **ScriptRunConfig** é uma classe para a configuração de configurações para o script é executado. Com esta opção, pode adicionar o código de monitorização para ser notificado da conclusão de ou para obter um widget visual para monitorizar.

## <a name="set-up-the-workspace"></a>Configurar a área de trabalho
Antes de adicionar o registo e submeter uma experimentação, tem de configurar a área de trabalho.

1. Carregar a área de trabalho. Para saber mais sobre como definir a configuração do espaço de trabalho, consulte [arquivo de configuração do espaço de trabalho](how-to-configure-environment.md#workspace).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Opção 1: Utilizar start_logging

**start_logging** cria uma execução interativa para utilização em cenários como blocos de notas. Qualquer métricas que são registadas durante a sessão são adicionadas ao registo de execução na experimentação.

O exemplo seguinte prepara um modelo simples de sklearn Ridge localmente num bloco de notas Jupyter local. Para saber mais sobre como enviar experimentos para ambientes diferentes, confira [Configurar destinos de computação para treinamento de modelo com o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Crie um script de treinamento num bloco de notas Jupyter local. 

   ```python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Adicione o acompanhamento de experimento usando o SDK do Azure Machine Learning e carregue um modelo persistente no registro de execução do experimento. O código a seguir adiciona etiquetas, os registos e carrega um ficheiro de modelo para a experimentação executar.

   ```python
    # Get an experiment object from Azure Machine Learning
    experiment = Experiment(workspace=ws, name="train-within-notebook")
    
    # Create a run object in the experiment
    run =  experiment.start_logging()
    # Log the algorithm parameter alpha to the run
    run.log('alpha', 0.03)
    
    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])
    
    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    run.log('mse', mean_squared_error(data['test']['y'], preds))
    
    # Save the model to the outputs directory for capture
    model_file_name = 'outputs/model.pkl'
    
    joblib.dump(value = regression_model, filename = model_file_name)
    
    # upload the model file explicitly into artifacts 
    run.upload_file(name = model_file_name, path_or_stream = model_file_name)
    
    # Complete the run
    run.complete()
   ```

    O script termina com ```run.complete()```, que marca a execução como concluída.  Esta função é normalmente utilizada em cenários de bloco de notas interativo.

## <a name="option-2-use-scriptrunconfig"></a>Opção 2: Utilizar ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) é uma classe para configurar as configurações para execuções de script. Com esta opção, pode adicionar o código de monitorização para ser notificado da conclusão de ou para obter um widget visual para monitorizar.

Este exemplo Expande o modelo básico de sklearn Ridge acima. Ele faz um parâmetro simple paramétrico para paramétrico sobre os valores alfabéticos do modelo para capturar métricas com modelos de formação em é executado sob a experimentação. O exemplo é executado localmente em relação a um ambiente gerenciado por utilizador. 

1. Crie um script de treinamento `train.py`.

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. O script de `train.py` faz referência a `mylib.py` que permite que você obtenha a lista de valores Alfa a serem usados no modelo de saliência.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Configure um ambiente local do utilizador gerido.

   ```python
   from azureml.core import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. Submeter o ```train.py``` script seja executado no ambiente do utilizador gerido. Esta pasta de script inteiro é submetida para treinamento, incluindo o ```mylib.py``` ficheiro.

   ```python
   from azureml.core import ScriptRunConfig
    
   exp = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   run = exp.submit(src)
   ```

## <a name="manage-a-run"></a>Gerenciar uma execução

O artigo [Iniciar, monitorar e cancelar execuções de treinamento](how-to-manage-runs.md) destaca Azure Machine Learning fluxos de trabalho específicos sobre como gerenciar seus experimentos.

## <a name="view-run-details"></a>Vista de detalhes da execução

### <a name="view-activequeued-runs-from-the-browser"></a>Exibir execuções ativas/em fila no navegador

Os destinos de computação usados para treinar modelos são um recurso compartilhado. Assim, eles podem ter várias execuções em fila ou ativas em um determinado momento. Para ver as execuções de um destino de computação específico do seu navegador, use as seguintes etapas:

1. No [Azure Machine Learning Studio](https://ml.azure.com/), selecione seu espaço de trabalho e, em seguida, selecione __computação__ no lado esquerdo da página.

1. Selecione __clusters de treinamento__ para exibir uma lista de destinos de computação usados para treinamento. Em seguida, selecione o cluster.

    ![Selecione o cluster de treinamento](./media/how-to-track-experiments/select-training-compute.png)

1. Selecione __execuções__. A lista de execuções que usam esse cluster é exibida. Para exibir detalhes de uma execução específica, use o link na coluna __executar__ . Para exibir detalhes do experimento, use o link na coluna __experimento__ .

    ![Selecionar execuções para o cluster de treinamento](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Uma execução pode conter execuções filhas, portanto um trabalho de treinamento pode resultar em várias entradas.

Quando uma execução for concluída, ela não será mais exibida nesta página. Para exibir informações sobre execuções concluídas, visite a seção __experimentos__ do estúdio e selecione o experimento e execute. Para obter mais informações, consulte a seção [métricas de execução de consulta](#queryrunmetrics) .

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Monitor executar com o widget do Jupyter Notebook
Ao usar o método **ScriptRunConfig** para enviar execuções, você pode observar o progresso da execução com um [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Tal como a submissão da execução, o widget é assíncrono e disponibiliza atualizações dinâmicas a cada 10 a 15 segundos até à conclusão do trabalho.

1. Ver o widget de Jupyter enquanto aguarda a execução concluir.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Widget de bloco de notas de captura de ecrã do Jupyter](./media/how-to-track-experiments/run-details-widget.png)

Você também pode obter um link para a mesma exibição em seu espaço de trabalho.

```python
print(run.get_portal_url())
```

2. **[Máquina automatizada aprendizagem execuções]**  Para acessar os gráficos de uma execução anterior. Substitua `<<experiment_name>>` pelo nome de experimento apropriado:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widget de bloco de notas do Jupyter para Machine Learning automatizada](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Para exibir mais detalhes de um pipeline, clique no pipeline que você gostaria de explorar na tabela e os gráficos serão renderizados em um pop-up do Azure Machine Learning Studio.

### <a name="get-log-results-upon-completion"></a>Obter resultados do registo após a conclusão

Modelo de formação e monitorização ocorre em segundo plano para que possam executar outras tarefas enquanto espera. Também pode aguardar até que o modelo de conclusão da formação antes de executar mais de código. Quando utiliza **ScriptRunConfig**, pode utilizar ```run.wait_for_completion(show_output = True)``` para mostrar quando a preparação de modelos está concluída. O ```show_output``` sinalizador dá-lhe uma saída verbosa ideal. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Métricas de execução da consulta

Pode ver as métricas de usar um modelo preparado ```run.get_metrics()```. Agora pode obter todas as métricas que foram registadas no exemplo acima para determinar o melhor modelo.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studiohttpsmlazurecom"></a>Exibir o experimento em seu espaço de trabalho no [Azure Machine Learning Studio](https://ml.azure.com)

Quando uma experimentação estiver concluída em execução, pode navegar para a experimentação gravada executar o registo. Você pode acessar o histórico do [Azure Machine Learning Studio](https://ml.azure.com).

Navegue até a guia experimentos e selecione seu experimento. Você é levado para o painel Executar teste, no qual você pode ver as métricas e os gráficos acompanhados que são registrados para cada execução. Neste caso, fizemos logon MSE e os valores alfa.

  ![Detalhes da execução no Azure Machine Learning Studio](./media/how-to-track-experiments/experiment-dashboard.png)

Você pode fazer uma busca detalhada em uma execução específica para exibir suas saídas ou logs ou baixar o instantâneo do teste enviado para que você possa compartilhar a pasta experimento com outras pessoas.

### <a name="viewing-charts-in-run-details"></a>Ver gráficos em detalhes da execução

Há várias maneiras de usar as APIs de log para registrar diferentes tipos de métricas durante uma execução e exibi-las como gráficos no Azure Machine Learning Studio.

|Valor com sessão iniciada|Código de exemplo| Ver no portal|
|----|----|----|
|Registo de uma matriz de valores numéricos| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|gráfico de linhas de variável de único|
|Um único valor numérico de registo com o mesmo nome de métrica repetidamente utilizado (semelhantes a partir de um for loop)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| gráfico de linhas de variável de único|
|Inicie uma linha com 2 colunas numéricas repetidamente|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Gráfico de linhas de duas variáveis|
|Tabela de logs de 2 colunas numéricas|`run.log_table(name='Sine Wave', value=sines)`|Gráfico de linhas de duas variáveis|


## <a name="example-notebooks"></a>Blocos de notas de exemplo
Os seguintes blocos de notas demonstram os conceitos deste artigo:
* [How-to-use-azureml/Training/Train-within-Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [How-to-use-azureml/Training/Train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [How-to-use-azureml/Training/Logging-API/Logging-API.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

Experimente os passos seguintes para aprender a utilizar o Azure Machine Learning SDK para Python:

* Veja um exemplo de como registar o melhor modelo e implementá-la no tutorial [preparar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).

* Saiba como [formar modelos PyTorch com o Azure Machine Learning](how-to-train-pytorch.md).
