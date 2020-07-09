---
title: Detetar deriva de dados nas implementações da AKS
titleSuffix: Azure Machine Learning
description: Detete a deriva de dados (pré-visualização) no Serviço Azure Kubernetes implantado em modelos implantados em Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: 0f56ab853983ebf9b3e27f38ae1737c0c2bce4ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84430290"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Detetar deriva de dados (pré-visualização) em modelos implantados no Serviço Azure Kubernetes (AKS)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo, aprende-se a monitorizar a deriva de dados entre o conjunto de dados de formação e os dados de inferência de um modelo implementado. No contexto da aprendizagem automática, modelos de aprendizagem automática treinados podem experimentar um desempenho de previsão degradado devido à deriva. Com o Azure Machine Learning, pode monitorizar a deriva de dados e o serviço pode enviar-lhe um alerta de e-mail quando a deriva for detetada.

## <a name="what-is-data-drift"></a>O que é a deriva de dados?

No contexto da aprendizagem automática, a deriva de dados é a mudança nos dados de entrada do modelo que leva à degradação do desempenho do modelo. É uma das principais razões em que a precisão do modelo se degrada ao longo do tempo, pelo que monitorizar a deriva de dados ajuda a detetar problemas de desempenho do modelo. 

## <a name="what-can-i-monitor"></a>O que posso monitorizar?

Com o Azure Machine Learning, pode monitorizar as entradas de um modelo implantado na AKS e comparar estes dados com o conjunto de dados de formação do modelo. A intervalos regulares, os dados de inferência são [instantâneos e perfilados,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)depois calculados contra o conjunto de dados de base para produzir uma análise de deriva de dados que: 

+ Mede a magnitude da deriva de dados, chamado coeficiente de deriva.
+ Mede a contribuição de deriva de dados por recurso, indicando quais as características que causaram a deriva de dados.
+ Mede as métricas de distância. Atualmente Wasserstein e Energy Distance são computados.
+ Mede a distribuição de características. Atualmente, estimação de densidade de núcleo e histogramas.
+ Envie alertas para a deriva de dados por e-mail.

> [!Note]
> Este serviço está em (pré-visualização) e limitado nas opções de configuração. Consulte a nossa [Documentação](https://docs.microsoft.com/python/api/azureml-datadrift/) da API e [As Notas de Lançamento](azure-machine-learning-release-notes.md) para mais detalhes e atualizações. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Como a deriva de dados é monitorizada em Azure Machine Learning

Utilizando a Azure Machine Learning, a deriva de dados é monitorizada através de conjuntos de dados ou implementações. Para monitorizar a deriva de dados, é especificado um conjunto de dados de base - geralmente o conjunto de dados de formação para um modelo - especificado. Um segundo conjunto de dados - geralmente dados de entrada de modelo recolhidos a partir de uma implementação - é testado com base no conjunto de dados. Ambos os conjuntos de dados são perfilados e introduzidos no serviço de monitorização da deriva de dados. Um modelo de aprendizagem automática é treinado para detetar diferenças entre os dois conjuntos de dados. O desempenho do modelo é convertido para o coeficiente de deriva, que mede a magnitude da deriva entre os dois conjuntos de dados. Utilizando a interpretação do [modelo,](how-to-machine-learning-interpretability.md)são calculadas as características que contribuem para o coeficiente de deriva. A partir do perfil do conjunto de dados, a informação estatística sobre cada recurso é rastreada. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

- O Azure Machine Learning SDK para Python instalado. Utilize as instruções da [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para fazer o seguinte:

    - Criar um ambiente miniconda
    - Instale o Azure Machine Learning SDK para Python

- Um [espaço de trabalho de aprendizagem automática Azure.](how-to-manage-workspace.md)

- Um ficheiro de [configuração](how-to-configure-environment.md#workspace)do espaço de trabalho.

- Instale o SDK de deriva de dados utilizando o seguinte comando:

    ```shell
    pip install azureml-datadrift
    ```

- Crie um [conjunto de dados a](how-to-create-register-datasets.md) partir dos dados de treino do seu modelo.

- Especifique o conjunto de dados de treino ao [registar](concept-model-management-and-deployment.md) o modelo. O exemplo a seguir demonstra a utilização do `datasets` parâmetro para especificar o conjunto de dados de formação:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Permitir a recolha](how-to-enable-data-collection.md) de dados do modelo para recolher dados da implantação AKS do modelo e confirmar que os dados estão a ser recolhidos no `modeldata` recipiente blob.

## <a name="configure-data-drift"></a>Configure deriva de dados
Para configurar a deriva de dados para a sua experiência, importe dependências como visto no exemplo python seguinte. 

Este exemplo demonstra a configuração do [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) objeto:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DataDriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Submeter uma corrida dataDriftDetector

Com o `DataDriftDetector` objeto configurado, pode submeter uma [deriva de dados executada](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-) numa data para o modelo. Como parte da execução, ative os alertas dataDriftDetector definindo o `drift_threshold` parâmetro. Se o [datadrift_coefficient](#visualize-drift-metrics) estiver acima do `drift_threshold` dado, é enviado um e-mail.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run.id)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Visualizar métricas de deriva

<a name="metrics"></a>

Depois de submeter a sua execução DataDriftDetector, é possível ver as métricas de deriva que são guardadas em cada iteração de execução para uma tarefa de deriva de dados:


|Metric|Descrição|
--|--|
wasserstein_distance|Distância estatística definida para distribuição numérica unidimensional.|
energy_distance|Distância estatística definida para distribuição numérica unidimensional.|
datadrift_coefficient|Calculado da mesma forma como o coeficiente de correlação de Matthew, mas esta saída é um número real que varia de 0 a 1. No contexto da deriva, 0 indica que não há deriva e 1 indica deriva máxima.|
datadrift_contribution|Característica importante das características que contribuem para a deriva.|

Existem várias formas de ver as métricas de deriva:

* Utilize o `RunDetails` [widget Jupyter.](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)
* Utilize a [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) função em qualquer `datadrift` objeto de execução.
* Veja as métricas da secção **de Modelos** do seu espaço de trabalho no [estúdio Azure Machine Learning](https://ml.azure.com).

O exemplo python que se segue demonstra como traçar métricas de deriva de dados relevantes. Pode utilizar as métricas devolvidas para construir visualizações personalizadas:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Ver deriva de dados detetada pela Azure Machine Learning](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Agendar análises à deriva de dados 

Quando ativa a deteção de deriva de dados, um DataDriftDetector é executado na frequência programada especificada. Se o datadrift_coefficient chegar ao `drift_threshold` dado, é enviado um e-mail com cada execução programada. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

A configuração do detetor de deriva de dados pode ser vista nos **Modelos** no separador **Detalhes** no seu espaço de trabalho no [estúdio Azure Machine Learning](https://ml.azure.com).

[![Azure Machine Learning estúdio Data Drift](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Veja os resultados no seu estúdio Azure Machine Learning

Para ver os resultados no seu espaço de trabalho no [estúdio Azure Machine Learning,](https://ml.azure.com)navegue para a página do modelo. No separador detalhes do modelo, é mostrada a configuração da deriva de dados. Um **separador de deriva de dados** está agora disponível visualizando as métricas de deriva de dados. 

[![Azure Machine Learning estúdio Data Drift](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>Receber alertas de deriva

Ao definir o limiar de alerta do coeficiente de deriva e fornecer um endereço de e-mail, um alerta de e-mail [do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) é automaticamente enviado sempre que o coeficiente de deriva estiver acima do limiar. 

Para configurar alertas e ações personalizadas, todas as métricas de deriva de dados são armazenadas no recurso [Application Insights](how-to-enable-app-insights.md) que foi criado juntamente com o espaço de trabalho Azure Machine Learning. Pode seguir o link no alerta de e-mail para a consulta 'Insights de Aplicação'.

![Alerta de email de deriva de dados](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Retreine o seu modelo após deriva

Quando a deriva de dados impacta negativamente o desempenho do seu modelo implantado, é hora de reconverter o modelo. Para tal, proceda com os seguintes passos.

* Investigue os dados recolhidos e prepare os dados para treinar o novo modelo.
* Divida-o em dados de comboio/teste.
* Treine o modelo novamente usando os novos dados.
* Avaliar o desempenho do modelo recém-gerado.
* Implementar um novo modelo se o desempenho for melhor do que o modelo de produção.

## <a name="next-steps"></a>Próximos passos

* Para obter um exemplo completo da utilização da deriva de dados, consulte o [caderno de deriva de dados Azure ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb). Este Caderno Jupyter demonstra o uso de um [Azure Open Dataset](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) para treinar um modelo para prever o tempo, implantá-lo para AKS e monitorizar a deriva de dados. 

* Detetar deriva de dados com [monitores de conjunto de dados](how-to-monitor-datasets.md).

* Apreciamos muito as suas perguntas, comentários ou sugestões à medida que a deriva de dados se move para a disponibilidade geral. Utilize o botão de feedback do produto abaixo! 
