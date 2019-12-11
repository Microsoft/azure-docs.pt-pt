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
ms.openlocfilehash: f4bb399e24b2c480023eba6bf5b71990f6d39c8c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978378"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorar e coletar dados de pontos de extremidade de serviço Web do ML
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende a coletar dados do e a monitorar modelos implantados em pontos de extremidade de serviço Web no AKS (serviço de kubernetes do Azure) ou em ACI (instâncias de contêiner do Azure) habilitando informações de Aplicativo Azure. Além de coletar dados de entrada e resposta de um ponto de extremidade, você pode monitorar:

* Taxas de solicitação, tempos de resposta e taxas de falha
* Taxas de dependência, tempos de resposta e taxas de falha
* Exceções

[Saiba mais sobre o aplicativo Azure insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje

* Trabalho do Azure Machine Learning, um diretório local que contém os seus scripts e o SDK do Azure Machine Learning para Python instalada. Para saber como obter esses pré-requisitos, consulte [como configurar um ambiente de desenvolvimento](how-to-configure-environment.md)
* Um modelo de aprendizagem de máquina preparado para ser implementada para o Azure Kubernetes Service (AKS) ou instância de contentor do Azure (ACI). Se você não tiver um, consulte o tutorial [treinar modelo de classificação de imagem](tutorial-train-models-with-aml.md)

## <a name="web-service-input-and-response-data"></a>Dados de entrada e resposta do serviço Web

A entrada e a resposta para o serviço, que corresponde às entradas para o modelo ML e sua previsão, são registradas nos rastreamentos do Aplicativo Azure insights sob a mensagem `"model_data_collection"`. Você pode consultar Aplicativo Azure informações diretamente para acessar esses dados ou configurar uma [exportação contínua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) para uma conta de armazenamento para maior retenção ou processamento adicional. Os dados de modelo podem ser usados no Azure Machine Learning para configurar o rotulamento, o retreinamento, a explicação, a análise de dados ou outro uso. 

## <a name="use-the-azure-portal-to-configure"></a>Usar o portal do Azure para configurar

Você pode habilitar e desabilitar informações de Aplicativo Azure no portal do Azure. 

1. No [portal do Azure](https://portal.azure.com), abra seu espaço de trabalho

1. Na guia **implantações** , selecione o serviço no qual você deseja habilitar o aplicativo Azure insights

   [![Lista de serviços no separador de implementações](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Selecione **Editar**

   [![Botão Editar](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. Em **Configurações avançadas**, marque a caixa de seleção **habilitar diagnóstico de AppInsights**

   [![Caixa de verificação selecionada para ativar os diagnósticos](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Selecione **Atualizar** na parte inferior da tela para aplicar as alterações

### <a name="disable"></a>Desativar

1. No [portal do Azure](https://portal.azure.com), abra seu espaço de trabalho
1. Selecione **implantações**, selecione o serviço e, em seguida, selecione **Editar**

   [![Utilize o botão de edição](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. Em **Configurações avançadas**, desmarque a caixa de seleção **habilitar diagnóstico de AppInsights**

   [![Caixa de verificação desmarcada para ativar os diagnósticos](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Selecione **Atualizar** na parte inferior da tela para aplicar as alterações
 
## <a name="use-python-sdk-to-configure"></a>Usar o SDK do Python para configurar 

### <a name="update-a-deployed-service"></a>Atualizar um serviço implementado

1. Identifica o serviço na sua área de trabalho. O valor de `ws` é o nome do seu espaço de trabalho

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Atualize seu serviço e habilite insights Aplicativo Azure

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Rastreios personalizados no seu serviço

Se quiser personalizados rastreios de registo, siga o processo de implementação padrão para o AKS ou ACI no [como implementar e, em que](how-to-deploy-and-where.md) documento. Em seguida, utilize os seguintes passos:

1. Atualizar o arquivo de Pontuação adicionando instruções PRINT
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Atualizar a configuração do serviço
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Criar uma imagem e implantá-la em [AKs](how-to-deploy-to-aks.md) ou [ACI](how-to-deploy-to-aci.md)

### <a name="disable-tracking-in-python"></a>Desativar o controle no Python

Para desabilitar o Aplicativo Azure insights, use o seguinte código:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

## <a name="evaluate-data"></a>Avaliar dados
Os dados do serviço são armazenados em sua conta do Aplicativo Azure insights, dentro do mesmo grupo de recursos que Azure Machine Learning.
Para vê-la:

1. Vá para o espaço de trabalho Azure Machine Learning no [Azure Machine Learning Studio](https://ml.azure.com) e clique no link Application insights

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Selecione a guia **visão geral** para ver um conjunto básico de métricas para seu serviço

   [![Descrição geral](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Para examinar suas cargas de entrada e resposta do serviço Web, selecione **análise**
1. Na seção esquema, selecione **rastreamentos** e filtrar rastreamentos com a mensagem `"model_data_collection"`. Nas dimensões personalizadas, você pode ver as entradas, previsões e outros detalhes relevantes

   [![dados de modelo](media/how-to-enable-app-insights/model-data-trace.png)](./media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Para examinar seus rastreamentos personalizados, selecione **análise**
4. Na secção de esquema, selecione **rastreios**. Em seguida, selecione **executar** para executar a consulta. Os dados devem aparecer em um formato de tabela e devem ser mapeados para suas chamadas personalizadas em seu arquivo de Pontuação

   [![Rastreios personalizados](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Para saber mais sobre como usar o Aplicativo Azure insights, confira [o que é Application insights?](../../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Exportar dados para processamento adicional e retenção mais longa

Você pode usar a [exportação contínua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) do aplicativo Azure insights para enviar mensagens para uma conta de armazenamento com suporte, em que uma retenção mais longa pode ser definida. As mensagens de `"model_data_collection"` são armazenadas no formato JSON e podem ser facilmente analisadas para extrair dados de modelo. Azure Data Factory, pipelines de ML do Azure ou outras ferramentas de processamento de dados podem ser usadas para transformar os dados conforme necessário. Depois de ter transformado os dados, você poderá registrá-los com o espaço de trabalho Azure Machine Learning como um DataSet. Para fazer isso, consulte [como criar e registrar conjuntos de registros](how-to-create-register-datasets.md).

   [![exportação contínua](media/how-to-enable-app-insights/continuous-export-setup.png)](./media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Bloco de notas de exemplo

O bloco de anotações [Enable-app-Revisions-in-Production-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) demonstra conceitos neste artigo. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

* Consulte [como implantar um modelo em um cluster do serviço kubernetes do Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-kubernetes-service) ou [como implantar um modelo em instâncias de contêiner do Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-container-instance) para implantar seus modelos em pontos de extremidade de serviço Web e habilitar aplicativo Azure insights para aproveitar a coleta de dados e o monitoramento de ponto de extremidades
* Consulte [MLOps: gerenciar, implantar e monitorar modelos com Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-model-management-and-deployment) para saber mais sobre como aproveitar os dados coletados de modelos em produção. Esses dados podem ajudar a melhorar continuamente o processo de aprendizado de máquina
