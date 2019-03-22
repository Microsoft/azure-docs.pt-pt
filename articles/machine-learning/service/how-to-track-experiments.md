---
title: Métricas de registo durante as execuções de preparações
titleSuffix: Azure Machine Learning service
description: Pode controlar suas experimentações e monitorizar as métricas para melhorar o processo de criação de modelo. Saiba como adicionar registos ao seu script de treinamento, como submeter a experimentação, como verificar o progresso de uma tarefa em execução e como visualizar os resultados de uma execução.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 835d1f41ffe940422554a8ca59d0a91ac8e98607
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336600"
---
# <a name="log-metrics-during-training-runs-in-azure-machine-learning"></a>Métricas de registo durante o treinamento é executado no Azure Machine Learning

No serviço do Azure Machine Learning, pode controlar suas experimentações e monitorizar as métricas para melhorar o processo de criação de modelo. Neste artigo, saiba como adicionar registos ao seu script de treinamento, submeter uma execução de experimentação, monitorizar a execução e ver os resultados de uma execução.

## <a name="list-of-training-metrics"></a>Lista de métricas de treinamento 

As métricas seguintes podem ser adicionadas a uma execução enquanto uma experimentação de preparação. Para ver uma lista mais detalhada das quais podem ser acompanhadas numa execução, consulte a [execute a documentação de referência de classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Tipo| Função de Python | Notas|
|----|:----|:----|
|Valores escalares |Função:<br>`run.log(name, value, description='')`<br><br>Exemplo:<br>Run.log ("precisão", 0.95) |Iniciar um numéricos ou de cadeia de valor para a execução com o nome fornecido. Registo de uma métrica para uma execução faz com que essa métrica sejam armazenadas no registo de execução na experimentação.  Pode se conectar a mesma métrica várias vezes dentro de uma execução, o resultado a ser considerado um vetor dessa métrica.|
|Apresenta uma lista|Função:<br>`run.log_list(name, value, description='')`<br><br>Exemplo:<br>Run.log_list ("precisões", [0,6, 0,7, 0,87]) | Inicie a sessão de uma lista de valores para a execução com o nome fornecido.|
|Linha|Função:<br>`run.log_row(name, description=None, **kwargs)`<br>Exemplo:<br>Run.log_row ("Y ao longo de X", x = 1, y = 0.4) | Usando *log_row* cria uma métrica com várias colunas, conforme descrito em kwargs. Cada parâmetro nomeado gera uma coluna com o valor especificado.  *log_row* pode ser chamado uma vez para iniciar sessão uma tupla arbitrária, ou várias vezes num loop para gerar uma tabela completa.|
|Tabela|Função:<br>`run.log_table(name, value, description='')`<br><br>Exemplo:<br>Run.log_table ("Y ao longo de X", {"x": [1, 2, 3], "y": [0,6, 0,7, 0.89]}) | Inicie a sessão de um objeto de dicionário para a execução com o nome fornecido. |
|Imagens|Função:<br>`run.log_image(name, path=None, plot=None)`<br><br>Exemplo:<br>Run.log_image ("", plt) MULTICLASSE | Inicie a sessão de uma imagem para o registo de execução. Utilizar log_image para registar um ficheiro de imagem ou um matplotlib gráfico para a execução.  Estas imagens serão visíveis e comparável no registo de execução.|
|Marcar uma execução|Função:<br>`run.tag(key, value=None)`<br><br>Exemplo:<br>Run.tag ("selecionado", "Sim") | Marca a execução com uma chave de cadeia de caracteres e o valor de cadeia de caracteres opcional.|
|Carregar o ficheiro ou diretório|Função:<br>`run.upload_file(name, path_or_stream)`<br> <br> Exemplo:<br>Run.upload_file ("best_model.pkl", ". / pkl") | Carregar um ficheiro para o registo de execução. Execuções de recolher automaticamente o ficheiro no diretório de saída especificado, o que está predefinida para ". / saídas" para a mais tipos de execução.  Utilize upload_file apenas quando os ficheiros adicionais devem ser carregados ou não for especificado um diretório de saída. Sugerimos que adicionar `outputs` para o nome, de modo que ele é carregado para o diretório de saídas. Pode listar todos os ficheiros que estão associados com esta opção executar o registo por chamada `run.get_file_names()`|

> [!NOTE]
> As métricas de escalares, listas, linhas e tabelas podem ter o tipo: número de vírgula flutuante, número inteiro ou uma cadeia.

## <a name="start-logging-metrics"></a>Iniciar as métricas de registo

Se pretender controlar ou monitorizar a sua experimentação, tem de adicionar o código para iniciar o log ao submeter a execução. Seguem-se formas para acionar o envio de execução:
* __Run.start_logging__ - adicionar funções de registo para o script de treinamento e iniciar uma sessão de registo interativo na experimentação do especificado. **start_logging** cria uma execução interativa para utilização em cenários como blocos de notas. Qualquer métricas que são registadas durante a sessão são adicionadas ao registo de execução na experimentação.
* __ScriptRunConfig__ - adicionar funções de registo para o script de formação e a pasta de script inteiro com a execução de carga.  **ScriptRunConfig** é uma classe para a configuração de configurações para o script é executado. Com esta opção, pode adicionar o código de monitorização para ser notificado da conclusão de ou para obter um widget visual para monitorizar.

## <a name="set-up-the-workspace"></a>Configurar a área de trabalho
Antes de adicionar o registo e submeter uma experimentação, tem de configurar a área de trabalho.

1. Carregar a área de trabalho. Para saber mais sobre como definir a configuração de área de trabalho, siga os [guia de introdução](https://docs.microsoft.com/azure/machine-learning/service/quickstart-get-started).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```
  
## <a name="option-1-use-startlogging"></a>Opção 1: Utilizar start_logging

**start_logging** cria uma execução interativa para utilização em cenários como blocos de notas. Qualquer métricas que são registadas durante a sessão são adicionadas ao registo de execução na experimentação.

O exemplo seguinte prepara um modelo simples de sklearn Ridge localmente num bloco de notas Jupyter local. Para saber mais sobre como enviar experimentações em ambientes diferentes, veja [configurar destinos de computação de preparação de modelos com o serviço Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Crie um script de treinamento num bloco de notas Jupyter local. 

   ``` python
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
   experiment = Experiment(workspace = ws, name = "train-within-notebook")
  
   # Create a run object in the experiment
   run = experiment.start_logging()# Log the algorithm parameter alpha to the run
   run.log('alpha', 0.03)

   # Create, fit, and test the scikit-learn Ridge regression model
   regression_model = Ridge(alpha=0.03)
   regression_model.fit(data['train']['X'], data['train']['y'])
   preds = regression_model.predict(data['test']['X'])

   # Output the Mean Squared Error to the notebook and to the run
   print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
   run.log('mse', mean_squared_error(data['test']['y'], preds))

   # Save the model to the outputs directory for capture
   joblib.dump(value=regression_model, filename='outputs/model.pkl')

   # Take a snapshot of the directory containing this notebook
   run.take_snapshot('./')

   # Complete the run
   run.complete()
  
   ```

O script termina com ```run.complete()```, que marca a execução como concluída.  Esta função é normalmente utilizada em cenários de bloco de notas interativo.

## <a name="option-2-use-scriptrunconfig"></a>Opção 2: Use ScriptRunConfig

[**ScriptRunConfig** ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) é uma classe para a configuração de configurações para o script é executado. Com esta opção, pode adicionar o código de monitorização para ser notificado da conclusão de ou para obter um widget visual para monitorizar.

Este exemplo Expande o modelo básico de sklearn Ridge acima. Ele faz um parâmetro simple paramétrico para paramétrico sobre os valores alfabéticos do modelo para capturar métricas com modelos de formação em é executado sob a experimentação. O exemplo é executado localmente em relação a um ambiente gerenciado por utilizador. 

1. Criar um script de treinamento `train.py`.

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

2. O `train.py` referências de script `mylib.py` que permite-lhe obter a lista de valores alfa para utilizar no modelo ridge.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Configure um ambiente local do utilizador gerido.

   ```python
   from azureml.core.runconfig import RunConfiguration

   # Editing a run configuration property on-fly.
   run_config_user_managed = RunConfiguration()

   run_config_user_managed.environment.python.user_managed_dependencies = True

   # You can choose a specific Python environment by pointing to a Python path 
   #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
   ```

4. Submeter o ```train.py``` script seja executado no ambiente do utilizador gerido. Esta pasta de script inteiro é submetida para treinamento, incluindo o ```mylib.py``` ficheiro.

   ```python
   from azureml.core import ScriptRunConfig
  
   experiment = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory = './', script = 'train.py', run_config = run_config_user_managed)
   run = experiment.submit(src)
   ```

## <a name="cancel-a-run"></a>Cancelar uma execução
Depois de uma execução é submetida, pode cancelá-lo, mesmo se perder a referência de objeto, desde que souber o nome de experimentação e id de execução. 

```python
from azureml.core import Experiment
exp = Experiment(ws, "my-experiment-name")

# if you don't know the run id, you can list all runs under an experiment
for r in exp.get_runs():  
    print(r.id, r.get_status())

# if you know the run id, you can "rehydrate" the run
from azureml.core import get_run
r = get_run(experiment=exp, run_id="my_run_id", rehydrate=True)
  
# check the returned run type and status
print(type(r), r.get_status())

# you can cancel a run if it hasn't completed or failed
if r.get_status() not in ['Complete', 'Failed']:
    r.cancel()
```
Tenha em atenção de que tipos de ScriptRun e PipelineRun atualmente, apenas suportam a operação de cancelamento.

Além disso, pode cancelar uma execução através da CLI com o seguinte comando:
```shell
az ml run cancel -r <run_id> -p <project_path>
```


## <a name="view-run-details"></a>Vista de detalhes da execução

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Monitor de executar com widgets de bloco de notas do Jupyter
Quando utiliza a **ScriptRunConfig** executa o método para submeter, pode ver o progresso da execução com um widget de bloco de notas do Jupyter. Tal como a submissão da execução, o widget é assíncrono e disponibiliza atualizações dinâmicas a cada 10 a 15 segundos até à conclusão do trabalho.

1. Ver o widget de Jupyter enquanto aguarda a execução concluir.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Widget de bloco de notas de captura de ecrã do Jupyter](./media/how-to-track-experiments/widgets.PNG)

2. **[Máquina automatizada aprendizagem execuções]**  Para acessar os gráficos de uma execução anterior. Substitua `<<experiment_name>>` com o nome de experimentação apropriado:

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
## <a name="view-the-experiment-in-the-azure-portal"></a>Ver a experimentação no portal do Azure

Quando uma experimentação estiver concluída em execução, pode navegar para a experimentação gravada executar o registo. Pode fazer aceder ao histórico de duas formas:

* Obter o URL para a execução diretamente ```print(run.get_portal_url())```
* Ver os detalhes da execução, enviando o nome da execução (neste caso, ```run```). Desta forma aponta o nome de experimentação, ID, tipo, estado, página de detalhes, uma ligação para o portal do Azure e uma ligação para a documentação.

A ligação para a execução traz até diretamente para a página de detalhes da execução no portal do Azure. Aqui pode ver quaisquer propriedades, métricas registadas, imagens e gráficos que são registados na experimentação. Neste caso, fizemos logon MSE e os valores alfa.

  ![Detalhes da execução no portal do Azure](./media/how-to-track-experiments/run-details-page-web.PNG)

Também pode ver qualquer saídas ou registos para a execução, ou transferir o instantâneo da experimentação submetida para que possa partilhar a pasta de experimentação com outras pessoas.

### <a name="viewing-charts-in-run-details"></a>Ver gráficos em detalhes da execução

Existem várias formas de utilizar as APIs para tipos diferentes de registo de métricas de log durante uma execução e visualizá-los como gráficos no portal do Azure. 

|Valor com sessão iniciada|Código de exemplo| Ver no portal|
|----|----|----|
|Registo de uma matriz de valores numéricos| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|gráfico de linhas de variável de único|
|Um único valor numérico de registo com o mesmo nome de métrica repetidamente utilizado (semelhantes a partir de um for loop)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| gráfico de linhas de variável de único|
|Inicie uma linha com 2 colunas numéricas repetidamente|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Gráfico de linhas de duas variáveis|
|Tabela de logs de 2 colunas numéricas|`run.log_table(name='Sine Wave', value=sines)`|Gráfico de linhas de duas variáveis|

<a name="auto"></a>
## <a name="understanding-automated-ml-charts"></a>Noções básicas sobre automatizada gráficos de ML

Depois de submeter um trabalho de ML automatizado num bloco de notas, um histórico dessas execuções pode ser encontrado na sua área de trabalho do serviço de aprendizagem. 

Saiba mais sobre:
+ [Os gráficos e as curvas para modelos de classificação](#classification)
+ [Tabelas e gráficos para modelos de regressão](#regression)
+ [Capacidade de explicar de modelo](#model-explain-ability-and-feature-importance)


### <a name="view-the-run-charts"></a>Ver os gráficos de execução

1. Vá para a área de trabalho. 

1. Selecione **experimentações** no painel mais à esquerda da sua área de trabalho.

   ![Captura de ecrã do menu de experimentação](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_menu.PNG)

1. Selecione a experimentação que está interessado.

   ![Lista de experimentação](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_list.PNG)

1. Na tabela, selecione o número de executar.

   ![Executar de experimentação](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_run.PNG)

1. Na tabela, selecione o número de iteração para o modelo que pretende explorar ainda mais.

   ![Modelo de experimentação](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_model.PNG)



### <a name="classification"></a>Classificação

Para cada modelo de classificação que cria através da utilização de recursos do Azure Machine Learning de aprendizagem automatizada, pode ver os gráficos seguintes: 
+ [Matriz de confusão](#confusion-matrix)
+ [Gráfico de Recolhimento de precisão](#precision-recall-chart)
+ [Recetor operacional características (ou ROC)](#ROC)
+ [Curva de comparação de precisão](#lift-curve)
+ [Curva de ganhos](#gains-curve)
+ [Desenho de calibragem](#calibration-plot)

#### <a name="confusion-matrix"></a>Matriz de confusão

Uma matriz de confusão é usada para descrever o desempenho de um modelo de classificação. Cada linha apresenta as instâncias da classe verdadeira, e cada coluna representa as instâncias da classe prevista. A matriz de confusão mostra as etiquetas classificadas corretamente e as etiquetas classificadas incorretamente de um modelo específico.

Para problemas de classificação, o Azure Machine Learning fornece automaticamente uma matriz de confusão para cada modelo que foi criado. Para cada matriz de confusão, ML automatizada mostrará as etiquetas classificadas corretamente como rótulos de verdes e classificados incorretamente a vermelho. O tamanho do círculo representa o número de amostras no contentor. Além disso, a contagem de frequência de cada etiqueta prevista e cada etiqueta verdadeira é fornecida em gráficos de barras adjacentes. 

Exemplo 1: Um modelo de classificação com precisão fraco ![um modelo de classificação com precisão fraca](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix1.PNG)

Exemplo 2: Um modelo de classificação com precisão elevada (ideal) ![um modelo de classificação com precisão elevada](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix2.PNG)


#### <a name="precision-recall-chart"></a>Gráfico de recolhimento de precisão

Com este gráfico, pode comparar as curvas de recolhimento de precisão para cada modelo determinar qual modelo tem uma relação aceitável entre precisão e lembre-se para o seu problema comercial em particular. Este gráfico mostra o Recolhimento de precisão de média Macro, o Recolhimento de precisão de média Micro e o recolhimento de precisão associado com todas as classes para um modelo.

O termo que precisão representa essa habilidade para um classificador que identifique todas as instâncias corretamente. Lembre-se representa a capacidade para um classificador localizar todas as instâncias de uma etiqueta específica. A curva de recolhimento de precisão mostra a relação entre esses dois conceitos. O ideal é que o modelo teria a precisão de 100% e a precisão de 100%.

Exemplo 1: Um modelo de classificação com precisão baixa e lembre-se de baixa ![um modelo de classificação com precisão baixa e lembre-se de baixa](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall1.PNG)

Exemplo 2: Um modelo de classificação com aproximadamente 100% precisão e ~ 100% recolhimento (ideal) ![uma precisão de alta de modelo de classificação e lembre-se](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall2.PNG)

#### <a name="roc"></a>COR MULTICLASSE

Recetor operacional característica (ou ROC) é um desenho das etiquetas classificadas corretamente vs. as etiquetas classificadas incorretamente para um determinado modelo. A curva cor MULTICLASSE pode ser menos informativa quando os modelos de formação em conjuntos de dados com tendência alto, como ele não mostrará as etiquetas de positivas FALSO.

Exemplo 1: Um modelo de classificação com etiquetas verdadeiras baixas e alta etiquetas falso ![modelo de classificação com etiquetas verdadeiras baixas e alta etiquetas FALSO](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc1.PNG)

Exemplo 2: Um modelo de classificação com etiquetas verdadeiras alta e baixas etiquetas falso ![um modelo de classificação com etiquetas verdadeiras alta e baixas etiquetas FALSO](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc2.PNG)

#### <a name="lift-curve"></a>Curva de comparação de precisão

Pode comparar a comparação de precisão do modelo criado automaticamente com o Azure Machine Learning para a linha de base para visualizar o ganho de valor desse modelo específico.

Gráficos de comparação de precisão são utilizados para avaliar o desempenho de um modelo de classificação. Mostra quanto melhor pode esperar fazer com um modelo em comparação comparado sem um modelo. 

Exemplo 1: Modelo executa pior do que um modelo de seleção aleatória ![um modelo de classificação que modelam os pior do que uma seleção aleatória](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve1.PNG)

Exemplo 2: Modelo tem um desempenho melhor do que um modelo de seleção aleatória ![um modelo de classificação que tem um desempenho melhor](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve2.PNG)

#### <a name="gains-curve"></a>Curva de ganhos

Um gráfico de ganhos avalia o desempenho de um modelo de classificação por cada parte dos dados. Ela mostra para cada percentil do conjunto de dados, quanto melhor pode esperar efetuar comparado com um modelo de seleção aleatória.

Utilize o gráfico de quadro de ganhos cumulativos para ajudar a escolher o limite classificação usando uma percentagem que corresponde a um ganho de pretendida do modelo. Estas informações proporciona outra forma de ver os resultados num gráfico de comparação de precisão que acompanha este artigo.

Exemplo 1: Um modelo de classificação com ganho mínimo ![um modelo de classificação com ganho mínimo](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve1.PNG)

Exemplo 2: Um modelo de classificação com ganho significativo ![um modelo de classificação com ganho significativo](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve2.PNG)

#### <a name="calibration-plot"></a>Desenho de calibragem

Para todos os problemas de classificação, pode rever a linha de calibração para cada classe num determinado modelo preditivo, média de macro e micro-média. 

Um desenho de calibragem é utilizado para apresentar a confiança de um modelo preditivo. Ele faz isso ao mostrar a relação entre a probabilidade prevista e a probabilidade real, em que "probabilidade" representa a probabilidade de que uma instância específica pertence alguns da etiqueta. Um modelo calibrated também se alinha com a y = x linha, onde é razoavelmente confiante em sua previsões. Um modelo de confident excessiva se alinha com a y = 0 linha, em que a probabilidade prevista está presente, mas não existe nenhuma probabilidade real.

Exemplo 1: Um modelo mais bem calibrated ![ mais bem calibrated modelo](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve1.PNG)

Exemplo 2: Um modelo de confident excessiva ![um modelo de confident excessiva](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve2.PNG)

### <a name="regression"></a>Regressão
Para cada modelo de regressão, será criado usando a automatizada capacidades de machine learning do Azure Machine Learning, pode ver os gráficos seguintes: 
+ [Vs previstas. VERDADEIRO](#pvt)
+ [Histograma da residuals](#histo)

<a name="pvt"></a>

#### <a name="predicted-vs-true"></a>Vs previstas. Verdadeiro

Vs previstas. TRUE mostra a relação entre um valor previsto e o respetivo valor de verdadeiro correlating para um problema de regressão. Este gráfico pode ser utilizado para medir o desempenho de um modelo como o mais perto de y = x linha os valores previstos são, melhor será a precisão de um modelo preditivo.

Após cada execução, pode ver um previstos versus verdadeiro gráfico para cada modelo de regressão. Para proteger a privacidade dos dados, os valores são posicionados em conjunto e o tamanho de cada contentor é mostrado como um gráfico de barras na parte inferior da área do gráfico. Pode comparar o modelo de previsão, com a área de tom mais clara, que mostra de margens de erro, com o valor ideal de onde o modelo deve ser.

Exemplo 1: Um modelo de regressão com precisão baixa em previsões ![um modelo de regressão com precisão baixa em previsões](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression1.PNG)

Exemplo 2: Um modelo de regressão com precisão elevada em suas previsões ![um modelo de regressão com precisão elevada em suas previsões](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression2.PNG)

<a name="histo"></a>

#### <a name="histogram-of-residuals"></a>Histograma da residuals

Um residuais representa um y observado – o y previsto. Para mostrar uma margem de erro com tendência baixa, o histograma de residuals deve ser formatado como uma curva de sino centrada em torno de 0. 

Exemplo 1: Um modelo de regressão com tendência a erros ![modelo de regressão de SA com tendência a erros](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression3.PNG)

Exemplo 2: Um modelo de regressão com distribuição mais uniforme de erros ![um modelo de regressão com distribuição mais uniforme de erros](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression4.PNG)

### <a name="model-explain-ability-and-feature-importance"></a>Importância de capacidade de explicar e funcionalidade de modelo

Importância da funcionalidade dá uma pontuação que indica o quão valiosa foi de cada funcionalidade na construção de um modelo. Pode rever a classificação de importância de funcionalidade para o modelo geral, bem como por classe num modelo preditivo. Pode ver por funcionalidade como a importância se compara em relação a cada classe e geral.

![Capacidade de explicar de funcionalidade](./media/how-to-track-experiments/azure-machine-learning-auto-ml-feature_explain1.PNG)

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
