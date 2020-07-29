---
title: Monitorize e recolha dados dos pontos finais do serviço web Machine Learning
titleSuffix: Azure Machine Learning
description: Monitorize serviços web implementados com Azure Machine Learning usando Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: 2bc3eb72ff0c5d29fd72de848abf87dfe84e2a01
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320226"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorize e recolha dados dos pontos finais do serviço web ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a recolher dados e monitorizar modelos implementados para os pontos finais do serviço web em Azure Kubernetes Service (AKS) ou Azure Container Instances (ACI), permitindo insights de aplicação do Azure através de Azure Application Insights via 
* [Azure Máquina aprendendo Python SDK](#python)
* [Azure Machine Learning estúdio](#studio) emhttps://ml.azure.com

Além de recolher os dados de saída e resposta de um ponto final, pode monitorizar:

* Taxas de pedido, tempos de resposta e taxas de insucesso
* Taxas de dependência, tempos de resposta e taxas de insucesso
* Exceções

[Saiba mais sobre a Azure Application Insights](../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree)

* Um espaço de trabalho de aprendizagem automática Azure, um diretório local que contém os seus scripts, e o Azure Machine Learning SDK para Python instalados. Para aprender a obter estes pré-requisitos, veja [como configurar um ambiente de desenvolvimento](how-to-configure-environment.md)

* Um modelo de aprendizagem automática treinado para ser implantado no Serviço Azure Kubernetes (AKS) ou na Instância de Contentores Azure (ACI). Se não tiver um, consulte o tutorial do [modelo de classificação de imagem do comboio](tutorial-train-models-with-aml.md)

## <a name="web-service-metadata-and-response-data"></a>Metadados de serviço web e dados de resposta

> [!IMPORTANT]
> A azure Application Insights apenas regista cargas de até 64kb. Se este limite for atingido, poderá ver erros como fora da memória, ou nenhuma informação poderá ser registada.

Para registar informações para um pedido ao serviço web, adicione `print` declarações ao seu ficheiro score.py. Cada `print` declaração resulta numa única entrada na tabela de rastreios em Insights de Aplicação, sob a mensagem `STDOUT` . O conteúdo da `print` declaração será contido na tabela de `customDimensions` `Contents` rastreios. Se imprimir uma corda JSON, produz uma estrutura de dados hierárquica na saída de vestígios sob `Contents` .

Pode consultar o Azure Application Insights diretamente para aceder a estes dados, ou configurar uma [exportação contínua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) para uma conta de armazenamento para uma retenção mais longa ou processamento posterior. Os dados do modelo podem então ser utilizados na Aprendizagem automática Azure para configurar a rotulagem, a reconversão, a explicabilidade, a análise de dados ou outra utilização. 

<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>Use Python SDK para configurar 

### <a name="update-a-deployed-service"></a>Atualizar um serviço implantado

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

Se pretender registar vestígios personalizados, siga o processo de implementação padrão para AKS ou ACI no [Como implementar e onde](how-to-deploy-and-where.md) documentar. Em seguida, utilize os seguintes passos:

1. Para enviar dados para o Application Insights durante a inferência, atualize o ficheiro de pontuação adicionando declarações de impressão. Para registar informações mais complexas, como os dados do pedido e a resposta, nós uma estrutura JSON. O exemplo seguinte score.py registos de ficheiros no momento em que o modelo é inicializado, a entrada e saída durante a inferência, e o tempo em que ocorrerem erros:

    > [!IMPORTANT]
    > A azure Application Insights apenas regista cargas de até 64kb. Se este limite for atingido, poderá ver erros como fora da memória, ou nenhuma informação poderá ser registada. Se os dados que pretende registar forem maiores de 64kb, deverá armazená-los para armazenar o armazenamento de bolhas utilizando as informações em [Recolha de Dados para modelos em produção](how-to-enable-data-collection.md).
    
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

2. Atualizar a configuração do serviço
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Construa uma imagem e implemente-a em [AKS ou ACI](how-to-deploy-and-where.md).

### <a name="disable-tracking-in-python"></a>Desativar o rastreio em Python

Para desativar os Insights de Aplicação Azure, utilize o seguinte código:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>Use o estúdio Azure Machine Learning para configurar

Também pode ativar o Azure Application Insights do estúdio Azure Machine Learning quando estiver pronto para implementar o seu modelo com estes passos.

1. Inscreva-se no seu espaço de trabalho emhttps://ml.azure.com/
1. Vá a **Modelos** e selecione qual o modelo que pretende implementar
1. Selecione **+Implementar**
1. Povoar a forma **do modelo implementar**
1. Expandir o menu **Avançado**

    ![Formulário de implantação](./media/how-to-enable-app-insights/deploy-form.png)
1. Selecione **Enable Application Insights diagnósticos e recolha de dados**

    ![Ativar insights de aplicativos](./media/how-to-enable-app-insights/enable-app-insights.png)

## <a name="view-metrics-and-logs"></a>Ver métricas e registos

Os dados do seu serviço são armazenados na sua conta Azure Application Insights, dentro do mesmo grupo de recursos que o Azure Machine Learning.
Para vê-lo:

1. Vá ao seu espaço de trabalho Azure Machine Learning no [estúdio.](https://ml.azure.com/)
1. Selecione **Pontos de final**.
1. Selecione o seu serviço implantado.
1. Desloque-se para baixo para encontrar o **url 'Insights de Aplicação'** e clique no link.

    [![Localizar insights de aplicação url](./media/how-to-enable-app-insights/appinsightsloc.png)](././media/how-to-enable-app-insights/appinsightsloc.png#lightbox)

1. Nas Isights de Aplicação, a partir do **separador Visão Geral** ou na secção __de Monitorização__ na lista à esquerda, selecione __Registos__.

    [![Separador geral da monitorização](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Para ver as informações registadas no ficheiro score.py, consulte a tabela __de vestígios.__ As seguintes pesquisas de consulta para registos onde o valor de __entrada__ foi registado:

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![vestígios de dados](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Para saber mais sobre como usar o Azure Application Insights, veja [o que é Insights de Aplicação?](../azure-monitor/app/app-insights-overview.md)

## <a name="export-data-for-further-processing-and-longer-retention"></a>Dados de exportação para posterior tratamento e retenção mais longa

>[!Important]
> A Azure Application Insights só suporta exportações para armazenamento de bolhas. Os limites adicionais desta capacidade de exportação estão listados na [telemetria exporto da App Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

Pode utilizar a [exportação contínua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) da Azure Application Insights para enviar mensagens para uma conta de armazenamento suportada, onde pode ser definida uma retenção mais longa. Os dados são armazenados em formato JSON e podem ser facilmente analisados para extrair dados do modelo. 

Azure Data Factory, Azure ML Pipelines ou outras ferramentas de processamento de dados podem ser usadas para transformar os dados conforme necessário. Quando tiver transformado os dados, pode registá-lo com o espaço de trabalho Azure Machine Learning como conjunto de dados. Para tal, consulte [Como criar e registar conjuntos de dados](how-to-create-register-datasets.md).

:::image type="content" source="media/how-to-enable-app-insights/continuous-export-setup.png" alt-text="Exportação contínua":::


## <a name="example-notebook"></a>Bloco de notas de exemplo

O notebook [enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) demonstra conceitos neste artigo. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

* Veja [como implementar um modelo num cluster de serviços Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) ou como implementar um modelo para [Azure Container Instances](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) para implementar os seus modelos em pontos finais de serviço web, e permitir que o Azure Application Insights aproveite a recolha de dados e a monitorização do ponto final
* Consulte [MLOps: Gerir, implementar e monitorizar modelos com Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) para saber mais sobre a alavancagem de dados recolhidos a partir de modelos em produção. Estes dados podem ajudar a melhorar continuamente o seu processo de aprendizagem automática
