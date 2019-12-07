---
title: Coletar dados em seus modelos de produção
titleSuffix: Azure Machine Learning
description: Saiba como coletar Azure Machine Learning dados de modelo de entrada no armazenamento de BLOBs do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 10a150a64a058a4bf346f07e2ef298c974641256
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901308"
---
# <a name="collect-data-for-models-in-production"></a>Recolher dados para modelos em produção

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> O SDK de monitoramento de Azure Machine Learning será desativado em breve. O SDK ainda é apropriado para os desenvolvedores que monitoram a descompasso de dados em modelos. Mas a maioria dos desenvolvedores deve usar o monitoramento de dados simplificado [com o Application insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights).

Este artigo mostra como coletar dados de modelo de entrada de Azure Machine Learning. Ele também mostra como implantar os dados de entrada em um cluster AKS (serviço kubernetes do Azure) e armazenar os dados de saída no armazenamento de BLOBs do Azure.

Quando a coleção estiver habilitada, os dados coletados ajudarão você a:

* [Monitore descompassos de dados](how-to-monitor-data-drift.md) como dados de produção insira seu modelo.

* Tome decisões melhores sobre quando treinar novamente ou otimizar seu modelo.

* Retreine seu modelo com os dados coletados.

## <a name="what-is-collected-and-where-it-goes"></a>O que é coletado e onde ele vai

Os seguintes dados podem ser recolhidos:

* Modelar dados de entrada de serviços Web implantados em um cluster AKS. Áudio de voz, imagens e vídeo *não* são coletados.
  
* Predições de modelos com dados de entrada de produção.

>[!NOTE]
> A preagregação e os precálculos desses dados não fazem parte do serviço de coleta no momento.

A saída é salva no armazenamento de BLOBs. Como os dados são adicionados ao armazenamento de BLOBs, você pode escolher sua ferramenta favorita para executar a análise.

O caminho para os dados de saída no blob segue esta sintaxe:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> Em versões do SDK do Azure Machine Learning para Python anteriores à versão 0.1.0 A16, o argumento `designation` é denominado `identifier`. Se você desenvolveu seu código com uma versão anterior, precisará atualizá-lo adequadamente.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.

- Um espaço de trabalho do AzureMachine Learning, um diretório local que contém seus scripts e o SDK do Azure Machine Learning para Python deve ser instalado. Para saber como instalá-los, consulte [como configurar um ambiente de desenvolvimento](how-to-configure-environment.md).

- Você precisa de um modelo de aprendizado de máquina treinado para ser implantado no AKS. Se você não tiver um modelo, consulte o tutorial [treinar modelo de classificação de imagem](tutorial-train-models-with-aml.md) .

- Você precisa de um cluster AKS. Para obter informações sobre como criar um e implantá-lo, consulte [como implantar e onde](how-to-deploy-and-where.md).

- [Configure seu ambiente](how-to-configure-environment.md) e instale o [SDK de monitoramento de Azure Machine Learning](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Ativar a recolha de dados

Você pode habilitar a coleta de dados independentemente do modelo implantado por meio de Azure Machine Learning ou outras ferramentas.

Para habilitar a coleta de dados, você precisa:

1. Abra o ficheiro de classificação.

1. Adicionar a [seguir código](https://aka.ms/aml-monitoring-sdk) na parte superior do ficheiro:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Declarar as variáveis de coleção de dados no seu `init` função:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* é um parâmetro opcional. Você não precisará usá-lo se seu modelo não exigir. O uso de *CorrelationId* o ajuda a mapear com mais facilidade com outros dados, como *LoanNumber* ou *CustomerID*.
    
    O parâmetro *Identifier* é usado posteriormente para criar a estrutura de pastas em seu BLOB. Você pode usá-lo para diferenciar dados brutos dos dados processados.

1. Adicione as seguintes linhas de código para o `run(input_df)` função:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. A coleta de dados *não* é definida automaticamente como **true** quando você implanta um serviço no AKs. Atualize seu arquivo de configuração, como no exemplo a seguir:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    Você também pode habilitar Application Insights para o monitoramento de serviço alterando essa configuração:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Para criar uma nova imagem e implantar o modelo de aprendizado de máquina, consulte [como implantar e onde](how-to-deploy-and-where.md).

Se você já tiver um serviço com as dependências instaladas em seu arquivo de ambiente e arquivo de pontuação, habilite a coleta de dados seguindo estas etapas:

1. Vá para [Azure Machine Learning](https://ml.azure.com).

1. Abra a área de trabalho.

1. Selecione **Implantações** > **selecione serviço** > **Editar**.

   ![Editar o serviço](media/how-to-enable-data-collection/EditService.PNG)

1. Em **Configurações avançadas**, selecione **habilitar coleta de dados de modelo**.

    [![selecionar coleta de dados](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   Você também pode selecionar **habilitar diagnóstico de AppInsights** para acompanhar a integridade do serviço.

1. Selecione **Atualizar** para aplicar as alterações.

## <a name="disable-data-collection"></a>Desativar a recolha de dados

Você pode interromper a coleta de dados a qualquer momento. Use o código Python ou Azure Machine Learning para desabilitar a coleta de dados.

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>Opção 1-desabilitar a coleta de dados no Azure Machine Learning

1. Entre no [Azure Machine Learning](https://ml.azure.com).

1. Abra a área de trabalho.

1. Selecione **Implantações** > **selecione serviço** > **Editar**.

   [![selecionar a opção Editar](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. Em **Configurações avançadas**, desmarque **habilitar coleta de dados de modelo**.

    [![desmarcar a caixa de seleção de coleta de dados](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

1. Selecione **atualização** para aplicar a alteração.

Você também pode acessar essas configurações em seu espaço de trabalho em [Azure Machine Learning](https://ml.azure.com).

### <a name="option-2---use-python-to-disable-data-collection"></a>Opção 2-usar o Python para desabilitar a coleta de dados

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Validar e analisar seus dados

Você pode escolher uma ferramenta de sua preferência para analisar os dados coletados no armazenamento de BLOBs.

### <a name="quickly-access-your-blob-data"></a>Acesse rapidamente seus dados de BLOB

1. Entre no [Azure Machine Learning](https://ml.azure.com).

1. Abra a área de trabalho.

1. Selecione **Armazenamento**.

    [![selecionar a opção de armazenamento](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Siga o caminho para os dados de saída do blob com esta sintaxe:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>Analisar dados de modelo usando Power BI

1. Baixe e abra [Power bi desktop](https://www.powerbi.com).

1. Selecione **obter dados** e selecione [**armazenamento de BLOBs do Azure**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![configuração de blob Power BI](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Adicionar o seu nome de conta de armazenamento e introduza a chave de armazenamento. Você pode encontrar essas informações selecionando **configurações** > **chaves de acesso** em seu BLOB.

1. Selecione o contêiner de **dados do modelo** e selecione **Editar**.

    [![Power BI Navigator](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. No editor de consultas, clique na coluna **nome** e adicione sua conta de armazenamento.

1. Insira o caminho do modelo no filtro. Se você quiser procurar apenas os arquivos de um ano ou mês específico, basta expandir o caminho do filtro. Por exemplo, para procurar apenas dados de março, use este caminho de filtro:

   /modeldata/\<SubscriptionId >/\<resourcegroupname >/\<espaços de trabalho >/\<WebServiceName >/\<ModelName >/\<modelversion >/\<designação >/\<ano >/3

1. Filtre os dados que são relevantes para você com base nos valores de **nome** . Se você armazenou previsões e entradas, precisará criar uma consulta para cada uma.

1. Selecione as setas duplas para baixo ao lado do título de coluna de **conteúdo** para combinar os arquivos.

    [![Power BI conteúdo](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Selecione **OK**. Os dados são pré-carregados.

    [![Power BI combinar arquivos](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Selecione **fechar e aplicar**.

1. Se você adicionou entradas e previsões, suas tabelas serão automaticamente ordenadas por valores **RequestId** .

1. Comece a criar relatórios personalizados nos seus dados de modelo.

### <a name="analyze-model-data-using-azure-databricks"></a>Analisar dados de modelo usando Azure Databricks

1. Crie um [espaço de trabalho Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

1. Aceda à sua área de trabalho do Databricks.

1. No espaço de trabalho do databricks, selecione **carregar dados**.

    [![selecionando a opção de dados de carregamento do databricks](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Selecione **criar nova tabela** e selecione **outras fontes de dados** > **armazenamento de BLOBs do Azure** > **criar tabela no bloco de anotações**.

    [criação de tabela do ![databricks](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Atualize o local dos seus dados. Segue-se um exemplo:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [instalação do ![databricks](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Siga as etapas no modelo para exibir e analisar seus dados.
