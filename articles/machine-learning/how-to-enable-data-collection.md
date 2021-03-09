---
title: Recolher dados sobre os seus modelos de produção
titleSuffix: Azure Machine Learning
description: Saiba como recolher dados de um modelo Azure Machine Learning implantado num cluster Azure Kubernetes Service (AKS).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 07/14/2020
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.openlocfilehash: f42a2f9e606eaa0475f2e35c479e99545ecf8193
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521905"
---
# <a name="collect-data-from-models-in-production"></a>Recolher dados de modelos em produção

Este artigo mostra como recolher dados de um modelo de Aprendizagem automática Azure implantado num cluster Azure Kubernetes Service (AKS). Os dados recolhidos são então armazenados no armazenamento da Azure Blob.

Uma vez ativada a recolha, os dados que recolhe ajudam-no:

* [Monitorize as derivas de dados](how-to-monitor-datasets.md) nos dados de produção que recolhe.

* Analisar dados recolhidos usando [Power BI](#powerbi) ou [Azure Databricks](#databricks)

* Tome melhores decisões sobre quando reconverter ou otimizar o seu modelo.

* Retreine o seu modelo com os dados recolhidos.

## <a name="what-is-collected-and-where-it-goes"></a>O que é recolhido e para onde vai

Os seguintes dados podem ser recolhidos:

* Modelo de dados de entrada de serviços web implantados num cluster AKS. Áudio de voz, imagens e vídeo *não* são recolhidos.
  
* Previsões de modelos usando dados de entrada de produção.

>[!NOTE]
> A pré-agregação e os pré-cálculos destes dados não fazem atualmente parte do serviço de recolha.

A saída é guardada no armazenamento blob. Como os dados são adicionados ao armazenamento blob, pode escolher a sua ferramenta preferida para executar a análise.

O caminho para os dados de saída na bolha segue esta sintaxe:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> Nas versões do Azure Machine Learning SDK para Python mais cedo do que a versão 0.1.0a16, o `designation` argumento é nomeado `identifier` . Se desenvolveu o seu código com uma versão anterior, tem de o atualizar em conformidade.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.

- Um espaço de trabalho de aprendizagem automática Azure, um diretório local que contém os seus scripts, e o Azure Machine Learning SDK para Python devem ser instalados. Para aprender a instalá-los, veja [como configurar um ambiente de desenvolvimento.](how-to-configure-environment.md)

- Precisa de um modelo de aprendizagem de máquinas treinado para ser implantado na AKS. Se não tiver um modelo, consulte o tutorial do [modelo de classificação de imagem train.](tutorial-train-models-with-aml.md)

- Precisas de um aglomerado AKS. Para obter informações sobre como criar um e implementar nele, consulte [como implementar e onde](how-to-deploy-and-where.md).

- [Confende o seu ambiente](how-to-configure-environment.md) e instale o [Azure Machine Learning Monitoring SDK](/python/api/overview/azure/ml/install).

## <a name="enable-data-collection"></a>Ativar a recolha de dados

Pode ativar a recolha de [dados](/python/api/azureml-monitoring/azureml.monitoring.modeldatacollector.modeldatacollector) independentemente do modelo que implementa através do Azure Machine Learning ou de outras ferramentas.

Para ativar a recolha de dados, é necessário:

1. Abra o ficheiro de pontuação.

1. Adicione o seguinte código na parte superior do ficheiro:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Declare as suas variáveis de recolha de dados na sua `init` função:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3", "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* é um parâmetro opcional. Não precisas de usá-lo se o teu modelo não o exigir. A utilização do *CorrelationId* ajuda-o a mapear mais facilmente outros dados, tais como *LoanNumber* ou *CustomerId.*
    
    O parâmetro *Identifier* é mais tarde utilizado para a construção da estrutura da pasta na sua bolha. Pode usá-lo para diferenciar dados brutos de dados processados.

1. Adicione as seguintes linhas de código à `run(input_df)` função:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. A recolha de dados *não* é automaticamente definida como **verdadeira** quando implementa um serviço em AKS. Atualize o seu ficheiro de configuração, como no seguinte exemplo:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    Também pode ativar insights de aplicação para monitorização de serviços alterando esta configuração:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Para criar uma nova imagem e implementar o modelo de aprendizagem automática, consulte [como implementar e onde](how-to-deploy-and-where.md).

1. Adicione o pacote de pip "Azure-Monitoring" às dependências conda do ambiente de serviço web:
  ```Python
    env = Environment('webserviceenv')
    env.python.conda_dependencies = CondaDependencies.create(conda_packages=['numpy'],pip_packages=['azureml-defaults','azureml-monitoring','inference-schema[numpy-support]'])
  ```


## <a name="disable-data-collection"></a>Desativar a recolha de dados

Pode parar de recolher dados a qualquer momento. Utilize o código Python para desativar a recolha de dados.

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Validar e analisar os seus dados

Pode escolher uma ferramenta da sua preferência para analisar os dados recolhidos no seu armazenamento Blob.

### <a name="quickly-access-your-blob-data"></a>Aceda rapidamente aos seus dados blob

1. Inscreva-se no [portal Azure](https://portal.azure.com).

1. Abra o seu espaço de trabalho.

1. Selecione **Armazenamento**.

    [![Selecione a opção Armazenamento](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Siga o caminho para os dados de saída do blob com esta sintaxe:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a><a id="powerbi"></a> Analisar dados de modelos usando Power BI

1. Descarregue e abra [o Power BI Desktop](https://www.powerbi.com).

1. Selecione **Obter Dados** e selecione [**Azure Blob Storage**](/power-bi/desktop-data-sources).

    [![Configuração do blob power BI](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Adicione o nome da sua conta de armazenamento e introduza a sua chave de armazenamento. Pode encontrar estas informações selecionando **as**  >  **teclas de acesso** de definições na sua bolha.

1. Selecione o recipiente **de dados do modelo** e selecione **Editar.**

    [![Navegador Bi de potência](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. No editor de consulta, clique na coluna **Nome** e adicione a sua conta de armazenamento.

1. Introduza o seu caminho de modelo no filtro. Se quiser procurar apenas ficheiros a partir de um ano ou mês específico, basta expandir o caminho do filtro. Por exemplo, para analisar apenas os dados de março, utilize este caminho de filtro:

   /modeldata/ \<subscriptionid> / \<resourcegroupname> / \<workspacename> / \<webservicename> / \<modelname> / \<modelversion> / \<designation> / \<year> /3

1. Filtrar os dados que lhe são relevantes com base nos valores **de Nome.** Se armazenar previsões e entradas, tem de criar uma consulta para cada um.

1. Selecione as setas duplas para baixo ao lado da coluna **Content** para combinar os ficheiros.

    [![Conteúdo de Power BI](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Selecione **OK**. Os dados são pré-carregados.

    [![Ficheiros de combinação de bis de energia](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Selecione **Fechar e Aplicar**.

1. Se adicionar entradas e previsões, as suas tabelas são automaticamente encomendadas pelos valores **RequestId.**

1. Comece a construir os seus relatórios personalizados sobre os seus dados de modelo.

### <a name="analyze-model-data-using-azure-databricks"></a><a id="databricks"></a> Analisar dados de modelos usando Azure Databricks

1. Criar um [espaço de trabalho Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).

1. Vá ao seu espaço de trabalho databricks.

1. No seu espaço de trabalho Databricks, selecione **Upload Data**.

    [![Selecionando a opção databricks upload data](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Selecione **Criar nova tabela** e selecione **Outras Fontes de Dados**  >  **Azure Blob Storage** Create Table in  >  **Notebook**.

    [![Databricks criação de tabela](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Atualize a localização dos seus dados. Segue-se um exemplo:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Configuração de databricks](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Siga os passos no modelo para visualizar e analisar os seus dados.

## <a name="next-steps"></a>Passos seguintes

[Detete a deriva de dados](how-to-monitor-datasets.md) nos dados recolhidos.