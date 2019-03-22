---
title: Modelos de monitor com o Application Insights
titleSuffix: Azure Machine Learning service
description: Utilizar o Application Insights para monitorizar os serviços web implementados através do serviço Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 1/07/2019
ms.custom: seodec18
ms.openlocfilehash: 32dc55927f614a91c390a417595b7a00c16e9386
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57847954"
---
# <a name="monitor-your-azure-machine-learning-models-with-application-insights"></a>Monitorizar os seus modelos do Azure Machine Learning com o Application Insights

Neste artigo, saiba como configurar o Azure Application Insights para o seu serviço Azure Machine Learning. O Application Insights dá-lhe a oportunidade para monitorizar:
* Pedido de taxas, tempos de resposta e taxas de falhas.
* Taxas de dependência, tempos de resposta e taxas de falhas.
* Exceções.

[Saiba mais sobre o Application Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Trabalho do Azure Machine Learning, um diretório local que contém os seus scripts e o SDK do Azure Machine Learning para Python instalada. Para saber como obter estes pré-requisitos, veja [como configurar um ambiente de desenvolvimento](how-to-configure-environment.md).
* Um modelo de aprendizagem de máquina preparado para ser implementada para o Azure Kubernetes Service (AKS) ou instância de contentor do Azure (ACI). Se não tiver uma, veja a [modelo de classificação de imagem Train](tutorial-train-models-with-aml.md) tutorial.


## <a name="enable-and-disable-from-the-sdk"></a>Ativar e desativar do SDK

### <a name="update-a-deployed-service"></a>Atualizar um serviço implementado
1. Identifica o serviço na sua área de trabalho. O valor para `ws` é o nome da sua área de trabalho.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Atualizar o seu serviço e ativar o Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Rastreios personalizados no seu serviço
Se quiser personalizados rastreios de registo, siga o processo de implementação padrão para o AKS ou ACI no [como implementar e, em que](how-to-deploy-and-where.md) documento. Em seguida, utilize os seguintes passos:

1. Atualize o ficheiro de classificação ao adicionar instruções de impressa.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Atualize a configuração do serviço.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Crie uma imagem e implementá-lo no [AKS](how-to-deploy-to-aks.md) ou [ACI](how-to-deploy-to-aci.md).  

### <a name="disable-tracking-in-python"></a>Desativar o controle no Python

Para desativar o Application Insights, utilize o seguinte código:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="enable-and-disable-in-the-portal"></a>Ativar e desativar no portal

Pode ativar e desativar o Application Insights no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com), abra a área de trabalho.

1. Sobre o **implementações** separador, selecione o serviço em que pretende ativar o Application Insights.

   [![Lista de serviços no separador de implementações](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Selecione **Editar**.

   [![Botão Editar](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. Na **definições avançadas**, selecione a **diagnóstico do AppInsights ativar** caixa de verificação.

   [![Caixa de verificação selecionada para ativar os diagnósticos](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Selecione **atualização** na parte inferior do ecrã, para aplicar as alterações. 

### <a name="disable"></a>Desativar
1. Na [portal do Azure](https://portal.azure.com), abra a área de trabalho.
1. Selecione **implementações**, selecione o serviço e selecione **editar**.

   [![Utilize o botão de edição](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. Na **definições avançadas**, desmarque a **diagnóstico do AppInsights ativar** caixa de verificação. 

   [![Caixa de verificação desmarcada para ativar os diagnósticos](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Selecione **atualização** na parte inferior do ecrã, para aplicar as alterações. 
 

## <a name="evaluate-data"></a>Avaliar dados
Dados do seu serviço são armazenados na sua conta do Application Insights, dentro do mesmo grupo de recursos que o seu serviço Azure Machine Learning.
Para vê-la:
1. Aceda à área de trabalho no serviço Machine Learning a [portal do Azure](https://portal.azure.com) e clique no link do Application Insights.

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Selecione o **descrição geral** guia para ver um conjunto básico de métricas para o seu serviço.

   [![Descrição geral](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Para examinar os rastreios personalizados, selecione **Analytics**.
4. Na secção de esquema, selecione **rastreios**. Em seguida, selecione **executar** para executar a consulta. Os dados devem aparecer num formato de tabela e devem ser mapeada para suas chamadas personalizadas no seu ficheiro de classificação. 

   [![Rastreios personalizados](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Para saber mais sobre como utilizar o Application Insights, veja [o que é o Application Insights?](../../azure-monitor/app/app-insights-overview.md).
    

## <a name="example-notebook"></a>Bloco de notas de exemplo

O [how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) bloco de notas demonstra conceitos deste artigo. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos Seguintes
Também é possível recolher dados em seus modelos em produção. Leia o artigo [recolher dados para modelos em produção](how-to-enable-data-collection.md). 


## <a name="other-references"></a>Outras referências
* [Monitor do Azure para contentores](https://docs.microsoft.com/azure/monitoring/monitoring-container-insights-overview?toc=%2fazure%2fmonitoring%2ftoc.json)
