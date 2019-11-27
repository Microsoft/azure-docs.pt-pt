---
title: Coletar dados em seus modelos de produção
titleSuffix: Azure Machine Learning
description: Saiba como recolher dados de entrada de modelo do Azure Machine Learning num armazenamento de Blobs do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 18b92fe090895c3aa08c3c931dfa8bd12db0f2d3
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406459"
---
# <a name="collect-data-for-models-in-production"></a>Recolher dados para modelos em produção
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Este SDK está sendo desativado em breve. Esse SDK ainda é apropriado para os desenvolvedores que monitoram a descompasso de dados em modelos, mas a maioria dos desenvolvedores deve usar o monitoramento de dados simplificado [com o Application insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights). 

Neste artigo, você pode aprender a coletar dados de modelo de entrada de Azure Machine Learning implantado no AKS (cluster kubernetes do Azure) em um armazenamento de BLOBs do Azure. 

Uma vez ativada, estes dados a que recolher ajudam-o:
* [Monitorar descompassos de dados](how-to-monitor-data-drift.md) como dados de produção insira seu modelo

* Tome decisões melhores sobre quando voltar a preparar ou otimizar o seu modelo

* Voltar a preparar seu modelo com os dados recolhidos

## <a name="what-is-collected-and-where-does-it-go"></a>O que é recolhido e onde ela vai?

Os seguintes dados podem ser recolhidos:
* Dados de **entrada** de modelo de serviços Web implantados no cluster de kubernetes do Azure (AKs) (voz, imagens e vídeo **não** são coletados) 
  
* Previsões de modelo usando dados de entrada de produção

> [!Note]
> Pré-agregação ou cálculos anteriores sobre estes dados não fazem parte do serviço neste momento.   

A saída é salvo num Blob do Azure. Uma vez que os dados são adicionados a um Blob do Azure, em seguida, pode escolher sua ferramenta favorita para executar a análise. 

O caminho para os dados de saída no blob segue esta sintaxe:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!Note]
> Em versões do SDK antes de `0.1.0a16` o argumento `designation` foi nomeado `identifier`. Se seu código foi desenvolvido com uma versão anterior, você precisará atualizar de acordo.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje

- Um espaço de trabalho Azure Machine Learning, um diretório local que contém seus scripts e o SDK do Azure Machine Learning para Python instalado. Saiba como obter esses pré-requisitos usando o documento [como configurar um ambiente de desenvolvimento](how-to-configure-environment.md)

- Um modelo de aprendizagem de máquina preparado para ser implementada para o Azure Kubernetes Service (AKS). Se você não tiver um, consulte o tutorial [treinar modelo de classificação de imagem](tutorial-train-models-with-aml.md)

- Um cluster do serviço Kubernetes do Azure. Para obter informações sobre como criar e implantar em um, consulte o documento [como implantar e onde](how-to-deploy-and-where.md)

- [Configurar seu ambiente](how-to-configure-environment.md) e instalar o [SDK de monitoramento](https://aka.ms/aml-monitoring-sdk)

## <a name="enable-data-collection"></a>Ativar a recolha de dados
A coleta de dados pode ser habilitada independentemente do modelo que está sendo implantado por meio de Azure Machine Learning ou outras ferramentas. 

Para ativá-la, terá de:

1. Abrir o arquivo de Pontuação

1. Adicione o [seguinte código](https://aka.ms/aml-monitoring-sdk) na parte superior do arquivo:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Declare suas variáveis de coleta de dados em sua função `init()`:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* é um parâmetro opcional, você não precisa configurá-lo se seu modelo não exigir. Ter uma correlationId em vigor ajudá-lo para o mapeamento mais fácil com outros dados. (Os exemplos incluem: LoanNumber, CustomerId, etc.)
    
    O *identificador* é usado posteriormente para criar a estrutura de pastas em seu BLOB, ele pode ser usado para dividir dados "brutos" versus "processados"

3.  Adicione as seguintes linhas de código à função `run(input_df)`:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. A coleta de dados **não** é definida automaticamente como **true** quando você implanta um serviço no AKs, portanto, você deve atualizar seu arquivo de configuração, como: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    Também pode ser ativado AppInsights para o monitoramento de serviço ao alterar esta configuração:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. Para criar uma nova imagem e implantar o serviço, consulte o documento [como implantar e onde](how-to-deploy-and-where.md)


Se você já tiver um serviço com as dependências instaladas em seu **arquivo de ambiente** e **arquivo de Pontuação**, habilite a coleta de dados por:

1. Ir para [Azure Machine Learning Studio](https://ml.azure.com)

1. Abra seu espaço de trabalho

1. Vá para **Implantações** -> **selecione serviço** -> **Editar**

   ![Editar serviço](media/how-to-enable-data-collection/EditService.PNG)

1. Em **Configurações avançadas**, selecione **habilitar coleta de dados de modelo**

    [![verificar coleta de dados](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   Nessa janela, você também pode optar por "habilitar o diagnóstico de Appinsights" para acompanhar a integridade do serviço

1. Selecione **Atualizar** para aplicar a alteração


## <a name="disable-data-collection"></a>Desativar a recolha de dados
Pode parar a recolha de dados de qualquer altura. Use o código Python ou o Azure Machine Learning Studio para desabilitar a coleta de dados.

+ Opção 1-desabilitar no Azure Machine Learning Studio: 
  1. Entrar no [Azure Machine Learning Studio](https://ml.azure.com)

  1. Abra seu espaço de trabalho

  1. Vá para **Implantações** -> **selecione serviço** -> **Editar**

     [![opção de edição](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. Em **Configurações avançadas**, desmarque **habilitar coleta de dados de modelo**

     [![desmarcar coleta de dados](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Selecione **Atualizar** para aplicar a alteração

  Você também pode acessar essas configurações em seu espaço de trabalho no [Azure Machine Learning Studio](https://ml.azure.com).

+ Opção 2 – utilizar o Python para desativar a recolha de dados:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Validar os seus dados e analisá-lo
Pode escolher qualquer ferramenta de sua preferência para analisar os dados recolhidos para o Blob do Azure.

Para aceder rapidamente os dados a partir do seu blob:

1. Entrar no [Azure Machine Learning Studio](https://ml.azure.com)

1. Abra seu espaço de trabalho
1. Clique em **armazenamento**

    [Armazenamento ![](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Siga o caminho para os dados de saída no blob com esta sintaxe:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Análise de dados de modelo através do Power BI

1. Baixar e abrir [Power bi desktop](https://www.powerbi.com)

1. Selecione **obter dados** e clique em [**armazenamento de BLOBs do Azure**](https://docs.microsoft.com/power-bi/desktop-data-sources)

    [![a configuração do blob PBI](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Adicionar o seu nome de conta de armazenamento e introduza a chave de armazenamento. Você pode encontrar essas informações nas **configurações** do blob > > chaves de acesso

1. Selecione o contêiner **modeldata** e clique em **Editar**

    [![navegador PBI](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. No editor de consultas, clique na coluna "Name" e adicionar a sua conta de armazenamento 1. Caminho de modelo para o filtro. Nota: Se pretender analisar apenas em arquivos, a partir de um ano específico ou mês, expanda apenas o caminho de filtro. Por exemplo, basta examinar os dados de março:/modeldata/SubscriptionId >/resourcegroupname >/WorkspaceName >/WebServiceName >/ModelName >/modelversion >/designation >/year >/3

1. Filtre os dados que são relevantes para você com base no **nome**. Se você armazenou **previsões** e **entradas**, precisará criar uma consulta para cada

1. Clique na seta dupla ao lado da coluna **conteúdo** para combinar os arquivos

    [![conteúdo do PBI](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Clique em OK e os dados serão pré-carregados

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Agora você pode clicar em **fechar e aplicar**

1.  Se você adicionou entradas e previsões, suas tabelas corresponderão automaticamente por **RequestId**

1. Comece a criar seus relatórios personalizados em seus dados de modelo


### <a name="analyzing-model-data-using-databricks"></a>Análise de dados de modelo com o Databricks

1. Criar um [espaço de trabalho do databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

1. Ir para o espaço de trabalho do databricks

1. No espaço de trabalho do databricks, selecione **carregar dados**

    [upload do ![DB](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Criar nova tabela e selecionar **outras fontes de dados** -> armazenamento de BLOBs do Azure-> criar tabela no bloco de anotações

    [tabela ![DB](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Atualize a localização dos seus dados. Segue-se um exemplo:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![O dbsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Siga as etapas no modelo para exibir e analisar seus dados

## <a name="example-notebook"></a>Bloco de notas de exemplo

O notebook [How-to-use-azureml/Deployment/Enable-Data-Collection-for-Models-in-AKs/Enable-Data-Collection-for-Models-in-AKs. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) demonstra os conceitos neste artigo.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
