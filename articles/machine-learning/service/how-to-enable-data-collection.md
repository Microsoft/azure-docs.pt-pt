---
title: Recolha de dados de seus modelos de produção
titleSuffix: Azure Machine Learning service
description: Saiba como recolher dados de entrada de modelo do Azure Machine Learning num armazenamento de Blobs do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 12/3/2018
ms.custom: seodec18
ms.openlocfilehash: fa1076fb523c3634cf8e4f4832d36aa7ee715797
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816356"
---
# <a name="collect-data-for-models-in-production"></a>Recolher dados para modelos em produção

Neste artigo, pode aprender a recolher dados de entrada de modelo dos serviços do Azure Machine Learning que implementou no Cluster de Kubernetes do Azure (AKS) para um armazenamento de Blobs do Azure. 

Uma vez ativada, estes dados a que recolher ajudam-o:
* Monitorizar dados drifts como dados de produção inserem seu modelo

* Tome decisões melhores sobre quando voltar a preparar ou otimizar o seu modelo

* Voltar a preparar seu modelo com os dados recolhidos

## <a name="what-is-collected-and-where-does-it-go"></a>O que é recolhido e onde ela vai?

Os seguintes dados podem ser recolhidos:
* Modelo **entrada** dados de serviços web implementados no Cluster de Kubernetes do Azure (AKS) (voz, imagens e vídeo são **não** recolhidos) 
  
* Predições de modelos com dados de entrada de produção.

> [!Note]
> Pré-agregação ou cálculos anteriores sobre estes dados não fazem parte do serviço neste momento.   

A saída é salvo num Blob do Azure. Uma vez que os dados são adicionados a um Blob do Azure, em seguida, pode escolher sua ferramenta favorita para executar a análise. 

O caminho para os dados de saída no blob segue esta sintaxe:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](http://aka.ms/AMLFree) hoje mesmo.

- Uma Azure Machine Learning serviço área de trabalho, um diretório local que contém os scripts e o SDK do Azure Machine Learning para Python instalada. Saiba como obter estes pré-requisitos com o [como configurar um ambiente de desenvolvimento](how-to-configure-environment.md) documento.

- Um modelo de aprendizagem de máquina preparado para ser implementada para o Azure Kubernetes Service (AKS). Se não tiver uma, veja a [preparar o modelo de classificação de imagem](tutorial-train-models-with-aml.md) tutorial.

- Um cluster do serviço Kubernetes do Azure. Para obter informações sobre como criar e implementar a um, consulte a [como implementar e, em que](how-to-deploy-and-where.md) documento.

- [Configurar o ambiente](how-to-configure-environment.md) e instale o [SDK monitorização](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Ativar a recolha de dados
Recolha de dados pode ser ativada, independentemente do modelo que está a ser implementado através do serviço do Azure Machine Learning ou outras ferramentas. 

Para ativá-la, terá de:

1. Abra o ficheiro de classificação. 

1. Adicionar a [seguir código](https://aka.ms/aml-monitoring-sdk) na parte superior do ficheiro:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Declarar as variáveis de coleção de dados no seu `init()` função:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* é um parâmetro opcional, não é necessário configurá-lo se seu modelo não requê-lo. Ter uma correlationId em vigor ajudá-lo para o mapeamento mais fácil com outros dados. (Os exemplos incluem: LoanNumber, CustomerId, etc.)
    
    *Identificador* posteriormente utilizado para criar a estrutura de pasta no seu Blob, pode ser utilizado para dividir os dados "brutos" versus "processados".

3.  Adicione as seguintes linhas de código para o `run(input_df)` função:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. Recolha de dados foi **não** automaticamente definido para **verdadeiro** quando implementa um serviço no AKS, por isso, tem de atualizar o ficheiro de configuração, tais como: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    Também pode ser ativado AppInsights para o monitoramento de serviço ao alterar esta configuração:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. Para criar uma nova imagem e implementar o serviço, consulte a [como implementar e, em que](how-to-deploy-and-where.md) documento.


Se já tiver um serviço com as dependências instaladas no seu **ficheiro de ambiente** e **ficheiro de classificação**, ativar a recolha de dados por:

1. Aceda a [Portal do Azure](https://portal.azure.com).

1. Abra a área de trabalho.

1. Aceda a **implementações** -> **selecionar serviço** -> **editar**.

   ![Editar serviço](media/how-to-enable-data-collection/EditService.PNG)

1. Na **definições avançadas**, Desselecionar **recolha de dados de modelo permitir**. 

    [![Verifique a recolha de dados](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   Nessa janela, também pode optar por "Ativar o diagnóstico do Appinsights" para controlar o estado de funcionamento do seu serviço.  

1. Selecione **atualização** para aplicar a alteração.


## <a name="disable-data-collection"></a>Desativar a recolha de dados
Pode parar a recolha de dados de qualquer altura. Utilize o código de Python ou o portal do Azure para desativar a recolha de dados.

+ Opção 1 - desativar no portal do Azure: 
  1. Inicie sessão no [portal do Azure](https://portal.azure.com).

  1. Abra a área de trabalho.

  1. Aceda a **implementações** -> **selecionar serviço** -> **editar**.

    [![Opção de editar](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. Na **definições avançadas**, Desselecionar **recolha de dados de modelo permitir**. 

    [![Desmarque a opção de recolha de dados](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Selecione **atualização** para aplicar a alteração.

* Opção 2 – utilizar o Python para desativar a recolha de dados:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Validar os seus dados e analisá-lo
Pode escolher qualquer ferramenta de sua preferência para analisar os dados recolhidos para o Blob do Azure. 

Para aceder rapidamente os dados a partir do seu blob:
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Abra a área de trabalho.
1. Clique em **armazenamento**.

    [![Armazenamento](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Siga o caminho para os dados de saída no blob com esta sintaxe:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Análise de dados de modelo através do Power BI

1. Transfira e abra [Power BI Desktop](https://www.powerbi.com)

1. Selecione **obter dados** e clique em [ **armazenamento de Blobs do Azure**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Configuração de Blob do PBI](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Adicionar o seu nome de conta de armazenamento e introduza a chave de armazenamento. Pode encontrar estas informações no seu blob **definições** >> chaves de acesso. 

1. Selecione o contentor **modeldata** e clique em **editar**. 

    [![Navegador do PBI](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. No editor de consultas, clique na coluna "Name" e adicionar a sua conta de armazenamento 1. Caminho de modelo para o filtro. Nota: Se pretender analisar apenas em arquivos, a partir de um ano específico ou mês, expanda apenas o caminho de filtro. Por exemplo, olhar os dados de Março: / modeldata/subscriptionid > / resourcegroupname > / workspacename > / webservicename > / modelname > / modelversion > / identificador > / ano > / 3

1. Filtrar os dados que são relevantes para si com base na **nome**. Se tiver armazenado **previsões** e **entradas** terá de criar uma consulta por cada uma.

1. Clique na seta dupla lado a **conteúdo** coluna para combinar os ficheiros. 

    [![Conteúdo do PBI](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Clique em OK e serão pré-carregar os dados.

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Pode agora clicar **fechar e aplicar** .

1.  Se tiver adicionado entradas e as previsões de suas tabelas serão automaticamente correlacionar pela **RequestId**.

1. Comece a criar relatórios personalizados nos seus dados de modelo.


### <a name="analyzing-model-data-using-databricks"></a>Análise de dados de modelo com o Databricks

1. Criar uma [área de trabalho do Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). 

1. Aceda à sua área de trabalho do Databricks. 

1. No seu databricks selecionar área de trabalho **carregar dados**.

    [![Carregamento de DB](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Criar nova tabela e selecione **outras origens de dados** -> armazenamento de Blobs do Azure -> Create Table no bloco de notas.

    [![Tabela de DB](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Atualize a localização dos seus dados. Segue-se um exemplo:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Siga os passos no modelo de fim de exibir e analisar os seus dados. 

## <a name="example-notebook"></a>Bloco de notas de exemplo

O [how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) bloco de notas demonstra conceitos deste artigo.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
