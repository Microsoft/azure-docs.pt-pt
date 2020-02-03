---
title: Monitorar e coletar dados de Machine Learning pontos de extremidade de serviço Web
titleSuffix: Azure Machine Learning
description: Monitorar serviços Web implantados com Azure Machine Learning usando o Aplicativo Azure insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: 68c7f3082adbf10ee6f5b5e6b6fd0bf79232a7d0
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722429"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorar e coletar dados de pontos de extremidade de serviço Web do ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende a coletar dados do e a monitorar modelos implantados em pontos de extremidade de serviço Web no AKS (serviço de kubernetes do Azure) ou em ACI (instâncias de contêiner do Azure) habilitando informações de Aplicativo Azure. Além de coletar dados de entrada e resposta de um ponto de extremidade, você pode monitorar:

* Taxas de pedido, tempos de resposta e taxas de insucesso
* Taxas de dependência, tempos de resposta e taxas de insucesso
* Exceções

[Saiba mais sobre os Insights de Aplicação Azure.](../azure-monitor/app/app-insights-overview.md) 


## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree)

* Trabalho do Azure Machine Learning, um diretório local que contém os seus scripts e o SDK do Azure Machine Learning para Python instalada. Para aprender como obter estes pré-requisitos, veja [como configurar um ambiente](how-to-configure-environment.md) de desenvolvimento
* Um modelo de aprendizagem de máquina preparado para ser implementada para o Azure Kubernetes Service (AKS) ou instância de contentor do Azure (ACI). Se não tiver um, consulte o tutorial do modelo de [classificação](tutorial-train-models-with-aml.md) de imagem do Comboio

## <a name="web-service-metadata-and-response-data"></a>Metadados do serviço Web e dados de resposta

>[!Important]
> Azure Application Insights apenas regista cargas até 64kb. Se esse limite for atingido, somente as saídas mais recentes do modelo serão registradas em log. 

Os metadados e a resposta ao serviço - correspondente aos metadados do serviço web e às previsões do modelo - estão registados nos vestígios de Insights de Aplicação Azure sob a mensagem `"model_data_collection"`. Pode consultar diretamente os Insights de Aplicação Azure para aceder a estes dados, ou criar uma [exportação contínua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) para uma conta de armazenamento para uma maior retenção ou processamento posterior. Os dados de modelo podem ser usados no Azure Machine Learning para configurar o rotulamento, o novo treinamento, a explicação, a análise de dados ou outro uso. 

## <a name="use-the-azure-portal-to-configure"></a>Usar o portal do Azure para configurar

Você pode habilitar e desabilitar informações de Aplicativo Azure no portal do Azure. 

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
 
## <a name="use-python-sdk-to-configure"></a>Usar o SDK do Python para configurar 

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

Para desabilitar o Aplicativo Azure insights, use o seguinte código:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

## <a name="evaluate-data"></a>Avaliar dados
Os dados do serviço são armazenados em sua conta do Aplicativo Azure insights, dentro do mesmo grupo de recursos que Azure Machine Learning.
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

## <a name="export-data-for-further-processing-and-longer-retention"></a>Exportar dados para processamento adicional e retenção mais longa

>[!Important]
> Aplicativo Azure insights só dá suporte a exportações para o armazenamento de BLOBs. Os limites adicionais desta capacidade de exportação estão listados na [telemetria exportação da App Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

Pode utilizar a [exportação contínua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) da Azure Application Insights para enviar mensagens para uma conta de armazenamento apoiada, onde pode ser definida uma retenção mais longa. As mensagens `"model_data_collection"` são armazenadas em formato JSON e podem ser facilmente analisadas para extrair dados do modelo. 

Azure Data Factory, pipelines de ML do Azure ou outras ferramentas de processamento de dados podem ser usadas para transformar os dados conforme necessário. Quando tiver transformado os dados, pode então registá-lo no espaço de trabalho azure machine learning como um conjunto de dados. Para tal, consulte [como criar e registar conjuntos](how-to-create-register-datasets.md)de dados .

   [![Exportação Contínua](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Bloco de notas de exemplo

O bloco de notas [enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) demonstra conceitos neste artigo. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos Seguintes

* Veja como implementar um modelo para um cluster de [serviço Saque Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) ou [como implementar um modelo para o Azure Container Instances](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) para implementar os seus modelos em pontos finais de serviço web e permitir que a Azure Application Insights aproveite a recolha de dados e a monitorização do ponto final
* Ver [MLOps: Gerir, implementar e monitorizar modelos com](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) o Azure Machine Learning para saber mais sobre alavancar os dados recolhidos a partir de modelos em produção. Estes dados podem ajudar a melhorar continuamente o seu processo de aprendizagem automática
