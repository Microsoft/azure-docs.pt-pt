---
title: Monitorize e recolha de dados dos pontos finais do serviço web machine learning
titleSuffix: Azure Machine Learning
description: Monitorize os serviços web implantados com O Machine Learning Azure utilizando insights de aplicação azure
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: peterlu
author: peterclu
ms.date: 11/12/2019
ms.openlocfilehash: 6498717f5d542be228483b9c323dbd8f3ca2ff00
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251928"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorizar e recolher dados de pontos finais do serviço web ml
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a recolher dados e monitorizar modelos implantados em pontos finais de serviço web no Serviço Azure Kubernetes (AKS) ou nas Instâncias de Contentores Azure (ACI), permitindo insights de aplicação do Azure. Além de recolher os dados e resposta de um ponto final, pode monitorizar:

* Taxas de pedido, tempos de resposta e taxas de insucesso
* Taxas de dependência, tempos de resposta e taxas de insucesso
* Exceções

[Saiba mais sobre os Insights de Aplicação Azure.](../azure-monitor/app/app-insights-overview.md) 


## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree)

* Trabalho do Azure Machine Learning, um diretório local que contém os seus scripts e o SDK do Azure Machine Learning para Python instalada. Para aprender como obter estes pré-requisitos, veja [como configurar um ambiente](how-to-configure-environment.md) de desenvolvimento
* Um modelo de aprendizagem de máquina preparado para ser implementada para o Azure Kubernetes Service (AKS) ou instância de contentor do Azure (ACI). Se não tiver um, consulte o tutorial do modelo de [classificação](tutorial-train-models-with-aml.md) de imagem do Comboio

## <a name="web-service-metadata-and-response-data"></a>Metadados de serviço web e dados de resposta

>[!Important]
> Azure Application Insights apenas regista cargas até 64kb. Se este limite for atingido, apenas as saídas mais recentes do modelo são registadas. 

Os metadados e a resposta ao serviço - correspondente aos metadados do serviço web e às previsões do modelo - estão registados nos vestígios de Insights de Aplicação Azure sob a mensagem `"model_data_collection"`. Pode consultar diretamente os Insights de Aplicação Azure para aceder a estes dados, ou criar uma [exportação contínua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) para uma conta de armazenamento para uma maior retenção ou processamento posterior. Os dados do modelo podem então ser utilizados no Azure Machine Learning para configurar a rotulagem, a reconversão, a explicabilidade, a análise de dados ou outra utilização. 

## <a name="use-the-azure-portal-to-configure"></a>Utilize o portal Azure para configurar

Pode ativar e desativar os Insights de Aplicação Azure no portal Azure. 

1. No [portal Azure,](https://portal.azure.com)abra o seu espaço de trabalho

1. No separador **Implementações,** selecione o serviço onde pretende ativar o Azure Application Insights

   [![Lista de serviços no separador Deployments](./media/how-to-enable-app-insights/Deployments.PNG)](././media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. **Selecione Editar**

   [botão ![Editar](././media/how-to-enable-app-insights/Edit.PNG)](./././media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. Em **Definições Avançadas,** selecione a caixa de verificação de **diagnósticos Enable AppInsights**

   [![caixa de verificação selecionada para permitir diagnósticos](./media/how-to-enable-app-insights/AdvancedSettings.png)](././media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Selecione **Atualizar** na parte inferior do ecrã para aplicar as alterações

### <a name="disable"></a>Desativar

1. No [portal Azure,](https://portal.azure.com)abra o seu espaço de trabalho
1. Selecione **Implementações,** selecione o serviço e, em seguida, **selecione Editar**

   [![Use o botão de edição](././media/how-to-enable-app-insights/Edit.PNG)](./././media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. Em **Definições Avançadas,** limpe a caixa de verificação de **diagnósticos Enable AppInsights**

   [![caixa de verificação limpa para permitir diagnósticos](./media/how-to-enable-app-insights/uncheck.png)](././media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Selecione **Atualizar** na parte inferior do ecrã para aplicar as alterações
 
## <a name="use-python-sdk-to-configure"></a>Use Python SDK para configurar 

### <a name="update-a-deployed-service"></a>Atualizar um serviço implementado

1. Identifica o serviço na sua área de trabalho. O valor para `ws` é o nome do seu espaço de trabalho

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Atualize o seu serviço e ative insights de aplicação do Azure

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Rastreios personalizados no seu serviço

Se pretender registar vestígios personalizados, siga o processo de implementação padrão de AKS ou ACI no [Como implementar e onde](how-to-deploy-and-where.md) documenta. Em seguida, utilize os seguintes passos:

1. Atualizar o ficheiro de pontuação adicionando declarações de impressão
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Atualizar a configuração do serviço
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Construa uma imagem e implemente-a em [AKS ou ACI](how-to-deploy-and-where.md).

### <a name="disable-tracking-in-python"></a>Desativar o controle no Python

Para desativar os Insights de Aplicação Azure, utilize o seguinte código:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

## <a name="evaluate-data"></a>Avaliar dados
Os dados do seu serviço são armazenados na sua conta Azure Application Insights, dentro do mesmo grupo de recursos que o Azure Machine Learning.
Para vê-la:

1. Vá ao seu espaço de trabalho Azure Machine Learning no [estúdio Azure Machine Learning](https://ml.azure.com) e clique no link Application Insights

    [![AppInsightsLoc](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Selecione o separador **Overview** para ver um conjunto básico de métricas para o seu serviço

   [visão geral ![](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. Para ver os metadados e resposta do seu serviço web, selecione a tabela **de pedidos** na secção **Registos (Analytics)** e selecione **Executar** para ver pedidos

   [dados do Modelo ![](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Para ver os seus vestígios personalizados, selecione **Analytics**
4. Na secção de esquemas, selecione **Traces**. Em seguida, selecione **Run** para executar a sua consulta. Os dados devem aparecer em formato de tabela e devem mapear as suas chamadas personalizadas no seu ficheiro de pontuação

   [![vestígios personalizados](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

Para saber mais sobre como usar insights de aplicação Azure, consulte [o que é a Aplicação Insights?](../azure-monitor/app/app-insights-overview.md)

## <a name="export-data-for-further-processing-and-longer-retention"></a>Dados de exportação para posterior tratamento e retenção mais longa

>[!Important]
> A Azure Application Insights apenas apoia as exportações para o armazenamento de bolhas. Os limites adicionais desta capacidade de exportação estão listados na [telemetria exportação da App Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

Pode utilizar a [exportação contínua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) da Azure Application Insights para enviar mensagens para uma conta de armazenamento apoiada, onde pode ser definida uma retenção mais longa. As mensagens `"model_data_collection"` são armazenadas em formato JSON e podem ser facilmente analisadas para extrair dados do modelo. 

A Azure Data Factory, os Pipelines Azure ML ou outras ferramentas de processamento de dados podem ser utilizadas para transformar os dados conforme necessário. Quando tiver transformado os dados, pode então registá-lo no espaço de trabalho azure machine learning como um conjunto de dados. Para tal, consulte [como criar e registar conjuntos](how-to-create-register-datasets.md)de dados .

   [![Exportação Contínua](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Bloco de notas de exemplo

O bloco de notas [enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) demonstra conceitos neste artigo. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

* Veja como implementar um modelo para um cluster de [serviço Saque Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) ou [como implementar um modelo para o Azure Container Instances](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) para implementar os seus modelos em pontos finais de serviço web e permitir que a Azure Application Insights aproveite a recolha de dados e a monitorização do ponto final
* Ver [MLOps: Gerir, implementar e monitorizar modelos com](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) o Azure Machine Learning para saber mais sobre alavancar os dados recolhidos a partir de modelos em produção. Estes dados podem ajudar a melhorar continuamente o seu processo de aprendizagem automática
