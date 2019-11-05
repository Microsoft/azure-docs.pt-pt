---
title: Readaptação e implantação de um serviço Web
titleSuffix: Azure Machine Learning Studio (classic)
description: Saiba como atualizar um serviço Web para usar um modelo de aprendizado de máquina treinado recentemente no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: 55b054ffe55430ea106c72cdd91fdfba3a457cf5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73482991"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Readaptação e implantação de um modelo de aprendizado de máquina

O novo treinamento é uma maneira de garantir que os modelos de aprendizado de máquina permaneçam precisos e com base nos dados mais relevantes disponíveis. Este artigo mostra como treinar novamente e implantar um modelo de aprendizado de máquina como um novo serviço Web no Studio (clássico). Se você estiver procurando treinar novamente um serviço Web clássico, [Veja este artigo de instruções.](retrain-classic-web-service.md)

Este artigo pressupõe que você já tenha um serviço Web de previsão implantado. Se você ainda não tiver um serviço Web de previsão, [saiba como implantar um serviço Web Studio (clássico) aqui.](publish-a-machine-learning-web-service.md)

Você seguirá estas etapas para treinar novamente e implantar um novo serviço Web do Machine Learning:

1. Implantar um **serviço Web** de novo treinamento
1. Treinar um novo modelo usando o **serviço Web** de novo treinamento
1. Atualize seu **experimento de previsão** existente para usar o novo modelo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Implantar o serviço Web de novo treinamento

Um serviço Web de novo treinamento permite que você retreine seu modelo com um novo conjunto de parâmetros, como novos dados, e salve-o para mais tarde. Quando você conecta uma **saída do serviço Web** a um **modelo**de treinamento, o teste de treinamento gera um novo modelo para você usar.

Use as etapas a seguir para implantar um serviço Web de novo treinamento:

1. Conecte um módulo de **entrada do serviço Web** à sua entrada de dados. Normalmente, você deseja garantir que os dados de entrada sejam processados da mesma maneira que os dados de treinamento originais.
1. Conecte um módulo de **saída do serviço Web** à saída do seu **modelo de treinamento**.
1. Se você tiver um módulo **modelo de avaliação** , poderá conectar um módulo saída do **serviço Web** para gerar os resultados da avaliação
1. Execute seu experimento.

    Depois de executar o experimento, o fluxo de trabalho resultante deve ser semelhante à imagem a seguir:

    ![Fluxo de trabalho resultante](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Agora, você implanta o teste de treinamento como um serviço Web de novo treinamento que gera um modelo treinado e os resultados de avaliação do modelo.

1. Na parte inferior da tela do experimento, clique em **configurar serviço Web**
1. Selecione **implantar serviço Web [novo]** . O portal de serviços Web do Azure Machine Learning é aberto na página **implantar serviço Web** .
1. Digite um nome para o serviço Web e escolha um plano de pagamento.
1. Selecione **Implementar**.

## <a name="retrain-the-model"></a>Treinar novamente o modelo

Para este exemplo, estamos usando C# para criar o aplicativo de novo treinamento. Você também pode usar o código de exemplo do Python ou do R para realizar essa tarefa.

Use as etapas a seguir para chamar as APIs de novo treinamento:

1. Criar um C# aplicativo de console no Visual Studio: **novo** > **projeto** > **Visual C#**  > **Desktop clássico do Windows** > **aplicativo de console (.NET Framework)** .
1. Entre no portal de serviços Web do Machine Learning.
1. Clique no serviço Web com o qual você está trabalhando.
1. Clique em **consumir**.
1. Na parte inferior da página **consumir** , na seção **código de exemplo** , clique em **lote**.
1. Copie o código C# de exemplo para a execução em lotes e cole-o no arquivo Program.cs. Certifique-se de que o namespace permaneça intacto.

Adicione o pacote NuGet Microsoft. AspNet. WebApi. Client, conforme especificado nos comentários. Para adicionar a referência a Microsoft. WindowsAzure. Storage. dll, talvez seja necessário instalar a [biblioteca de cliente para os serviços de armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

A captura de tela a seguir mostra a página **consumir** no portal de serviços Web Azure Machine Learning.

![Página de consumo](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Atualizar a declaração apiKey

Localize a Declaração **apiKey** :

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Na seção **informações básicas de consumo** da página **consumir** , localize a chave primária e copie-a para a Declaração **apiKey** .

### <a name="update-the-azure-storage-information"></a>Atualizar as informações do armazenamento do Azure

O código de exemplo BES carrega um arquivo de uma unidade local (por exemplo, "C:\temp\CensusInput.csv") para o armazenamento do Azure, processa-o e grava os resultados de volta no armazenamento do Azure.

1. Iniciar sessão no portal do Azure
1. Na coluna de navegação à esquerda, clique em **mais serviços**, procure **contas de armazenamento**e selecione-o.
1. Na lista de contas de armazenamento, selecione uma para armazenar o modelo retreinado.
1. Na coluna de navegação à esquerda, clique em **chaves de acesso**.
1. Copie e salve a **chave de acesso primária**.
1. Na coluna de navegação à esquerda, clique em **BLOBs**.
1. Selecione um contêiner existente ou crie um novo e salve o nome.

Localize as declarações *StorageAccountName*, *StorageAccountKey*e *StorageContainerName* e atualize os valores que você salvou do Portal.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Você também deve garantir que o arquivo de entrada esteja disponível no local que você especificar no código.

### <a name="specify-the-output-location"></a>Especificar o local de saída

Quando você especifica o local de saída na carga de solicitação, a extensão do arquivo especificado em *RelativeLocation* deve ser especificada como `ilearner`.

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Aqui está um exemplo de saída de novo treinamento:

![Saída de novo treinamento](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Avaliar os resultados de novos treinamentos

Quando você executa o aplicativo, a saída inclui a URL e o token de assinaturas de acesso compartilhado que são necessários para acessar os resultados da avaliação.

Você pode ver os resultados de desempenho do modelo retreinado combinando *BaseLocation*, *RelativeLocation*e *SasBlobToken* dos resultados de saída para *output2* e colando a URL completa na barra de endereços do navegador.

Examine os resultados para determinar se o modelo treinado recentemente tem um desempenho melhor do que o existente.

Salve *BaseLocation*, *RelativeLocation*e *SasBlobToken* dos resultados da saída.

## <a name="update-the-predictive-experiment"></a>Atualizar o experimento de previsão

### <a name="sign-in-to-azure-resource-manager"></a>Entrar no Azure Resource Manager

Primeiro, entre em sua conta do Azure de dentro do ambiente do PowerShell usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

### <a name="get-the-web-service-definition-object"></a>Obter o objeto de definição de serviço Web

Em seguida, obtenha o objeto de definição de serviço Web chamando o cmdlet [Get-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservice) .

    $wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Para determinar o nome do grupo de recursos de um serviço Web existente, execute o cmdlet Get-AzMlWebService sem parâmetros para exibir os serviços Web em sua assinatura. Localize o serviço Web e, em seguida, examine sua ID de serviço Web. O nome do grupo de recursos é o quarto elemento na ID, logo após o elemento *resourceGroups* . No exemplo a seguir, o nome do grupo de recursos é default-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Como alternativa, para determinar o nome do grupo de recursos de um serviço Web existente, entre no Azure Machine Learning Portal de serviços Web. Selecione o serviço Web. O nome do grupo de recursos é o quinto elemento da URL do serviço Web, logo após o elemento *resourceGroups* . No exemplo a seguir, o nome do grupo de recursos é default-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>Exportar o objeto de definição de serviço Web como JSON

Para modificar a definição do modelo treinado para usar o modelo treinado recentemente, você deve primeiro usar o cmdlet [Export-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) para exportá-lo para um arquivo de formato JSON.

    Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>Atualizar a referência para o blob ilearner

Nos ativos, localize o [modelo treinado], atualize o valor do *URI* no nó *LOCATIONINFO* com o URI do blob ilearner. O URI é gerado pela combinação de *BaseLocation* e *RelativeLocation* da saída da chamada de retreinamento BES.

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

### <a name="import-the-json-into-a-web-service-definition-object"></a>Importar o JSON para um objeto de definição de serviço Web

Use o cmdlet [Import-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/import-azmlwebservice) para converter o arquivo JSON modificado de volta em um objeto de definição de serviço Web que você pode usar para atualizar o experimento do predicativo.

    $wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>Atualizar o serviço Web

Por fim, use o cmdlet [Update-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/update-azmlwebservice) para atualizar o experimento de previsão.

    Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como gerenciar serviços Web ou controlar várias execuções de experimentos, consulte os seguintes artigos:

* [Explore o portal de serviços Web](manage-new-webservice.md)
* [Gerenciar iterações de experimento](manage-experiment-iterations.md)
