---
title: Monitorize e recolha dados dos pontos finais do serviço web Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como recolher dados de modelos implantados para os pontos finais do serviço web em Azure Kubernetes Service (AKS) ou Azure Container Instances (ACI).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 13b99fe129191b89b5bb2d7f5473e910fa619ce7
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739846"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorizar e recolher dados de pontos finais de serviço Web de ML


Neste artigo, aprende-se a recolher dados de modelos implantados para os pontos finais do serviço web em Azure Kubernetes Service (AKS) ou Azure Container Instances (ACI). Utilize [insights de aplicação Azure](../azure-monitor/app/app-insights-overview.md) para recolher os seguintes dados de um ponto final:
* Dados de saída
* Respostas
* Taxas de pedido, tempos de resposta e taxas de insucesso
* Taxas de dependência, tempos de resposta e taxas de insucesso
* Exceções

O notebook [enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) demonstra conceitos neste artigo.
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
 
## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um espaço de trabalho de aprendizagem automática Azure, um diretório local que contém os seus scripts, e o Azure Machine Learning SDK para Python instalados. Para saber mais, veja [Como configurar um ambiente de desenvolvimento.](how-to-configure-environment.md)

* Um modelo de aprendizagem automática treinado. Para saber mais, consulte o tutorial do [modelo de classificação de imagem train.](tutorial-train-models-with-aml.md)

<a name="python"></a>

## <a name="configure-logging-with-the-python-sdk"></a>Configurar a exploração madeireira com o Python SDK

Nesta secção, aprende-se a ativar o registo de Insight de Aplicação utilizando o Python SDK. 

### <a name="update-a-deployed-service"></a>Atualizar um serviço implantado

Utilize os seguintes passos para atualizar um serviço web existente:

1. Identifique o serviço no seu espaço de trabalho. O valor `ws` para é o nome do seu espaço de trabalho

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Atualize o seu serviço e ative o Azure Application Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Registar vestígios personalizados no seu serviço

> [!IMPORTANT]
> A azure Application Insights apenas regista cargas de até 64kb. Se este limite for atingido, poderá ver erros como fora da memória, ou nenhuma informação poderá ser registada. Se os dados que pretende registar forem maiores de 64kb, deverá armazená-los para armazenar o armazenamento de bolhas utilizando as informações em [Recolha de Dados para modelos em produção](how-to-enable-data-collection.md).
>
> Para situações mais complexas, como o rastreio de modelos dentro de uma implementação AKS, recomendamos a utilização de uma biblioteca de terceiros como [o OpenCensus](https://opencensus.io).

Para registar vestígios personalizados, siga o processo de implementação padrão para AKS ou ACI no [Como implementar e onde](how-to-deploy-and-where.md) documentar. Em seguida, utilize os seguintes passos:

1. Atualize o ficheiro de pontuação adicionando declarações de impressão para enviar dados para o Application Insights durante a inferência. Para informações mais complexas, como os dados do pedido e a resposta, utilize uma estrutura JSON. 

    Os registos de ficheiros de exemplo a seguir `score.py` quando o modelo foi inicializado, a entrada e a saída durante a inferência, e o tempo em que ocorrerem erros.

    
    ```python
    import pickle
    import json
    import numpy 
    from sklearn.externals import joblib
    from sklearn.linear_model import Ridge
    from azureml.core.model import Model
    import time

    def init():
        global model
        #Print statement for appinsights custom traces:
        print ("model initialized" + time.strftime("%H:%M:%S"))
        
        # note here "sklearn_regression_model.pkl" is the name of the model registered under the workspace
        # this call should return the path to the model.pkl file on the local disk.
        model_path = Model.get_model_path(model_name = 'sklearn_regression_model.pkl')
        
        # deserialize the model file back into a sklearn model
        model = joblib.load(model_path)
    

    # note you can pass in multiple rows for scoring
    def run(raw_data):
        try:
            data = json.loads(raw_data)['data']
            data = numpy.array(data)
            result = model.predict(data)
            # Log the input and output data to appinsights:
            info = {
                "input": raw_data,
                "output": result.tolist()
                }
            print(json.dumps(info))
            # you can return any datatype as long as it is JSON-serializable
            return result.tolist()
        except Exception as e:
            error = str(e)
            print (error + time.strftime("%H:%M:%S"))
            return error
    ```

2. Atualize a configuração do serviço e certifique-se de ativar o Application Insights.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Construa uma imagem e implemente-a em AKS ou ACI. Para mais informações, consulte [Como implementar e onde.](how-to-deploy-and-where.md)


### <a name="disable-tracking-in-python"></a>Desativar o rastreio em Python

Para desativar os Insights de Aplicação Azure, utilize o seguinte código:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="configure-logging-with-azure-machine-learning-studio"></a>Configuure a madeira com estúdio Azure Machine Learning

Também pode ativar a Azure Application Insights do estúdio Azure Machine Learning. Quando estiver pronto para implementar o seu modelo como um serviço web, utilize os seguintes passos para ativar o Application Insights:

1. Inscreva-se no estúdio da https://ml.azure.com .
1. Vá a **Modelos** e selecione o modelo que pretende implementar.
1. Selecione  **+Implementar**.
1. Povoar o **modelo implementar.**
1. Expandir o menu **Advanced.**

    ![Formulário de implantação](./media/how-to-enable-app-insights/deploy-form.png)
1. Selecione **Enable Application Insights diagnósticos e recolha de dados**.

    ![Ativar insights de aplicativos](./media/how-to-enable-app-insights/enable-app-insights.png)

## <a name="view-metrics-and-logs"></a>Ver métricas e registos

### <a name="query-logs-for-deployed-models"></a>Registos de consulta para modelos implantados

Os registos dos pontos finais em tempo real são dados do cliente. Pode utilizar a `get_logs()` função para recuperar registos de um serviço web previamente implantado. Os registos podem conter informações detalhadas sobre quaisquer erros ocorridos durante a implantação.

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Se tiver vários Inquilinos, poderá ter de adicionar o seguinte código autenticado antes `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

### <a name="view-logs-in-the-studio"></a>Ver registos no estúdio

A Azure Application Insights armazena os seus registos de serviço no mesmo grupo de recursos que o espaço de trabalho Azure Machine Learning. Utilize os seguintes passos para visualizar os seus dados utilizando o estúdio:

1. Vá ao seu espaço de trabalho Azure Machine Learning no [estúdio.](https://ml.azure.com/)
1. Selecione **Pontos de final**.
1. Selecione o serviço implantado.
1. Selecione a ligação **url Insights Application Insights.**

    [![Localizar insights de aplicação url](./media/how-to-enable-app-insights/appinsightsloc.png)](././media/how-to-enable-app-insights/appinsightsloc.png#lightbox)

1. No 'Insights de Aplicação', a partir do **separador Visão Geral** ou da secção __de Monitorização,__ selecione __Registos__.

    [![Separador geral da monitorização](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Para ver as informações registadas no ficheiro score.py, consulte a tabela __de vestígios.__ As seguintes pesquisas de consulta para registos onde o valor de __entrada__ foi registado:

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![vestígios de dados](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Para obter mais informações sobre como utilizar o Azure Application Insights, consulte [o que é Insights de Aplicação?](../azure-monitor/app/app-insights-overview.md)

## <a name="web-service-metadata-and-response-data"></a>Metadados de serviço web e dados de resposta

> [!IMPORTANT]
> A azure Application Insights apenas regista cargas de até 64kb. Se este limite for atingido, poderá ver erros como fora da memória, ou nenhuma informação poderá ser registada.

Para registar informações de pedido de serviço web, adicione `print` declarações ao seu ficheiro score.py. Cada `print` declaração resulta numa única entrada na tabela de rastreios de insights de aplicação sob a mensagem `STDOUT` . A Application Insights armazena as `print` saídas de declaração  `customDimensions` dentro e na tabela de `Contents` rastreios. A impressão de cordas JSON produz uma estrutura hierárquica de dados na saída de vestígios sob `Contents` .

## <a name="export-data-for-retention-and-processing"></a>Dados de exportação para retenção e tratamento

>[!Important]
> A Azure Application Insights só suporta exportações para armazenamento de bolhas. Para obter mais informações sobre os limites desta implementação, consulte [a telemetria exporto da App Insights](../azure-monitor/app/export-telemetry.md#continuous-export-advanced-storage-configuration).

Utilize a [exportação contínua](../azure-monitor/app/export-telemetry.md) da Application Insights para exportar dados para uma conta de armazenamento de bolhas onde pode definir as definições de retenção. A Application Insights exporta os dados em formato JSON. 

:::image type="content" source="media/how-to-enable-app-insights/continuous-export-setup.png" alt-text="Exportação contínua":::

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a ativar a gravação e visualizar registos para pontos finais de serviço web. Experimente estes artigos para os próximos passos:


* [Como implementar um modelo num cluster AKS](./how-to-deploy-azure-kubernetes-service.md)

* [Como implementar um modelo para instâncias de contentores Azure](./how-to-deploy-azure-container-instance.md)

* [MLOps: Gerir, implementar e monitorizar modelos com a Azure Machine Learning](./concept-model-management-and-deployment.md) para aprender mais sobre a alavancagem de dados recolhidos a partir de modelos em produção. Estes dados podem ajudar a melhorar continuamente o seu processo de aprendizagem automática.