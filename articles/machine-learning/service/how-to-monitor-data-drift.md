---
title: Detectar descompasso de dados (versão prévia) em implantações de AKS
titleSuffix: Azure Machine Learning
description: Detectar descompasso de dados nos modelos implantados do serviço kubernetes do Azure no Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: 536f3ab506dcbe2b8997f2c1870f25244b6c070f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489666"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Detectar descompasso de dados (versão prévia) em modelos implantados no serviço kubernetes do Azure (AKS)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo, você aprenderá a monitorar a descompasso de dados entre o conjunto de dados de treinamento e de inferência de um modelo implantado. No contexto do aprendizado de máquina, os modelos de aprendizado de máquina treinados podem enfrentar desempenho de previsão degradado devido à descompasso. Com Azure Machine Learning, você pode monitorar a descompasso de dados e o serviço pode enviar um alerta por email para você quando for detectado descompasso.

## <a name="what-is-data-drift"></a>O que é descompasso de dados?

No contexto do Machine Learning, a descompasso de dados é a alteração nos dados de entrada do modelo que leva à degradação do desempenho do modelo. É um dos principais motivos em que a precisão do modelo degrada ao longo do tempo, o que monitora a descompasso de dados ajuda a detectar problemas de desempenho do modelo. 

## <a name="what-can-i-monitor"></a>O que posso monitorar?

Com Azure Machine Learning, você pode monitorar as entradas para um modelo implantado em AKS e comparar esses dados com o conjunto de informações de treinamento para o modelo. Em intervalos regulares, os dados de inferência são [instantâneos e](how-to-explore-prepare-data.md)analisados, em seguida, computados em relação ao conjunto de dados de linha de base para produzir uma análise de descompasso que: 

+ Mede a magnitude da descompasso de dados, chamada de coeficiente de descompasso.
+ Mede a contribuição de descompasso de dados por recurso, informando quais recursos causaram a descompasso de dados.
+ Mede métricas de distância. Atualmente, a Wasserstein e a distância de energia são computadas.
+ Mede as distribuições de recursos. Estimativa e histogramas de densidade do kernel no momento.
+ Enviar alertas para descompasso de dados por email.

> [!Note]
> Esse serviço está em (versão prévia) e limitado nas opções de configuração. Consulte nossa [documentação de API](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) e [notas de versão](azure-machine-learning-release-notes.md) para obter detalhes e atualizações. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Como a descompasso de dados é monitorada no Azure Machine Learning

Usando Azure Machine Learning, a descompasso de dados é monitorada por meio de conjuntos ou implantações. Para monitorar a descompasso de dados, é um DataSet de linha de base – geralmente o conjunto de dado de treinamento para um modelo-é especificado. Um segundo conjunto de dados-geralmente modelar os dados de entrada coletados de uma implantação-é testado no conjunto de dado de linha de base. Os dois conjuntos de dados são de perfil e são inseridos para o serviço de monitoramento de descompasso. Um modelo de aprendizado de máquina é treinado para detectar diferenças entre os dois conjuntos de valores. O desempenho do modelo é convertido para o coeficiente de descompasso, que mede a magnitude do descompasso entre os dois conjuntos de valores. Usando a [interpretação de modelo](how-to-machine-learning-interpretability.md), os recursos que contribuem para o coeficiente de descompasso são calculados. No perfil do conjunto de dados, as informações estatísticas sobre cada recurso são rastreadas. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se você não tiver uma, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

- O SDK do Azure Machine Learning para Python instalado. Use as instruções em [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para fazer o seguinte:

    - Criar um ambiente Miniconda
    - Instalar o SDK do Azure Machine Learning para Python

- Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).

- Um [arquivo de configuração](how-to-configure-environment.md#workspace)do espaço de trabalho.

- Instale o SDK de descompasso de dados usando o seguinte comando:

    ```shell
    pip install azureml-datadrift
    ```

- Crie um [conjunto](how-to-create-register-datasets.md) de dados a partir de seus dados de treinamento do modelo.

- Especifique o conjunto de registros de treinamento ao [registrar](concept-model-management-and-deployment.md) o modelo. O exemplo a seguir demonstra o uso do parâmetro `datasets` para especificar o conjunto de os de treinamento:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Habilite a coleta de dados de modelo](how-to-enable-data-collection.md) para coletar dados da implantação AKs do modelo e confirmar que os dados estão sendo coletados no contêiner de blob `modeldata`.

## <a name="configure-data-drift"></a>Configurar descompasso de dados
Para configurar a descompasso de dados para seu experimento, importe as dependências conforme mostrado no exemplo de Python a seguir. 

Este exemplo demonstra como configurar o objeto [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py) :

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Enviar uma execução do DataDriftDetector

Com o objeto `DataDriftDetector` configurado, você pode enviar uma [execução de descompasso de dados](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) em uma determinada data para o modelo. Como parte da execução, habilite alertas do DataDriftDetector definindo o parâmetro `drift_threshold`. Se o [datadrift_coefficient](#metrics) estiver acima do determinado `drift_threshold`, um email será enviado.

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

## <a name="visualize-drift-metrics"></a>Visualizar métricas de descompasso

<a name="metrics"></a>

Depois de enviar sua execução do DataDriftDetector, você poderá ver as métricas de descompasso salvas em cada iteração de execução para uma tarefa de descompasso de dados:


|Métrica|Descrição|
--|--|
wasserstein_distance|Distância estatística definida para distribuição numérica unidimensional.|
energy_distance|Distância estatística definida para distribuição numérica unidimensional.|
datadrift_coefficient|Calculado da mesma forma que o coeficiente de correlação de Matthew, mas essa saída é um número real que varia de 0 a 1. No contexto de descompasso, 0 indica que não há descompasso e 1 indica o descompasso máximo.|
datadrift_contribution|Importância dos recursos que contribuem para descompasso.|

Há várias maneiras de exibir as métricas de descompasso:

* Use o [widget `RunDetails`Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Use a função [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) em qualquer `datadrift` objeto de execução.
* Exiba as métricas na seção **modelos** do seu espaço de trabalho no [Azure Machine Learning Studio](https://ml.azure.com).

O exemplo de Python a seguir demonstra como plotar métricas de descompasso de dados relevantes. Você pode usar as métricas retornadas para criar visualizações personalizadas:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Consulte descompasso de dados detectado por Azure Machine Learning](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Agendar verificações de descompasso de dados 

Quando você habilita a detecção de descompasso de dados, um DataDriftDetector é executado na frequência especificada, agendada. Se o datadrift_coefficient atingir o `drift_threshold`fornecido, um email será enviado com cada execução agendada. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

A configuração do detector de descompasso de dados pode ser vista em **modelos** na guia **detalhes** em seu espaço de trabalho no [Azure Machine Learning Studio](https://ml.azure.com).

![Descompasso de dados do Azure Machine Learning Studio](media/how-to-monitor-data-drift/drift-config.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Exibir resultados em seu Azure Machine Learning Studio

Para exibir os resultados em seu espaço de trabalho no [Azure Machine Learning Studio](https://ml.azure.com), navegue até a página modelo. Na guia detalhes do modelo, a configuração de descompasso de dados é mostrada. Uma guia **descompasso de dados** agora está disponível visualizando as métricas de descompasso de dados. 

[descompasso de dados do ![Azure Machine Learning Studio](media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)


## <a name="receiving-drift-alerts"></a>Recebendo alertas de descompasso

Ao definir o limite de alerta de coeficiente de descompasso e fornecer um endereço de email, um alerta de email [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) é enviado automaticamente sempre que o coeficiente de descompasso está acima do limite. 

Para configurar ações e alertas personalizados, todas as métricas de descompasso de dados são armazenadas no recurso [Application insights](how-to-enable-app-insights.md) que foi criado junto com o espaço de trabalho Azure Machine Learning. Você pode seguir o link no alerta de email para a consulta de Application Insights.

![Alerta de email de descompasso de dados](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Treinar novamente o modelo após a descompasso

Quando a descompasso de dados afeta negativamente o desempenho do modelo implantado, é hora de treinar novamente o modelo. Para fazer isso, continue com as etapas a seguir.

* Investigue os dados coletados e prepare os dados para treinar o novo modelo.
* Divida-o em dados de treinamento/teste.
* Treine o modelo novamente usando os novos dados.
* Avalie o desempenho do modelo recém-gerado.
* Implante o novo modelo se o desempenho for melhor do que o modelo de produção.

## <a name="next-steps"></a>Passos seguintes

* Para obter um exemplo completo de como usar a descompasso de dados, consulte o [notebook de descompasso de dados do Azure ml](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/azure-ml-datadrift.ipynb). Este Jupyter Notebook demonstra como usar um conjunto de dados [aberto do Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) para treinar um modelo para prever o clima, implantá-lo no AKs e monitorar a descompasso de dados. 

* Agradecemos suas dúvidas, seus comentários ou suas sugestões, pois a descompasso de dados se move para a disponibilidade geral. Use o botão comentários do produto abaixo! 
