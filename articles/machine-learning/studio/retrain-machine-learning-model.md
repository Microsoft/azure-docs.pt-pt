---
title: Voltar a preparar e implementar um serviço web
titleSuffix: Azure Machine Learning Studio
description: Saiba como atualizar um serviço web para utilizar um recentemente treinado modelo machine learning no Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: 9a43a382c1f45f0bf0fb7e93322fcbeef58cb99d
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651180"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Voltar a preparar e implementar um modelo de machine learning

Reparametrização é uma forma de garantir a se manter modelos de aprendizagem automática com base nos dados mais relevantes disponíveis e precisas. Este artigo mostra como voltar a preparar e implementar um modelo de machine learning como um novo serviço web no Studio. Se estiver procurando readaptar um serviço web clássico, [exibir este artigo de procedimento.](retrain-classic-web-service.md)

Este artigo pressupõe que já tem um serviço web preditivo implementado. Se ainda não tiver um serviço web preditivo, [Saiba como implementar um serviço da web de Studio aqui.](publish-a-machine-learning-web-service.md)

Executar os seguintes passos para voltar a preparar e implementar um novo serviço web machine learning:

1. Implementar um **reparametrização do serviço web**
1. Preparar um novo modelo com sua **reparametrização do serviço web**
1. Atualizar os já existentes **experimentação preditiva** para usar o novo modelo

## <a name="deploy-the-retraining-web-service"></a>Implementar o serviço web reparametrização

Um serviço da web reparametrização permite-lhe voltar a preparar o modelo com um novo conjunto de parâmetros, como novos dados e guarde-o para utilizar mais tarde. Quando liga um **saída de serviço Web** para um **Train Model**, a experimentação de preparação produz um novo modelo que pode utilizar.

Utilize os seguintes passos para implementar um serviço da web reparametrização:

1. Ligar um **entrada de serviço da Web** módulo para a sua entrada de dados. Normalmente, desejar garantir que os seus dados de entrada são processados da mesma forma como os dados de treinamento original.
1. Ligar um **saída de serviço Web** módulo à saída do seu **Train Model**.
1. Se tiver uma **Evaluate Model** módulo, pode ligar um **saída de serviço da Web** módulo para enviar os resultados de avaliação
1. Execute a sua experimentação.

    Depois de executar a experimentação, o fluxo de trabalho resultante deve ser semelhante à imagem seguinte:

    ![Fluxo de trabalho resultante](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Agora, implementar a experimentação de preparação como um serviço web reparametrização que produz um modelo preparado e resultados de avaliação do modelo.

1. Na parte inferior da tela de experimentação, clique em **no serviço Web**
1. Selecione **implementar serviço da Web [novo]**. O portal de serviços da Web do Azure Machine Learning abre-se para o **implementar serviço Web** página.
1. Escreva um nome para o seu serviço web e escolha um plano de pagamento.
1. Selecione **Implementar**.

## <a name="retrain-the-model"></a>Voltar a preparar o modelo

Neste exemplo, estamos a utilizar c# para criar o aplicativo de reparametrização. Também pode utilizar o código de exemplo do Python ou R para realizar esta tarefa.

Utilize os seguintes passos para chamar as APIs de reparametrização:

1. Criar um C# consola de aplicação no Visual Studio: **Novos** > **projeto** > **Visual C#**   >  **ambiente de trabalho clássico do Windows**  >   **Aplicação da consola (.NET Framework)**.
1. Inicie sessão no portal de serviços Web Machine Learning.
1. Clique no serviço de web que está trabalhando com.
1. Clique em **consumir**.
1. Na parte inferior a **Consume** página, além do **código de exemplo** secção, clique em **Batch**.
1. Copie o código de exemplo em C# para execução de lotes e colá-lo no ficheiro Program.cs. Certifique-se de que o espaço de nomes permanece intacto.

Adicione o pacote de NuGet Microsoft.AspNet.WebApi.Client, conforme especificado nos comentários. Para adicionar a referência ao Microsoft.WindowsAzure.Storage.dll, poderá ter de instalar o [biblioteca de clientes para serviços de armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

A captura de ecrã a seguir mostra a **Consume** página no portal do Azure Machine Learning Web Services.

![Consumir de página](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Atualizar a declaração de apikey

Localize a **apikey** declaração:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

No **informações básicas de consumo** secção a **Consume** página, localize a chave primária e copie-o para o **apikey** declaração.

### <a name="update-the-azure-storage-information"></a>Atualizar as informações de armazenamento do Azure

O código de exemplo BES carrega um ficheiro de uma unidade local (por exemplo, "C:\temp\CensusInput.csv") ao armazenamento do Azure, processa-os e grava os resultados de volta ao armazenamento do Azure.

1. Iniciar sessão no portal do Azure
1. Na coluna de navegação esquerda, clique em **mais serviços**, procure **contas de armazenamento**e selecioná-lo.
1. Na lista de contas de armazenamento, selecione um para armazenar o modelo retrained.
1. Na coluna de navegação esquerda, clique em **chaves de acesso**.
1. Copie e guarde o **chave de acesso primária**.
1. Na coluna de navegação esquerda, clique em **contentores**.
1. Selecione um contentor existente, ou criar um novo e guarde o nome.

Localize a *StorageAccountName*, *StorageAccountKey*, e *StorageContainerName* declarações e atualize os valores que guardou a partir do portal.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Também tem de garantir que o ficheiro de entrada está disponível na localização que especificou no código.

### <a name="specify-the-output-location"></a>Especifique a localização de saída

Quando especificar a localização de saída no Payload do pedido, a extensão do ficheiro que é especificada na *RelativeLocation* deve ser especificado como `ilearner`.

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Eis um exemplo de saída reparametrização:

![Saída de reparametrização](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Avaliar os resultados de reparametrização

Quando executar o aplicativo, a saída inclui o URL e o token de assinaturas de acesso partilhado que são necessárias para acessar os resultados da avaliação.

Pode ver os resultados de desempenho do modelo retrained ao combinar a *BaseLocation*, *RelativeLocation*, e *SasBlobToken* nos resultados da saída para *output2* e colar o URL completo na barra de endereço do browser.

Examine os resultados para determinar se o modelo treinado recentemente tem um desempenho melhor do que a já existente.

Guardar a *BaseLocation*, *RelativeLocation*, e *SasBlobToken* nos resultados da saída.

## <a name="update-the-predictive-experiment"></a>Atualizar a experimentação preditiva

### <a name="sign-in-to-azure-resource-manager"></a>Inicie sessão para o Azure Resource Manager

Primeiro, inicie sessão sua conta do Azure a partir de ambiente do PowerShell, utilizando o [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet.

### <a name="get-the-web-service-definition-object"></a>Obter o objeto de definição de serviço da Web

Em seguida, obtém o objeto de definição de serviço Web chamando o [Get-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservice) cmdlet.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Para determinar o nome do grupo de recursos de um serviço web existente, execute o cmdlet Get-AzureRmMlWebService sem quaisquer parâmetros para apresentar os serviços web na sua subscrição. Localize o serviço web e, em seguida, examinar sua ID de serviço web. O nome do grupo de recursos é o quarto elemento no ID, logo a seguir a *resourceGroups* elemento. No exemplo a seguir, o nome do grupo de recursos é a predefinição-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Em alternativa, para determinar o nome do grupo de recursos de um serviço web existente, inicie sessão no portal do Azure Machine Learning Web Services. Selecione o serviço web. O nome do grupo de recursos é o elemento quinto do URL do serviço web, logo a seguir a *resourceGroups* elemento. No exemplo a seguir, o nome do grupo de recursos é a predefinição-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>Exportar o objeto de definição de serviço da Web como JSON

Para modificar a definição do modelo preparado para utilizar o modelo treinado recentemente, primeiro tem de utilizar o [Export-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/export-azurermmlwebservice) cmdlet para os exportar para um ficheiro de formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>Atualizar a referência para o blob de ilearner

No ativos, localize o [modelo preparado], atualize o *uri* valor no *locationInfo* nó com o URI do ilearner blob. O URI é gerado ao combinar a *BaseLocation* e o *RelativeLocation* da saída do BES reparametrização chamada.

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

### <a name="import-the-json-into-a-web-service-definition-object"></a>Importe o JSON para um objeto de definição de serviço da Web

Utilizar o [Import-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/import-azurermmlwebservice) cmdlet para converter o ficheiro JSON modificado novamente para um objeto de definição de serviço da Web que pode utilizar para atualizar a experimentação predicative.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>Atualizar o serviço web

Por último, utilize o [AzureRmMlWebService atualização](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/update-azurermmlwebservice) cmdlet para atualizar a experimentação preditiva.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como gerir os serviços web ou mantenha um registo de várias execuções de experimentações, veja os artigos seguintes:

* [Explorar o portal de serviços da Web](manage-new-webservice.md)
* [Gerir iterações de experimentação](manage-experiment-iterations.md)
