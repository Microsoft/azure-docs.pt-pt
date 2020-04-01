---
title: Recolher dados sobre os seus modelos de produção
titleSuffix: Azure Machine Learning
description: Saiba como recolher dados do modelo de entrada Azure Machine Learning no armazenamento de Azure Blob.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 44acc81df9eb6dc6a6af28b5b0f4730aa93adffc
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475440"
---
# <a name="collect-data-for-models-in-production"></a>Recolher dados para modelos em produção

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> O SDK de Monitorização de Aprendizagem automática Azure será retirado em breve. O SDK ainda é apropriado para os desenvolvedores que atualmente utilizam o SDK para monitorizar a deriva de dados em modelos. Mas para novos clientes, recomendamos a utilização da monitorização simplificada de dados com insights de [aplicação](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights).

Este artigo mostra como recolher dados do modelo de entrada da Azure Machine Learning. Também mostra como implantar os dados de entrada num cluster do Serviço Azure Kubernetes (AKS) e armazenar os dados de saída no armazenamento do Azure Blob.

Uma vez que a recolha é ativada, os dados que recolhe ajudam-no:

* [Monitorize](how-to-monitor-data-drift.md) as derivas de dados à medida que os dados de produção entram no seu modelo.

* Tome melhores decisões sobre quando retreinar ou otimizar o seu modelo.

* Retrete o seu modelo com os dados recolhidos.

## <a name="what-is-collected-and-where-it-goes"></a>O que é recolhido e para onde vai

Podem ser recolhidos os seguintes dados:

* Dados de entrada de modelos de serviços web implantados num cluster AKS. Áudio de voz, imagens e vídeo *não* são recolhidos.
  
* Previsões de modelo utilizando dados de entrada de produção.

>[!NOTE]
> A preagregação e os pré-cálculos destes dados não fazem atualmente parte do serviço de recolha.

A saída é guardada no armazenamento blob. Como os dados são adicionados ao armazenamento blob, você pode escolher a sua ferramenta favorita para executar a análise.

O caminho para os dados de saída na bolha segue esta sintaxe:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> Nas versões do Azure Machine Learning SDK para Python mais cedo do `designation` que `identifier`a versão 0.1.0a16, o argumento é nomeado . Se desenvolveu o seu código com uma versão anterior, precisa atualizá-lo em conformidade.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.

- Um espaço de trabalho AzureMachine Learning, um diretório local que contenha os seus scripts, e o Azure Machine Learning SDK para Python devem ser instalados. Para aprender a instalá-los, consulte [como configurar um ambiente](how-to-configure-environment.md)de desenvolvimento.

- Precisa de um modelo de aprendizagem automática treinado para ser implantado na AKS. Se não tiver um modelo, consulte o tutorial do modelo de classificação de [imagem do Comboio.](tutorial-train-models-with-aml.md)

- Precisas de um aglomerado aks. Para obter informações sobre como criar um e implantá-lo, veja [como implementar e onde](how-to-deploy-and-where.md).

- [Instale o seu ambiente](how-to-configure-environment.md) e instale o SDK de Monitorização de [Aprendizagem automática Azure](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Ativar a recolha de dados

Pode ativar a recolha de dados independentemente do modelo que implementa através do Azure Machine Learning ou de outras ferramentas.

Para permitir a recolha de dados, é necessário:

1. Abra o arquivo de pontuação.

1. Adicione o [seguinte código](https://aka.ms/aml-monitoring-sdk) na parte superior do ficheiro:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Declare as suas variáveis `init` de recolha de dados na sua função:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* é um parâmetro opcional. Não precisas de usá-lo se o teu modelo não o exigir. A utilização do *CorrelationId* ajuda-o a mapear mais facilmente com outros dados, tais como *LoanNumber* ou *CustomerId*.
    
    O parâmetro *identificador* é mais tarde utilizado para a construção da estrutura da pasta na sua bolha. Pode usá-lo para diferenciar dados brutos de dados processados.

1. Adicione as seguintes linhas `run(input_df)` de código à função:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. A recolha de dados *não* é automaticamente definida para **ser verdadeira** quando implementa um serviço no AKS. Atualize o seu ficheiro de configuração, como no seguinte exemplo:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    Também pode ativar os Insights de Aplicação para monitorização do serviço alterando esta configuração:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Para criar uma nova imagem e implementar o modelo de aprendizagem automática, veja [como implementar e onde.](how-to-deploy-and-where.md)

Se já tiver um serviço com as dependências instaladas no seu ficheiro ambiente e ficheiro de pontuação, ative a recolha de dados seguindo estes passos:

1. Ir ao [Azure Machine Learning.](https://ml.azure.com)

1. Abra o seu espaço de trabalho.

1. Selecione **Implementações** > **Selecione o serviço** > **Editar**.

   ![Editar o serviço](././media/how-to-enable-data-collection/EditService.PNG)

1. Em **Definições Avançadas,** selecione **Ativar insights de diagnóstico e recolha**de dados.

1. Selecione **Atualizar** para aplicar as alterações.

## <a name="disable-data-collection"></a>Desativar a recolha de dados

Pode parar de recolher dados a qualquer momento. Utilize o código Python ou o Azure Machine Learning para desativar a recolha de dados.

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>Opção 1 - Desativar a recolha de dados em Aprendizagem automática azure

1. Inscreva-se na [Aprendizagem automática azure.](https://ml.azure.com)

1. Abra o seu espaço de trabalho.

1. Selecione **Implementações** > **Selecione o serviço** > **Editar**.

   [![Selecione a opção Editar](././media/how-to-enable-data-collection/EditService.PNG)](./././media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. Em **Definições Avançadas,** claro **enable Insights diagnósticos e recolha**de dados .

1. Selecione **Atualizar** para aplicar a alteração.

Também pode aceder a estas definições no seu espaço de trabalho em [Azure Machine Learning](https://ml.azure.com).

### <a name="option-2---use-python-to-disable-data-collection"></a>Opção 2 - Utilizar python para desativar a recolha de dados

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Validar e analisar os seus dados

Pode escolher uma ferramenta da sua preferência para analisar os dados recolhidos no seu armazenamento Blob.

### <a name="quickly-access-your-blob-data"></a>Aceda rapidamente aos seus dados blob

1. Inscreva-se na [Aprendizagem automática azure.](https://ml.azure.com)

1. Abra o seu espaço de trabalho.

1. Selecione **Armazenamento**.

    [![Selecione a opção Armazenamento](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Siga o caminho para os dados de saída da bolha com esta sintaxe:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>Analisar dados do modelo usando O Power BI

1. Descarregue e abra [o Power BI Desktop](https://www.powerbi.com).

1. Selecione **Obter Dados** e selecione [**Armazenamento De Blob Azure**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Configuração de blob Power BI](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Adicione o nome da sua conta de armazenamento e introduza a sua chave de armazenamento. Pode encontrar esta informação selecionando**as teclas de acesso** de **Definições** > na sua bolha.

1. Selecione o recipiente de dados do **modelo** e selecione **Editar**.

    [![Power BI Navigator](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. No editor de consulta, clique na coluna **Nome** e adicione a sua conta de armazenamento.

1. Introduza o seu caminho modelo no filtro. Se quiser ver apenas ficheiros de um ano ou mês específicos, basta expandir o caminho do filtro. Por exemplo, para analisar apenas os dados de março, utilize este caminho de filtro:

   /modeldata/\<>\<subscrição/ nome\<de grupo de\<recursos>/\<nome do\<espaço de\<trabalho\<>/ nome de serviço web>/ nome de modelo>/ modeloversão>/ designação>/ano>/3

1. Filtre os dados que lhe são relevantes com base nos valores do **Nome.** Se armazenasse previsões e inputs, teria de criar uma consulta para cada um.

1. Selecione as setas duplas para baixo junto à direção da coluna **Content** para combinar os ficheiros.

    [![Conteúdo bi de potência](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Selecione **OK**. Os dados precarregam.

    [![Power BI Combine Ficheiros](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Selecione **Fechar e Aplicar**.

1. Se adicionou inputs e previsões, as suas tabelas são automaticamente encomendadas pelos valores **do RequestId.**

1. Comece a construir os seus relatórios personalizados sobre os seus dados de modelo.

### <a name="analyze-model-data-using-azure-databricks"></a>Analisar dados do modelo usando tijolos de dados azure

1. Criar um espaço de [trabalho Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

1. Vá ao seu espaço de trabalho databricks.

1. No espaço de trabalho dos Databricks, selecione **Dados de Upload**.

    [![Selecionar a opção data upload de datas de datades de data](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Selecione **Criar uma nova tabela** e selecione **Outras fontes** > de dados**Azure Blob Storage** > **Create Table in Notebook**.

    [![Criação de tabela de tijolos de dados](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Atualize a localização dos seus dados. Segue-se um exemplo:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Configuração de tijolos de dados](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Siga os passos no modelo para ver e analisar os seus dados.
