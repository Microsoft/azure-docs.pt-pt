---
title: Detetar os descompassos de dados (pré-visualização) nas implantações do AKS
titleSuffix: Azure Machine Learning service
description: Saiba como detetar os descompassos de dados no serviço Kubernetes do Azure implementadas modelos no serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 06/20/2019
ms.openlocfilehash: c446c8236ca64948f0bb6a8354a83579cc6ff24c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443947"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service"></a>Detetar os descompassos de dados (pré-visualização) em modelos implementados no serviço Kubernetes do Azure
Neste artigo, saiba como monitorizar para desvios de dados entre o conjunto de dados de formação e inferência de tipos de dados de um modelo implementado. 

## <a name="what-is-data-drift"></a>O que é descompassos de dados?

Desvios de dados, também conhecido como descompassos de conceito, é uma das razões principais em que a precisão do modelo degrada ao longo do tempo. Isso acontece quando os dados fornecidos para um modelo na produção são diferentes dos dados utilizados para preparar o modelo. O serviço Azure Machine Learning pode monitorizar os descompassos de dados e, quando é detetado descompassos, o serviço pode enviar um alerta por e-mail para si.  

> [!Note]
> Este serviço está em (pré-visualização) e limitado de opções de configuração. Consulte nossos [documentação da API](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) e [notas de versão](azure-machine-learning-release-notes.md) para obter detalhes e atualizações. 

## <a name="what-can-i-monitor"></a>O que posso monitorizar?
Com o serviço do Azure Machine Learning, pode monitorizar as entradas para um modelo implementado no AKS e comparar estes dados para o conjunto de dados de treinamento para o modelo. Em intervalos regulares, os dados de inferência de tipos são [tirar instantâneo e para as quais criar perfis](how-to-explore-prepare-data.md), em seguida, calculado com base no conjunto de linha de base dados para produzir uma análise de desvios de dados que: 

+ Mede a magnitude da descompassos de dados, chamado o coeficiente de desvios.
+ Medidas dos dados inconsistências contribuição pela funcionalidade, informando quais recursos causada descompassos de dados.
+ Métricas de distâncias de medidas. Atualmente são calculadas Wasserstein e a distância de energia.
+ Distribuições de medidas de recursos. Estimativa de densidade de kernel atualmente e histogramas.
+ Envie alertas para dados de inconsistências por e-mail.

Para obter detalhes sobre a forma como estas métricas são computadas, consulte a [conceito de desvios de dados](concept-data-drift.md) artigo.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

- Uma área de trabalho do serviço do Azure Machine Learning e o Azure Machine Learning SDK para Python instalada. Saiba como obter estes pré-requisitos com o [como configurar um ambiente de desenvolvimento](how-to-configure-environment.md) documento.

- [Configurar o ambiente](how-to-configure-environment.md)e, em seguida, instale os descompassos de dados SDK com o seguinte comando:

    ```
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

## <a name="import-dependencies"></a>Dependências de importação 
Importe as dependências utilizadas neste guia:

```python
# Azure ML service packages 
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration
``` 

## <a name="configure-data-drift"></a>Configurar os descompassos de dados 

O exemplo de Python seguinte demonstra a configurar o `DataDriftDetector` objeto:

```python
# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

Para obter mais informações, consulte o `[DataDrift](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py)` documentação de referência de classe.

## <a name="submit-a-datadriftdetector-run"></a>Submeter uma execução de DataDriftDetector

Com o `DataDriftDetector` objeto configurado, pode submeter um [descompassos de dados, executar](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) numa determinada data para o modelo. 

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

O exemplo de Python seguinte demonstra como a representar dados relevantes descompassos métricas. Pode utilizar as métricas retornadas para criar visualizações personalizadas:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Consulte os descompassos de dados detetados pelo Azure Machine Learning](media/how-to-monitor-data-drift/drift_show.png)

Para obter detalhes sobre as métricas que são computadas, consulte a [conceito de desvios de dados](concept-data-drift.md) artigo.

## <a name="schedule-data-drift-scans"></a>Verificações de desvios de dados de agenda 

Quando ativa a deteção de desvios de dados, um DataDriftDetector é executado com a frequência especificada, agendada. Se o coeficiente de desvios é superior ao limiar especificado, é enviado um e-mail. 

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

Definindo o coeficiente de desvios limiar de alerta e fornecer um endereço de e-mail, um [do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) alerta por e-mail é enviada automaticamente sempre que o coeficiente de desvios é superior ao limiar. Para configurar alertas personalizados e ações efetuada, todas as métricas de desvios de dados são armazenadas no recurso do Application Insights que foi criado, juntamente com a área de trabalho do serviço do Azure Machine Learning. Pode seguir a ligação no e-mail do alerta para a consulta do Application Insights.

![Alerta de E-Mail de desvios de dados](media/how-to-monitor-data-drift/drift_email.png)

## <a name="next-steps"></a>Passos Seguintes

* Para obter um exemplo completo de usar os descompassos de dados, consulte a [bloco de notas de inconsistências de dados do Azure ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb). Este bloco de notas do Jupyter demonstra como utilizar um [conjunto de dados aberto do Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) para preparar um modelo para prever a meteorologia, implementá-la no AKS e monitorar os descompassos de dados. 

* Seria muito Agradecemos suas perguntas, comentários ou sugestões conforme os descompassos de dados se move na direção geral disponibilidade. Utilize o botão de feedback do produto abaixo! 
