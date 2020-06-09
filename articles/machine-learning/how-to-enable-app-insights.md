---
title: Monitorize e recolha dados dos pontos finais do serviço web Machine Learning
titleSuffix: Azure Machine Learning
description: Monitorize serviços web implementados com Azure Machine Learning usando Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 03/12/2020
ms.custom: tracking-python
ms.openlocfilehash: 2473d864e0ad0fca4a886a6135a9caac0742e3d7
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84557078"
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

>[!Important]
> A azure Application Insights apenas regista cargas de até 64kb. Se este limite for atingido, apenas as saídas mais recentes do modelo são registadas. 

Os metadados e a resposta ao serviço - correspondente aos metadados do serviço web e às previsões do modelo - são registados nos vestígios de Azure Application Insights sob a mensagem `"model_data_collection"` . Pode consultar o Azure Application Insights diretamente para aceder a estes dados, ou configurar uma [exportação contínua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) para uma conta de armazenamento para uma retenção mais longa ou processamento posterior. Os dados do modelo podem então ser utilizados na Aprendizagem automática Azure para configurar a rotulagem, a reconversão, a explicabilidade, a análise de dados ou outra utilização. 

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

1. Atualizar o ficheiro de pontuação adicionando declarações de impressão
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
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
## <a name="evaluate-data"></a>Avaliar dados
Os dados do seu serviço são armazenados na sua conta Azure Application Insights, dentro do mesmo grupo de recursos que o Azure Machine Learning.
Para vê-lo:

1. Vá ao seu espaço de trabalho de aprendizagem de máquinas Azure no [portal Azure](https://ms.portal.azure.com/) e clique no link Application Insights

    [![AppInsightsLoc](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Selecione o **separador Visão Geral** para ver um conjunto básico de métricas para o seu serviço

   [![Descrição geral](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. Para ver o seu serviço web solicitar metadados e resposta, selecione a tabela de **pedidos** na secção **Registos (Analytics)** e selecione **Executar** para visualizar pedidos

   [![Modelar dados](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Para ver os seus vestígios personalizados, selecione **Analytics**
4. Na secção de esquemas, selecione **Traces**. Em seguida, **selecione Executar** para executar a sua consulta. Os dados devem aparecer num formato de tabela e devem mapear as suas chamadas personalizadas no seu ficheiro de pontuação

   [![Vestígios personalizados](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

Para saber mais sobre como usar o Azure Application Insights, veja [o que é Insights de Aplicação?](../azure-monitor/app/app-insights-overview.md)

## <a name="export-data-for-further-processing-and-longer-retention"></a>Dados de exportação para posterior tratamento e retenção mais longa

>[!Important]
> A Azure Application Insights só suporta exportações para armazenamento de bolhas. Os limites adicionais desta capacidade de exportação estão listados na [telemetria exporto da App Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

Pode utilizar a [exportação contínua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) da Azure Application Insights para enviar mensagens para uma conta de armazenamento suportada, onde pode ser definida uma retenção mais longa. As `"model_data_collection"` mensagens são armazenadas em formato JSON e podem ser facilmente analisadas para extrair dados do modelo. 

Azure Data Factory, Azure ML Pipelines ou outras ferramentas de processamento de dados podem ser usadas para transformar os dados conforme necessário. Quando tiver transformado os dados, pode registá-lo com o espaço de trabalho Azure Machine Learning como conjunto de dados. Para tal, consulte [Como criar e registar conjuntos de dados](how-to-create-register-datasets.md).

   [![Exportação Contínua](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Caderno de exemplo

O notebook [enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) demonstra conceitos neste artigo. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

* Veja [como implementar um modelo num cluster de serviços Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) ou como implementar um modelo para [Azure Container Instances](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) para implementar os seus modelos em pontos finais de serviço web, e permitir que o Azure Application Insights aproveite a recolha de dados e a monitorização do ponto final
* Consulte [MLOps: Gerir, implementar e monitorizar modelos com Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) para saber mais sobre a alavancagem de dados recolhidos a partir de modelos em produção. Estes dados podem ajudar a melhorar continuamente o seu processo de aprendizagem automática
