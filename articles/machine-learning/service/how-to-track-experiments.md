---
title: Métricas de log durante execuções de treinamento
titleSuffix: Azure Machine Learning service
description: Você pode acompanhar seus experimentos e monitorar as métricas para aprimorar o processo de criação de modelo. Saiba como adicionar o log ao script de treinamento, como enviar o experimento, como verificar o progresso de um trabalho em execução e como exibir os resultados registrados de uma execução.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/11/2019
ms.custom: seodec18
ms.openlocfilehash: 0f295bf3a76d89e811fe9a022a3ccb68fbe7556a
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858713"
---
# <a name="track-machine-learning-training-metrics-with-azure-machine-learning"></a>Acompanhe as métricas de treinamento do Machine Learning com Azure Machine Learning

Aprimore o processo de criação de modelo acompanhando suas métricas de monitoramento e experimentos. Neste artigo, saiba como adicionar o código de registro em log ao script de treinamento, enviar uma execução de experimento, monitorar a execução e inspecionar os resultados no serviço Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning serviço também pode registrar informações de outras fontes durante o treinamento, como execuções automatizadas do Machine Learning ou o contêiner do Docker que executa o trabalho de treinamento. Esses logs não estão documentados. Se você encontrar problemas e entrar em contato com o suporte da Microsoft, eles podem ser capazes de usar esses logs durante a solução de problemas.

## <a name="available-metrics-to-track"></a>Métricas disponíveis para acompanhar

As métricas seguintes podem ser adicionadas a uma execução enquanto uma experimentação de preparação. Para ver uma lista mais detalhada das quais podem ser acompanhadas numa execução, consulte a [execute a documentação de referência de classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Tipo| Função de Python | Notas|
|----|:----|:----|
|Valores escalares |Função:<br>`run.log(name, value, description='')`<br><br>Exemplo:<br>Run.log ("precisão", 0.95) |Iniciar um numéricos ou de cadeia de valor para a execução com o nome fornecido. Registo de uma métrica para uma execução faz com que essa métrica sejam armazenadas no registo de execução na experimentação.  Pode se conectar a mesma métrica várias vezes dentro de uma execução, o resultado a ser considerado um vetor dessa métrica.|
|Apresenta uma lista|Função:<br>`run.log_list(name, value, description='')`<br><br>Exemplo:<br>Run.log_list ("precisões", [0,6, 0,7, 0,87]) | Inicie a sessão de uma lista de valores para a execução com o nome fornecido.|
|Linha|Função:<br>`run.log_row(name, description=None, **kwargs)`<br>Exemplo:<br>Run.log_row ("Y ao longo de X", x = 1, y = 0.4) | Usando *log_row* cria uma métrica com várias colunas, conforme descrito em kwargs. Cada parâmetro nomeado gera uma coluna com o valor especificado.  *log_row* pode ser chamado uma vez para iniciar sessão uma tupla arbitrária, ou várias vezes num loop para gerar uma tabela completa.|
|Tabela|Função:<br>`run.log_table(name, value, description='')`<br><br>Exemplo:<br>Run.log_table ("Y ao longo de X", {"x": [1, 2, 3], "y": [0,6, 0,7, 0.89]}) | Inicie a sessão de um objeto de dicionário para a execução com o nome fornecido. |
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
  
## <a name="option-1-use-start_logging"></a>Opção 1: Usar start_logging

**start_logging** cria uma execução interativa para utilização em cenários como blocos de notas. Qualquer métricas que são registadas durante a sessão são adicionadas ao registo de execução na experimentação.

O exemplo seguinte prepara um modelo simples de sklearn Ridge localmente num bloco de notas Jupyter local. Para saber mais sobre como enviar experimentações em ambientes diferentes, veja [configurar destinos de computação de preparação de modelos com o serviço Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

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

2. Adicionar controlo de experimentação com o SDK do serviço do Azure Machine Learning e carregar um modelo persistente para a experimentação executar o registo. O código a seguir adiciona etiquetas, os registos e carrega um ficheiro de modelo para a experimentação executar.

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

## <a name="option-2-use-scriptrunconfig"></a>Opção 2: Usar ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) é uma classe para configurar as configurações para execuções de script. Com esta opção, pode adicionar o código de monitorização para ser notificado da conclusão de ou para obter um widget visual para monitorizar.

Este exemplo Expande o modelo básico de sklearn Ridge acima. Ele faz um parâmetro simple paramétrico para paramétrico sobre os valores alfabéticos do modelo para capturar métricas com modelos de formação em é executado sob a experimentação. O exemplo é executado localmente em relação a um ambiente gerenciado por utilizador. 

1. Crie um script `train.py`de treinamento.

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

2. O `train.py` script faz `mylib.py` referência, que permite obter a lista de valores Alfa a serem usados no modelo de saliência.

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

2. **[Máquina automatizada aprendizagem execuções]**  Para acessar os gráficos de uma execução anterior. Substituir `<<experiment_name>>` pelo nome de experimento apropriado:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widget de bloco de notas do Jupyter para Machine Learning automatizada](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Para ver mais detalhes de um clique de pipeline ao pipeline que gostaria de explorar na tabela e os gráficos serão renderizados num pop-up do portal do Azure.

### <a name="get-log-results-upon-completion"></a>Obter resultados do registo após a conclusão

Modelo de formação e monitorização ocorre em segundo plano para que possam executar outras tarefas enquanto espera. Também pode aguardar até que o modelo de conclusão da formação antes de executar mais de código. Quando utiliza **ScriptRunConfig**, pode utilizar ```run.wait_for_completion(show_output = True)``` para mostrar quando a preparação de modelos está concluída. O ```show_output``` sinalizador dá-lhe uma saída verbosa ideal. 


### <a name="query-run-metrics"></a>Métricas de execução da consulta

Pode ver as métricas de usar um modelo preparado ```run.get_metrics()```. Agora pode obter todas as métricas que foram registadas no exemplo acima para determinar o melhor modelo.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal-or-your-workspace-landing-page-previewhttpsmlazurecom"></a>Exibir o experimento no portal do Azure ou na [página de aterrissagem do espaço de trabalho (visualização)](https://ml.azure.com)

Quando uma experimentação estiver concluída em execução, pode navegar para a experimentação gravada executar o registo. Você pode acessar o histórico de duas maneiras:

* Obter o URL para a execução diretamente ```print(run.get_portal_url())```
* Ver os detalhes da execução, enviando o nome da execução (neste caso, ```run```). Desta forma aponta o nome de experimentação, ID, tipo, estado, página de detalhes, uma ligação para o portal do Azure e uma ligação para a documentação.

A ligação para a execução traz até diretamente para a página de detalhes da execução no portal do Azure. Aqui pode ver quaisquer propriedades, métricas registadas, imagens e gráficos que são registados na experimentação. Neste caso, fizemos logon MSE e os valores alfa.

  ![Detalhes da execução no portal do Azure](./media/how-to-track-experiments/run-details-page.png)

Também pode ver qualquer saídas ou registos para a execução, ou transferir o instantâneo da experimentação submetida para que possa partilhar a pasta de experimentação com outras pessoas.

### <a name="viewing-charts-in-run-details"></a>Ver gráficos em detalhes da execução

Existem várias formas de utilizar as APIs para tipos diferentes de registo de métricas de log durante uma execução e visualizá-los como gráficos no portal do Azure. 

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

## <a name="next-steps"></a>Passos Seguintes

Experimente os passos seguintes para aprender a utilizar o Azure Machine Learning SDK para Python:

* Veja um exemplo de como registar o melhor modelo e implementá-la no tutorial [preparar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).

* Saiba como [formar modelos PyTorch com o Azure Machine Learning](how-to-train-pytorch.md).
