---
title: Detetar os descompassos de dados (pré-visualização) nas implantações do AKS
titleSuffix: Azure Machine Learning service
description: Detete os descompassos de dados no serviço Kubernetes do Azure implementadas modelos no serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 07/08/2019
ms.openlocfilehash: 3b8152bde8b7e44dde1b0b9c82216333778f83da
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806010"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Detetar os descompassos de dados (pré-visualização) sobre os modelos de implementação para o Azure Kubernetes Service (AKS)

Neste artigo, saiba como monitorizar para desvios de dados entre o conjunto de dados de formação e inferência de tipos de dados de um modelo implementado. No contexto do machine learning, com formação modelos de machine learning poderão ter de predição de degradação de desempenho devido a desvios. Com o serviço Azure Machine Learning, pode monitorizar os descompassos de dados e o serviço pode enviar um alerta por e-mail para si quando descompassos é detetado.

## <a name="what-is-data-drift"></a>O que é descompassos de dados?

Desvios de dados acontece quando os dados fornecidos para um modelo na produção são diferentes dos dados utilizados para preparar o modelo. É uma das razões principais em que a precisão do modelo degrada ao longo do tempo, os dados de monitorização, portanto, descompassos ajuda a detetar problemas de desempenho do modelo. 

## <a name="what-can-i-monitor"></a>O que posso monitorizar?

Com o serviço do Azure Machine Learning, pode monitorizar as entradas para um modelo implementado no AKS e comparar estes dados para o conjunto de dados de treinamento para o modelo. Em intervalos regulares, os dados de inferência de tipos são [tirar instantâneo e para as quais criar perfis](how-to-explore-prepare-data.md), em seguida, calculado com base no conjunto de linha de base dados para produzir uma análise de desvios de dados que: 

+ Mede a magnitude da descompassos de dados, chamado o coeficiente de desvios.
+ Medidas dos dados inconsistências contribuição pela funcionalidade, informando quais recursos causada descompassos de dados.
+ Métricas de distâncias de medidas. Atualmente são calculadas Wasserstein e a distância de energia.
+ Distribuições de medidas de recursos. Estimativa de densidade de kernel atualmente e histogramas.
+ Envie alertas para dados de inconsistências por e-mail.

> [!Note]
> Este serviço está em (pré-visualização) e limitado de opções de configuração. Consulte nossos [documentação da API](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) e [notas de versão](azure-machine-learning-release-notes.md) para obter detalhes e atualizações. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Como os descompassos de dados está sendo monitorado no serviço Azure Machine Learning

Utilizar o serviço Azure Machine Learning, descompassos de dados é monitorizado por meio de conjuntos de dados ou implementações. Para monitorar os descompassos de dados, um conjunto de dados de linha de base - normalmente, o treinamento conjunto de dados para um modelo - é especificado. Normalmente, de recolha de dados de entrada de modelo de uma implementação - é testado um segundo conjunto de dados - conjunto de dados da linha de base. Ambos os conjuntos de dados são [profiled](how-to-explore-prepare-data.md#explore-with-summary-statistics) e serviço de monitoramento de inconsistências de entrada para os dados. A preparar um modelo de machine learning é utilizado para detetar as diferenças entre os dois conjuntos de dados. Desempenho do modelo é convertido para o coeficiente de desvios, que mede a magnitude da descompassos entre os dois conjuntos de dados. Usando [modelar interpretability](machine-learning-interpretability-explainability.md), os recursos que contribuem para o coeficiente de desvios são computados. O perfil de conjunto de dados, são rastreadas informações estatísticas sobre cada funcionalidade. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, crie uma conta gratuita, antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

- Uma área de trabalho do serviço do Azure Machine Learning e o Azure Machine Learning SDK para Python instalada. Utilize as instruções em [criar uma área de trabalho do serviço do Azure Machine Learning](setup-create-workspace.md#sdk) para fazer o seguinte:

    - Criar um ambiente de Miniconda
    - Instalar o Azure Machine Learning SDK para Python
    - Criar uma área de trabalho
    - Escreva um ficheiro de configuração da área de trabalho (aml_config/config.json).

- Instale os descompassos de dados SDK com o seguinte comando:

    ```shell
    pip install azureml-contrib-datadrift
    ```

- Criar uma [conjunto de dados](how-to-create-register-datasets.md) de dados de preparação de seu modelo.

- Especificar o conjunto de dados de treinamento quando [registar](concept-model-management-and-deployment.md) o modelo. O exemplo seguinte demonstra como utilizar o `datasets` parâmetro para especificar o conjunto de dados de treinamento:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Ativar a recolha de dados de modelo](how-to-enable-data-collection.md) recolher dados da implementação do AKS do modelo e confirmarem os dados é recolhidos no `modeldata` contentor de Blobs.

## <a name="configure-data-drift"></a>Configurar os descompassos de dados
Para configurar os descompassos de dados para a experimentação, importe as dependências como mostrado no seguinte exemplo de Python. 

Este exemplo demonstra a configurar o [ `DataDriftDetector` ](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py) objeto:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Submeter uma execução de DataDriftDetector

Com o `DataDriftDetector` objeto configurado, pode submeter um [descompassos de dados, executar](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) numa determinada data para o modelo. Como parte da execução, ativar alertas de DataDriftDetector ao definir o `drift_threshold` parâmetro. Se o [datadrift_coefficient](#metrics) for superior a determinado `drift_threshold`, é enviado um e-mail.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Visualize os descompassos de métricas

<a name="metrics"></a>

Depois de submeter o seu DataDriftDetector executar, é possível ver as métricas de desvios são salvas em cada iteração de execução de uma tarefa de desvios de dados:


|Métrica|Descrição|
--|--|
wasserstein_distance|Distância estatística definida para distribuição numérica unidimensional.|
energy_distance|Distância estatística definida para distribuição numérica unidimensional.|
datadrift_coefficient|Da mesma forma como o coeficiente de correlação de Matthew calculados, mas esta saída é um número real entre 0 e 1. No contexto de desvios, 0 indica sem descompassos e 1 descompassos máximo.|
datadrift_contribution|Importância da funcionalidade dos recursos que contribuem para divergir.|

Existem várias formas para visualizar as métricas de desvios:

* Utilize o widget do Jupyter.
* Utilize o [ `get_metrics()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) função em qualquer `datadrift` executar o objeto.
* Ver as métricas no portal do Azure no seu modelo

O exemplo de Python seguinte demonstra como a representar dados relevantes descompassos métricas. Pode utilizar as métricas retornadas para criar visualizações personalizadas:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Consulte os descompassos de dados detetados pelo Azure Machine Learning](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Verificações de desvios de dados de agenda 

Quando ativa a deteção de desvios de dados, um DataDriftDetector é executado com a frequência especificada, agendada. Se atingir o datadrift_coefficient a determinado `drift_threshold`, é enviado um e-mail com cada execução agendada. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

A configuração do detetor de desvios de dados pode ser vista na página de detalhes do modelo no portal do Azure.

![Portal do Azure dados Descompassos de configuração](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>Ver os resultados na interface do Usuário de área de trabalho do Azure ML

Para ver os resultados na IU de área de trabalho do Azure ML, navegue para a página do modelo. No separador de detalhes do modelo, é apresentada a configuração de desvios de dados. Um separador de 'Descompassos de dados (pré-visualização)' já está disponível a visualização das métricas de desvios de dados. 

![Portal do Azure Descompassos de dados](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="receiving-drift-alerts"></a>Recebimento de desvios de alertas

Definindo o coeficiente de desvios limiar de alerta e fornecer um endereço de e-mail, um [do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) alerta por e-mail é enviada automaticamente sempre que o coeficiente de desvios é superior ao limiar. 

Para configurar alertas personalizados e ações efetuada, todas as métricas de desvios de dados são armazenadas no [Application Insights](how-to-enable-app-insights.md) recurso que foi criado, juntamente com a área de trabalho do serviço do Azure Machine Learning. Pode seguir a ligação no e-mail do alerta para a consulta do Application Insights.

![Alerta de E-Mail de desvios de dados](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Voltar a preparar seu modelo após descompassos

Quando os descompassos de dados afeta negativamente o desempenho do seu modelo implementado, é hora de voltar a preparar o modelo. O seguinte procedimento [ `diff()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#diff-rhs-dataset--compute-target-none--columns-none-
) método lhe dá uma idéia inicial do que mudou entre os conjuntos de dados de treinamento antigos e novos. 

```python
from azureml.core import Dataset

old_training_dataset.diff(new_training_dataset)
```

Com base no resultado do código anterior, pode pretender voltar a preparar seu modelo. Para fazer isso, continue com os passos seguintes.

* Investigue os dados recolhidos e preparar dados para preparar o modelo de novo.
* Dividi-la em dados de formação e teste.
* Prepare o modelo novamente usando os novos dados.
* Avalie o desempenho do modelo gerado recentemente.
* Implemente o novo modelo se o desempenho é melhor do que o modelo de produção.

## <a name="next-steps"></a>Passos seguintes

* Para obter um exemplo completo de usar os descompassos de dados, consulte a [bloco de notas de inconsistências de dados do Azure ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb). Este bloco de notas do Jupyter demonstra como utilizar um [conjunto de dados aberto do Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) para preparar um modelo para prever a meteorologia, implementá-la no AKS e monitorar os descompassos de dados. 

* Seria muito Agradecemos suas perguntas, comentários ou sugestões conforme os descompassos de dados se move na direção geral disponibilidade. Utilize o botão de feedback do produto abaixo! 
