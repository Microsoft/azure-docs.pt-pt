---
title: Retreinar um serviço web
titleSuffix: ML Studio (classic) - Azure
description: Aprenda a atualizar um serviço web para utilizar um modelo de machine learning recentemente treinado no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: 218c1c98a2ed775ae86c1657156991879708cc7a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217942"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Retree e implante um modelo de aprendizagem automática

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

A reconversão é uma forma de garantir que os modelos de aprendizagem automática se mantenham precisos e baseados nos dados mais relevantes disponíveis. Este artigo mostra como retreinar e implementar um modelo de machine learning como um novo serviço web em Studio (clássico). Se procura retreinar um serviço web clássico, [veja este artigo como fazer.](retrain-classic-web-service.md)

Este artigo assume que já tem um serviço web preditivo implementado. Se ainda não tem um serviço web preditivo, [aprenda a implementar um serviço web Studio (clássico) aqui.](deploy-a-machine-learning-web-service.md)

Seguirá estes passos para retreinar e implementar um novo serviço web de aprendizagem automática:

1. Implementar um serviço web de **reconversão**
1. Treine um novo modelo usando o seu **serviço web de reconversão**
1. Atualize a sua **experiência preditiva** existente para usar o novo modelo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Implementar o serviço web reparametrização

Um serviço web de reconversão permite-lhe retreinar o seu modelo com um novo conjunto de parâmetros, como novos dados, e guardá-lo para mais tarde. Quando liga uma saída de **serviço web** a um modelo de **comboio,** a experiência de treino produz um novo modelo para utilizar.

Utilize os seguintes passos para implementar um serviço web de reconversão:

1. Ligue um módulo de entrada de **serviço Web** à sua entrada de dados. Normalmente, pretende garantir que os seus dados de entrada são tratados da mesma forma que os dados de treino originais.
1. Ligue um módulo de saída do **serviço Web** à saída do seu Modelo de **Comboio**.
1. Se tiver um módulo **De Avaliar O Modelo,** pode ligar um módulo de saída de **serviço Web** para obter os resultados da avaliação
1. Executa a sua experiência.

    Após a execução da sua experiência, o fluxo de trabalho resultante deve ser semelhante à seguinte imagem:

    ![Fluxo de trabalho resultante](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Agora, você implementa a experiência de treino como um serviço web de reconversão que produz um modelo treinado e resultados de avaliação de modelos.

1. Na parte inferior da tela de experiência, clique em **Configurar o Serviço Web**
1. Selecione **implementar o Serviço Web [Novo]** . O portal Azure Machine Learning Web Services abre para a página **de Serviço Web de implantação.**
1. Digite um nome para o seu serviço web e escolha um plano de pagamento.
1. Selecione **Implementar**.

## <a name="retrain-the-model"></a>Retreinar o modelo

Para este exemplo, estamos C# a usar para criar a aplicação de reconversão. Também pode utilizar o código de amostra Python ou R para realizar esta tarefa.

Utilize os seguintes passos para chamar as APIs de reconversão:

1. Crie C# uma aplicação de consola no Estúdio Visual: **New** > **Project** > Visual >  **C# Visual** **Windows Classic Desktop** > Console App **(.NET Framework)** .
1. Inscreva-se no portal de Serviços Web de Aprendizagem automática.
1. Clique no serviço web com o qual está a trabalhar.
1. Clique **em Consumir**.
1. Na parte inferior da página **Consumir,** na secção Código da **Amostra,** clique **em Lote**.
1. Copie C# o código da amostra para a execução do lote e cole-o no ficheiro Program.cs. Certifique-se de que o espaço de nome permanece intacto.

Adicione o pacote NuGet Microsoft.AspNet.WebApi.Client, conforme especificado nos comentários. Para adicionar a referência a Microsoft.WindowsAzure.Storage.dll, poderá ser necessário instalar a [biblioteca de clientes para serviços](https://www.nuget.org/packages/WindowsAzure.Storage)de armazenamento Azure .

A imagem que se segue mostra a página **Consumir** no portal Azure Machine Learning Web Services.

![Página de consumir](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Atualizar a declaração de apikey

Localize a declaração de **apiás:**

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Na secção de **informação** de consumo básico da página **Consumir,** localize a chave primária e copie-a para a declaração de **apios.**

### <a name="update-the-azure-storage-information"></a>Atualizar as informações de armazenamento do Azure

O código de amostra bes envia um ficheiro de uma unidade local (por exemplo, "C:\temp\CensusInput.csv") para o Armazenamento Azure, processa-o e escreve os resultados de volta ao Armazenamento Azure.

1. Iniciar sessão no portal do Azure
1. Na coluna de navegação à esquerda, clique em **Mais serviços,** procure **contas de Armazenamento**e selecione-as.
1. Na lista de contas de armazenamento, selecione um para armazenar o modelo retrained.
1. Na coluna de navegação à esquerda, clique nas **teclas de acesso**.
1. Copiar e guardar a **chave de acesso primário**.
1. Na coluna de navegação esquerda, clique em **Blobs**.
1. Selecione um recipiente existente ou crie um novo e guarde o nome.

Localize as declarações de *StorageAccountName*, *StorageAccountKey*e *StorageContainerName* e atualize os valores que guardou do portal.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Deve também certificar-se de que o ficheiro de entrada está disponível no local que especifica no código.

### <a name="specify-the-output-location"></a>Especifique a localização de saída

Quando especificar a localização de saída na Carga Útil do Pedido, a extensão do ficheiro especificado no *RelativoLocalização* deve ser especificada como `ilearner`.

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Eis um exemplo de reconversão da produção:

![Saída de reparametrização](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Avaliar os resultados da reconversão

Quando executa a aplicação, a saída inclui o URL e assinaturas de acesso partilhados que são necessárias para aceder aos resultados da avaliação.

Pode ver os resultados de desempenho do modelo retreinado combinando a *BaseLocation*, *RelativeLocation*e *SasBlobToken* dos resultados de saída para *a saída2* e colando o URL completo na barra de endereços do navegador.

Examine os resultados para determinar se o modelo recém-treinado tem um melhor desempenho do que o existente.

Guarde a *Localização Base,* *relativalocalização*e *SasBlobToken* dos resultados da saída.

## <a name="update-the-predictive-experiment"></a>Atualizar a experiência preditiva

### <a name="sign-in-to-azure-resource-manager"></a>Inscreva-se no Gestor de Recursos do Azure

Primeiro, inscreva-se na sua conta Azure a partir do ambiente PowerShell utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

### <a name="get-the-web-service-definition-object"></a>Obtenha o objeto de Definição de Serviço Web

Em seguida, obtenha o objeto de Definição de Serviço Web, chamando o cmdlet [Get-AzMlWebService.](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservice)

    $wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Para determinar o nome do grupo de recursos de um serviço web existente, execute o cmdlet Get-AzMlWebService sem quaisquer parâmetros para exibir os serviços web na sua subscrição. Localize o serviço web e, em seguida, examinar sua ID de serviço web. O nome do grupo de recursos é o quarto elemento no ID, logo após o elemento *recursosGroup.* No exemplo a seguir, o nome do grupo de recursos é a predefinição-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Alternativamente, para determinar o nome do grupo de recursos de um serviço web existente, inscreva-se no portal Azure Machine Learning Web Services. Selecione o serviço web. O nome do grupo de recursos é o quinto elemento do URL do serviço web, logo após o elemento *recursosGroup.* No exemplo a seguir, o nome do grupo de recursos é a predefinição-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>Exportar o objeto de Definição de Serviço Web como JSON

Para modificar a definição do modelo treinado para utilizar o modelo recém-treinado, deve primeiro utilizar o cmdlet [Export-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) para exportá-lo para um ficheiro formato JSON.

    Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>Atualize a referência à bolha de ias

Nos ativos, localize o [modelo treinado], atualize o valor *uri* no nó *localInfo* com o URI da bolha iasso. O URI é gerado combinando a *BaseLocation* e a *Localização Relativa* a partir da saída da chamada de reconversão DO BES.

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

### <a name="import-the-json-into-a-web-service-definition-object"></a>Importar o JSON num objeto de Definição de Serviço Web

Utilize o cmdlet [Import-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/import-azmlwebservice) para converter o ficheiro JSON modificado de volta num objeto de Definição de Serviço Web que pode utilizar para atualizar a experiência predicada.

    $wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>Atualizar o serviço web

Por fim, utilize o cmdlet [Update-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/update-azmlwebservice) para atualizar a experiência preditiva.

    Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como gerir os serviços web ou acompanhar as várias experiências, consulte os seguintes artigos:

* [Explore o portal de Serviços Web](manage-new-webservice.md)
* [Gerir iterações de experimentação](manage-experiment-iterations.md)
