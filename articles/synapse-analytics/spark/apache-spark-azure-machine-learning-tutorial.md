---
title: 'Tutorial: Treine um modelo em Python com aprendizagem automática de máquinas'
description: Tutorial sobre como treinar um modelo de aprendizagem automática em Python usando Apache Spark e machine learning automatizado.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: e547d047e8d736acbd1cdda5ffe3a78dbe8259f7
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901067"
---
# <a name="tutorial-train-a-model-in-python-with-automated-machine-learning"></a>Tutorial: Treine um modelo em Python com aprendizagem automática de máquinas

A azure Machine Learning é um ambiente baseado na nuvem que permite treinar, implantar, automatizar, gerir e rastrear modelos de aprendizagem de máquinas. 

Neste tutorial, você usa [machine learning automatizado](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) em Azure Machine Learning para criar um modelo de regressão para prever os preços das tarifas de táxi. Este processo chega ao melhor modelo aceitando definições de dados de treino e configuração, e iterando automaticamente através de combinações de diferentes métodos, modelos e configurações de hiperparímetro.

Neste tutorial, vai aprender a:
- Descarregue os dados utilizando o Apache Spark e o Azure Open Datasets.
- Transforme e limpe os dados utilizando os dataframes apache spark.
- Treine um modelo automatizado de regressão de aprendizagem automática.
- Calcular a precisão do modelo.

## <a name="before-you-begin"></a>Antes de começar

- Crie um Apache Spark Pool sem servidor seguindo o [arranque rápido da piscina Apache Spark sem servidor](../quickstart-create-apache-spark-pool-studio.md).
- Complete o tutorial de configuração do [espaço de trabalho Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup) se não tiver um espaço de trabalho de aprendizagem automática Azure existente. 

## <a name="understand-regression-models"></a>Compreender modelos de regressão

*Os modelos de regressão* prevêem valores de saída numéricos baseados em preditores independentes. Na regressão, o objetivo é ajudar a estabelecer a relação entre essas variáveis preditores independentes, estimando como uma variável afeta as outras.  

### <a name="example-based-on-new-york-city-taxi-data"></a>Exemplo baseado em dados de táxi de Nova Iorque

Neste exemplo, você usa Spark para realizar algumas análises em dados de dicas de viagem de táxi de Nova Iorque (NYC). Os dados estão disponíveis através [de Conjuntos de Dados Azure Open](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Este subconjunto do conjunto de dados contém informações sobre viagens de táxi amarelas, incluindo informações sobre cada viagem, o início e o fim e locais, e o custo.

> [!IMPORTANT]
> Pode haver taxas adicionais por retirar estes dados do seu local de armazenamento. Nos passos seguintes, desenvolve-se um modelo para prever os preços das tarifas de táxi de NYC. 

##  <a name="download-and-prepare-the-data"></a>Baixar e preparar os dados

Eis como:

1. Crie um caderno utilizando o núcleo PySpark. Para obter instruções, consulte [Criar um caderno](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook).
   
    > [!Note]
    > Por causa do núcleo PySpark, não precisa de criar nenhum contexto explicitamente. O contexto Spark é automaticamente criado para si quando executar a primeira célula de código.
  
2. Como os dados brutos estão num formato Parquet, pode utilizar o contexto Spark para puxar o ficheiro diretamente para a memória como um dataframe. Crie um dataframe Spark recuperando os dados através da API open Datasets. Aqui, você usa as propriedades do dataframe Spark `schema on read` para inferir os tipos de dados e esquema. 
   
    ```python
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow Spark to read from Blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # Spark read parquet, note that it won't load any data yet by now
    df = spark.read.parquet(wasbs_path)

    ```

3. Dependendo do tamanho da sua piscina Spark, os dados brutos podem ser demasiado grandes ou demorar muito tempo a operar. Pode filtrar estes dados para algo menor utilizando os ```start_date``` ```end_date``` filtros e filtros. Isto aplica um filtro que devolve um mês de dados. Depois de ter o dataframe filtrado, também executou a ```describe()``` função no novo dataframe para ver estatísticas sumárias de cada campo. 

   Com base nas estatísticas resumidas, pode ver que existem algumas irregularidades nos dados. Por exemplo, as estatísticas mostram que a distância mínima de viagem é inferior a 0. É necessário filtrar estes pontos de dados irregulares.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime< "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. Em seguida, gerer funcionalidades a partir do conjunto de dados selecionando um conjunto de colunas e criando várias funcionalidades baseadas no tempo a partir do campo de data de recolha. Filtre os forasteiros que foram identificados a partir do passo anterior, e depois remova as últimas colunas porque são desnecessárias para o treino.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster and less expensive down sample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                   , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                   , column('puMonth').alias('month_num') \
                                   , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                   , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                   , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                   ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                           .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                   & (sampled_taxi_df.tipAmount >= 0)\
                                   & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                   & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                   & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                   & (sampled_taxi_df.rateCodeId <= 5)\
                                   & (sampled_taxi_df.paymentType.isin({"1", "2"})))
   taxi_df.show(10)
   ```
   
   Como pode ver, isto criará um novo dataframe com colunas adicionais para o dia do mês, hora de recolha, dia da semana e tempo total de viagem. 

   ![Imagem do quadro de dados do táxi.](./media/azure-machine-learning-spark-notebook/dataset.png#lightbox)

## <a name="generate-test-and-validation-datasets"></a>Gerar conjuntos de dados de teste e validação

Depois de ter o conjunto de dados final, pode dividir os dados em conjuntos de treino e teste utilizando a ```random_ split ``` função em Spark. Utilizando os pesos fornecidos, esta função divide aleatoriamente os dados no conjunto de dados de treino para a formação de modelos e o conjunto de dados de validação para testes.

```python
# Random split dataset using Spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```
Este passo garante que os pontos de dados para testar o modelo acabado não foram utilizados para treinar o modelo. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Conecte-se a um espaço de trabalho de aprendizagem de máquinas Azure
No Azure Machine Learning, um espaço de trabalho é uma classe que aceita a sua subscrição Azure e informações de recursos. Também cria um recurso em nuvem para monitorizar e rastrear as suas execuções de modelos. Neste passo, você cria um objeto de espaço de trabalho a partir do espaço de trabalho de Aprendizagem da Máquina Azure existente.
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Converter um conjunto de dados para um conjunto de dados de aprendizagem de máquinas Azure
Para submeter uma experiência remota, converta o seu conjunto de dados numa Aprendizagem automática Azure ```TabularDatset``` . Um [SeparadorDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) representa dados num formato tabular através da análise dos ficheiros fornecidos.

O código seguinte obtém o espaço de trabalho existente e a loja de dados padrão Azure Machine Learning. Em seguida, passa a datastore e arquiva localizações para o parâmetro do caminho para criar um novo ```TabularDataset``` . 

```python
import pandas 
from azureml.core import Dataset

# Get the Azure Machine Learning Default Datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into Azure Machine Learning Tabular Dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```
![Imagem do conjunto de dados carregado.](./media/azure-machine-learning-spark-notebook/upload-dataset.png)

## <a name="submit-an-automated-experiment"></a>Submeter uma experiência automatizada

As secções seguintes acompanham-no através do processo de submissão de uma experiência automatizada de aprendizagem automática de máquinas.

### <a name="define-training-settings"></a>Definir definições de formação
1. Para submeter uma experiência, é necessário definir o parâmetro de experiência e as definições do modelo para o treino. Para obter a lista completa de configurações, consulte [experiências automatizadas de aprendizagem automática de máquinas em Python](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train).

   ```python
   import logging

   automl_settings = {
       "iteration_timeout_minutes": 10,
       "experiment_timeout_minutes": 30,
       "enable_early_stopping": True,
       "primary_metric": 'r2_score',
       "featurization": 'auto',
       "verbosity": logging.INFO,
       "n_cross_validations": 2}
   ```

1. Passe as definições de treino definidas como `kwargs` parâmetro para um `AutoMLConfig` objeto. Como está a usar o Spark, também deve passar o contexto Spark, que é automaticamente acessível pela ```sc``` variável. Além disso, especifique os dados de formação e o tipo de modelo, que é a regressão neste caso.

   ```python
   from azureml.train.automl import AutoMLConfig

   automl_config = AutoMLConfig(task='regression',
                                debug_log='automated_ml_errors.log',
                                training_data = dataset_training,
                                spark_context = sc,
                                model_explainability = False, 
                                label_column_name ="fareAmount",**automl_settings)
   ```

> [!NOTE]
>Os passos de pré-processamento de aprendizagem automática de máquinas tornam-se parte do modelo subjacente. Estes passos incluem a normalização do recurso, o tratamento de dados em falta e a conversão do texto em numérico. Quando se utiliza o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treino são aplicadas automaticamente aos seus dados de entrada.

### <a name="train-the-automatic-regression-model"></a>Treine o modelo de regressão automática 
Em seguida, você cria um objeto de experiência no seu espaço de trabalho Azure Machine Learning. Uma experiência funciona como um recipiente para as suas corridas individuais. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```
Quando a experiência tiver terminado, a saída retorna detalhes sobre as iterações completas. Para cada iteração, vê-se o tipo de modelo, a duração da execução e a precisão do treino. O **campo BEST** acompanha a melhor pontuação de treino com base no seu tipo métrico.

![Screenshot da saída do modelo.](./media/azure-machine-learning-spark-notebook/model-output.png)

> [!NOTE]
> Depois de submeter a experiência automatizada de aprendizagem automática, executa várias iterações e tipos de modelos. Esta corrida normalmente leva 60-90 minutos. 

### <a name="retrieve-the-best-model"></a>Obter o melhor modelo
Para selecionar o melhor modelo das suas iterações, utilize a ```get_output``` função para devolver o melhor funcionamento e o modelo equipado. O código seguinte recupera o modelo de melhor execução e equipado para qualquer métrica registada ou uma iteração particular.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

### <a name="test-model-accuracy"></a>Precisão do modelo de teste
1. Para testar a precisão do modelo, utilize o melhor modelo para executar previsões de tarifas de táxi no conjunto de dados de teste. A ```predict``` função utiliza o melhor modelo e prevê os valores de y (quantidade de tarifa) a partir do conjunto de dados de validação. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

1. O erro de raiz-médio-quadrado é uma medida frequentemente utilizada das diferenças entre os valores da amostra previstos por um modelo e os valores observados. Calcula-se o erro de raiz-médio-quadrado dos resultados comparando o `y_test` quadro de dados com os valores previstos pelo modelo. 

   A função ```mean_squared_error``` requer duas matrizes e calcula o erro quadrado médio entre eles. Em seguida, pegue a raiz quadrada do resultado. Esta métrica indica aproximadamente a distância entre as previsões das tarifas de táxi dos valores reais das tarifas.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate Root Mean Square Error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   O erro de raiz-médio-quadrado é uma boa medida de como o modelo prevê com precisão a resposta. Pelos resultados, você vê que o modelo é bastante bom em prever tarifas de táxi a partir das características do conjunto de dados, tipicamente dentro de $2,00.

1. Executar o seguinte código para calcular o erro de percentagem média absoluta. Esta métrica expressa precisão em percentagem do erro. Fá-lo calculando uma diferença absoluta entre cada valor previsto e real e, em seguida, resumindo todas as diferenças. Em seguida, expressa essa soma como uma por cento do total dos valores reais.

   ```python
   # Calculate MAPE and Model Accuracy 
   sum_actuals = sum_errors = 0

   for actual_val, predict_val in zip(y_actual, y_predict):
       abs_error = actual_val - predict_val
       if abs_error < 0:
           abs_error = abs_error * -1

       sum_errors = sum_errors + abs_error
       sum_actuals = sum_actuals + actual_val

   mean_abs_percent_error = sum_errors / sum_actuals

   print("Model MAPE:")
   print(mean_abs_percent_error)
   print()
   print("Model Accuracy:")
   print(1 - mean_abs_percent_error)
   ```

   ```Output
   Model MAPE:
   0.03655071038487368

   Model Accuracy:
   0.9634492896151263
   ```
   A partir das duas métricas de precisão de previsão, você vê que o modelo é bastante bom em prever tarifas de táxi a partir das características do conjunto de dados. 

1. Depois de encaixar um modelo linear de regressão, precisa agora de determinar até que ponto o modelo se encaixa nos dados. Para isso, você traça os valores reais da tarifa contra a produção prevista. Além disso, calcula-se a medida ao quadrado R para perceber a proximidade dos dados à linha de regressão instalada.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the Actual vs Predicted Fare Amount Values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amont R^2={}".format(r2))
   plt.show()

   ```
   ![Screenshot do enredo da regressão.](./media/azure-machine-learning-spark-notebook/fare-amount.png)

   Pelos resultados, pode ver-se que a medida ao quadrado de R representa 95% da variação. Isto também é validado pelo enredo real contra o enredo observado. Quanto mais variação for contabilizada pelo modelo de regressão, mais perto os pontos de dados cairão para a linha de regressão equipada.  

## <a name="register-model-to-azure-machine-learning"></a>Registar modelo para Azure Machine Learning
Depois de validar o seu melhor modelo, pode registá-lo no Azure Machine Learning. Em seguida, pode descarregar ou implementar o modelo registado e receber todos os ficheiros que registou.

```python
description = 'My automated ML model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```
```Output
NYCGreenTaxiModel 1
```
## <a name="view-results-in-azure-machine-learning"></a>Ver resultados em Azure Machine Learning
Por último, também pode aceder aos resultados das iterações indo à experiência no seu espaço de trabalho Azure Machine Learning. Aqui, pode pesquisar detalhes adicionais sobre o estado da sua execução, modelos de tentativas e outras métricas de modelo. 

![Screenshot do espaço de trabalho de Aprendizagem de Máquinas Azure.](./media/azure-machine-learning-spark-notebook/azure-machine-learning-workspace.png)

## <a name="next-steps"></a>Passos seguintes
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Tutorial: Construa uma app de machine learning com Apache Spark MLlib e Azure Synapse Analytics](./apache-spark-machine-learning-mllib-notebook.md)
